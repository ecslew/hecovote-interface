<template>
  <Layout>
    <template #content-left>
      <div class="px-4 px-md-0 mb-3">
        <router-link :to="{ name: 'home' }" class="text-gray">
          <Icon name="back" size="22" class="v-align-middle" />
          首页
        </router-link>
      </div>
      <div class="px-4 px-md-0">
        <h1 v-if="loaded" v-text="'设置空间'" class="mb-4" />
        <PageLoading v-else />
      </div>
      <template v-if="loaded">
        <!-- <Block title="注册空间合约">
          <UiButton class="d-flex width-full mb-2">
            <input
              readonly
              v-model="contenthash"
              class="input width-full"
              placeholder="Content hash"
            />
            <Icon
              v-clipboard:copy="contenthash"
              v-clipboard:success="handleCopy"
              name="copy"
              size="24"
              class="text-gray p-2 mr-n3"
            />
          </UiButton>
          <a
            :href="`https://app.ens.domains/name/${key}`"
            target="_blank"
            class="mb-2 d-block"
          >
            <UiButton
              :class="!isReady && 'button--submit'"
              class="button-outline width-full"
            >
              {{ isReady ? 'See on ENS' : 'Set record on ENS' }}
              <Icon name="external-link" class="ml-1" />
            </UiButton>
          </a>
        </Block> -->
        <div v-if="isReady">
          <Block title="基本资料">
            <div class="mb-2">
              <a
                :href="
                  `https://github.com/go-protocol/hecovote-spaces/upload/main/spaces/${key}`
                "
                target="_blank"
              >
                <UiButton class="width-full mb-2">
                  上传Logo,文件名为space.png
                  <Icon name="external-link" class="ml-1" />
                </UiButton>
              </a>
              <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">项目名称</div>
                <input v-model="form.name" class="input flex-auto" required />
              </UiButton>
              <!-- <UiButton
                @click="modalNetworksOpen = true"
                class="text-left width-full mb-2 d-flex px-3"
              > -->
              <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">所在网络</div>
                <div class="flex-auto">
                  {{
                    form.network
                      ? networks[form.network].name
                      : key.includes('.heco')
                      ? networks[128].name
                      : networks[256].name
                  }}
                </div>
              </UiButton>
              <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">符号</div>
                <input v-model="form.symbol" class="input flex-auto" required />
              </UiButton>
              <UiButton
                @click="modalSkinsOpen = true"
                class="text-left width-full mb-2 d-flex px-3"
              >
                <div class="text-gray mr-2">主题颜色</div>
                <div class="flex-auto">
                  {{ form.skin ? form.skin : '默认主题' }}
                </div>
              </UiButton>
              <!-- <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">域名</div>
                <input v-model="form.domain" class="input flex-auto" />
                <a
                  class="d-block py-1 mr-n2"
                  target="_blank"
                  href="https://docs.snapshot.page/spaces/add-custom-domain"
                >
                  <Icon name="info" size="24" class="text-gray p-1" />
                </a>
              </UiButton> -->
            </div>
          </Block>
          <Block title="治理策略">
            <div
              v-for="(strategy, i) in form.strategies"
              :key="i"
              class="mb-3 position-relative"
            >
              <a
                @click="handleRemoveStrategy(i)"
                class="position-absolute p-4 right-0"
              >
                <Icon name="close" size="12" />
              </a>
              <a
                @click="handleEditStrategy(i)"
                class="p-4 d-block border rounded-2"
              >
                <h4 v-text="strategy.name" />
              </a>
            </div>
            <UiButton @click="handleAddStrategy" class="d-block width-full">
              添加策略
            </UiButton>
          </Block>
          <Block title="核心成员">
            <UiButton class="d-block width-full px-3" style="height: auto">
              <TextareaArray
                v-model="form.members"
                :placeholder="
                  `0x8C28Cf33d9Fd3D0293f963b1cd27e3FF422B425c\n0xeF8305E140ac520225DAf050e2f71d5fBcC543e7`
                "
                class="input width-full text-left"
                style="font-size: 18px"
              />
            </UiButton>
          </Block>
          <Block title="选项">
            <div class="mb-2">
              <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">默认标签</div>
                <input
                  v-model="form.filters.defaultTab"
                  class="input flex-auto"
                  :placeholder="`&quot;all&quot; &quot;core&quot; 或 &quot;community&quot;`"
                />
              </UiButton>
              <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">发起提案最小Token数量</div>
                <div class="flex-auto">
                  <InputNumber v-model="form.filters.minScore" class="input" />
                </div>
              </UiButton>
              <UiButton class="text-left width-full mb-2 d-flex px-3">
                <div class="text-gray mr-2">只能核心成员发其提案</div>
                <Checkbox
                  v-model="form.filters.onlyMembers"
                  class="input flex-auto"
                  :placeholder="`&quot;yes&quot; or &quot;no&quot;`"
                />
              </UiButton>
              <UiButton class="d-block width-full px-3" style="height: auto">
                <TextareaArray
                  v-model="form.filters.invalids"
                  :placeholder="
                    `隐藏的提案 \nQmc4VSHwY3SVmo4oofhL2qDPaYcGaQqndM4oqdQQe2aZHQ\nQmTMAgnPy2q6LRMNwvj27PHvWEgZ3bw7yTtNNEucBZCWhZ`
                  "
                  class="input width-full text-left"
                  style="font-size: 18px"
                />
              </UiButton>
            </div>
          </Block>
          <Block
            v-if="form.plugins && Object.keys(form.plugins)"
            title="Plugins"
          >
            <div v-for="(plugin, i) in form.plugins" :key="i" class="mb-2">
              <div class="p-4 d-block border rounded-2">
                <h4 v-text="i" />
              </div>
            </div>
          </Block>
        </div>
      </template>
    </template>
    <template v-if="loaded && isReady" #sidebar-right>
      <Block title="动作">
        <UiButton @click="handleReset" class="d-block width-full mb-2">
          重置
        </UiButton>
        <UiButton
          @click="handleSubmit"
          :disabled="!isValid"
          :loading="loading"
          class="d-block width-full button--submit"
        >
          保存
        </UiButton>
      </Block>
    </template>
  </Layout>
  <teleport to="#modal">
    <ModalNetworks
      :open="modalNetworksOpen"
      @close="modalNetworksOpen = false"
      v-model="form.network"
    />
    <ModalSkins
      :open="modalSkinsOpen"
      @close="modalSkinsOpen = false"
      v-model="form.skin"
    />
    <ModalStrategy
      :open="modalStrategyOpen"
      @close="modalStrategyOpen = false"
      @add="handleSubmitAddStrategy"
      :strategy="currentStrategy"
      :strategyIndex="currentStrategyIndex"
    />
  </teleport>
