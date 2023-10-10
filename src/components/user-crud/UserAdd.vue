<template>
  <q-dialog :model-value="isVisible" persistent>
    <q-card style="width: 40%;">
      <q-card-section>
        <div class="row items-center">
          <div class="col">Agregar Usuario</div>
          <br />
          <div class="col-auto">
            <q-btn icon="close" color="black" flat @click="close('nothing')" />
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
            v-model="userData.roleId"
            :options="roles"
            :option-value="'id'"
            :option-label="'roleName'"
            label="Rol"
            emit-value
            dense
            map-options
            options-dense
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

          <div class="q-pt-lg">
            <q-btn class="btn" icon="save" style="margin-right: 4%;"  type="submit" label="Guardar" color="primary" />
            <q-btn class="btn" icon="close" color="negative" @click="close('nothing')">Cancelar</q-btn>
          </div>
        </q-form>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";
import { useQuasar } from "quasar";

const $q = useQuasar();

const emits = defineEmits(["emitter"]);

const statuses = ref([]);
const applications = ref([]);
const roles = ref([]);
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
  roleId: "",
  statusId: "",
  password: "",
});

const close = (did) => {
  switch (did) {
    case 'nothing':
      emits("emitter", {
        caller: "userAddNothing",
        isVisible: props.isVisible,
      });
      break;
  
    default:
      emits("emitter", {
        caller: "userAdd",
        isVisible: props.isVisible,
      });
      break;
  }
};

const save = async () => {
  let samePass = userData.value.password == passwordConfirmInput.value;
  // console.log(userData.value);
  if (samePass) {
    try {
      await axios
        .post("http://localhost:3000/security/users/createUser", userData.value)
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
            userData.value = {
              username: "",
              applicationId: "",
              statusId: "",
              password: "",
            };
            passwordConfirmInput.value = "";

            close();
          } else {
            $q.notify({
              progress: true,
              message: "Hubo un error al registrar al usuario",
              color: "negative",
              multiLine: false,
              // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
              // actions: [
              //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
              // ]
            });
            close('nothing');
          }
        })
        .catch((error) => {
          console.log(error);
        });
    } catch (error) {
      console.log(error);
      $q.notify({
        progress: true,
        message: "Hubo un error al registrar al usuario",
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

defineExpose({statuses, applications, roles});
</script>

<style lang="scss">
.btn {
  width: 48%;
}
</style>
