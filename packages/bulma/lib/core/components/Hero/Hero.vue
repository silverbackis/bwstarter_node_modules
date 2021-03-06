<template>
  <div v-if="component">
    <component-wrapper
      :class-name="className"
      :extend-class="false"
      :nested="nested"
    >
      <div class="hero-body">
        <div class="container">
          <div class="columns is-vcentered">
            <div class="column">
              <h1 class="title">
                <admin-text-input
                  v-if="$bwstarter.isAdmin"
                  :model="realComponentData.title"
                  :component-id="endpoint"
                  component-field="title"
                  placeholder="Enter page title here"
                />
                <span v-else>{{ realComponentData.title }}</span>
              </h1>
              <h2
                v-if="realComponentData.subtitle || $bwstarter.isAdmin"
                class="subtitle"
              >
                <admin-text-input
                  v-if="$bwstarter.isAdmin"
                  :model="realComponentData.subtitle"
                  :component-id="endpoint"
                  component-field="subtitle"
                  placeholder="Enter optional subtitle here"
                />
                <span v-else>{{ realComponentData.subtitle }}</span>
              </h2>
              <slot name="title-end" />
            </div>
            <div v-if="hasImage" class="column is-narrow">
              <image-loader
                class="image hero-image"
                :image="imageData['file:imagine'].thumbnail.publicPath"
                :placeholder="imageData['file:imagine'].placeholder.publicPath"
                :alt="component.title"
              />
            </div>
          </div>
        </div>
      </div>
      <div v-if="tabs" class="hero-foot">
        <div class="container">
          <bulma-tabs
            _style="boxed"
            :component="tabs"
            :include-nuxt-child="false"
            :nested="true"
            :depth="depth"
          />
        </div>
      </div>
    </component-wrapper>
    <nuxt-child
      v-if="tabs"
      :key="childKey"
      :component-group="tabs.childComponentGroup"
      :nested="false"
    />
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import NuxtChildMixin from '~/.nuxt/bwstarter/bulma/components/nuxtChildMixin'

export default {
  components: {
    BulmaTabs: () =>
      import('~/.nuxt/bwstarter/bulma/components/Nav/Tabs/Tabs.vue'),
    AdminTextInput: () => import('~/.nuxt/bwstarter/components/Admin/Text'),
    ImageLoader: () => import('~/.nuxt/bwstarter/components/Utils/ImageLoader')
  },
  mixins: [NuxtChildMixin],
  computed: {
    ...mapGetters({ getApiUrl: 'bwstarter/getApiUrl' }),
    hasImage() {
      return this.component && this.component.publicPath
    },
    className() {
      const className = ['hero']
      if (this.component.className) {
        className.push(this.component.className)
      } else {
        className.push('is-primary')
      }
      if (this.hasImage) {
        className.push('has-image')
      }
      return className
    },
    tabs() {
      const groups = this.componentGroups || []
      if (
        !groups.length ||
        !groups[0].componentLocations ||
        !groups[0].componentLocations.length
      ) {
        return
      }
      return this.getEntity(
        this.getEntity(groups[0].componentLocations[0]).component
      )
    },
    imageData() {
      if (!this.component) {
        return {}
      }
      const imagePath = this.injectDynamicData(this.component.publicPath)
      if (imagePath !== this.component.publicPath) {
        return this.dynamicData
      }
      return this.component
    }
  }
}
</script>

<style lang="sass">
@import "../../assets/css/vars"

.hero
  +tablet
    &.has-image
      .column.is-narrow
        max-width: 30%
  h1,
  h2
    .cms-text-input
      display: inline-block
      position: relative
      width: 100%
      background: transparent
      border: 1px solid $grey-light
      color: inherit
      font-size: inherit
      padding: .5rem
      &::placeholder
        color: $grey-light
  h2 .cms-text-input
    margin-top: .5rem
</style>
