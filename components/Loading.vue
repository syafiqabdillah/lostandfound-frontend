<template>
  <b-modal
    centered
    hide-header
    hide-footer
    no-close-on-backdrop
    no-close-on-esc
    :visible="visible"
  >
    <div class="content">
      <b-spinner class="mr-2" small></b-spinner>
      Loading...
    </div>
  </b-modal>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      default: false,
    },
  },
}
</script>

<style scoped>
.content {
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>
