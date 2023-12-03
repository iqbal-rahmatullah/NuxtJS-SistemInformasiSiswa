<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRoute } from 'vue-router';

const nilaiMatkul = ref({});
const id = useRoute().params.id;

onMounted(() => {
    getNilai();
});

const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Matakuliah' }, { label: 'Details Matakuliah' }]);

const getNilai = async () => {
    try {
        const response = await axios.get('http://localhost:8000/api/nilai/matkul/' + id);
        nilaiMatkul.value = response.data.data;
        console.log(nilaiMatkul.value);
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
        <div class="col-12">
            <div class="card">
                <h5>Nilai Mata Kuliah {{ data }}</h5>
                <table class="p-datatable-table">
                    <tr>
                        <th class="p-datatable-header p-3 border-1">Nama</th>
                        <th class="p-datatable-header p-3 border-1">Nilai</th>
                        <th class="p-datatable-header p-3 border-1">Action</th>
                    </tr>
                    <tr v-for="nilai in nilaiMatkul" :key="nilai._id">
                        <td class="p-datatable-body border-1 p-3 text-center">{{ nilai.mahasiswa.nama }}</td>
                        <td class="p-datatable-body border-1 p-3 text-center">{{ nilai.data.nilai }}</td>
                        <td class="p-datatable-body border-1 p-3 text-center">
                            <router-link :to="`/matakuliah/`">
                                <Button label="Edit" icon="pi pi-pencil" class="mx-1 p-button-warning"></Button>
                            </router-link>
                        </td>
                    </tr>
                </table>
            </div>
        </div>
    </div>
</template>
