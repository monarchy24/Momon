<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tambah Makanan</ion-title>

        <ion-buttons slot="end">
          <ion-button color ="primary" @click="kembali">Kembali</ion-button>
        </ion-buttons>

      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <ion-input v-model="nama_makanan" placeholder="Nama Makanan"></ion-input>
      <ion-input v-model="stok" placeholder="stok "></ion-input>
      <ion-input v-model="harga" type="number" placeholder="harga"></ion-input>
      <ion-button expand="block" @click="addUser">Tambah</ion-button>

      <p>{{ message }}</p>
    </ion-content>
  </ion-page>
</template>

<script>
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonInput, IonButton } from '@ionic/vue'
import axios from "axios"

export default {
  name: "Tab3Page",
  components: { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonInput, IonButton },
  data() {
    return {
      nama_makanan: "",
      stok: "",
      harga: "",
      message: ""
    }
  },
  methods: {
    addUser() {
      // ✅ Cek apakah ada input kosong
      if (!this.nama_makanan || !this.stok || !this.harga) {
        alert("Semua kolom harus diisi!")
        return
      }

      // ✅ Jika semua terisi, lanjut kirim ke backend
      axios.post("http://localhost:3000/barang", {
        nama_makanan: this.nama_makanan,
        stok: this.stok,
        harga: this.harga
      })
      .then(res => {
        alert("makanan berhasil ditambahkan!") // ✅ Peringatan sukses
        this.message = res.data.message
        this.nama_makanan = ""
        this.stok = ""
        this.harga = ""
        this.$router.push("/tabs/tab1") // Arahkan ke halaman user
      })
      .catch(err => {
        console.error(err)
        alert("Gagal menambahkan makanan!") // ✅ Peringatan error
      })
    },
    kembali() {
      this.$router.push("/tabs/tab1")
    }
  } 
}
</script>