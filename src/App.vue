<script setup>
import { ref, computed } from "vue";

const modal = ref(false);

const horasDelDia = computed(() => {
  return Array.from({ length: 24 }, (_, index) => ({
    hora: index.toString().padStart(2, "0") + ":00",
    sectores: [],
  }));
});

const configuracionesRiego = ref([]);
const seleccionesSector = ref([]);
const horaInicio = ref("");
const horaFin = ref("");
const unidadesFertilizante = ref("");
const tipoFertilizante = ref("");

function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}
const opciones = [
  { id: "123", valor: "sector 1", texto: "Sector 1" },
  { id: "254", valor: "sector 2", texto: "Sector 2" },
  { id: "323", valor: "sector 3", texto: "Sector 3" },
];

const toggleModal = (estado) => {
  modal.value = estado;
};

const añadirConfiguracion = () => {
  const nuevaConfiguracion = {
    sector: seleccionesSector.value,
    inicio: horaInicio.value,
    fin: horaFin.value,
    fertilizante: tipoFertilizante.value,
    unidades: unidadesFertilizante.value,
    bg: getRandomColor(),
  };

  configuracionesRiego.value.push(nuevaConfiguracion);

  const color = getRandomColor(); // Obtener un color aleatorio para esta configuración
  const inicioHora = parseInt(horaInicio.value.split(":")[0]);
  const finHora = parseInt(horaFin.value.split(":")[0]);

  // Asegurarse de que cada hora entre inicio y fin se actualice con el sector y el color
  horasDelDia.value.forEach((hora) => {
    const horaIndex = parseInt(hora.hora.split(":")[0]);
    if (horaIndex >= inicioHora && horaIndex < finHora) {
      // Asignar el nombre del sector (asumiendo selección única por ahora) y el color
      hora.sectores.push({
        nombre:
          opciones.find((o) => o.valor === seleccionesSector.value[0])?.texto ||
          "Sector desconocido",
        color: color,
      });
    }
  });

  // Limpiar los inputs después de añadir la configuración
  seleccionesSector.value = "";
  horaInicio.value = "";
  horaFin.value = "";
  tipoFertilizante.value = "";
  unidadesFertilizante.value = "";

  seleccionesSector.value = [];
  toggleModal(false); // Cerrar el modal
  
};


</script>

<template>
  <div v-if="modal" class="overlay">
    <div class="modal">
      <h2>Configurar riego</h2>
      <div class="cuerpo_modal">
        <div class="sectores">
          <div v-for="opcion in opciones" :key="opcion.id" class="sector_item">
            <label :for="opcion.id">{{ opcion.texto }}</label>
            <input
              type="checkbox"
              v-model="seleccionesSector"
              :id="opcion.id"
              :value="opcion.valor"
            />
          </div>
        </div>
        <div class="inputs">
          <label for="">Inicio</label>
          <input type="time" v-model="horaInicio" />
        </div>
        <div class="inputs">
          <label for="">Fin</label>
          <input type="time" v-model="horaFin" />
        </div>
        <div class="input_fertilizante">
          <label for="">Selecciona fertilizante</label>
          <select v-model="tipoFertilizante">
            <option value="Urea">Urea</option>
            <option value="Sulfato de Amonio">Sulfato de Amonio</option>
            <option value="Nitrato de Amonio">Nitrato de Amonio</option>
            <option value="Nitrato de Calcio">Nitrato de Calcio</option>
            <option value="Ácido Fosfórico">Ácido Fosfórico</option>
          </select>
          <input
            type="number"
            placeholder="Unidades"
            v-model="unidadesFertilizante"
          />
        </div>
        <div class="modal_buttons">
          <button class="primario" @click="añadirConfiguracion()">
            Añadir configuración
          </button>
          <button class="cerrar" @click="toggleModal(false)">Cerrar</button>
        </div>
      </div>
    </div>
  </div>
  <main class="container">

    <div class="content">
      <div class="calendario_semanal">
        <h1>Semana 1</h1>
        <div class="semana_container">
          <div @click="toggleModal(true)" class="dias">Lunes</div>
          <div @click="toggleModal(true)" class="dias">Martes</div>
          <div @click="toggleModal(true)" class="dias">Miercóles</div>
          <div @click="toggleModal(true)" class="dias">Jueves</div>
          <div @click="toggleModal(true)" class="dias">Viernes</div>
          <div @click="toggleModal(true)" class="dias">Sábado</div>
          <div @click="toggleModal(true)" class="dias">Domingo</div>
        </div>
      </div>

      <button class="atras"><<</button>
      <button class="adelante">>></button>
    </div>
    <h2>Lunes</h2>
    <div class="horas_dia">
      <div v-for="hora in horasDelDia" :key="hora.hora" class="hora">
        <p>{{ hora.hora }}</p>
        <div
          v-for="sector in hora.sectores"
          :key="sector.nombre"
          :style="{ backgroundColor: sector.color }"
          class="sector_seleccionado"
        >
          <p>{{ sector.nombre }}</p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.container {
  max-width: 80%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.overlay {
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.6);
  z-index: 5;
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
}
.content {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative; /* Esto permite posicionar absolutamente los botones respecto a este contenedor */
}

