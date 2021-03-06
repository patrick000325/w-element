<template>
  <el-form-item
    :label="label"
    :prop="prop"
    :label-width="labelWidth"
    :rules="rules"
    v-bind="{...formItemAttrs}"
  >
    <el-checkbox-group v-model="selectedValue">
      <template v-for="item in optionsList">
        <el-checkbox-button
          v-if="radioButton"
          :true-label="item.value"
          :false-label="item.value"
          :label="item.value"
          :key="item.value"
          @change="change"
        >{{item.label}}</el-checkbox-button>
        <el-checkbox
          v-else
          :label="item.value"
          :true-label="item.value"
          :false-label="item.value"
          :key="item.value"
          @change="change"
        >{{item.label}}</el-checkbox>
      </template>
    </el-checkbox-group>
  </el-form-item>
</template>
<script>
import { isArray, isPlainObject } from '../../utils'

export default {
  name: 'w-checkbox',
  props: {
    label: {
      type: String,
      default: ''
    },
    prop: {
      type: String,
      default: ''
    },
    value: { default: '' },
    labelWidth: {
      type: [String, Number],
      default: ''
    },
    formItemAttrs: {
      type: Object,
      default: () => ({})
    },
    required: {
      type: Boolean,
      default: null
    },
    options: {
      type: [Array, Object],
      default: () => []
    },
    optionsAttr: {
      type: String,
      default: 'label,value'
    },
    valueKey: {
      type: [String, Number]
    },
    valueLabel: {
      type: [String, Number]
    },
    radioButton: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      selectedValue: [],
      defaultAttrs: {
        clearable: true,
        filterable: true,
        defaultFirstOption: true
      }
    }
  },
  methods: {
    change(val) {
      let index = this.value.indexOf(val)
      if (index !== -1) {
        this.value.splice(index, 1)
      } else {
        this.value.push(val)
      }
    },
    handleChange(val) {
      this.$emit('change', val)
      this.$emit('input', val)
    },
    init() {
      this.selectedValue = this.value || []
    }
  },
  computed: {
    optionsList() {
      // 数组类型
      if (isArray(this.options)) {
        let [label, value] = this.optionsAttr.split(',')
        let list = []
        this.options.map(item => {
          // 如果每项是对象形式
          if (isPlainObject(item)) {
            list.push({ label: item[label], value: item[value] })
          } else {
            list.push({ label: item, value: item })
          }
        })
        return list
      }
      if (isPlainObject(this.options)) {
        let list = []
        Object.keys(this.options).forEach(key => {
          list.push({
            label: this.options[key],
            value: key
          })
        })
        return list
      }
      return this.options
    },
    rules() {
      let trigger = 'change'
      if (this.trigger) {
        trigger = this.trigger
      }
      const required = this.required ?? 'no-required'
      return required !== 'no-required'
        ? {
            required: this.required,
            message: `${this.label}不能为空`,
            trigger
          }
        : undefined
    }
  },
  watch: {
    value: {
      immediate: true,
      handler(val) {
        this.init()
      }
    }
  }
}
</script>

