<template>
  <div class="container mt-5">
    <div class="card p-4 shadow">
      <form @submit.prevent="handleSubmit">
        <div class="mb-3">
          <input
            id="email"
            type="email"
            class="form-control"
            v-model="email"
            placeholder="Email Address"
          />
        </div>
  
        <div class="mb-3">
          <input
            id="password"
            type="password"
            class="form-control"
            v-model="password"
            placeholder="Password"
          />
        </div>
  
        <div class="text-center">
          <button class="btn btn-primary w-100" type="submit">Log In</button>
        </div>
      </form>
      <Toast position="bottom-center" />
    </div>
  </div>
</template>

<script lang="ts">
import "../style/User.css"
import { defineComponent, ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import { useToast } from 'primevue/usetoast';

export default defineComponent({
  setup() {
    const email = ref('');
    const password = ref('');
    const router = useRouter();
    const toast = useToast();

    const handleSubmit = async () => {
      try {
        const res = await axios.post(`${process.env.VUE_APP_API_URL}/api/login`, {
          email: email.value,
          password: password.value,
        });
        const data = JSON.stringify(res.data);
        localStorage.setItem('userInfo', data);
        router.push('/tasks');
      } catch (err) {
        toast.add({
          severity: 'error',
          summary: 'Login Failed',
          detail: 'Credentials do not match',
          life: 3000,
        });
        console.error((err as Error).message);
      }
    };

    return {
      email,
      password,
      handleSubmit,
    };
  },
});
</script>
