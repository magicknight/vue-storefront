<template>
  <transition name="fade" appear>
    <div class="cookie fixed w-100 bg-darkgray c-lightgray-secondary" v-if="isOpen">
      <div class="container">
        <div class="row between-xs middle-xs px15">
          <div class="col-xs-10 start-xs">
            <span class="pr5">
              {{ message }}
            </span>
            <router-link :to="detailsLink" :title="detailsLinkText" class="c-gray">
              {{ detailsLinkText }}
            </router-link>
          </div>
          <div class="col-xs-2 end-xs">
            <i class="material-icons icon p15 pointer" @click="accept">close</i>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import i18n from 'lib/i18n'
export default {
  props: {
    detailsLinkText: {
      type: String,
      default: i18n.t('See details')
    },
    detailsLink: {
      type: String,
      default: '/privacy'
    },
    message: {
      type: String,
      default: i18n.t('We use cookies to give you the best shopping experience.')
    }
  },
  data () {
    return {
      isOpen: false
    }
  },
  methods: {
    accept () {
      this.setVisited()
      this.isOpen = false
    },
    setVisited () {
      this.$store.dispatch('claims/set', {claimCode: 'cookiesAccepted', value: true})
    }
  },
  created () {
    this.$store.dispatch('claims/check', {claimCode: 'cookiesAccepted'}).then((cookieClaim) => {
      if (!cookieClaim) {
        this.isOpen = true
        this.$store.dispatch('claims/set', {claimCode: 'cookiesAccepted', value: false})
      } else {
        this.isOpen = !cookieClaim.value
      }
    })
  }
}
</script>

<style lang="scss" scoped>
  @import '~theme/css/base/global_vars';

  $gray: map-get($colors, gray);
  $black: map-get($colors, black);

  .cookie {
    z-index: 2;
    bottom: 0;
  }

  .icon:hover {
    color: $black;
    background-color: $gray;
  }
</style>
