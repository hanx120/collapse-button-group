<template>
  <el-button-group class="el-button-group-enhance">
    <el-loading-button
      v-for="(button, index) in buttonGroup"
      :key="index"
      :type="button.type || type"
      v-bind="button"
      :loading-click="button.click"
      >{{ button.text }}</el-loading-button
    >
    <el-dropdown v-if="dropDownItem.length" @command="handleCommand">
      <el-button class="el-dropdown__caret-button" :type="type">
        {{ moreActionText }}
        <i class="el-icon-arrow-down el-icon--right"></i>
      </el-button>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item
          v-for="(item, key) in dropDownItem"
          :key="key"
          v-bind="item"
          :command="key"
          >{{ item.text }}</el-dropdown-item
        >
      </el-dropdown-menu>
    </el-dropdown>
  </el-button-group>
</template>

<script>
function isFunction(value) {
  return typeof value === 'function'
}

function isUndefined(value) {
  return value === void 0
}

export default {
  name: 'ElButtonGroupEnhance',

  componentName: 'ElButtonGroupEnhance',

  props: {
    buttons: {
      type: Array,
      default: () => []
    },

    // 从第 n 个按钮开始折叠
    collapseAt: {
      type: Number,
      default: 2,
      validator(value) {
        return value >= 2
      }
    },

    moreActionText: {
      type: String,
      default: '更多'
    },

    type: {
      type: String,
      default: 'default'
    },

    data: {
      type: Object,
      default: () => ({})
    }
  },

  computed: {
    filterButtons() {
      return this.filterHiddenButton(this.buttons)
    },

    shouldDropDown() {
      return this.filterButtons.length > this.collapseAt
    },

    sliceIndex() {
      return this.collapseAt - 1
    },

    buttonGroup() {
      let button
      if (this.shouldDropDown) {
        button = this.filterButtons.slice(0, this.sliceIndex)
      } else {
        button = this.filterButtons
      }

      button.forEach(button => {
        if (isFunction(button.click)) {
          button.click = button.click.bind(this, this.data)
        } else {
          button.click = () => {}
        }
      })

      return button
    },

    dropDownItem() {
      if (this.shouldDropDown) {
        return this.filterButtons.slice(this.sliceIndex)
      }

      return []
    }
  },

  methods: {
    filterHiddenButton(buttons = []) {
      return buttons.filter(button => {
        if (isFunction(button.show)) {
          return button.show(this.data)
        } else {
          return isUndefined(button.show) || button.show
        }
      })
    },

    handleCommand(index) {
      const click = this.dropDownItem[index].click || (() => {})
      window.Promise.resolve(click(this.data))
    }
  }
}
</script>

<style lang="less">
.el-button-group-enhance {
  .el-dropdown .el-dropdown__caret-button {
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
  }

  .el-button:not(:first-child) {
    border-left: none;

    &::before {
      @gap: 5px;

      content: '';
      position: absolute;
      display: block;
      width: 1px;
      top: @gap;
      bottom: @gap;
      left: 0;
      background: mix(white, transparent, 50%);
    }

    &.el-button--default::before {
      background: mix(#cad1e8, transparent, 50%);
    }
  }
}
</style>
