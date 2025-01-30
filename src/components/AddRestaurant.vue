<script setup>
import { ref, defineEmits, defineProps, watch, computed } from "vue";
import axios from "axios";

const props = defineProps({
  restaurantData: Object, // Accepts data for restaurant update
  userData: Object, // Accepts data for user update
  isEdit: Boolean, // Determines if it's edit mode
  formType: String, // 'restaurant' or 'user'
});

const emit = defineEmits(["dataUpdated"]);

// **Determine Default Form Structure**
const defaultForm = computed(() => {
  return props.formType === "Restaurant"
    ? { name: "", address: "", contact: "" }
    : { name: "", email: "", password: "" };
});

const form = ref({ ...defaultForm.value });

// **Watch for Changes (For Edit Mode)**
watch(
  () =>
    props.formType === "Restaurant" ? props.restaurantData : props.userData,
  (newData) => {
    if (newData && Object.keys(newData).length > 0) {
      form.value = { ...newData }; // Ensures the form is populated with new data
      console.log("Form populated with:", form.value); // Log to debug
    } else {
      form.value = { ...defaultForm.value }; // Reset if no data
      console.log("Form reset:", form.value); // Log to debug
    }
  },
  { immediate: true }
);

// **Save or Update Data**
const saveData = async () => {
  try {
    console.log("Form data before saving:", form.value); // Log to debug

    const url =
      props.formType === "Restaurant"
        ? "http://localhost:3000/restaurants"
        : "http://localhost:3000/users";

    if (props.isEdit) {
      if (!form.value.id) {
        console.error("ID is missing!"); // Log error if ID is missing
        return;
      }

      let result = await axios.put(`${url}/${form.value.id}`, form.value);

      if (result.status === 200) {
        emit("dataUpdated", form.value);
      }
    } else {
      let result = await axios.post(url, form.value);

      if (result.status === 201) {
        emit("dataUpdated", result.data);
        form.value = { ...defaultForm.value }; // Reset form
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

        <input
          v-if="formType === 'Restaurant'"
          type="text"
          placeholder="Enter Address"
          v-model="form.address"
        />
        <input
          v-if="formType === 'Restaurant'"
          type="text"
          placeholder="Enter Contact"
          v-model="form.contact"
        />

        <input
          v-if="formType === 'User'"
          type="email"
          placeholder="Enter Email"
          v-model="form.email"
        />
        <input
          v-if="formType === 'User'"
          type="password"
          placeholder="Enter Password"
          v-model="form.password"
        />

        <VBtn color="primary" @click="saveData" class="addEditButton">
          {{ isEdit ? `Update ${formType}` : `Add ${formType}` }}
        </VBtn>
      </form>
    </VCardText>
  </VCard>
</template>
