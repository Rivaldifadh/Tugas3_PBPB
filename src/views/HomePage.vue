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
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
  IonCard, IonCardHeader, IonCardTitle, IonCardContent, IonSearchbar,
} from "@ionic/vue";
import { ref, onMounted, computed } from "vue";

const coins = ref<any[]>([]);
const searchText = ref("");

const filteredCoins = computed(() => {
  return coins.value.filter((coin) =>
    coin.name.toLowerCase().includes(searchText.value.toLowerCase()),
  );
});

const getCoins = async () => {
  try {
    // 1. Pake API CoinGecko Live. Gratis tanpa key
    const url = "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&ids=bitcoin,ethereum,tether,binancecoin&order=market_cap_desc&per_page=4&page=1&sparkline=false";
    
    const response = await fetch(url);
    const data = await response.json();

    // 2. Mapping biar formatnya sama kayak JSON statis kamu
    // Jadi `{{ coin.rank }}` dan `{{ coin.price_usd }}` di template tetap jalan
    coins.value = data.map((coin: any) => ({
      id: coin.id,
      rank: coin.market_cap_rank, // dari API: market_cap_rank -> rank
      name: coin.name,
      symbol: coin.symbol.toUpperCase(),
      price_usd: coin.current_price.toFixed(2) // dari API: current_price -> price_usd
    }));

  } catch (error) {
    console.log("Gagal fetch API:", error);
  }
};

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

