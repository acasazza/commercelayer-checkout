<template>
  <div class="step-wrapper">
    <v-stepper-step :step="step" :complete="complete" :editable="complete" :edit-icon="editIcon">
      <div>{{ $t('steps.customer.title') | capitalize }}<span v-if="complete"> &mdash; <a>{{ $t('generic.edit') }}</a></span></div>
      <small>{{ $t('steps.customer.hint') | capitalize }}</small>
    </v-stepper-step>

    <v-stepper-content :step="step">
      <CustomerFields />
      <BillingAddressFields />
      <ShippingAddressFields />
      <v-btn
        color="primary"
        @click="submit()"
        :block="isMobile"
        :disabled="disabled">
          {{ $t('steps.customer.button') }}
      </v-btn>
    </v-stepper-content>

    <div class="step-summary" v-if="complete">
      <v-layout row wrap>
        <v-flex xs12 md6>
          <div class="header">
            {{ order.customer_email }}
          </div>
          <div class="billing-address">
            <AddressSummary :address="order.billing_address" />
          </div>
        </v-flex>
        <v-flex xs12 md4>
          <div class="header">
            {{ $t('generic.ship_to') | capitalize }}:
          </div>
          <div class="shipping-address">
            <AddressSummary :address="order.shipping_address" />
          </div>
        </v-flex>
      </v-layout>
    </div>
  </div>
</template>

<script>
import { checkoutStepMixin } from '@/mixins/checkoutStepMixin'
import CustomerFields from '@/components/fields/CustomerFields'
import BillingAddressFields from '@/components/fields/BillingAddressFields'
import ShippingAddressFields from '@/components/fields/ShippingAddressFields'
import AddressSummary from '@/components/summaries/AddressSummary'

import { mapState } from 'vuex'

export default {
  components: {
    CustomerFields,
    BillingAddressFields,
    ShippingAddressFields,
    AddressSummary
  },
  mixins: [checkoutStepMixin],
  computed: {
    disabled () {
      return this.validations.invalid_customer || this.validations.invalid_billing_address || this.validations.invalid_shipping_address
    },
    ...mapState(['order'])
  },
  methods: {
    submit () {
      this.$store.dispatch('setOrderAddresses')
        .then(() => {
          this.nextStep()
        })
    }
  }
}
</script>

<style scoped>
  .header {
    font-weight: bolder;
    margin-bottom: 0.5rem;
  }
</style>