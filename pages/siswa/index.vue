<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import Swal from 'sweetalert2';

const siswa = ref(null);
const filters1 = ref(null);
const loading1 = ref(null);
const products = ref(null);

const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Siswa' }]);

const getDataSiswa = async () => {
    try {
        const response = await axios.get('http://localhost:8000/api/mahasiswa');
        siswa.value = response.data.data;
    } catch (error) {
        console.error(error);
    } finally {
        loading1.value = false;
    }
};

onMounted(() => {
    getDataSiswa();
});

const deleteModal = (nis) => {
    Swal.fire({
        title: 'Apakah anda yaking?',
        text: 'Data akan di hapus permanen!',
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!'
    }).then((result) => {
        if (result.isConfirmed) {
            try {
                const response = axios.delete('http://localhost:8000/api/mahasiswa/' + nis);
                Swal.fire({
                    title: 'Deleted!',
                    text: 'Data mahasiswa berhasil di hapus.',
                    icon: 'success'
                });
            } catch (error) {
                if (error.response.status == 400) {
                    Swal.fire({
                        title: 'Error!',
                        text: error.response.data.message,
                        icon: 'error'
                    });
                } else {
                    Swal.fire({
                        title: 'Error!',
                        text: 'Terjadi kesalahan server',
                        icon: 'error'
                    });
                }
            }
            getDataSiswa();
        }
    });
};
</script>

<template>
    <div class="grid">
        <div class="col-12">
            <Breadcrumb :home="breadcrumbHome" :model="breadcrumbItems" />
        </div>
        <div class="col-12">
            <div class="card">
                <h5>Data Semua Siswa</h5>
                <router-link to="siswa/tambah">
                    <Button label="Tambah Siswa" icon="pi pi-user-plus" iconPos="right" class="p-button-rounded p-button-info mr-2 my-3"></Button>
                </router-link>
                <DataTable :value="siswa" paginator showGridlines :rows="6" dataKey="id" filterDisplay="menu" :loading="loading1">
                    <template #empty> Tidak ada siswa ditemukan </template>
                    <template #loading> Loading siswa data. Please wait. </template>
                    <Column field="name" header="Nama" style="min-width: 12rem">
                        <template #body="{ data }">
                            {{ data.nama }}
                        </template>
                    </Column>
                    <Column field="nis" header="NIS" style="min-width: 12rem">
                        <template #body="{ data }">
                            {{ data.nis }}
                        </template>
                    </Column>
                    <Column field="alamat" header="Alamat" style="min-width: 12rem">
                        <template #body="{ data }">
                            {{ data.alamat }}
                        </template>
                    </Column>
                    <Column field="nohp" header="No HP" style="min-width: 12rem">
                        <template #body="{ data }">
                            {{ data.nohp }}
                        </template>
                    </Column>
                    <Column field="TTL" header="Tempat Tanggal Lahir" style="min-width: 12rem">
                        <template #body="{ data }">
                            {{ data.ttl }}
                        </template>
                    </Column>
                    <Column field="nis" header="Action" bodyClass="text-center" style="min-width: 8rem">
                        <template #body="{ data }">
                            <div class="flex">
                                <router-link :to="`/siswa/details/${data.nis}`" :key="data.nis">
                                    <Button label="Detail" icon="pi pi-user" class="mr-2"></Button>
                                </router-link>
                                <router-link :to="`/siswa/edit/${data.nis}`" :key="data.nis">
                                    <Button label="Edit" icon="pi pi-pencil" class="p-button-warning mr-2"></Button>
                                </router-link>
                                <Button @click="deleteModal(`${data.nis}`)" label="Delete" icon="pi pi-trash" class="p-button-danger mr-2"></Button>
                            </div>
                        </template>
                    </Column>
                </DataTable>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
:deep(.p-datatable-frozen-tbody) {
    font-weight: bold;
}

:deep(.p-datatable-scrollable .p-frozen-column) {
    font-weight: bold;
}
</style>
