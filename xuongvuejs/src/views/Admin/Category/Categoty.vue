<script setup>
let url = "http://localhost:3000/api.php/categories";
import { onMounted, ref } from 'vue';
import axios from 'axios';
import { RouterLink } from 'vue-router';

const listCategory = ref([]);
onMounted(async () => {
    let response = await axios.get(url);
    listCategory.value = response.data;
})
const handleDelete = async (idCategory) => {
    const check = confirm("Bạn có muốn xóa không?");
    if (check) {
        let response = await axios.delete(url + "/" + idCategory);
        if (response) {
            alert(response.data.message);
            let response2 = await axios.get(url);
            listCategory.value = response2.data;
        }
    }
}
</script>

<template>
    <div class="p-4" style="min-height: 800px;">
        <h1>Quản lý danh mục</h1>
        <RouterLink to="/admin/add-category" class="btn btn-primary">Add Category</RouterLink>
        <table class="table">
            <thead>
                <tr>
                    <th>STT</th>
                    <th>Name</th>
                    <th>Description</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(category, index) in listCategory" :key="index">
                    <td>{{ index + 1 }}</td>
                    <td>{{ category.name }}</td>
                    <td>{{ category.description }}</td>
                    <td>
                        <RouterLink :to="`/admin/update-category/${category.id}`" class="btn btn-warning">Sửa
                        </RouterLink>
                        <button @click="handleDelete(category.id)" class="btn btn-danger">Xóa</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>