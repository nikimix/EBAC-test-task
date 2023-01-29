<template>
  <form class="profile-form form">
    <profile-avatar
      ref="profileAvatarRef"
      :avatar-src="avatarSrc"
      @upload-avatar="uploadAvatar"
      @delete-avatar="deleteAvatar"
    />
    <hr class="separator" />
    <profile-info
      ref="profileInfoRef"
      :user-info="userInfo"
    />
    <hr class="separator" />
    <profile-password
      ref="profilePasswordRef"
      :password="userPassword"
    />
    <div class="form__actions">
      <button
        class="button"
        type="button"
        @click="resetForm"
        @keydown.enter="resetForm"
      >
        Reset
      </button>
      <button
        class="button button_primary"
        type="button"
        @click="submitForm"
        @keydown.enter="submitForm"
      >
        Submit
      </button>
    </div>
  </form>
</template>

<script>
  import ProfileAvatar from '@/components/ProfileAvatar.vue';
  import ProfileInfo from '@/components/ProfileInfo.vue';
  import ProfilePassword from '@/components/ProfilePassword.vue';
  import {ref} from 'vue';

  export default {
    name: 'ProfileForm',
    components: {
      ProfileAvatar,
      ProfileInfo,
      ProfilePassword,
    },
    emits: ['submit'],
    setup(props, {emit}) {
      // AVATAR
      const avatarSrc = ref('');

      const uploadAvatar = (src) => {
        avatarSrc.value = src;
      };
      const deleteAvatar = () => {
        avatarSrc.value = '';
      };

      // USER INFO
      const defaultUserInfo = {
        name: '',
        lastName: '',
        patronymic: '',
        birthdayDate: '',
        email: '',
        city: '',
        phoneNumber: '+375 ',
        languagesSpoken: ['rus'],
      };

      const userInfo = ref({
        ...defaultUserInfo,
      });

      // PASSWORD
      const defaultUserPassword = {
        currentPassword: '',
        newPassword: '',
        confirmPassword: '',
      };
      const userPassword = ref({
        ...defaultUserPassword,
      });

      const profileInfoRef = ref(null);
      const profilePasswordRef = ref(null);

      const resetForm = async () => {
        await profileInfoRef.value.reset();
        await profilePasswordRef.value.reset();
        Object.assign(userInfo.value, defaultUserInfo);
        Object.assign(userPassword.value, defaultUserPassword);
      };

      const submitForm = async () => {
        const isValidUserInfo = await profileInfoRef.value.validate();
        const isValidPassword = await profilePasswordRef.value.validate();
        if (isValidUserInfo && isValidPassword) {
          emit('submit', {
            ...userInfo.value,
            avatarSrc: avatarSrc.value,
          });
          await resetForm();
        }
      };

      return {
        avatarSrc,
        uploadAvatar,
        deleteAvatar,
        userInfo,
        userPassword,
        profileInfoRef,
        profilePasswordRef,
        submitForm,
        resetForm,
      };
    },
  };
</script>

<style scoped lang="scss">
  .form {
    display: grid;
    gap: 1.5rem;
    justify-content: flex-start;

    &__actions {
      display: flex;
      gap: 1rem;
    }
  }

  .separator {
    border: 1px solid #dcdee2;
  }
</style>
