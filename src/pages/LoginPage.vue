<template>
  <q-page class="q-pa-md fixed-center">

    <q-card class="login-card">
      <q-card-section>
        <q-form @submit="login">
          <q-input v-model="loginData.username" label="Nombre de Usuario" />
          <q-input
            v-model="loginData.password"
            label="Contraseña"
            type="password"
          />
          <q-btn label="Login" type="submit" />
        </q-form>
      </q-card-section>
    </q-card>

    <br />

    <q-card class="login-card">
      <q-card-section>
        <q-form @submit="register">
          <q-input
            v-model="registerData.username"
            label="Nuevo Nombre de Usuario"
          />
          <q-input
            v-model="registerData.password"
            label="Nueva Contraseña"
            type="password"
          />
          <q-input
            v-model="registerData.confirmPassword"
            label="Confirme Contraseña"
            type="password"
          />
          <q-btn label="Registrarse" type="submit" />
        </q-form>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref } from "vue";
import { useUserStore } from "src/stores/user-store";
import axios from "axios";
import { useRouter } from "vue-router";
import { useQuasar } from "quasar";

const $q = useQuasar();
const router = useRouter();
const userStore = useUserStore();
const loginData = ref({
  username: "",
  password: "",
});
const registerData = ref({
  username: "",
  password: "",
  confirmPassword: "",
});

const login = async (e) => {
  e.preventDefault();

  try {
    const response = await axios.post(
      "http://localhost:3000/security/users/login",
      {
        username: loginData.value.username,
        password: loginData.value.password,
      }
    );

    console.log(response.data);
    if (response.data.success) {
      // localStorage.setItem('token', response.data.token);
      userStore.setUser(response.data.user); // set user info using Pinia
      router.push("/home"); // if you use Router, navigate to main page
    } else {
      $q.notify({
        progress: true,
        message: response.data.error,
        color: "negative",
        multiLine: false,
        // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
        // actions: [
        //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
        // ]
      });
    }
  } catch (error) {
    console.error("An error occurred during login:", error);
    // Handle login failure, possibly showing a notification to the user
  }
};

const register = async (e) => {
  e.preventDefault();

  try {
    if (registerData.value.password === registerData.value.confirmPassword) {
      const response = await axios.post(
        "http://localhost:3000/security/users/createUser",
        {
          username: registerData.value.username,
          password: registerData.value.password,
          applicationId: 1,
        }
      );
      if (response.data.success) {
        $q.notify({
          progress: true,
          message: "Usuario registrado con éxito",
          color: "primary",
          multiLine: false,
          // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
          // actions: [
          //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
          // ]
        });
      }
    } else {
      $q.notify({
        progress: true,
        message: "La contraseña no coincide.",
        color: "negative",
        multiLine: false,
        // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
        // actions: [
        //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
        // ]
      });
    }
  } catch (error) {
    console.log(error);
    $q.notify({
      progress: true,
      message: "Error al registrar al usuario",
      color: "negative",
      multiLine: false,
      // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
      // actions: [
      //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
      // ]
    });
    // Handle login failure, possibly showing a notification to the user
  }
};
</script>

<style lang="scss">
  .login-card {
    min-width: 300px;
    width: 40%;
  }
</style>
