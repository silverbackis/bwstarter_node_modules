<template>
  <component :is="component" :class="finalClass">
    <slot></slot>
  </component>
</template>

<script>
export default {
  props: {
    className: {
      type: Array,
      default() {
        return []
      }
    },
    nested: {
      type: Boolean,
      required: false,
      default: false
    },
    extendClass: {
      type: Boolean,
      default: true
    },
    domTag: {
      type: String,
      required: false,
      default: null
    }
  },
  computed: {
    component() {
      return this.domTag || (this.nested ? 'div' : 'section')
    },
    componentClass() {
      return this.nested ? 'is-block component' : 'section'
    },
    finalClass() {
      return this.extendClass
        ? [...this.className, this.componentClass]
        : this.className
    }
  }
}
</script>

<style lang="sass">
.component
  position: relative
  + .component
    margin-top: 1.5rem
</style>
