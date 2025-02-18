<template lang="pug">
div(:class="$style.checkbox")
  input(:type="need ? 'radio' : 'checkbox'" :id="id" :disabled="disabled" :value="value" :name="name" @change="change" v-model="bool")
  label(:for="id" :class="$style.content")
    div(v-if="indeterminate")
      svg(v-show="indeterminate" version='1.1' xmlns='http://www.w3.org/2000/svg' xlink='http://www.w3.org/1999/xlink' height='100%' width="100%" viewBox='0 32 448 448' space='preserve')
        use(xlink:href='#icon-check-indeterminate')
      svg(v-show="!indeterminate" version='1.1' xmlns='http://www.w3.org/2000/svg' xlink='http://www.w3.org/1999/xlink' height='100%' width="100%" viewBox='0 0 448 512' space='preserve')
        use(xlink:href='#icon-check-true')
    div(v-else)
      svg(version='1.1' xmlns='http://www.w3.org/2000/svg' xlink='http://www.w3.org/1999/xlink' height='100%' width="100%" viewBox='0 32 448 448' space='preserve')
        use(xlink:href='#icon-check-true')
    span(v-if="label != null" v-html="label")
</template>

<script>
export default {
  model: {
    prop: 'checked',
    event: 'input',
  },
  props: {
    checked: {},
    value: {},
    id: {
      type: String,
      required: true,
    },
    name: {
      type: String,
    },
    need: {
      type: Boolean,
      default: false,
    },
    label: {},
    disabled: {
      type: Boolean,
      default: false,
    },
    indeterminate: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      bool: false,
    }
  },
  watch: {
    checked(n) {
      this.setValue(n)
    },
  },
  mounted() {
    this.setValue(this.checked)
  },
  methods: {
    change() {
      let checked
      if (Array.isArray(this.checked)) {
        checked = [...this.checked]
        const index = checked.indexOf(this.value)
        if (index < 0) {
          checked.push(this.value)
        } else {
          checked.splice(index, 1)
        }
      } else if (typeof this.checked == 'string') {
        checked = this.bool ? this.value : ''
      } else if (typeof this.checked == 'boolean') {
        let bool = this.bool
        if (this.indeterminate) {
          bool = true
          this.$nextTick(() => {
            this.bool = true
          })
        }
        checked = bool
      }
      this.$emit('input', checked)
      this.$emit('change', checked)
    },
    setValue(value) {
      let bool
      if (Array.isArray(value)) {
        bool = value.includes(this.value)
      } else {
        switch (typeof value) {
          case 'string':
            bool = value === this.value
            break
          case 'boolean':
            bool = value
            break
          default:
            return
        }
      }

      this.bool = this.need ? bool && this.value : bool
    },
  },
}
</script>


<style lang="less" module>
@import '../../assets/styles/layout.less';

.checkbox {
  display: inline-block;
  // font-size: 56px;

  > input {
    display: none;
    &[disabled] {
      + .content {
        opacity: .5;
      }
    }
    &:checked {
      + .content {
        > div {
          &:after {
            border-color: @color-theme;
          }
          svg {
            transform: scale(1);
            opacity: 1;
          }
        }
      }
    }
  }
}
.content {
  display: flex;
  justify-content: center;

  > div {
    flex: none;
    position: relative;
    display: inline-block;
    width: 1em;
    height: 1em;
    cursor: pointer;
    display: flex;
    color: @color-theme;
    // border: 1px solid #ccc;
    &:after {
      position: absolute;
      content: ' ';
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      border: 1px solid #ccc;
      transition: border-color 0.2s ease;
      border-radius: 15%;
    }
    svg {
      transition: 0.2s ease;
      transition-property: transform, opacity;
      transform: scale(0.5);
      opacity: 0;
    }
  }

  > span {
    flex: auto;
    line-height: 1;
    margin-left: 5px;
    cursor: pointer;
  }
}

each(@themes, {
  :global(#container.@{value}) {
    .checkbox {
      > input {
        &:checked {
          + .content {
            > div {
              &:after {
                border-color: ~'@{color-@{value}-theme}';
              }
            }
          }
        }
      }
    }
    .content {
      > div {
        color: ~'@{color-@{value}-theme}';
        // border: 1px solid #ccc;
        &:after {
          border-color: #ccc;
        }
      }
    }
  }
})
</style>
