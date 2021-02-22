<template>
  <span :class="state.class" v-text="state.name" class="State text-normal" />
</template>

<script>
export default {
  props: {
    proposal: Object
  },
  computed: {
    state() {
      const ts = (Date.now() / 1e3).toFixed();
      const { start, end } = this.proposal.msg.payload;
      if (ts > end) return { name: '已关闭', class: 'bg-purple' };
      if (ts > start) return { name: '进行中', class: 'bg-green' };
      return { name: '未开始' };
    }
  }
};
</script>
