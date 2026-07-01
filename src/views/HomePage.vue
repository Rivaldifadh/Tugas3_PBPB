<template>
  <!-- Container utama halaman Ionic -->
  <ion-page>
    <!-- Header / bagian atas aplikasi -->
    <ion-header>
      <!-- Toolbar dengan warna primary -->
      <ion-toolbar color="primary">
        <!-- Judul aplikasi -->
        <ion-title> Daftar Cryptocurrency </ion-title>
      </ion-toolbar>
    </ion-header>

    <!-- Area isi halaman -->
    <ion-content class="ion-padding">
      <!--
        Search bar Ionic
        v-model menghubungkan input user
        dengan variabel searchText
      -->
      <ion-searchbar
        v-model="searchText"
        placeholder="Cari cryptocurrency..."
      ></ion-searchbar>

      <!--
        v-for digunakan untuk mengulang card
        sebanyak jumlah data filteredCoins
      -->
      <ion-card class="coin-card" v-for="coin in filteredCoins" :key="coin.id">
        <!-- Header card -->
        <ion-card-header>
          <!--
            Menampilkan rank dan nama coin
            {{ }} = interpolation / data binding
          -->
          <ion-card-title> {{ coin.rank }} - {{ coin.name }} </ion-card-title>
        </ion-card-header>

        <!-- Isi card -->
        <ion-card-content class="card-content">
          <!-- Menampilkan symbol -->
          <p>
            <b>Symbol:</b>
            {{ coin.symbol }}
          </p>

          <!-- Menampilkan harga USD -->
          <p>
            <b>Price USD:</b>
            ${{ coin.price_usd }}
          </p>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
/*
  Import komponen Ionic
  agar bisa dipakai di template
*/
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonSearchbar,
} from "@ionic/vue";

/*
  Import Vue Composition API

  ref()      = membuat data reactive
  onMounted  = jalan saat page selesai load
  computed() = membuat data hasil filter/perhitungan
*/
import { ref, onMounted, computed } from "vue";

/*
  Menyimpan data cryptocurrency

  ref([]) artinya:
  data reactive berupa array kosong
*/
const coins = ref<any[]>([]);

/*
  Menyimpan teks pencarian user

  Awalnya kosong:
  ""
*/
const searchText = ref("");

/*
  Computed property untuk search

  filteredCoins otomatis berubah
  ketika searchText atau coins berubah
*/
const filteredCoins = computed(() => {
  /*
    filter() digunakan untuk memilih
    data coin yang cocok
  */
  return coins.value.filter((coin) =>
    /*
      toLowerCase()
      agar search tidak sensitif huruf besar/kecil

      includes()
      mengecek apakah nama coin
      mengandung teks search
    */
    coin.name.toLowerCase().includes(searchText.value.toLowerCase()),
  );
});

/*
  Function mengambil data API
*/
const getCoins = async () => {
  /*
    try-catch dipakai
    untuk menangani error
  */
  try {
    /*
      fetch() request ke API online
    */
    const response = await fetch("https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&ids=bitcoin,ethereum,tether,binancecoin")
      response diubah
      menjadi JSON object
    */
    const data = await response.json();

    /*
      Memasukkan data API
      ke variabel coins

      data.data karena struktur API:
      {
        data:[]
      }
    */
    coins.value = data.data;
  } catch (error) {
    /*
      Jika error,
      tampilkan di console
    */
    console.log(error);
  }
};

/*
  onMounted()

  Jalan saat halaman selesai dibuka
  lalu memanggil getCoins()
*/
onMounted(() => {
  getCoins();
});
</script>

<style scoped>
/*
  Styling isi card
*/
.card-content {
  transition: 0.3s;
  border-radius: 15px;
}

/*
  Hover pada isi card
*/
.card-content:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 15px rgba(3, 171, 243, 0.25);
}

/*
  Styling card coin
*/
.coin-card {
  transition: 0.3s;
  border-radius: 15px;
}

/*
  Hover card

  Saat mouse menyentuh card:
  - card naik sedikit
  - muncul shadow
*/
.coin-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.25);
}
</style>

