<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const name = ref("");
const address = ref("");
const phone = ref("");
const email = ref("");
const router = useRouter();
const cartDetail = ref([]);
let urlCartDetail = "http://localhost:3000/api.php/cart-detail";
let urlCheckout = "http://localhost:3000/api.php/check-out";

const nameError = ref("");
const addressError = ref("");
const phoneError = ref("");
const emailError = ref("");

const callAPI = async () => {
    try {
        let user_id = JSON.parse(localStorage.getItem("userLogin")).id;
        let response = await axios.get(urlCartDetail + '?user_id=' + user_id);
        if (response.status == 200) {
            cartDetail.value = response.data.cart_details;
        }
    } catch (error) {
        console.log(error);
    }
};

onMounted(() => {
    callAPI();
});
const convertPrice = (number) => {
    return number.toLocaleString("vi-VN");
}
const handleSubmit = async () => {
    checkValidate();
    if (nameError.value == "" && addressError.value == "") {
    try {
        let user_id = JSON.parse(localStorage.getItem("userLogin")).id;
        let formData = new FormData();
        formData.append("user_id", user_id);
        formData.append("name", name.value);
        formData.append("address", address.value);
        formData.append("phone", phone.value);
        formData.append("email", email.value);

        let response = await axios.post(urlCheckout, formData);
        if (response.status == 200) {
            alert("Thanh toán thành công");
            router.push('/don-hang');
        }
    } catch (error) {
        console.log(error);
    }
}
};
const checkValidate = () => {
    if (name.value == "") {
        nameError.value = "Không được để trống Tên";
    } else {
        nameError.value = "";
    }
    if (address.value == "") {
        addressError.value = "Không được để trống địa chỉ";
    } else {
        addressError.value = "";
    }
    if (phone.value == "") {
        phoneError.value = "Không được để trống số điện thoại";
    } else {
        phoneError.value = "";
    }
    if (email.value == "") {
        emailError.value = "Không được để trống email";
    } else {
        emailError.value = "";
    }
}
</script>

<template>
    <div class="row main-website justify-content-center mt-5">
        <div class="col-12">
            <div class="container">
                <div class="row">
                    <div class="col-7">
                        <h4>Thông tin thanh toán</h4>
                        <form @submit.prevent="handleSubmit">
                            <div class="mb-4">
                                <label for="name">Name</label>
                                <input @keyup="handleValidate('name')" type="text" placeholder="Name" id="name"
                                    v-model="name" class="form-control" />
                                <span v-if="nameError != ''" class="text-danger">{{ nameError }}</span>
                            </div>
                            <div class="mb-4">
                                <label for="address">Address</label>
                                <input @keyup="handleValidate('address')" type="text" placeholder="Address" id="address"
                                    v-model="address" class="form-control" />
                                <span v-if="addressError != ''" class="text-danger">{{ addressError }}</span>
                            </div>
                            <div class="mb-4">
                                <label for="phone">Phone</label>
                                <input @keyup="handleValidate('phone')" type="text" placeholder="Phone" id="phone"
                                    v-model="phone" class="form-control" />
                                <span v-if="phoneError != ''" class="text-danger">{{ phoneError }}</span>
                            </div>
                            <div class="mb-4">
                                <label for="email">Email</label>
                                <input @keyup="handleValidate('email')" type="text" placeholder="Email" id="email"
                                    v-model="email" class="form-control" />
                                <span v-if="emailError != ''" class="text-danger">{{ emailError }}</span>
                            </div>
                            <button class="btn btn-success">Xác nhận thanh toán</button>
                        </form>
                    </div>
                    <div class="col-5">
                        <h4>Danh sách sản phẩm</h4>
                        <table class="table">
                            <thead>
                                <tr>
                                    <th>STT</th>
                                    <th>Tên sản phẩm</th>
                                    <th>Giá</th>
                                    <th>Số lượng</th>
                                    <th>Thành tiền</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(item, index) in cartDetail" :key="index">
                                    <td>{{ index + 1 }}</td>
                                    <td>{{ item.product_name }}</td>
                                    <td>{{ convertPrice(item.price) }}</td>
                                    <td>{{ item.quantity }}</td>
                                    <td>
                                        {{ convertPrice(Number(item.price) * Number(item.quantity)) }}
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>