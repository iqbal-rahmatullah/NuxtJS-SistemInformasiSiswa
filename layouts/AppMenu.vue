<script setup>
import { ref } from 'vue';
import AppMenuItem from './AppMenuItem.vue';
const model = ref([
    {
        label: 'Home',
        items: [{ label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/' }]
    },
    {
        label: 'Menu',
        items: [
            {
                label: 'Data Siswa',
                icon: 'pi pi-fw pi-users',
                items: [
                    { label: 'Semua Siswa', icon: 'pi pi-fw pi-eye', to: '/siswa' },
                    { label: 'Tambah Siswa', icon: 'pi pi-fw pi-plus-circle', to: '/siswa/tambah' }
                ]
            },
            {
                label: 'Matakuliah',
                icon: 'pi pi-fw pi-book',
                items: [
                    { label: 'Semua Matakuliah', icon: 'pi pi-fw pi-eye', to: '/matakuliah' },
                    { label: 'Tambah Matakuliah', icon: 'pi pi-fw pi-plus-circle', to: '/matakuliah/tambah' }
                ]
            }
        ]
    }
]);
</script>

<template>
    <ul class="layout-menu">
        <template v-for="(item, i) in model" :key="item">
            <app-menu-item v-if="!item.separator" :item="item" :index="i"></app-menu-item>
            <li v-if="item.separator" class="menu-separator"></li>
        </template>
    </ul>
</template>

<style lang="scss" scoped></style>
