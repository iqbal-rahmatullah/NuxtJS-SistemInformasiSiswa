<script setup>
import axios from 'axios';
import Swal from 'sweetalert2';
import { useRoute } from 'vue-router';
import { ref } from 'vue';
const router = useRouter();

const dataForm = ref({});
const id = useRoute().params.id;

onMounted(() => {
    getMatkul();
});

const breadcrumbHome = ref({ icon: 'pi pi-home', to: '/' });
const breadcrumbItems = ref([{ label: 'Matakuliah' }, { label: 'Edit Matakuliah' }]);

const getMatkul = async () => {
    try {
        const data = await axios.get('http://localhost:8000/api/matakuliah/' + id);
        dataForm.value = data.data.data;
        console.log(dataForm.value);
    } catch (error) {
        console.error(error);
    }
};

const submitForm = async () => {
    try {
        const response = await axios.put('http://localhost:8000/api/matakuliah/' + id, dataForm.value);
        if (response.status === 200) {
            Swal.fire({
                title: 'Success!',
                text: 'Berhasil mengupdate matakuliah!',
                icon: 'success'
            });
            router.push('/matakuliah');
        }
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
            <Breadcrumb :home="breadcrumbHome" :model="breadcrumbItems" />
        </div>
        <div class="col-12">
            <div class="card">
                <h5>Edit Matakuliah</h5>
                <form @submit.prevent="submitForm">
                    <div class="p-fluid formgrid grid">
                        <div class="field col-12">
                            <label for="nama">Nama Matkul : </label>
                            <InputText required id="nama" type="text" v-model="dataForm.nama" />
                        </div>
                        <div class="field col-12">
                            <label for="description">Description : </label>
                            <Textarea rows="10" required id="description" v-model="dataForm.description" style="resize: none" />
                        </div>
                    </div>
                    <Button type="submit" label="Submit" icon="pi pi-send" class="mr-2 mb-2"></Button>
                </form>
            </div>
        </div>
    </div>
</template>
