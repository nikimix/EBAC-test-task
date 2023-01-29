<template>
  <fieldset class="profile__info profile-info fieldset">
    <div class="field">
      <label for="profile-name">Имя*</label>
      <div class="input-wrapper">
        <input
          id="profile-name"
          v-model="name"
          class="input"
          :class="{input_invalid: v$.name.$error}"
          type="text"
        />
        <div
          v-if="v$.name.$error"
          class="input-wrapper__error-message"
        >
          {{ v$.name.$errors[0].$message }}
        </div>
      </div>
    </div>
    <div class="field">
      <label for="profile-last-name">Фамилия*</label>
      <div class="input-wrapper">
        <input
          id="profile-last-name"
          v-model="lastName"
          class="input"
          :class="{input_invalid: v$.lastName.$error}"
          type="text"
        />
        <div
          v-if="v$.lastName.$error"
          class="input-wrapper__error-message"
        >
          {{ v$.lastName.$errors[0].$message }}
        </div>
      </div>
    </div>
    <div class="field">
      <label for="profile-patronymic">Отчество*</label>
      <div class="input-wrapper">
        <input
          id="profile-patronymic"
          v-model="patronymic"
          class="input"
          :class="{input_invalid: v$.patronymic.$error}"
          type="text"
        />
        <div
          v-if="v$.patronymic.$error"
          class="input-wrapper__error-message"
        >
          {{ v$.patronymic.$errors[0].$message }}
        </div>
      </div>
    </div>
    <div class="field">
      <label for="profile-birthday">Дата рождения</label>
      <input
        id="profile-birthday"
        v-model="birthdayDate"
        class="input-date input"
        type="date"
      />
    </div>
    <div class="field">
      <label for="profile-email">Email*</label>
      <div class="input-wrapper">
        <input
          id="profile-email"
          v-model="userEmail"
          class="input"
          :class="{input_invalid: v$.userEmail.$error}"
          type="email"
        />
      </div>
      <div
        v-if="v$.userEmail.$error"
        class="input-wrapper__error-message"
      >
        {{ v$.userEmail.$errors[0].$message }}
      </div>
    </div>

    <div class="field">
      <label for="profile-city">Город</label>
      <input
        id="profile-city"
        v-model="city"
        class="input"
        type="text"
      />
    </div>
    <div class="field">
      <label for="profile-phone-number">Номер телефона</label>
      <div class="field__phone-number-wrapper">
        <select
          v-model="phoneNumberFormat"
          class="format-country"
          @change="changePhoneNumberFormat"
        >
          <option value="+375 ">BLR</option>
          <option value="+7 ">RUS</option>
        </select>
        <input
          id="profile-phone-number"
          v-model="phoneNumber"
          type="tel"
          class="input"
          @input="formatPhoneNumber"
        />
      </div>
    </div>
    <div class="field">
      <label>Владение языками:</label>
      <select
        v-model="languagesSpoken"
        class="languages-spoken"
        multiple
      >
        <option value="rus">Русский</option>
        <option value="eng">English</option>
        <option value="bel">Белорусский</option>
      </select>
    </div>
  </fieldset>
</template>

<script>
  import {ref, toRefs} from 'vue';
  import {useVuelidate} from '@vuelidate/core';
  import {required, email, minLength} from '@vuelidate/validators';

  export default {
    name: 'ProfileInfo',
    props: {
      userInfo: {
        type: Object,
        required: true,
      },
    },
    emits: ['changePhoneNumberFormat'],
    setup(props, {expose}) {
      const {name, lastName, patronymic, birthdayDate, email: userEmail, city, phoneNumber, languagesSpoken} = toRefs(props.userInfo);

      const DEFAULT_NUMBER_FORMAT = '+375 ';
      const phoneNumberFormat = ref(DEFAULT_NUMBER_FORMAT);

      const changePhoneNumberFormat = () => {
        phoneNumber.value = phoneNumberFormat.value;
      };

      const formatPhoneNumber = (evt) => {
        const {value} = evt.target;
        if (value.length < phoneNumberFormat.value.length) {
          phoneNumber.value = phoneNumberFormat.value;
        } else if (!+value[value.length - 1]) {
          phoneNumber.value = value.slice(0, -1);
        }
      };

      const validationRules = {
        name: {
          required,
          minLength: minLength(2),
        },
        lastName: {
          required,
          minLength: minLength(2),
        },
        patronymic: {
          required,
          minLength: minLength(2),
        },
        userEmail: {
          required,
          email,
        },
      };

      const v$ = useVuelidate(validationRules, {
        name,
        lastName,
        patronymic,
        userEmail,
      });

      const reset = () => {
        phoneNumberFormat.value = DEFAULT_NUMBER_FORMAT;
        phoneNumber.value = phoneNumberFormat.value;
        v$.value.$reset();
      };

      expose({
        validate: v$.value.$validate,
        reset,
      });

      return {
        name,
        lastName,
        patronymic,
        birthdayDate,
        userEmail,
        city,
        phoneNumberFormat,
        phoneNumber,
        formatPhoneNumber,
        changePhoneNumberFormat,
        languagesSpoken,
        v$,
      };
    },
  };
</script>

<style scoped lang="scss">
  @use '../styles/utils/variables';

  .input-date {
    cursor: text;
  }

  .format-country {
    width: 60px;
    outline: none;
    padding: 0.5rem 0;
    border-radius: 0.25rem;
  }

  .field {
    &__phone-number-wrapper {
      display: flex;
    }
  }

  .languages-spoken {
    outline: none;
    border-radius: 4px;

    & option {
      padding: 8px;
    }

    &:focus {
      border: 0.0625rem solid variables.$color-black-hard;
    }
  }
</style>
