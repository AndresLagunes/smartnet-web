<template>
  <div>
    <q-page class="flex flex-center">
      <div class="row table-card">
        <q-table
          class="user-table"
          title="Usuarios"
          :rows="usersData"
          :columns="usersColumns"
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
              <q-btn icon="person_add" @click="addUser()">
                <span class="q-pl-xs">Agregar Usuario</span>
              </q-btn>
            </div>
          </template>
          <template v-slot:body-cell-actions="props">
            <q-td :props="props">
              <q-btn class="action-btn q-mx-sm" icon="mode_edit" color="primary" @click="editUser(props.row)"></q-btn>
              <q-btn class="action-btn q-mx-sm" icon="delete" color="negative" @click="deleteUser(props.row)"></q-btn>
            </q-td>
          </template>
        </q-table>
      </div>
    </q-page>
  </div>

  <UserAdd 
    :isVisible="isAddDialogVisible" 
    @emitter="receiver" 
    ref="addUserRef"
  />
  <UserEdit
    :isVisible="isEditDialogVisible"
    @emitter="receiver"
    ref="editUserRef"
  />
  <UserDelete
    :isVisible="isDeleteDialogVisible"
    @emitter="receiver"
    ref="deleteUserRef"
  />
</template>

<script setup>
import UserAdd from "../components/user-crud/UserAdd.vue";
import UserEdit from "../components/user-crud/UserEdit.vue";
import UserDelete from "../components/user-crud/UserDelete.vue";
import { onMounted, ref } from "vue";
import axios from "axios";

const isAddDialogVisible = ref(false);
const isEditDialogVisible = ref(false);
const isDeleteDialogVisible = ref(false);

const addUserRef = ref(null);
const editUserRef = ref(null);
const deleteUserRef = ref(null);
const statuses = ref([]);
const applications = ref([]);
const filter = ref("");
const usersData = ref([]);
const usersColumns = ref([
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
    name: "username",
    align: "center",
    label: "Nombre de Usuario",
    field: (row) => row.username,
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
      .get("http://localhost:3000/security/users/getAllUsers")
      .then((response) => {
        // console.log(response.data);
        if (response.data.success) {
          usersData.value = response.data.users;
          // console.log(usersData.value);
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
          editUserRef.value.statuses = response.data.statuses;
          addUserRef.value.statuses = response.data.statuses;
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
          editUserRef.value.applications = response.data.applications;
          addUserRef.value.applications = response.data.applications;
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

const editUser = (user) => {
  // console.log(user);
  isEditDialogVisible.value = true;
  editUserRef.value.setupUser(user);
};

const addUser = () => {
  isAddDialogVisible.value = true;
};

const deleteUser = (user) => {
  isDeleteDialogVisible.value = true;
  deleteUserRef.value.setupUser(user);
};

const receiver = (data) => {
  switch (data.caller) {
    case "userAdd":
      isAddDialogVisible.value = false;
      fetchData();
      break;
    case "userAddNothing":
      isAddDialogVisible.value = false;
      break;
    case "userEdit":
      isEditDialogVisible.value = false;
      fetchData();
      break;
    case "userEditNothing":
      isEditDialogVisible.value = false;
      break;
    case "userDelete":
      isDeleteDialogVisible.value = false;
      fetchData();
      break;
    case "userDeleteNothing":
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
.user-table {
  width: 100%;
}
.action-btn {
  flex: 1;
}
.action-box {
  display: flex;
}
</style>
