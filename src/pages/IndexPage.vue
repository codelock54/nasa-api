<template>
  <q-page class="q-pa-md">
    <q-form @submit.prevent="fetchAPODData">
      <q-input v-model="startDate" label="Fecha de Inicio" type="date" outlined />
      <q-input v-model="endDate" label="Fecha de Fin" type="date" outlined class="q-mt-md" />

      <!-- <q-btn type="submit" label="Consultar"  class="q-mt-md" /> -->

      <div class="flex-center q-mt-md">
        <q-btn type="submit" label="Consultar" color="primary" />
      </div>

    </q-form>

    <div v-if="apodData.length === 0" class="q-mt-md">
      <q-banner class="q-my-md" dense>
        <q-icon name="info" />
        No hay datos para mostrar. Por favor, selecciona un rango de fechas y presiona "Consultar".
      </q-banner>
    </div>

    <q-card v-for="item in apodData" :key="item.date" class="q-mt-md">
      <q-card-section>
        <div class="text-h6">{{ item.title }}</div>
        <div class="text-subtitle1">{{ item.date }}</div>
      </q-card-section>
      
     
      <q-img
        v-if="item.url || item.hdurl"
        :src="item.hdurl || item.url"
        :alt="item.title"
        ratio="16:9"
        @error="handleImageError"
      />
      <img :src="item.url">
      <q-card-section>{{ item.explanation }}</q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      startDate: '',
      endDate: '',
      apodData: []
    };
  },
  methods: {
    async fetchAPODData() {
      const apiKey = 'KnsMFLhGOLcMetOAfZc8QsXojDr97YlH1oB8JpG7';
      const startDate = this.startDate;
      const endDate = this.endDate;

      if (!startDate || !endDate) {
        this.$q.notify({
          type: 'negative',
          message: 'Por favor, complete las fechas de inicio y fin.'
        });
        return;
      }

      try {
        const response = await axios.get('https://api.nasa.gov/planetary/apod', {
          params: {
            api_key: apiKey,
            start_date: startDate,
            end_date: endDate
          }
        });

        console.log('API Response:', response.data); 

        // La API devuelve un objeto si la consulta es exitosa, no un arreglo
        this.apodData = Array.isArray(response.data) ? response.data : [response.data];
      } catch (error) {
        this.$q.notify({
          type: 'negative',
          message: 'Error al obtener los datos de la API.'
        });
        console.error('API Error:', error); 
      }
    },
    handleImageError(event) {
      event.target.src = 'https://via.placeholder.com/400x300?text=Image+Not+Available';
    }
  }
};
</script>

<style>
.q-page {
  max-width: 600px;
  margin: auto;
}
/* .q-btn {
 align-self: center; 
 color: #2622eb;
} */

.flex-center {
  display: flex;
  justify-content: center;
  
}
</style>
