<template>
  <fieldset class="fieldset">
    <div class="field">
      <label for="profile-current-password">Текущий пароль*</label>
      <div class="input-wrapper">
        <input
          id="profile-current-password"
          v-model="currentPassword"
          type="password"
          class="input"
          :class="{input_invalid: v$.currentPassword.$error}"
        />
        <div
          v-if="v$.currentPassword.$error"
          class="input-wrapper__error-message"
        >
          {{ v$.currentPassword.$errors[0].$message }}
        </div>
      </div>
    </div>
    <div class="field">
      <label for="profile-new-password">Новый пароль*</label>
      <div class="input-wrapper">
        <input
          id="profile-new-password"
          v-model="newPassword"
          type="password"
          class="input"
          :class="{input_invalid: v$.newPassword.$error}"
        />
        <div
          v-if="v$.newPassword.$error"
          class="input-wrapper__error-message"
        >
          {{ v$.newPassword.$errors[0].$message }}
        </div>
      </div>
    </div>
    <div class="field">
      <label for="profile-confirm-password">Подтверждение пароля*</label>
      <div class="input-wrapper">
        <input
          id="profile-confirm-password"
          v-model="confirmPassword"
          type="password"
          class="input"
          :class="{input_invalid: v$.confirmPassword.$error}"
        />
        <div
          v-if="v$.confirmPassword.$error"
          class="input-wrapper__error-message"
        >
          {{ v$.confirmPassword.$errors[0].$message }}
        </div>
      </div>
    </div>
  </fieldset>
</template>
<script>
  import {useVuelidate} from '@vuelidate/core';
  import {required, minLength, sameAs} from '@vuelidate/validators';

  import {toRefs} from 'vue';

  export default {
    name: 'ProfilePassword',
    props: {
      password: {
        type: Object,
        required: true,
      },
    },
    setup(props, {expose}) {
      const {currentPassword, newPassword, confirmPassword} = toRefs(props.password);

      const validationRules = {
        currentPassword: {
          required,
          minLength: minLength(8),
        },
        newPassword: {
          required,
          minLength: minLength(8),
        },
        confirmPassword: {
          required,
          minLength: minLength(8),
          sameAs: sameAs(newPassword),
        },
      };

      const v$ = useVuelidate(validationRules, {
        currentPassword,
        newPassword,
        confirmPassword,
      });

      const reset = () => {
        v$.value.$reset();
      };

      expose({
        validate: v$.value.$validate,
        reset,
      });

      return {
        currentPassword,
        newPassword,
        confirmPassword,
        v$,
      };
    },
  };
</script>
