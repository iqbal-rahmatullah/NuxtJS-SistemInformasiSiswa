<script setup>
import axios from 'axios';
import Swal from 'sweetalert2';
import { ref } from 'vue';
import { useRoute } from 'vue-router';

const nilaiMatkul = ref([]);
const mahasiswa = ref([]);
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
        getMahasiswa();
    } catch (error) {
        console.error(error);
        getMahasiswa();
    }
};

const getMahasiswa = async () => {
    try {
        const nisSiswaWithGrades = nilaiMatkul.value.map((nilai) => nilai.data.nis_siswa);
        axios
            .get('http://localhost:8000/api/mahasiswa/')
            .then((responseMahasiswa) => {
                const mahasiswaData = responseMahasiswa.data.data;

                mahasiswa.value = mahasiswaData.filter((mhs) => {
                    return !nisSiswaWithGrades.includes(mhs.nis);
                });
            })
            .catch((error) => {
                console.error(error);
            });
    } catch (error) {
        console.error(error);
    }
};

const editNilai = async (id, idmatkul, nis) => {
    const { value: nilai } = await Swal.fire({
        title: 'Input nilai baru',
        input: 'text',
        inputPlaceholder: 'Masukkan nilai baru'
    });
    if (nilai) {
        try {
            const response = await axios.put('http://localhost:8000/api/nilai/' + id, {
                id_matkul: idmatkul,
                nis_siswa: nis,
                nilai
            });
            Swal.fire({
                title: 'Success!',
                text: 'Berhasil mengupdate nilai!',
                icon: 'success'
            });
            getNilai();
        } catch (error) {
            console.error(error);
        }
    }
};

const addNilai = async () => {
    const { value: formValues } = await Swal.fire({
        title: 'Tambah Nilai',
        html: `
        <div style="margin: 10px 0"><label>Nilai : </label> <input type="number" id="nilai" style="padding: 3px;" /></div>
                <div style="margin: 10px 0">
                    <label>Mahasiswa : </label>
                    <select style="padding: 3px" id="nis">
                        ${mahasiswa.value.map((nis) => `<option value="${nis.nis}">${nis.nama}</option>`).join('')}
                    </select>
                </div>
    `,
        focusConfirm: false,
        preConfirm: () => {
            return [document.getElementById('nilai').value, document.getElementById('nis').value];
        }
    });
    if (formValues) {
        try {
            const response = axios.post('http://localhost:8000/api/nilai/', {
                id_matkul: id,
                nis_siswa: formValues[1],
                nilai: formValues[0]
            });
            Swal.fire({
                title: 'Success!',
                text: 'Berhasil Menambahkan nilai!',
                icon: 'success'
            });
            getNilai();
        } catch (error) {
            console.error(error);
        }
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
                <Button label="Tambah Nilai" icon="pi pi-plus" class="p-button-info my-2" @click="addNilai()" />
                <table class="p-datatable-table" v-if="nilaiMatkul.length > 0">
                    <tr>
                        <th class="p-datatable-header p-3 border-1">Nama</th>
                        <th class="p-datatable-header p-3 border-1">Nilai</th>
                        <th class="p-datatable-header p-3 border-1">Action</th>
                    </tr>
                    <tr v-for="nilai in nilaiMatkul" :key="nilai._id">
                        <td class="p-datatable-body border-1 p-3 text-center">{{ nilai.mahasiswa.nama }}</td>
                        <td class="p-datatable-body border-1 p-3 text-center">{{ nilai.data.nilai }}</td>
                        <td class="p-datatable-body border-1 p-3 text-center">
                            <Button label="Edit" icon="pi pi-pencil" class="p-button-warning" @click="editNilai(nilai.data._id, id, nilai.data.nis)" />
                        </td>
                    </tr>
                </table>
                <template v-else>
                    <table class="p-datatable-table">
                        <h5 class="text-center">Tidak ada data</h5>
                    </table>
                </template>
            </div>
        </div>
    </div>
</template>
