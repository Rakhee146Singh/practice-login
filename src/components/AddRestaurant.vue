<script setup>
import axios from "axios";
import { computed, defineEmits, defineProps, ref, watch } from "vue";
import * as yup from "yup";

const props = defineProps({
  restaurantData: Object, // Accepts data for restaurant update
  userData: Object, // Accepts data for user update
  isEdit: Boolean, // Determines if it's edit mode
  formType: String, // 'restaurant' or 'user'
});

const emit = defineEmits(["dataUpdated"]);

// **Define Validation Schema with Yup**
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

// **Determine Default Form Structure**
const defaultForm = computed(() => {
  return props.formType === "Restaurant"
    ? { name: "", address: "", contact: "" }
    : { name: "", email: "", address: "", password: "" };
});

const errors = ref({});
const form = ref({ ...defaultForm.value, addressTemp: "" }); // Added addressTemp

// **Watch for Changes (For Edit Mode)**
watch(
  () =>
    props.formType === "Restaurant" ? props.restaurantData : props.userData,
  (newData) => {
    if (newData && Object.keys(newData).length > 0) {
      form.value = { ...newData, addressTemp: newData.address || "" }; // Ensure addressTemp is populated
    } else {
      form.value = { ...defaultForm.value, addressTemp: "" }; // Reset addressTemp if no data
    }
  },
  { immediate: true }
);

// In saveData(), map addressTemp to address correctly before sending to the server
const saveData = async () => {
  try {
    // Before sending data to the API, make sure to map addressTemp to address
    const dataToSend = { ...form.value, address: form.value.addressTemp };

    // Make the API request accordingly
    const url = props.formType === "Restaurant"
      ? "http://localhost:3000/restaurants"
      : "http://localhost:3000/users";

    if (props.isEdit) {
      // For editing, ensure you have an ID
      let result = await axios.put(`${url}/${form.value.id}`, dataToSend);
      if (result.status === 200) {
        emit("dataUpdated", result.data);
      }
    } else {
      // For adding, send data directly
      let result = await axios.post(url, dataToSend);
      if (result.status === 201) {
        emit("dataUpdated", result.data);
        form.value = { ...defaultForm.value, addressTemp: "" }; // Reset form
      }
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

        <input v-if="formType === 'User'" type="text" placeholder="Enter Password" v-model="form.password" />
        <span v-if="errors.password" class="text-error">{{ errors.password }}</span>

        <input v-if="formType === 'User'" type="text" placeholder="Enter Address" v-model="form.addressTemp" />
        <span v-if="formType === 'User' && errors.address" class="text-error">{{ errors.address }}</span>

        <VBtn color="primary" @click="saveData" class="addEditButton">
          {{ isEdit ? `Update ${formType}` : `Add ${formType}` }}
        </VBtn>
      </form>
    </VCardText>
  </VCard>
</template>
