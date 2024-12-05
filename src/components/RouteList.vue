<template>
  <section class="route-list-view">
    <div class="route-list-view-header">
      <h3 class="ui header">Route List</h3>
      <select @change="sortRoute($event)">
        <option selected disabled>Filtrar:</option>
        <optgroup label="Distance">
          <option value="distance-asc">Mais curtas:</option>
          <option value="distance-desc">Mais longas:</option>
        </optgroup>
        <optgroup label="Duration">
          <option value="duration-asc">Mais rápidas:</option>
          <option value="duration-desc">Mais lentas:</option>
        </optgroup>
      </select>
      <button class="ui button show-all" @click="showAllRoutesButtonPressed">Todas</button>
    </div>

    <div
      class="item"
      v-for="route in routes"
      :key="route.id"
      @click="routeItemPressed(route)"
      :style="{'background-color':route.color}"
    >
      <div>
        <i class="marker alternate icon"></i>
        {{ route.origin.address }}
      </div>
      <div>
        <i class="flag checkered icon"></i>
        {{ route.destination.address }}
      </div>
      <div class="ui label small">{{ route.distance.text }}</div>
      <div class="ui label small">{{ route.duration.text }}</div>
    </div>
  </section>
</template>

<script>
import { EventBus } from "@/EventBus.js";

export default {
  data() {
    return {
      routes: [],
    };
  },
  created() {
    // Carregar as rotas do localStorage
    this.loadRoutes();
  },
  methods: {
    // Carregar as rotas armazenadas no localStorage
    loadRoutes() {
      const storedRoutes = JSON.parse(localStorage.getItem("routes"));
      if (storedRoutes) {
        this.routes = storedRoutes;
      }
    },
    // Ordenar as rotas com base no critério selecionado
    sortRoute(e) {
      const sortName = e.target.value.split("-")[0];
      const sortOrder = e.target.value.split("-")[1];

      // Ordenar as rotas localmente
      this.routes.sort((a, b) => {
        if (sortOrder === "asc") {
          return a[sortName].value - b[sortName].value;
        } else {
          return b[sortName].value - a[sortName].value;
        }
      });
    },
    // Emitir os dados da rota clicada
    routeItemPressed(route) {
      EventBus.$emit("routes-data", [route]);
    },
    // Mostrar todas as rotas
    showAllRoutesButtonPressed() {
      EventBus.$emit("routes-data", this.routes);
    },
    // Função para salvar uma nova rota localmente
    saveRoute(route) {
      this.routes.push(route);
      localStorage.setItem("routes", JSON.stringify(this.routes));
    },
  },
};
</script>

<style scoped>
.route-list-view {
  position: relative;
  z-index: 1;
  max-width: 250px;
  margin: 10px;
  background-color: white;
}

.route-list-view-header {
  padding: 10px;
}

.item {
  padding: 10px;
  cursor: pointer;
}

.item:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.show-all {
  padding: 4px 10px;
}
</style>
