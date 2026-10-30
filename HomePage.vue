<template>
  <ion-page>
    <!-- Header Aplikasi -->
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Prakiraan Cuaca Jakarta</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <!-- Card Keterangan Singkat -->
      <ion-card>
        <ion-card-content>
          <h2>Monitoring Cuaca Per Jam</h2>
          <p>Menampilkan seluruh data prakiraan suhu di Jakarta (Latitude: -6.2, Longitude: 106.8) menggunakan Ionic & Vue.</p>
        </ion-card-content>
      </ion-card>

      <!-- Indikator Loading -->
      <div v-if="isLoading" class="ion-text-center ion-padding">
        <ion-spinner name="crescent"></ion-spinner>
        <p>Memuat seluruh data cuaca...</p>
      </div>

      <!-- Pesan Error -->
      <ion-text color="danger" v-if="errorMessage">
        <p class="ion-text-center">{{ errorMessage }}</p>
      </ion-text>

      <!-- Daftar Seluruh Data Cuaca -->
      <ion-list v-if="!isLoading && !errorMessage">
        <ion-list-header>
          <ion-label><b>Total Data: {{ times.length }} Jam</b></ion-label>
        </ion-list-header>

        <ion-item v-for="(time, index) in times" :key="index">
          <ion-label>
            <h3><strong>No. {{ index + 1 }} | Waktu:</strong> {{ time.replace('T', ' ') }}</h3>
            <p>Temperatur (temperature_2m): <strong>{{ temperatures[index] }} °C</strong></p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { 
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
  IonCard, IonCardContent, IonList, IonListHeader, 
  IonItem, IonLabel, IonSpinner, IonText 
} from '@ionic/vue';

// Variabel reaktif untuk menampung data
const times = ref([]);
const temperatures = ref([]);
const isLoading = ref(true);
const errorMessage = ref('');

// Fungsi mengambil data dari API Open-Meteo
const fetchWeatherData = async () => {
  const apiUrl = 'https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m';
  
  try {
    const response = await fetch(apiUrl);
    if (!response.ok) {
      throw new Error('Gagal terhubung ke server API');
    }
    const data = await response.json();
    
    // Memasukkan seluruh data dari array API
    times.value = data.hourly.time;
    temperatures.value = data.hourly.temperature_2m;
  } catch (error) {
    console.error(error);
    errorMessage.value = 'Gagal memuat data dari API. Periksa koneksi internet Anda.';
  } finally {
    isLoading.value = false;
  }
};

// Menjalankan fetch data saat komponen pertama kali dimuat
onMounted(() => {
  fetchWeatherData();
});
</script>