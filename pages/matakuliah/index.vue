<script setup>
import { ProductService } from '@/service/ProductService';
import axios from 'axios';
import Swal from 'sweetalert2';
import { onMounted, ref } from 'vue';

const dataviewValue = ref(null);
const layout = ref('grid');
const mataKuliah = ref(null);

onMounted(() => {
    ProductService.getProductsSmall().then((data) => (dataviewValue.value = data));
    getMatkul();
});

const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Matakuliah' }]);

const getMatkul = async () => {
    try {
        const data = await axios.get('http://localhost:8000/api/matakuliah');
        mataKuliah.value = data.data.data;
        console.log(mataKuliah.value);
    } catch (error) {
        console.error(error);
    }
};

const deleteModal = (id) => {
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
                const response = axios.delete('http://localhost:8000/api/matakuliah/' + id);
                Swal.fire({
                    title: 'Deleted!',
                    text: 'Data matakuliah berhasil di hapus.',
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
            getMatkul();
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
                <h5>Mata Kuliah</h5>
                <Button label="Tambah Matakuliah" icon="pi pi-plus" class="p-button p-button-info my-3"></Button>
                <DataView :value="mataKuliah" :layout="layout" :paginator="true" :rows="9">
                    <template #grid="slotProps">
                        <div class="col-12 md:col-4">
                            <div class="card m-3 border-1 surface-border">
                                <div class="text-center">
                                    <img src="/assets/books.png" :alt="slotProps.data.nama" class="w-3 shadow-2 my-3 mx-0" />
                                    <div class="text-2xl font-bold">
                                        {{ slotProps.data.nama }}
                                    </div>
                                    <div class="my-3">{{ slotProps.data.description }}</div>
                                </div>
                                <div class="flex align-items-center justify-content-center">
                                    <router-link :to="`/matakuliah/details/${slotProps.data._id}`">
                                        <Button label="Lihat" icon="pi pi-eye"></Button>
                                    </router-link>
                                    <router-link :to="`/matakuliah/edit/${slotProps.data._id}`">
                                        <Button label="Edit" icon="pi pi-pencil" class="mx-1 p-button-warning"></Button>
                                    </router-link>
                                    <Button @click="deleteModal(slotProps.data._id)" label="Hapus" icon="pi pi-trash" class="mx-1 p-button-danger"></Button>
                                </div>
                            </div>
                        </div>
                    </template>
                </DataView>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
@import '@/assets/demo/styles/badges.scss';
</style>
