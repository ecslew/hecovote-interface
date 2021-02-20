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
          使用一个.heco后缀的空间名称开始,Heco测试网使用.test后缀
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
          class="button--submit width-full"
        >
          下一步
        </UiButton>
      </Block>
    </template>
  </Layout>
</template>

<script>
export default {
  data() {
    return {
      id: ''
    };
  },
  methods: {
    handleSubmit() {
      this.$router.push({ name: 'settings', params: { key: this.id } });
    }
  }
};
</script>
