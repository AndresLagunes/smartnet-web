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
              <q-btn icon="mode_edit" @click="editUser(props.row)"></q-btn>
              <q-btn icon="delete" @click="deleteUser(props.row)"></q-btn>
            </q-td>
          </template>
        </q-table>
      </div>
    </q-page>
  </div>

  <UserAdd v-if="isAddDialogVisible" :isVisible="isAddDialogVisible" @emitter="receiver"/>
</template>

<script setup>
import UserAdd from '../components/user-crud/UserAdd.vue'
import { onMounted, ref } from "vue";
import axios from "axios";

const isAddDialogVisible = ref(false);
const isEditDialogVisible = ref(false);
const isDeleteDialogVisible = ref(false);

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
const fetchUsersData = async () => {
  try {
    // Make an API request or fetch data here
    await axios
      .get("http://localhost:3000/security/users/getAllUsers")
      .then((response) => {
        console.log(response.data);
        if (response.data.success) {
          usersData.value = response.data.users;
          console.log(usersData.value);
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
  console.log(user);
};

const addUser = () => {
  console.log("added");
  isAddDialogVisible.value = true;
};

const receiver = (data) => {
  switch (data.caller) {
    case "userAdd":
      isAddDialogVisible.value = false;
      fetchUsersData();
      break;
  
    default:
      break;
  }
}

// Call fetchUsersData when the component is mounted
onMounted(() => {
  fetchUsersData();
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
</style>
