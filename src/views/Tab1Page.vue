<template>
  <ion-page>
    <ion-header>
      <ion-toolbar class="header-bar">
        <ion-title class="header-title">User</ion-title>

        <ion-buttons slot="end">
          <ion-button class="btn-logout" color="danger" @click="logout">Logout</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content class="content-body">

      <!-- Tombol Tambah -->
      <div class="add-wrapper">
        <ion-button class="btn-add" color="success" @click="tambah">+ Tambah Makanan</ion-button>
      </div>

      <!-- List Barang -->
      <ion-list class="card-container">
        <ion-item v-for="user in users" :key="user.id" class="food-card">

          <ion-label class="card-label">
            <h2 class="food-title">{{ user.nama_makanan }}</h2>
            <p class="food-stock">Stok : {{ user.stok }}</p>
            <p class="food-price">{{ Rupiah(user.harga) }}</p>
          </ion-label>

          <div class="button-group">
            <ion-button class="btn-edit" color="primary" @click="openEditModal(user)">Edit</ion-button>
            <ion-button class="btn-delete" color="danger" @click="deleteUser(user.id)">Hapus</ion-button>
          </div>

        </ion-item>
      </ion-list>

      <!-- Modal Edit -->
      <ion-modal v-model:is-open="isEditOpen">
        <ion-header>
          <ion-toolbar class="modal-header">
            <ion-title>Edit Makanan</ion-title>
            <ion-buttons slot="end">
              <ion-button @click="isEditOpen = false">Close</ion-button>
            </ion-buttons>
          </ion-toolbar>
        </ion-header>

        <ion-content class="modal-content">
          <ion-list>
            <ion-item>
              <ion-label position="stacked">Nama Makanan</ion-label>
              <ion-input v-model="selectedUser.nama_makanan"></ion-input>
            </ion-item>

            <ion-item>
              <ion-label position="stacked">Stok Barang</ion-label>
              <ion-input v-model="selectedUser.stok"></ion-input>
            </ion-item>

            <ion-item>
              <ion-label position="stacked">Harga</ion-label>
              <ion-input type="Number" v-model="selectedUser.harga"></ion-input>
            </ion-item>
          </ion-list>

          <ion-button expand="block" class="btn-save" @click="saveEdit">
            Simpan
          </ion-button>
        </ion-content>
      </ion-modal>
    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
  IonList, IonItem, IonLabel, IonButton, IonModal, IonInput, IonButtons
} from '@ionic/vue'
import axios from "axios"

export default {
  name: "Tab1Page",
  components: {
    IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
    IonList, IonItem, IonLabel, IonButton, IonModal, IonInput, IonButtons
  },
  data() {
    return {
      users: [],
      isEditOpen: false,
      selectedUser: { id: null, nama_makanan: "", stok: "", harga: "" }
    }
  },
  mounted() {
    this.loadUsers()
  },
  methods: {
    Rupiah(angka) {
    if (!angka) return "Rp 0";
    return new Intl.NumberFormat("id-ID", {
      style: "currency",
      currency: "IDR",
      minimumFractionDigits: 0
    }).format(angka);
  },
  
    async loadUsers() {
      try {
        const res = await axios.get("http://localhost:3000/barang")
        this.users = res.data
      } catch (err) {
        console.error("Gagal load barang:", err)
      }
    },
    async deleteUser(id) {
      const konfirmasi = confirm("Yakin hapus pesanan ini?")
      if (!konfirmasi) {
        return;
      }
      try {
        await axios.delete(`http://localhost:3000/barang/${id}`);
    alert("makanan berhasil dihapus!");
    this.loadUsers(); 
  } catch (err) {
    console.error("Gagal hapus user:", err);
    alert("Terjadi kesalahan saat menghapus data.");
  }
    },
    openEditModal(user) {
      this.selectedUser = { ...user } 
      this.isEditOpen = true
    },
    async saveEdit() {
      try {
        const { id, nama_makanan, stok, harga } = this.selectedUser
        await axios.put(`http://localhost:3000/barang/${id}`, { nama_makanan, stok, harga })
        console.log("barang diupdate")
        this.isEditOpen = false
        this.loadUsers()
      } catch (err) {
        console.error("Gagal update makanan:", err)
      }
    },
    logout () {
      this.$router.push("/tabs/tab2")
    },
    tambah () {
      this.$router.push("/tabs/tab3")
  }
  }
}
</script>

<style scoped>
/* HEADER */
.toolbar-header {
  --background: #3b82f6; /* biru cerah */
  color: white;
  font-weight: 600;
  font-size: 1.4rem;
  padding: 0 16px;
}

.btn-logout {
  font-weight: 700;
  font-size: 0.9rem;
  color: #ff5252 !important;
}

/* CONTENT */
.content-body {
  background: #f5f7fa;
  min-height: 100vh;
}

/* Tombol tambah */
.button-wrapper {
  margin-bottom: 16px;
}

.btn-add {
  font-weight: 700;
  font-size: 1.1rem;
  --background: #10b981; /* hijau segar */
  --box-shadow: 0 4px 10px rgba(16, 185, 129, 0.4);
  transition: transform 0.2s ease-in-out;
}

.btn-add:hover {
  transform: scale(1.05);
}

/* LIST ITEM */
.food-item {
  --background: #ffffff;
  margin-bottom: 12px;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgb(0 0 0 / 0.1);
  padding: 16px 12px;
  align-items: center;
  --padding-start: 16px;
  --padding-end: 12px;
  --inner-padding-end: 8px;
}

.item-label {
  flex: 1;
  white-space: normal;
}

.food-name {
  font-weight: 700;
  font-size: 1.25rem;
  color: #111827;
  margin: 0 0 6px 0;
}

.food-stock {
  font-size: 0.9rem;
  color: #6b7280;
  margin-bottom: 4px;
}

.food-stock span {
  font-weight: 600;
  color: #374151;
}

.food-price {
  font-weight: 700;
  font-size: 1.1rem;
  color: #16a34a;
  margin: 0;
}

/* Tombol Edit dan Hapus */
.btn-edit, .btn-delete {
  font-weight: 600;
  font-size: 0.85rem;
  width: 70px;
  height: 32px;
  border-radius: 6px;
  box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
}

.btn-edit {
  margin-right: 8px;
}

.btn-delete {
  --background: #ef4444;
}

/* MODAL */
ion-modal {
  --border-radius: 14px;
}

.btn-save {
  margin-top: 20px;
  --background: #2563eb; /* biru lebih gelap */
  font-weight: 700;
  border-radius: 8px;
  box-shadow: 0 6px 12px rgba(37, 99, 235, 0.5);
  transition: background-color 0.3s ease;
}

.btn-save:hover {
  --background: #1e40af;
}
</style>