.calendario_semanal {
  display: grid;
  margin: 0 auto;
  width: 85%;
}

.calendario_semanal h1 {
  text-align: center;
  font-weight: bold;
}

.semana_container {
  margin-top: 4rem;
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 0.3rem; /* Agrega espacio entre los días para evitar que se toquen */
}

.semana_container .dias {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 8rem;
  border: 1px solid rgba(0, 0, 0, 0.2);
  border-radius: 0.4rem;
  cursor: pointer;
}

.content button {
  border-radius: 50%;
  width: 5rem;
  height: 5rem;
  border: none;
  background-color: aquamarine;
  cursor: pointer;
  font-weight: bold;
  position: absolute; /* Posicionamiento absoluto respecto al contenedor .content */
}

.atras {
  left: 2rem; /* Ajusta según necesidad */
  bottom: 1rem;
}

.adelante {
  right: 2rem; /* Ajusta según necesidad */
  bottom: 1rem;
}

/* MODAL */

.modal {
  width: 25%;
  background-color: #fff;
  display: flex;
  flex-direction: column;
  padding: 2rem;
  border-radius: 0.5rem;
}

.modal h2 {
  font-size: 2rem;
  font-weight: bold;
  text-align: center;
}

.cuerpo_modal {
  margin-top: 2rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 1rem;
}
.cuerpo_modal .sectores {
  display: flex;
  gap: 1rem;
}

.sectores .sector_item {
  display: flex;
  gap: 0.4rem;
}

.inputs {
  display: flex;
  gap: 1rem;
}

.inputs label {
  display: inline-block;
  width: 5rem;
}

.input_fertilizante {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.input_fertilizante select,
.input_fertilizante input {
  width: 40%;
}

.modal_buttons {
  display: flex;
  flex-direction: row-reverse;
  gap: 1rem;
}

.modal_buttons button {
  border: none;
  padding: 1rem;
  cursor: pointer;
  color: #fff;
  border-radius: 0.3rem;
}

.primario {
  background-color: blueviolet;
}

.cerrar {
  background-color: brown;
}

/* 
DETALLE DE HORARIOS */
.horas_dia {
  margin-top: 4rem;
  width: 100%;
  display: grid;
  
}

.hora {
  color: #000;
  height: 6rem;
  border: 1px solid rgba(0, 0, 0, 0.2);
}

main h2 {
  text-align: center;
  margin-top: 4rem;
  font-weight: bold;
}

.sector_seleccionado {
  background-color: chocolate;
  max-width: 50%;
  margin-left: 1rem;

  border-radius: 0.4rem;
}

.sector_seleccionado p {
  color: #fff;
  text-align: center;
}

.sector2 {
  background-color: darkslateblue;
}
</style>
