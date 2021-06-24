<template>
  <div class="container">
    <TheModalWindow ref="modal">
      <div
        class="card-form__close-btn"
        @click="closeModal"
      ></div>
      <div
        class="card-form__sign-btn"
        @click="titleFormLogin = !titleFormLogin"
      >
        {{ titleFormLogin ? 'Sign Up' : 'Log In' }}
      </div>
      <h1 class="title">{{ titleFormLogin ? 'Log In' : 'Sign Up' }}</h1>
      <form @submit.prevent="onSubmit">
        <AppInput
          v-model.trim="formLogin.login"
          :class="{invalid: $v.formLogin.login.$error}"
          :inputData="{ title: 'Name' }"
          :v="$v.formLogin.login"
          autofocus
          placeholder="Name"
        />

        <AppInput
          v-model.trim="formLogin.password"
          :class="{invalid: $v.formLogin.password.$error}"
          :inputData="{ title: 'Password' }"
          :v="$v.formLogin.password"
          type="password"
          placeholder="Password"
        />

        <AppButton class="offset">{{ titleFormLogin ? 'Log In' : 'Sign Up' }}</AppButton>
      </form>
      <p
        v-if="titleFormLogin"
        class="text-primary text-btn"
      >Forgot your password?</p>
    </TheModalWindow>
    <p
      v-if="!validationResult"
      class="text-primary text-btn"
      @click="showModal"
    >{{ titleFormLogin ? 'Log In' : 'Sign Up' }}</p>
    <p
      v-else
      class="text-primary text-btn"
      @click="validationResult = !validationResult"
    >Валидация прошла успешно</p>
  </div>
</template>

<script>
import { validationMixin } from 'vuelidate'
import { required, minLength } from 'vuelidate/lib/validators'

export default {
  mixins: [validationMixin],

  data() {
    return {
      titleFormLogin: true,
      formLogin: {
        login: '',
        password: '',
      },
      validationResult: ''
    }
  },

  validations: {
    formLogin: {
      login: { required, minLength: minLength(4) },
      password: { required, minLength: minLength(8) }
    }
  },

  mounted() {
    this.initClientOnlyComp()
  },

  methods: {
    initClientOnlyComp(count = 10) {
      this.$nextTick(() => {
        if (this.$refs.modal) {
          this.$refs.modal.show = true
        } else if (count > 0) {
          this.initClientOnlyComp(count - 1)
        }
      })
    },
    showModal() {
      this.$refs.modal.show = true
    },
    closeModal() {
      this.$refs.modal.show = false
    },
    onSubmit() {
      if (this.$v.formLogin.$invalid) {
        this.$v.$touch()
        return
      }
      if (!this.$v.formLogin.$error) {
        console.log('Валидация прошла успешно')

        const formData = {
          login: this.formLogin.login,
          password: this.formLogin.password
        }

        // logic push data

        this.validationResult = true
        this.closeModal()
      }
    },
  }

}
</script>

<style lang="sass">
.container
  margin: 0 auto
  min-height: 100vh
  display: flex
  justify-content: center
  align-items: center
  text-align: center
  background-color: #e5e5e5

.card-form
  &__close-btn
    position: absolute
    top: 0
    left: 0
    width: 4rem
    height: 4rem
    cursor: pointer
    &:before,
    &:after
      content: ''
      width: 16px
      height: 2px
      background: $gray-03
      border-radius: 20px
      position: absolute
      left: 19px
      top: 36px
    &:before
      transform: rotate(45deg)
    &:after
      transform: rotate(-45deg)
    &:hover
      &:before,
      &:after
        background-color: darken($gray-03, 5%)

  &__sign-btn
    position: absolute
    top: 1px
    right: 15px
    padding-top: 1.8rem
    height: 4.5rem
    cursor: pointer
    color: $green-primary
    transition: $transitionDefaultHover
    &:hover
      color: darken($green-primary, 5%)

.title
  font-size: 30px
  line-height: 2.9rem
  padding-left: 5px
  margin-bottom: 1.7rem

.text-primary
  color: $green-primary
.text-btn
  cursor: pointer

.button.offset
  margin-top: 1.5rem
  margin-bottom: 1rem
</style>
