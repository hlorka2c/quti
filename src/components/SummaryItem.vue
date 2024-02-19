<script>
export default {
  name: "summary-item",
  props: {
    title: String,
    extraError: Boolean,
    extra: String,
    body: String,
  },
};
</script>

<template>
  <div class="summary-block">
    <div class="summary-block__header">
      <h3 class="h-3">{{ title }}</h3>
      <p :class="extraError ? 'extra-error' : 'extra-access'" v-if="extra">
        {{ extra }}
      </p>
    </div>
    <div class="summary-block__body">
      {{ body }}
    </div>
  </div>
</template>

<style lang="sass" scoped>
.summary
    &-block
        &__header
            display: flex
            gap: 16px
</style>