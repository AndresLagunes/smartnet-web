<template>
  <q-dialog :model-value="isVisible" persistent>
    <q-card style="width: 40%">
      <q-card-section>
        <div class="row items-center">
          <div class="col">Agregar Rol</div>
          <br />
          <div class="col-auto">
            <q-btn icon="close" color="black" flat @click="close('nothing')" />
          </div>
        </div>
      </q-card-section>
      <q-card-section>
        <q-form @submit="save">
          <!-- Form fields go here -->
          <q-input v-model="roleData.roleName" label="Nombre del Rol" dense />
          <q-input v-model="roleData.description" label="Descripción" dense />
          <q-select
            v-model="roleData.applicationId"
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
            v-model="roleData.statusId"
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
            <q-btn
              class="btn"
              icon="save"
              style="margin-right: 4%"
              type="submit"
              label="Guardar"
              color="primary"
            />
            <q-btn
              class="btn"
              icon="close"
              color="negative"
              @click="close('nothing')"
              >Cancelar</q-btn
            >
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
const passwordConfirmInput = ref("");

const props = defineProps({
  isVisible: {
    type: Boolean,
    default: false,
  },
});

const roleData = ref({
  roleName: "",
  description: "",
  applicationId: "",
  statusId: "",
});

const close = (did) => {
  switch (did) {
    case "nothing":
      emits("emitter", {
        caller: "roleAddNothing",
        isVisible: props.isVisible,
      });
      break;

    default:
      emits("emitter", {
        caller: "roleAdd",
        isVisible: props.isVisible,
      });
      break;
  }
};

const save = async () => {
  try {
    await axios
      .post("http://localhost:3000/security/roles/createRole", roleData.value)
      .then((response) => {
        if (response.data.success) {
          $q.notify({
            progress: true,
            message: "Rol registrado con éxito",
            color: "primary",
            multiLine: false,
            // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
            // actions: [
            //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
            // ]
          });
          roleData.value = {
            roleName: "",
            applicationId: "",
            statusId: "",
            password: "",
          };
          close();
        } else {
          $q.notify({
            progress: true,
            message: "Hubo un error al registrar el rol",
            color: "negative",
            multiLine: false,
            // avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
            // actions: [
            //   { label: 'Reply', color: 'yellow', handler: () => { /* ... */ } }
            // ]
          });
          close("nothing");
        }
      })
      .catch((error) => {
        console.log(error);
      });
  } catch (error) {
    console.log(error);
    $q.notify({
      progress: true,
      message: "Hubo un error al registrar el rol",
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

defineExpose({ statuses, applications });
</script>

<style lang="scss">
.btn {
  width: 48%;
}
</style>
