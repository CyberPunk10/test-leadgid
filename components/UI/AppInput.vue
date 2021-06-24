<template>
  <div class="control text-field">
    <input
      class="text-field__input"
      v-bind="$attrs"
      :value="value"
      @input="$emit('input', $event.target.value)"
      ref="input"
    >
    <span
      v-if="v.$error"
      class="text-field__error-msg"
    >
      {{
        (v.$dirty && !v.required) ? messageEmpty
        : ((v.$dirty && !v.minLength) ? messageIncorrect : '')
      }}
    </span>
  </div>
</template>

<script>
export default {
  inheritAttrs: false, // чтобы КОРНЕВОЙ элемент компонента НЕ наследовал атрибуты (наследовать будет элемент с v-bind="$attrs")

  props: {
    value: {
      type: String,
      default: ''
    },
    v: {
      type: Object,
      required: true
    },
    inputData: {
      type: Object,
      default() { return {} },
    },
  },

  data() {
    return {
      messageEmpty: `Введите ${this.inputData.title}`,
    }
  },

  computed: {
    name: {
      get() {
        return this.value
      },
      set(value) {
        this.$emit("input", value)
      }
    },
    messageIncorrect() {
      // если есть валидация по длине, то выводим сколько символов необходимо
      if ('minLength' in this.v) {
        return `${this.inputData.title} должен быть не менее ${this.v.$params.minLength.min} символов, сейчас: ${this.value.length}`
      }
      // если есть валидация по email, то выводим требования к написанию почты
      if ('email' in this.v) {
        return `${this.inputData.title} должен содержать символ "@". Например myemail@ya.ru`
      }
      return `Введите корректный ${this.inputData.title}`
    }
  },

  mounted() {
    // добавляем автофокус, так как автоматом он добавляется только при загрузке страницы (а нам нужно и при переходе между страницами)
    // (выставляем задержку, чтобы всё успело зарендериться)
    this.$nextTick(function () {
      if (this.$refs.input.getAttribute('autofocus')) this.$refs.input.focus()
    })
  }

}
</script>

<style lang="sass">
.text-field
  $gray-01: #F6F6F6
  $gray-03: #BDBDBD
  $red: #ff6163

  position: relative
  max-width: 343px
  margin-bottom: 1rem
  color: #606266

  &__input
    height: 50px
    width: 100%
    background-color: $gray-01
    font-size: 16px
    padding: 1rem 1rem 15px
    border: 1px solid #E8E8E8
    border-radius: 8px
    letter-spacing: .3px
    color: $gray-03
    outline: none
    box-sizing: border-box
    transition: 0.2s ease all
    &::-webkit-input-placeholder
      color: $gray-03
    &::-moz-placeholder
      color: $gray-03

    &:hover
      border: 1px solid $gray-03
    &:focus
      border: 1px solid $gray-03
      background-color: #fff

  &.invalid
    margin-bottom: .5rem
    input
      border-color: $red

  &__error-msg
    color: $red
    font-size: 12px
</style>