</template>

<script>
import { mapActions } from 'vuex';
import { getAddress } from '@ethersproject/address';
// import { ipfsGet } from 'hecovote.js/src/utils';
// import getProvider from 'hecovote.js/src/utils/provider';
// import { resolveContent } from 'hecovote.js/src/utils/contentHash';
import {
  validateSchema,
  getSpace,
  getSpaceExist,
  fleekGet
} from 'hecovote.js/src/utils';
import schemas from 'hecovote.js/src/schemas';
import networks from 'hecovote.js/src/networks.json';
import { clone } from '@/helpers/utils';
// import gateways from 'hecovote.js/src/gateways.json';

// const gateway = process.env.VUE_APP_IPFS_GATEWAY || gateways[0];

export default {
  data() {
    return {
      key: this.$route.params.key,
      from: this.$route.params.from,
      currentSettings: {},
      currentContenthash: '',
      currentStrategy: {},
      currentStrategyIndex: false,
      modalNetworksOpen: false,
      modalSkinsOpen: false,
      modalStrategyOpen: false,
      loaded: false,
      loading: false,
      form: {
        strategies: [],
        filters: {}
      },
      networks
    };
  },
  computed: {
    validate() {
      return validateSchema(schemas.space, this.form);
    },
    isValid() {
      if (this.validate !== true) console.log(this.validate);
      return !this.loading && this.web3.account && this.validate === true;
    },
    contenthash() {
      const address = this.web3.account
        ? getAddress(this.web3.account)
        : '<your-address>';
      return `ipns://storage.hecovote.com/registry/${address}/${this.key}`;
    },
    isReady() {
      // return this.currentContenthash === this.contenthash;
      return true;
    }
  },
  async created() {
    try {
      // const { protocolType, decoded } = await resolveContent(
      //   getProvider('128'),
      //   this.key
      // );
      // this.currentContenthash = `${protocolType}://${decoded}`;
      // const space = await ipfsGet(gateway, decoded, protocolType);
      // space.filters = space.filters || {};
      // space.strategies = space.strategies || [];
      // this.currentSettings = clone(space);
      // this.form = space;
      const spaceExist = await getSpaceExist(this.key);
      if (spaceExist) {
        const ownerAddress = await getSpace(this.key);
        const space = await fleekGet(ownerAddress, this.key);
        space.filters = space.filters || {};
        space.strategies = space.strategies || [];
        this.currentSettings = clone(space);
        this.form = space;
      }
    } catch (e) {
      console.log('created err:', e);
    }
    if (this.from) {
      const from = clone(this.app.spaces[this.from]);
      delete from.key;
      delete from._activeProposals;
      this.form = from;
    }
    this.loaded = true;
  },
  methods: {
    ...mapActions(['notify', 'send', 'getSpaces']),
    async handleSubmit() {
      this.loading = true;
      try {
        await this.send({
          space: this.key,
          type: 'settings',
          payload: this.form
        });
      } catch (e) {
        console.log(e);
      }
      await this.getSpaces();
      this.loading = false;
    },
    handleReset() {
      if (this.from) return (this.form = clone(this.app.spaces[this.from]));
      if (this.currentSettings) return (this.form = this.currentSettings);
      this.form = {
        strategies: [],
        filters: {}
      };
    },
    handleCopy() {
      this.notify('Copied!');
    },
    handleEditStrategy(i) {
      this.currentStrategyIndex = i;
      this.currentStrategy = clone(this.form.strategies[i]);
      this.modalStrategyOpen = true;
    },
    handleRemoveStrategy(i) {
      this.form.strategies = this.form.strategies.filter(
        (strategy, index) => index !== i
      );
    },
    handleAddStrategy() {
      this.currentStrategyIndex = false;
      this.currentStrategy = {};
      this.modalStrategyOpen = true;
    },
    handleSubmitAddStrategy(strategy) {
      if (this.currentStrategyIndex !== false) {
        this.form.strategies[this.currentStrategyIndex] = strategy;
      } else {
        this.form.strategies = this.form.strategies.concat(strategy);
      }
    }
  }
};
</script>
