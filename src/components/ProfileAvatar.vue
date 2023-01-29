<template>
  <fieldset class="profile__preview avatar fieldset">
    <img
      class="avatar__picture"
      :src="avatarSrc ? avatarSrc : defaultPicture"
      alt="user image"
    />
    <div class="avatar__actions">
      <label class="avatar__action avatar__action_upload input">
        <input
          type="file"
          hidden
          @change="uploadAvatar"
        />
        Загрузить изображение
      </label>
      <button
        class="avatar__action avatar__action_delete input"
        :disabled="!avatarSrc"
        @click="deleteAvatar"
      >
        Удалить
      </button>
    </div>
  </fieldset>
</template>

<script>
  import defaultPicture from '../assets/profile_preview_default.png';

  export default {
    name: 'ProfileAvatar',
    props: {
      avatarSrc: {
        type: String,
        default: '',
      },
    },
    emits: ['uploadAvatar', 'deleteAvatar'],
    setup(props, {emit}) {
      const uploadAvatar = (evt) => {
        const image = evt.target.files[0];
        const reader = new FileReader();
        reader.readAsDataURL(image);
        reader.onload = (evt) => {
          emit('uploadAvatar', evt.target.result);
        };
      };

      const deleteAvatar = () => {
        emit('deleteAvatar');
      };

      return {
        defaultPicture,
        uploadAvatar,
        deleteAvatar,
      };
    },
  };
</script>

<style scoped lang="scss">
  .avatar {
    &__picture {
      width: 150px;
      height: 150px;
      border-radius: 100%;
    }

    &__actions {
      display: flex;
      flex-wrap: wrap;
      gap: 1rem;
    }

    &__action {
      cursor: pointer;
    }
  }
</style>
