<template>
  <div>
    <q-page class="flex flex-center">
      <div class="row table-card">
        <q-table
          class="roles-table"
          title="Roles"
          :rows="rolesData"
          :columns="rolesColumns"
          row-key="name"
          :filter="filter"
        >
          <template v-slot:top-right>
            <div>
              <q-input
                borderless
                dense
                debounce="300"
                v-model="filter"
                placeholder="Search"
              >
                <template v-slot:append>
                  <q-icon name="search" />
                </template>
              </q-input>
            </div>
            <div class="q-pl-md">
              <q-btn icon="add_box" @click="addRole()">
                <span class="q-pl-xs">Agregar Rol</span>
              </q-btn>
            </div>
          </template>
          <template v-slot:body-cell-actions="props">
            <q-td :props="props">
              <q-btn class="action-btn q-mx-sm" icon="mode_edit" color="primary" @click="editRole(props.row)"></q-btn>
              <q-btn class="action-btn q-mx-sm" icon="delete" color="negative" @click="deleteRole(props.row)"></q-btn>
            </q-td>
          </template>
        </q-table>
      </div>
    </q-page>
  </div>

  <RoleAdd 
    :isVisible="isAddDialogVisible" 
    @emitter="receiver" 
    ref="addRoleRef"
  />
  <RoleEdit
    :isVisible="isEditDialogVisible"
    @emitter="receiver"
    ref="editRoleRef"
  />
  <RoleDelete
    :isVisible="isDeleteDialogVisible"
    @emitter="receiver"
    ref="deleteRoleRef"
  />
</template>

<script setup>
import RoleAdd from "../components/role-crud/RoleAdd.vue";
import RoleEdit from "../components/role-crud/RoleEdit.vue";
import RoleDelete from "../components/role-crud/RoleDelete.vue";
import { onMounted, ref } from "vue";
import axios from "axios";

const isAddDialogVisible = ref(false);
const isEditDialogVisible = ref(false);
const isDeleteDialogVisible = ref(false);

const addRoleRef = ref(null);
const editRoleRef = ref(null);
const deleteRoleRef = ref(null);
const statuses = ref([]);
const applications = ref([]);
const filter = ref("");
const rolesData = ref([]);
const rolesColumns = ref([
  {
    name: "id",
    required: true,
    label: "ID",
    align: "left",
    field: (row) => row.id,
    format: (val) => `${val}`,
    sortable: true,
  },
  {
    name: "rolename",
    align: "center",
    label: "Nombre del Rol",
    field: (row) => row.rolename,
    sortable: true,
  },
  {
    name: "description",
    align: "center",
    label: "Descripción",
    field: (row) => row.description,
    sortable: true,
  },
  {
    name: "application",
    label: "Aplicación",
    field: (row) => row.application.applicationName,
    sortable: true,
  },
  {
    name: "status",
    label: "Estatus",
    field: (row) => row.status.statusName,
  },
  {
    name: "actions",
    label: "Acciones",
  },
]);

// Function to fetch and load data
const fetchData = async () => {
  try {
    // Make an API request or fetch data here
    await axios
      .get("http://localhost:3000/security/roles/getAllRoles")
      .then((response) => {
        // console.log(response.data);
        if (response.data.success) {
          rolesData.value = response.data.roles;
          // console.log(rolesData.value);
        } else {
        }
      })
      .catch((error) => {
        console.log(error);
      });

    await axios
      .get("http://localhost:3000/security/statuses/getAllStatuses")
      .then((response) => {
        // console.log(response.data);
        if (response.data.success) {
          statuses.value = response.data.statuses;
          editRoleRef.value.statuses = response.data.statuses;
          addRoleRef.value.statuses = response.data.statuses;
          // console.log(statuses.value);
        } else {
        }
      })
      .catch((error) => {
        console.log(error);
      });

    await axios
      .get("http://localhost:3000/security/applications/getAllApplications")
      .then((response) => {
        // console.log(response.data);
        if (response.data.success) {
          applications.value = response.data.applications;
          editRoleRef.value.applications = response.data.applications;
          addRoleRef.value.applications = response.data.applications;
          // console.log(applications.value);
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

const editRole = (role) => {
  // consoleRole(user);
  isEditDialogVisible.value = true;
  editRoleRef.value.setupRole(role);
};

const addRole = () => {
  isAddDialogVisible.value = true;
};

const deleteRole = (role) => {
  isDeleteDialogVisible.value = true;
  deleteRoleRef.value.setupRole(role);
};

const receiver = (data) => {
  switch (data.caller) {
    case "roleAdd":
      isAddDialogVisible.value = false;
      fetchData();
      break;
    case "roleAddNothing":
      isAddDialogVisible.value = false;
      break;
    case "roleEdit":
      isEditDialogVisible.value = false;
      fetchData();
      break;
    case "roleEditNothing":
      isEditDialogVisible.value = false;
      break;
    case "roleDelete":
      isDeleteDialogVisible.value = false;
      fetchData();
      break;
    case "roleDeleteNothing":
      isDeleteDialogVisible.value = false;
      break;
    default:
      break;
  }
};

// Call fetchData when the component is mounted
onMounted(() => {
  fetchData();
});
</script>

<style>
.options-card {
  border-radius: 5px;
  width: 98%;
  background-color: #ffffff;
  box-shadow: inset 0 3px 4px #0000001a;
}
.table-card {
  width: 98%;
  background-color: #ffffff;
  box-shadow: inset 0 3px 4px #0000001a;
}
.roles-table {
  width: 100%;
}
.action-btn {
  flex: 1;
}
.action-box {
  display: flex;
}
</style>
