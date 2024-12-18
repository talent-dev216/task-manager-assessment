<template>
  <div class="container mt-5" style="margin-top: 10rem;">
    <div class="card p-4 shadow">
      <form @submit.prevent="handleSubmit">
        <div class="mb-3">
          <input
            id="name"
            type="text"
            class="form-control"
            v-model="name"
            placeholder="Name"
          />
          <small v-if="!nameValid && nameTouched" class="text-danger">
            Name must be at least 3 characters.
          </small>
        </div>

        <div class="mb-3">
          <input
            id="email"
            type="email"
            class="form-control"
            v-model="email"
            placeholder="Email"
          />
          <small v-if="!emailValid && emailTouched" class="text-danger">
            Please enter a valid email address.
          </small>
        </div>

        <div class="mb-3">
          <input
            id="password"
            type="password"
            class="form-control"
            v-model="password"
            placeholder="Password"
          />
          <small v-if="!passwordValid && passwordTouched" class="text-danger">
            Password must be at least 6 characters.
          </small>
        </div>

        <div class="text-center">
          <button
            class="btn btn-primary w-100"
            type="submit"
            :disabled="!formValid"
          >
            Create Account
          </button>
        </div>
      </form>
    </div>
    <Toast position="bottom-center" />
  </div>
</template>

<script lang="ts">
import "../style/User.css"
import { defineComponent, ref, computed } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import { useToast } from 'primevue/usetoast';

export default defineComponent({
  setup() {
    const name = ref('');
    const email = ref('');
    const password = ref('');
    const nameTouched = ref(false);
    const emailTouched = ref(false);
    const passwordTouched = ref(false);
    const router = useRouter();
    const toast = useToast();

    // Validation Rules
    const nameValid = computed(() => name.value.length >= 3);
    const emailValid = computed(() => /\S+@\S+\.\S+/.test(email.value));
    const passwordValid = computed(() => password.value.length >= 6);
    const formValid = computed(() => nameValid.value && emailValid.value && passwordValid.value);

    const handleSubmit = async () => {
      try {
        const res = await axios.post(`${process.env.VUE_APP_API_URL}/api/register`, {
          name: name.value,
          email: email.value,
          password: password.value,
        });
        const data = JSON.stringify(res.data);
        localStorage.setItem('userInfo', data);
        toast.add({ severity: 'success', summary: 'Registration Successful', detail: 'Welcome!', life: 3000 });
        router.push('/tasks');
      } catch (err) {
        toast.add({ severity: 'error', summary: 'Registration Failed', detail: 'Invalid information provided', life: 3000 });
        console.error((err as Error).message);
      }
    };

    return {
      name,
      email,
      password,
      nameTouched,
      emailTouched,
      passwordTouched,
      nameValid,
      emailValid,
      passwordValid,
      formValid,
      handleSubmit,
    };
  },
});
</script>

