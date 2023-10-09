<template>
  <q-dialog :model-value="isVisible" persistent>
    <q-card style="width: 30%;">
      <q-card-section>
        <div class="row items-center">
          <div class="col">
            <q-avatar icon="delete" color="warning" text-color="white" />
            <span class="q-ml-md title-text">Eliminar Usuario</span>
          </div>
          <br />
          <div class="col-auto">
            <q-btn icon="close" color="black" flat @click="close" />
          </div>
        </div>
      </q-card-section>

      <q-card-section>
        <div class="row items-center">
          <div class="col">
            <span class="q-ml-sm">
              Está seguro que desea eleminar al usuario:
            </span>
          </div>
        </div>
        <br />
        <div class="row items-center"> 
          <div class="col">
            <span class="user">
              {{ userData.username }}
            </span>
          </div>
        </div>
      </q-card-section>

      <q-card-section>
        <div class="">
          <q-btn class="btn" icon="delete" color="negative" style="margin-right: 4%;" @click="deleteUser(props.row)">Eliminar</q-btn>
          <q-btn class="btn" icon="close" color="primary" @click="close('nothing')">Cancelar</q-btn>
        </div>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useQuasar } from "quasar";

const $q = useQuasar();

const emits = defineEmits(["emitter"]);

const props = defineProps({
  isVisible: {
    type: Boolean,
    default: false,
  },
});

const userData = ref({
  id: "",
  username: "",
});

const close = (did) => {
  switch (did) {
    case 'nothing':
      emits("emitter", {
        caller: "userDeleteNothing",
        isVisible: props.isVisible,
      });
      break;
  
    default:
      emits("emitter", {
        caller: "userDelete",
        isVisible: props.isVisible,
      });
      break;
  }
};

const deleteUser = async () => {
  console.log(userData.value);
  try {
    await axios
      .post("http://localhost:3000/security/users/deleteUser", userData.value)
      .then((response) => {
        if (response.data.success) {
          $q.notify({
            progress: true,
            message: "Usuario eliminado con éxito",
            color: "primary",
            multiLine: false,
            // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
            // actions: [
            //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
            // ]
          });
          userData.value = {
            id: "",
            username: "",
          }
          close();
        } else {
            $q.notify({
              progress: true,
              message: "Hubo un error al eliminar al usuario",
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
      message: "Hubo un error al eliminar al usuario",
      color: "negative",
      multiLine: false,
      // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
      // actions: [
      //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
      // ]
    });
  }

  close(); // Close the dialog
};

const setupUser = (user) => {
  userData.value = user;
};
defineExpose({ setupUser });
</script>

<style lang="scss">
.title-text {
  font-size: large;
  font-weight: 410;
}
.btn {
  width: 48%;
}

.user {
  font-weight: bold;
  font-size: 20px;
  text-align: center;
  width: 100%;
  display:inline-block
}
</style>
