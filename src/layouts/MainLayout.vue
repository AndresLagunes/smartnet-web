<template>
  <q-layout view="lHh Lpr lFf">
    <div>
      <q-header :class="topbarClasses + ' ' + sidebarExpandedTopbar">
        <q-toolbar>
          <div style="margin-right: 1.5rem">
            <q-btn flat @click="menuExpanded = !menuExpanded">
              <q-icon
                :name="menuExpanded ? 'menu' : 'menu_open'"
                size="2.3rem"
              />
            </q-btn>
          </div>
          <div style="flex: 1">
            <div>
              <img src="../assets/images/tony.png" class="tony-logo" />
            </div>
          </div>
          <div class="menu-container">
            <q-toolbar-title> {{}} </q-toolbar-title>
          </div>
          <div class="filtro-opciones">
            <q-select
              :disabled="true"
              use-input
              v-model="model"
              label="Buscar"
              :options="options"
              :option-label="'text'"
              :option-disable="'header'"
              @filter="filterFn"
              style="width: 250px"
              behavior="menu"
            >
              <template v-slot:no-option>
                <q-item>
                  <q-item-section class="text-grey">
                    No results
                  </q-item-section>
                </q-item>
              </template>
            </q-select>
          </div>
         
        </q-toolbar>
      </q-header>
    </div>

    <div :class="sidebarClasses + ' ' + sidebarExpandedSidebar">
      <div class="menu">
        <br />
        <div class="q-pl-sm">
          <q-list separator>
            <q-item clickable>
              <q-item-section @click="changeRoute('/home')">
                <div class="row">
                  <div style="padding-right: 1rem;">
                    <q-icon name="home" style="font-size: 24px;"/>
                  </div>
                  Inicio
                </div>
              </q-item-section>
            </q-item>
            <div
              v-for="parent in Object.keys(menu)"
              :key="parent"
            >
              <q-expansion-item
                expand-separator
                :icon="menu[parent].icon"
                :label="parent"
                class="parent"
                group="parent-group"
              >
                <q-card style="background-color: inherit">
                  <q-card-section>
                    <div
                      v-for="child in Object.keys(
                        menu[parent].items
                      )"
                      :key="child"
                      class="child"
                    >
                      <q-expansion-item
                        expand-separator
                        :icon="menu[parent].items[child].icon"
                        :label="child"
                        group="child-group"
                      >
                        <div class="child-card">
                          <q-card style="background-color: inherit">
                            <q-card-section>
                              <div
                                v-for="grandchild in menu[parent]
                                  .items[child].items"
                                :key="grandchild"
                                class="grandchild"
                                @click="changeRoute(grandchild.path)"
                              >
                                <q-icon :name="grandchild.icon" style="font-size: 24px;"/>
                                {{ grandchild.text }}
                              </div>
                            </q-card-section>
                          </q-card>
                        </div>
                      </q-expansion-item>
                    </div>
                  </q-card-section>
                </q-card>
              </q-expansion-item>
            </div>
          </q-list>
        </div>
      </div>
    </div>

    <q-page-container>
      <div :class="contentClasses + ' ' + sidebarExpandedContent">
        <router-view />
      </div>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref, watch } from "vue";
import { useRouter, useRoute } from 'vue-router'
const router = useRouter();

const menu = ref({
    Seguridad: {
      items: {
        Catálogos: {
          items: [
            { text: "Usuarios", icon: "lan", path:"/users" },
            { text: "Roles", icon: "hail", path: "/roles" },
          ],
          icon: "menu_book",
        },
      },
      icon: "security",
    },
    Compras: {
      items: {
        Catálogos: {
          items: [
            { text: "Criterios de Almacen", icon: "warehouse", path:"index" },
            { text: "Compradores", icon: "hail", path: "/table" },
            { text: "Proveedores", icon: "local_shipping" },
            { text: "Productos", icon: "category" },
            { text: "Productos Vigentes por Almacén", icon: "warehouse" },
            { text: "Condiciones de Devolución", icon: "keyboard_return" },
          ],
          icon: "menu_book",
        },
        Procesos: {
          items: [
            { text: "Minimo Compra, Prov Sustituto,etc", icon: "" },
            { text: "Captura de Pedido Sugerido", icon: "" },
            { text: "Captura Pedido Manual", icon: "" },
            { text: "Captura de Requisición de Traspaso", icon: "" },
          ],
          icon: "account_tree",
        },
        Consultas: {
          items: [
            { text: "Ventas por Producto", icon: "" },
            { text: "Estadisticas de Ventas-Inventarios", icon: "" },
            { text: "Existencia actual", icon: "" },
            { text: "Rep Caratula Proveedores", icon: "" },
            { text: "Rep Alta Productos", icon: "" },
            { text: "Rep Pedidos por Condición", icon: "" },
          ],
          icon: "quiz",
        },
      },
      icon: "shopping_cart",
    },
    Costos: {
      items: {
        Catálogos: {
          items: [
            { text: "Productos", icon: "" },
            { text: "Productos Vigentes por Almacén", icon: "" },
            { text: "Condiciones de Devolución", icon: "" },
          ],
          icon: "dictionary",
        },
        Consultas: {
          items: [
            { text: "Productos-Crayolas", icon: "" },
            { text: "Crayolas", icon: "" },
          ],
          icon: "quiz",
        },
        Rotativos: {
          items: [
            { text: "Inventario Rotativo", icon: "" },
            { text: "Inventario Rotativo", icon: "" },
          ],
          icon: "360",
        },
      },
      icon: "request_quote",
    },
  },);
