<script setup>
import axios from "axios";
import { computed, defineEmits, defineProps, ref, watch } from "vue";
import * as yup from "yup";

const props = defineProps({
  restaurantData: Object,
  userData: Object,
  isEdit: Boolean,
  formType: String, // 'restaurant' or 'user'
});

const emit = defineEmits(["dataUpdated"]);

const restaurantSchema = yup.object({
  name: yup.string().required("Name is required"),
  address: yup.string().required("Address is required"),
  contact: yup.string().required("Contact is required"),
});

const userSchema = yup.object({
  name: yup.string().required("Name is required"),
  email: yup.string().email("Invalid email format").required("Email is required"),
  password: yup.string().min(6, "Password must be at least 6 characters").required("Password is required"),
  address: yup.string().required("Address is required"),
});

const defaultForm = computed(() => {
  return props.formType === "Restaurant"
    ? { name: "", address: "", contact: "", image: "" }
    : { name: "", email: "", address: "", password: "", image: "" };
});

const errors = ref({});
const form = ref({ ...defaultForm.value, addressTemp: "" });

watch(
  () => (props.formType === "Restaurant" ? props.restaurantData : props.userData),
  (newData) => {
    if (newData && Object.keys(newData).length > 0) {
      form.value = { ...newData, addressTemp: newData.address || "" };
    } else {
      form.value = { ...defaultForm.value, addressTemp: "" };
    }
  },
  { immediate: true }
);

// Handle Image Upload
// const handleImageUpload = (event) => {
//   const file = event.target.files[0];
//   if (file) {
//     form.value.image = URL.createObjectURL(file); // Temporary URL (JSON Server doesn’t store files)
//   }
// };

const handleImageUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => {
      form.value.image = reader.result; // Store Base64 string in JSON Server
    };
  }
};


const saveData = async () => {
  try {
    // ✅ Ensure `address` is updated before validation
    form.value.address = form.value.addressTemp;

    const schema = props.formType === "Restaurant" ? restaurantSchema : userSchema;

    try {
      await schema.validate(form.value, { abortEarly: false });
    } catch (validationError) {
      errors.value = validationError.inner.reduce((acc, error) => {
        acc[error.path] = error.message;
        return acc;
      }, {});
      return;
    }

    const dataToSend = { ...form.value };

    const url = props.formType === "Restaurant"
      ? "http://localhost:3000/restaurants"
      : "http://localhost:3000/users";

    let result;
    if (props.isEdit) {
      result = await axios.put(`${url}/${form.value.id}`, dataToSend);
    } else {
      result = await axios.post(url, dataToSend);
    }

    if (result.status === 200 || result.status === 201) {
      emit("dataUpdated", result.data);
      form.value = { ...defaultForm.value, addressTemp: "" }; // Reset after success
    }
  } catch (error) {
    console.error("Error saving data", error);
  }
};
</script>

<template>
  <VCard :title="isEdit ? `Edit ${formType}` : `Add ${formType}`" class="addEditModal">
    <VCardText>
      <form @submit.prevent="saveData" class="add">
        <input type="text" placeholder="Enter Name" v-model="form.name" />
        <span v-if="errors.name" class="text-error">{{ errors.name }}</span>

        <input v-if="formType === 'Restaurant'" type="text" placeholder="Enter Address" v-model="form.addressTemp" />
        <span v-if="formType === 'Restaurant' && errors.address" class="text-error">{{ errors.address }}</span>

        <input v-if="formType === 'Restaurant'" type="text" placeholder="Enter Contact" v-model="form.contact" />
        <span v-if="errors.contact" class="text-error">{{ errors.contact }}</span>

        <input v-if="formType === 'User'" type="email" placeholder="Enter Email" v-model="form.email" />
        <span v-if="errors.email" class="text-error">{{ errors.email }}</span>

        <input v-if="formType === 'User'" type="password" placeholder="Enter Password" v-model="form.password" />
        <span v-if="errors.password" class="text-error">{{ errors.password }}</span>

        <input v-if="formType === 'User'" type="text" placeholder="Enter Address" v-model="form.addressTemp" />
        <span v-if="formType === 'User' && errors.address" class="text-error">{{ errors.address }}</span>

        <!-- Display Existing Image -->
        <img v-if="form.image" :src="form.image" alt="Uploaded Image" width="100" height="100" />

        <!-- Image Upload -->
        <input type="file" @change="handleImageUpload" accept="image/*" />

        <VBtn color="primary" @click="saveData" class="addEditButton">
          {{ isEdit ? `Update ${formType}` : `Add ${formType}` }}
        </VBtn>
      </form>
    </VCardText>
  </VCard>
</template>
