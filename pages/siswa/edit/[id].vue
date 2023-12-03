<script setup>
import axios from 'axios';
import Swal from 'sweetalert2';
const router = useRouter();

const dataForm = ref({});
const id = useRoute().params.id;

onMounted(() => {
    getDataSiswa();
});

const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Siswa' }, { label: 'Edit Siswa' }]);

const getDataSiswa = async () => {
    try {
        const data = await axios.get('http://localhost:8000/api/mahasiswa/' + id);
        dataForm.value = data.data.data;
    } catch (error) {
        console.error(error);
    }
};

const submitForm = async () => {
    try {
        const response = await axios.put(`http://localhost:8000/api/mahasiswa/${id}`, dataForm.value);
        console.log(response);
        Swal.fire({
            title: 'Success!',
            text: 'Berhasil mengupdate mahasiswa!',
            icon: 'success'
        });
        router.push('/siswa');
    } catch (error) {
        console.error(error);
        if (error.response.status == 400) {
            Swal.fire({
                title: 'Error!',
                text: error.response.data.message,
                icon: 'error'
            });
        } else {
            Swal.fire({
                title: 'Error!',
                text: error,
                icon: 'error'
            });
        }
    }
};
</script>

<template>
    <div class="grid">
        <div class="col-12">
            <div class="col-12">
                <Breadcrumb :home="breadcrumbHome" :model="breadcrumbItems" />
            </div>
            <div class="card">
                <h5>Edit Mahasiswa</h5>
                <form @submit.prevent="submitForm">
                    <div class="p-fluid formgrid grid">
                        <div class="field col-12">
                            <label for="nis">NIS : </label>
                            <InputText required id="nis" type="text" v-model="dataForm.nis" />
                        </div>
                        <div class="field col-12">
                            <label for="name">Nama Lengkap : </label>
                            <InputText required id="name" type="text" v-model="dataForm.nama" />
                        </div>
                        <div class="field col-12">
                            <label for="alamat">Alamat : </label>
                            <InputText required id="alamat" type="text" v-model="dataForm.alamat" />
                        </div>
                        <div class="field col-12">
                            <label for="nohp">No HP : </label>
                            <InputText required id="nohp" type="text" v-model="dataForm.nohp" />
                        </div>
                        <div class="field col-12">
                            <label for="ttl">Tempat/Tanggal Lahir : </label>
                            <InputText required id="ttl" type="text" v-model="dataForm.ttl" />
                        </div>
                    </div>
                    <Button type="submit" label="Submit" icon="pi pi-send" class="mr-2 mb-2"></Button>
                </form>
            </div>
        </div>
    </div>
</template>
