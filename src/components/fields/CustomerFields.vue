<template>
  <v-layout row wrap>
    <v-flex xs12 px-2>
      <v-text-field
        id="customer-email"
        label="Email"
        v-model="customer_email"
        :autofocus="autofocusEmail"
        :error-messages="errorMessages"
        @input="handleInput()"
        @blur="handleBlur()"
      ></v-text-field>
    </v-flex>
  </v-layout>
</template>

<script>
import _ from 'lodash'
import { mapFields } from 'vuex-map-fields'
import { required, email } from 'vuelidate/lib/validators'

export default {
  computed: {
    autofocusEmail () {
      return _.isEmpty(this.customer_email)
    },
    errorMessages () {
      const errors = []
      if (!this.$v.customer_email.$dirty) return errors
      !this.$v.customer_email.email && errors.push('Must be valid email')
      !this.$v.customer_email.required && errors.push("Can't be blank")
      return errors
    },
    ...mapFields(['validations.invalid_customer', 'order.customer_email'])
  },
  validations: {
    customer_email: { required, email }
  },
  methods: {
    updateValidations () {
      this.invalid_customer = this.$v.$invalid
    },
    handleInput () {
      this.updateValidations()
    },
    handleBlur () {
      this.$v.customer_email.$touch()
      if (!this.$v.$invalid) this.setCustomerEmail()
    },
    setCustomerEmail () {
      this.$store.dispatch('setOrderCustomerEmail')
    }
  },
  mounted () {
    this.updateValidations()
  }
}
</script>

<style>
</style>
