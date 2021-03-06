<template>
  <div :class="classes">
    <div :class="[cssPrefix + 'toast-inner',cssPrefix + 'toast-' + this.align]">
      <div :class="[cssPrefix + 'toast-content']">
        <template v-if="type">
          <icon v-if="iconCode[type]" v-html="iconCode[type]"></icon>
          <spinner v-if="type==='loading'" color="#999" primary-color="#fff" :class="[cssPrefix + 'toast-spinner']"/>
          <br/>
        </template>
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
import { cssPrefix } from 'utils/variable.js'
import Popup from '../popup'
import Spinner from '../spinner'
import Icon from '../icon'

const iconCode = {
  success: '&#xe654;',
  warn: '&#xe653;',
  fail: '&#xe605;'
}

export default {
  components: {
    Popup,
    Spinner,
    Icon
  },
  props: {
    open: {
      type: Boolean,
      default: false
    },
    align: {
      type: String,
      default: 'top'
    },
    duration: {
      type: Number,
      default: 2000
    },
    type: {
      type: String
    },
    destroy: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    classes () {
      return [cssPrefix + 'toast']
    }
  },
  mounted () {
    this.openChange(this.open)
  },
  methods: {
    openChange (value) {
      if (value) {
        requestAnimationFrame(() => {
          this.$el.style.display = 'table'
        })
        this.duration && setTimeout(() => {
          requestAnimationFrame(() => {
            this.$el.style.display = 'none'
            this.$emit('on-close')
            if (this.destroy) {
              this.$destroy()
            }
          })
        }, this.duration)
      }
    }
  },
  destroyed () {
    requestAnimationFrame(() => {
      this.$el.remove()
    })
  },
  watch: {
    open (value) {
      this.openChange(value)
    }
  },
  data () {
    return {
      cssPrefix: cssPrefix,
      iconCode: iconCode
    }
  }
}
</script>

<style lang="scss">
  @import '~styles/variable.scss';
  @import '~styles/mixins.scss';
  .#{$css-prefix}{
    &toast{
      z-index:1001;
      position:fixed;
      width: 100%;
      height:100%;
      text-align: center;
      display:none;
      top:0;
      left:0;
      &-inner{
        display:table-cell;
      }
      &-content{
        background:rgba(0,0,0,0.7);
        color:#fff;
        padding: 0.2rem;
        border-radius:3px;
        box-shadow: 0px 1px 5px rgba(0,0,0,0.1);
        display:inline-block;
        word-break: break-all;
        box-sizing: border-box;
        max-width: 80%;
        min-width: 2rem;
      }
      &-top{
        padding-top:20%;
      }
      &-bottom{
        vertical-align: bottom;
        padding-bottom:20%;
      }
      &-center{
        vertical-align: middle;
      }
      .#{$css-prefix}iconfont{
        font-size:40px;
        margin-bottom:5px;
      }
      &-spinner{
        width:36px!important;
        height:36px!important;
        margin-bottom:8px;
      }
    }
  }
</style>
