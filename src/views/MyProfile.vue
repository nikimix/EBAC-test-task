<template>
  <div class="page page_profile">
    <the-header />
    <profile-form @submit="createUser" />
  </div>
</template>

<script>
  import {ref} from 'vue';
  import TheHeader from "@/components/TheHeader.vue";
  import ProfileForm from '@/components/ProfileForm.vue';

  export default {
    components: {
      TheHeader,
      ProfileForm,
    },
    setup() {
      const users = ref([]);
      const createUser = (userData) => {
        users.value.push(userData);
      };

      return {
        users,
        createUser,
      };
    },
  };
</script>
