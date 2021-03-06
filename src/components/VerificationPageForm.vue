<template lang="pug">
form.verificationForm
  VerificationPageFormGroup(
    label="First Name:",
    :validation="getFieldValidationStatus('firstName')"
  )
    VerificationPageFormInput.verificationForm__input(v-model="verification.firstName")
  VerificationPageFormGroup(
    label="Last Name:",
    :validation="getFieldValidationStatus('lastName')"
  )
    VerificationPageFormInput.verificationForm__input(v-model="verification.lastName")
  VerificationPageFormGroup(
    label="Country:",
    :validation="getFieldValidationStatus('country')",
    label-for="null",
  )
    Dropdown.verificationForm__input(
      :options="countries",
      label="name",
      track-by="code",
      v-model="verification.country",
      :loading="countriesLoading",
      searchable
    )
  VerificationPageFormGroup(
    label="City:",
    :validation="getFieldValidationStatus('city')"
    label-for="null",
  )
    Dropdown.verificationForm__input(
      :options="countryCities",
      v-model="verification.city",
      :loading="citiesLoading",
      searchable
    )
  VerificationPageFormGroup(
    label="Street Address:",
    :validation="getFieldValidationStatus('address')"
  )
    VerificationPageFormInput.verificationForm__input(v-model="verification.address")
  VerificationPageFormGroup(
    label="Postcode:",
    :validation="getFieldValidationStatus('postCode')"
  )
    VerificationPageFormInput.verificationForm__input(v-model="verification.postCode")
  VerificationPageFormGroup(
    label="Phone Number:",
    :validation="getFieldValidationStatus('phoneNumber')"
  )
    VerificationPageFormInput.verificationForm__input(
      v-model="verification.phoneNumber",
      :helper="phoneCode"
    )
  VerificationPageFormGroup(
    label="Date of Birth:",
    :validation="getFieldValidationStatus('birthday')"
    label-for="null",
  )
    .verificationForm__dataContainer
      Dropdown.verificationForm__input.verificationForm__input--small(
        placeholder="Day",
        :options="birthdayDays",
        v-model="verification.day",
        searchable,
      )
      Dropdown.verificationForm__input.verificationForm__input--small(
        placeholder="Month",
        :options="birthdayMonths",
        v-model="verification.month",
        searchable,
      )
      Dropdown.verificationForm__input.verificationForm__input--small(
        placeholder="Year",
        :options="birthdayYears",
        v-model="verification.year",
        searchable,
      )
  VerificationPageFormGroup(
    label="ID or Passport #:",
    :validation="getFieldValidationStatus('passportId')"
  )
    VerificationPageFormInput.verificationForm__input(v-model="verification.passportId")
</template>

<script>
import {mapState, mapActions} from 'vuex';
import {birthdayDays, birthdayYears, birthdayMonths} from 'services/birthday';
import Dropdown from 'components/Dropdown';
import VerificationPageFormGroup from './VerificationPageFormGroup';
import VerificationPageFormInput from './VerificationPageFormInput';

export default {
  name: 'VerificationForm',
  data() {
    return {
      birthdayDays,
      birthdayYears,
      birthdayMonths,
    };
  },
  computed: {
    ...mapState('verify', [
      'verification',
    ]),
    ...mapState('geo', [
        'countries',
        'cities',
        'countriesLoading',
        'citiesLoading',
    ]),
    selectedCountry() {
      return this.countries.find((country) => country.code === this.verification.country);
    },
    phoneCode() {
      return this.selectedCountry ? this.selectedCountry.phoneCode : null;
    },
    countryCities() {
      const cities = this.cities[this.verification.country];
      if (!cities) {
        return [];
      } else if (!cities.length) {
        return ['No City'];
      }
      return cities;
    },
  },
  methods: {
    ...mapActions('geo', [
      'getCountries',
      'getCities',
    ]),
    getFieldValidationStatus(name) {
      const $v = this.validations[name] ?
          this.validations :
          this.validations.verification;
      if ($v[name].$error) return 'error';
      return $v[name].$invalid ? 'invalid' : 'valid';
    },
  },
  watch: {
    'verification.country'(country) {
      if (country) {
        this.getCities(country);
      }
    },
    'countryCities'(cities) {
      if (cities.length && cities.indexOf(this.verification.city) === -1) {
        this.verification.city = '';
      }
    },
  },
  mounted() {
    this.getCountries();
  },
  props: {
    validations: Object,
  },
  components: {
    Dropdown,
    VerificationPageFormGroup,
    VerificationPageFormInput,
  },
};
</script>

<style lang="scss" scoped>
  .verificationForm {
    &__input {
      display: block;
      width: 191px;
      &--small {
        width: 80px;
        &:not(:last-of-type) {
          margin-right: 25px;
        }
      }
    }
    &__dataContainer {
      display: flex;
    }
  }
</style>
