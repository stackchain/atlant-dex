<template lang="pug">
.page
  PageHeader(:title="title")
  .page__body
    .page__sidebar(v-if="sidebar")
      PageNavigation
    .page__content
      slot
</template>

<script>
import PageHeader from 'components/PageLayoutHeader';
import PageNavigation from 'components/PageLayoutNavigation';

export default {
  props: {
    title: {
      type: String,
      default: '',
      required: false,
    },
    sidebar: {
      type: Boolean,
      default: false,
      required: false,
    },
  },
  components: {
    PageHeader,
    PageNavigation,
  },
};
</script>

<style lang="scss">
@import 'variables';
.page {
  min-height: 100%;
  position: relative;
  &__body {
    height: 100%;
    display: flex;
  }
  &__content {
    width: 100%;
    padding: 36px;
    border-top: 1px solid $color_tangaroa;
  }
}
</style>
