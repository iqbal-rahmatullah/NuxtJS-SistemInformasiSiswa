<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRoute } from 'vue-router';

const dataSiswa = ref({});
const nilaiSiswa = ref({});
const nis = useRoute().params.id;

onMounted(() => {
    getDataSiswa();
    getNilai();
});

const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Siswa' }, { label: 'Details Siswa' }]);

const getNilai = async () => {
    try {
        const response = await axios.get('http://localhost:8000/api/nilai/siswa/' + nis);
        nilaiSiswa.value = response.data.data;
        console.log(nilaiSiswa.value);
    } catch (error) {
        console.error(error);
    }
};

const getDataSiswa = async () => {
    try {
        const data = await axios.get('http://localhost:8000/api/mahasiswa/' + nis);
        dataSiswa.value = data.data.data;
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
                <h5>Detail Siswa</h5>
                <template v-if="dataSiswa.nama != null">
                    <Panel header="Informasi Siswa" :toggleable="true">
                        <div>
                            <p><span class="font-semibold">Nama : </span> {{ dataSiswa.nama }}</p>
                            <p><span class="font-semibold">NIS : </span> {{ dataSiswa.nis }}</p>
                            <p><span class="font-semibold">Alamat : </span> {{ dataSiswa.alamat }}</p>
                            <p><span class="font-semibold">No HP : </span> {{ dataSiswa.nohp }}</p>
                            <p><span class="font-semibold">Tempat, Tanggal Lahir : </span> {{ dataSiswa.ttl }}</p>
                        </div>
                    </Panel>
                    <Fieldset legend="Nilai Matakuliah" :toggleable="true" class="mt-5">
                        <table class="p-datatable-table" v-if="nilaiSiswa.length > 0">
                            <tr>
                                <th v-for="nilai in nilaiSiswa" :key="nilai._id" class="p-datatable-header border-1 p-3" style="border-collapse: collapse">{{ nilai.id_matkul.nama }}</th>
                            </tr>
                            <tr>
                                <td v-for="nilai in nilaiSiswa" :key="nilai._id" class="p-datatable-body border-1 p-3 text-center">{{ nilai.nilai }}</td>
                            </tr>
                        </table>
                        <template v-else> Loading... </template>
                    </Fieldset>
                </template>
                <template v-else> Loading... </template>
            </div>
        </div>
    </div>
</template>
