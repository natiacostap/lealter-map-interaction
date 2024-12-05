<template>
  <div class="gt-sm q-px-xs">
    <q-item>
      <q-item-label header>Selecciona bloque horario</q-item-label>
    </q-item>
    <q-select
      outlined
      dense
      clearable
      label="Selecciona"
      :options="timeSlots"
      :option-label="(opt) => opt.displayName"
      v-model="timeSlotSelected"
      @update:model-value="setTimeSlot"
      class="q-px-md"
    />

    <q-list class="q-py-md">
      <q-item>
        <q-item-section v-if="!data">
          <q-item-label header>No hay selección</q-item-label>
        </q-item-section>

        <q-item-section v-else>
          <q-item-label header>Detalles de selección</q-item-label>
          <q-list>
            <q-item class="column">
              <q-item-section class="text-bold">Nombre comuna:</q-item-section>
              <q-item-section>{{ data.nombre_comuna }}</q-item-section>
            </q-item>
            <q-item class="column">
              <q-item-section class="text-bold">Código Comuna:</q-item-section>
              <q-item-section>{{ data.comuna }}</q-item-section>
            </q-item>
            <q-item class="column">
              <q-item-section class="text-bold">Zona Censal:</q-item-section>
              <q-item-section>{{ data.zona_censal }}</q-item-section>
            </q-item>
            <div v-if="showFloatingPopulation">
              <q-item class="column">
                <q-item-section class="text-bold">Fecha:</q-item-section>
                <q-item-section>{{ data.fecha }}</q-item-section>
              </q-item>
              <q-item class="column">
                <q-item-section class="text-bold"
                  >Bloque horario:</q-item-section
                >
                <q-item-section>{{ data.bloque_horario }}</q-item-section>
              </q-item>
              <q-item class="column">
                <q-item-section class="text-bold"
                  >Población Flotante:</q-item-section
                >
                <q-item-section>{{
                  Math.round(data.poblacion_flotante)
                }}</q-item-section>
              </q-item>
            </div>
          </q-list>
        </q-item-section>
      </q-item>
    </q-list>
  </div>
</template>

<script>
import { defineComponent, ref, computed } from "vue";

const timeSlots = [
  { displayName: "07:00 - 08:00", value: "7-8" },
  { displayName: "09:00 - 10:00", value: "9-10" },
  { displayName: "11:00 - 12:00", value: "11-12" },
  { displayName: "13:00 - 14:00", value: "13-14" },
  { displayName: "15:00 - 16:00", value: "15-16" },
  { displayName: "17:00 - 18:00", value: "17-18" },
  { displayName: "19:00 - 20:00", value: "19-20" },
  { displayName: "21:00 - 22:00", value: "21-22" },
  { displayName: "23:00 - 24:00", value: "23-24" },
];

export default defineComponent({
  name: "MainLayout",
  props: ["data", "showFloatingPopulation"],
  emits: ["set-timeslot"],
  setup(props, { emit }) {
    const timeSlotSelected = ref(null);

    const setTimeSlot = () => {
      emit("set-timeslot", timeSlotSelected.value);
    };

    const dataDetails = computed(() => props.data);

    return {
      timeSlots,
      timeSlotSelected,
      setTimeSlot,
      dataDetails,
    };
  },
});
</script>
<style lang="scss" scope>
list-size {
  height: 100%;
  width: 100%;
}
</style>