const menuExpanded = ref(true);
const options = ref([
  { header: true, text: "Costos" },
  { header: false, text: "Costos 1" },
  { header: false, text: "Costos 2" },
]);
const model = ref(null);
const sidebarClasses = ref("sidebar-normal");
const topbarClasses = ref("topbar-normal");
const bodyClasses = ref("body-normal");
const contentClasses = ref("content-normal");

const sidebarExpandedTopbar = ref("");
const sidebarExpandedSidebar = ref("menu-open");
const sidebarExpandedContent = ref("content-side-bar-open");
const filterFn = (val, update, abort) => {
  update(() => {
    const needle = val.toLowerCase();
    let opt = [];
    let original = menu.value;
    Object.keys(original.value).forEach((m, index) => {
      opt.push({
        header: true,
        text: `- - ${m} - -`,
        index: index,
        parentIndex: 0,
      });

      Object.keys(original.value[m]).forEach((i, index2) => {
        opt.push({
          header: true,
          text: `- ${i} -`,
          index: index2,
          parentIndex: index,
        });
        original.value[m][i].forEach((o, index3) => {
          opt.push({
            header: false,
            text: o,
            index: index3,
            parentIndex: index2,
          });
        });
      });
      opt.push({ header: true, text: " " });
    });
    let filtered = opt.filter((v) => {
      if (v.header) {
        return true;
      } else {
        return v.text.toLowerCase().indexOf(needle) > -1;
      }
    });

    // let toPop = filtered.filter( f => {
    //   if(!f.header){
    //     filtered.filter(x => {
    //       return f.parentIndex ==
    //     })

    //   }
    // });

    filtered.forEach((f) => {
      // if(f.header){
      //   let found = filtered.find(x => {
      //     return x.parent == f.value;
      //   });
      //   if (!found) {
      //     toPop.push(f);
      //   }
      // }
    });
    // console.log(toPop);
    // toPop.forEach(p => {
    //   filtered.splice( filtered.findIndex(f => f == p) , 1);
    // });
    options.value = filtered;
  });
};

watch(menuExpanded, (newVal) => {
  if (newVal) {
    sidebarExpandedSidebar.value = "menu-closed";
    sidebarExpandedContent.value = "content-side-bar-closed";
  } else {
    sidebarExpandedSidebar.value = "menu-open";
    sidebarExpandedContent.value = "content-side-bar-open";
  }
});

const changeRoute = (path) => {
  router.push(path);
}
</script>

<style scoped>
.sidebar-normal {
  top: 5rem;
  height: calc(100vh - 5rem);
  position: fixed;
  display: flex;
  left: 0; /* Add this line */
  z-index: 999; /* Add this line to make sure it's above other elements */
  user-select: none;
  background-color: #eeeeee;
  transition: 0.5s;
}
.menu-open {
  width: 15.5rem;
}
.menu-closed {
  position: absolute;
  left: -15.5rem;
  width: 0;
}
.menu {
  /* padding: 1.5rem; */
  /* color: whitesmoke; */
  flex: auto;
  line-height: 1.5;
  overflow: auto;
}
.topbar-normal {
  width: 100vw;
  height: 5rem;
  position: fixed;
  top: 0; /* Add this line */
  z-index: 1000; /* Add this line to make sure it's above other elements */
  background-color: #ffffff;
}
.topbar-normal .q-icon {
  color: #717171;
}
.content-normal {
  height: calc(100vh - 5rem);
  overflow: auto;
  background-color: #eff3f8;
  box-shadow: inset 0 3px 4px #0000001a;
  box-sizing: border-box;
  transition: 0.5s;

}
.content-side-bar-open {
  /* padding-top: 5rem; */
  padding-left: 15.5rem;
}
.content-side-bar-close {
  padding-left: 0;
}
.tony-logo {
  padding-top: 1rem;
  height: 70px;
}
.parent {
  /* border-top: 1px solid white;
  border-bottom: 1px solid white; */
  /* border-radius: 6px; */
  color: black;
  margin-bottom: 5px;
}
.parent-underline {
  text-decoration: underline;
}
.child {
  /* border-top: 1px solid white;
  border-bottom: 1px solid white; */
  /* border-radius: 6px; */
  /* margin-bottom: 5px; */
}
.child-card {
  /* border-bottom: 2px solid white; */
}
.grandchild {
  /* border-bottom: 1px solid white; */
  margin-left: 10px;
  margin-bottom: 10px;
}
.grandchild:hover {
  background-color: #eeeeee;
  padding-left: 5px;
  box-shadow: 0 1px 5px rgba(0, 0, 0, 0.2), 0 2px 2px rgba(0, 0, 0, 0.14),
    0 3px 1px -2px rgba(0, 0, 0, 0.12);
  text-decoration: underline;
  cursor: pointer;
}
.boton-contextual {
  background-color: antiquewhite;
  color: black;
  border-radius: 3px;
  cursor: pointer;
  margin: 5px;
}
.boton-contextual span {
  padding: 5px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.5);
  flex: 0;
}
.menu-container {
  padding: 5px;
  flex: 0;
}
.filtro-opciones {
  background-color: white;
  border-radius: 3px;
  color: white;
}
.menu-titulo {
  padding-left: 10px !important;
  color: red !important;
}
.body-normal {
  background-color: #ffffff; /* Change the background color when the toggle is on */
}
</style>
