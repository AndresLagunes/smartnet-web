<template>
  <q-dialog :model-value="isVisible" persistent>
    <q-card>
      <q-card-section>
        <div class="row items-center">
          <div class="col">Agregar Usuario</div>
          <br />
          <div class="col-auto">
            <q-icon name="close" @click="close" />
          </div>
        </div>
      </q-card-section>
      <q-card-section>
        <q-form @submit="save">
          <!-- Form fields go here -->
          <q-input 
            v-model="userData.username"
            label="Nombre de Usuario" 
            dense 
          />
          <q-input
            v-model="userData.password"
            label="Contraseña"
            type="password"
            dense
          />
          <q-input
            v-model="passwordConfirmInput"
            label="Confirmar Contraseña"
            type="password"
            dense
          />
          <q-select
            v-model="userData.applicationId"
            :options="applications"
            :option-value="'id'"
            :option-label="'applicationName'"
            label="Aplicación"
            emit-value
            dense
            map-options
            options-dense
          />
          <q-select
            v-model="userData.statusId"
            :options="statuses"
            :option-value="'id'"
            :option-label="'statusName'"
            label="Estatus"
            emit-value
            dense
            map-options
            options-dense
          />

          <q-btn type="submit" label="Save" color="primary" />
        </q-form>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { onMounted, ref, defineEmits } from "vue";
import axios from "axios";
import { useQuasar } from "quasar";

const $q = useQuasar();

const emits = defineEmits(["emitter"]);

const statuses = ref([]);
const applications = ref([]);
const passwordConfirmInput = ref("");

const props = defineProps({
  isVisible: {
    type: Boolean,
    default: false,
  },
});

const userData = ref({
  username: "",
  applicationId: "",
  statusId: "",
  password: "",
});

const close = () => {
  emits("emitter", {
    caller: "userAdd",
    isVisible: props.isVisible,
  });
};

const save = async () => {
  let samePass = userData.value.password == passwordConfirmInput.value;
  console.log(userData.value);
  if (samePass) {
    try {
      await axios
        .post("http://localhost:3000/security/users/createUser", userData)
        .then((response) => {
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
        })
        .catch((error) => {
          console.log(error);
        });
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
    }

    close(); // Close the dialog
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
};

const fetchData = async () => {
  try {
    // Make an API request or fetch data here
    await axios
      .get("http://localhost:3000/security/statuses/getAllStatuses")
      .then((response) => {
        console.log(response.data);
        if (response.data.success) {
          statuses.value = response.data.statuses;
          console.log(statuses.value);
        } else {
        }
      })
      .catch((error) => {
        console.log(error);
      });

    await axios
      .get("http://localhost:3000/security/applications/getAllApplications")
      .then((response) => {
        console.log(response.data);
        if (response.data.success) {
          applications.value = response.data.applications;
          console.log(applications.value);
        } else {
        }
      })
      .catch((error) => {
        console.log(error);
      });
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};
onMounted(() => {
  fetchData();
});
</script>

<style lang="scss"></style>
