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
        <h1 v-text="'创建一个空间'" class="mb-4" />
      </div>
      <Block>
        <div class="mb-3">
          使用一个.heco后缀的空间名称开始,Heco测试网使用.test后缀;空间购买合约地址为<a href="https://hecoinfo.com/address/0xC403190d6155cd2A44fBe80A09c23cf3707B1B69#writeContract">0xC403190d6155cd2A44fBe80A09c23cf3707B1B69</a>
          购买空间需要通过<a href="https://goswap.app/#/swap?inputCurrency=0x0298c2b32eae4da002a15f36fdf7615bea3da047&outputCurrency=0x271B54EBe36005A7296894F819D626161C44825C">GOC代币</a>,
          目前象征性收取10GOC或等值HUSD
        </div>
        <UiButton class="text-left width-full mb-3 d-flex px-3">
          <input
            v-model="id"
            class="input flex-auto"
            placeholder="例如: goswap.heco"
          />
          <a
            class="d-block py-1 mr-n2"
            target="_blank"
            href="https://docs.snapshot.page/spaces/create"
          >
            <Icon name="info" size="24" class="text-gray p-1" />
          </a>
        </UiButton>
        <UiButton
          :disabled="!id.includes('.heco') && !id.includes('.test')"
          @click="handleSubmit"
          :loading="loading"
          class="button--submit width-full"
        >
          下一步
        </UiButton>
      </Block>
    </template>
  </Layout>
</template>

<script>
import { mapActions } from 'vuex';
import { getSpace, getSpaceExist } from 'hecovote.js/src/utils';
export default {
  data() {
    return {
      id: '',
      loading: false
    };
  },
  methods: {
    ...mapActions(['notify']),
    async handleSubmit() {
      this.loading = true;
      const spaceExist = await getSpaceExist(this.id);
      if (spaceExist) {
        const result = await getSpace(this.id);
        if (result.toLowerCase() === this.web3.account.toLowerCase()) {
          this.loading = false;
          this.$router.push({ name: 'settings', params: { key: this.id } });
        } else {
          this.notify('空间已被注册,钱包地址不是注册的地址!');
        }
      } else {
        this.loading = false;
        this.notify('空间未被注册');
        // this.$router.push({ name: 'settings', params: { key: this.id } });
      }
    }
  }
};
</script>
