<script setup>
import axios from 'axios';
import { ref } from 'vue';
const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Home' }]);

const dataSiswa = ref([]);
const dataMatkul = ref([]);

onMounted(() => {
    getSiswa();
    getMatkul();
});

const getSiswa = async () => {
    try {
        const response = await axios.get('http://localhost:8000/api/mahasiswa');
        dataSiswa.value = response.data.data;
    } catch (error) {
        console.error(error);
    }
};

const getMatkul = async () => {
    try {
        const response = await axios.get('http://localhost:8000/api/matakuliah');
        dataMatkul.value = response.data.data;
    } catch (error) {
        console.error(error);
    }
};
</script>

<template>
    <div class="grid">
        <div class="col-12">
            <Breadcrumb :home="breadcrumbHome" :model="breadcrumbItems" />
        </div>
        <div class="col-6">
            <div class="card mb-0">
                <div class="flex justify-content-between mb-3">
                    <div>
                        <span class="block text-500 font-medium mb-3">Jumlah Mahasiswa</span>
                        <div v-if="dataSiswa.length == 0" class="text-900 font-medium text-xl">Loading...</div>
                        <div v-else class="text-900 font-medium text-xl">{{ dataSiswa.length }} Siswa</div>
                    </div>
                    <div class="flex align-items-center justify-content-center bg-blue-100 border-round" style="width: 2.5rem; height: 2.5rem">
                        <i class="pi pi-user text-blue-500 text-xl"></i>
                    </div>
                </div>
            </div>
        </div>
        <div class="col-6">
            <div class="card mb-0">
                <div class="flex justify-content-between mb-3">
                    <div>
                        <span class="block text-500 font-medium mb-3">Jumlah Matakuliah</span>
                        <div v-if="dataMatkul.length == 0" class="text-900 font-medium text-xl">Loading...</div>
                        <div v-else class="text-900 font-medium text-xl">{{ dataMatkul.length }} Mata Kuliah</div>
                    </div>
                    <div class="flex align-items-center justify-content-center bg-orange-100 border-round" style="width: 2.5rem; height: 2.5rem">
                        <i class="pi pi-book text-orange-500 text-xl"></i>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <Message>Selamat datang di Database Siswa</Message>
    <Message severity="success">Hari Ini Tanggal {{ new Date().getDate() }}-{{ new Date().getUTCMonth() }}-{{ new Date().getFullYear() }}</Message>
</template>
