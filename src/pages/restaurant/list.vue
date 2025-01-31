<script setup>
import AddRestaurant from "@/components/AddRestaurant.vue";
import axios from "axios";
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
import {
  VBtn,
  VCard,
  VCardText,
  VDataTable,
  VDialog,
} from "vuetify/components";

const router = useRouter();
const name = ref("Guest");
const restaurant = ref([]);
const dialog = ref(false);
const isEdit = ref(false);
const selectedRestaurant = ref(null);

// **Load Restaurant Data**
const loadData = async () => {
  let user = localStorage.getItem("user-info");
  if (user) {
    try {
      const userInfo = JSON.parse(user);
      name.value = Array.isArray(userInfo) && userInfo.length > 0
        ? userInfo[0]?.name || "Guest"
        : "Guest";
    } catch (e) {
      console.error("Error parsing user-info:", e);
      name.value = "Guest";
    }
  } else {
    router.push("/register");
  }

  try {
    let result = await axios.get("http://localhost:3000/restaurants");
    restaurant.value = result.data;
  } catch (error) {
    console.error("Error fetching restaurants:", error);
  }
};

// **Delete Restaurant**
const deleteRestaurant = async (id) => {
  try {
    let result = await axios.delete(`http://localhost:3000/restaurants/${id}`);
    if (result.status === 200) {
      restaurant.value = restaurant.value.filter((r) => r.id !== id); // Remove deleted item
    }
  } catch (error) {
    console.error("Error deleting restaurant:", error);
  }
};

// **Update Restaurant List Immediately Without Reload**
const updateRestaurantList = (updatedRestaurant) => {
  if (isEdit.value) {
    // Update existing restaurant
    const index = restaurant.value.findIndex(
      (r) => r.id === updatedRestaurant.id
    );
    if (index !== -1) {
      restaurant.value[index] = updatedRestaurant;
    }
  } else {
    // Add new restaurant
    restaurant.value.push(updatedRestaurant);
  }
  dialog.value = false; // Close modal
};

// **Open Modal for Adding**
const openAddModal = () => {
  isEdit.value = false;
  selectedRestaurant.value = null;
  dialog.value = true;
};

// **Open Modal for Updating**
const openEditModal = (item) => {
  isEdit.value = true;
  selectedRestaurant.value = { ...item };
  dialog.value = true;
};

// **Load Data on Mounted**
onMounted(() => {
  loadData();
});
</script>

<template>
  <div>
    <VCard title="List Of Restaurants">
      <VCardText>Hello {{ name }}, Welcome to the Restaurant List Page</VCardText>

      <div class="addButton">
        <VBtn @click="openAddModal" color="success" variant="outlined">Add Restaurant</VBtn>
      </div>

      <VDialog v-model="dialog" max-width="600px">
        <VCard>
          <AddRestaurant :restaurantData="selectedRestaurant" :isEdit="isEdit" :formType="'Restaurant'"
            @dataUpdated="updateRestaurantList" />
        </VCard>
      </VDialog>

      <VCardText style="text-align: start;">
        <VDataTable :headers="[
          { title: 'ID', key: 'id' },
          { title: 'Name', key: 'name' },
          { title: 'Contact', key: 'contact' },
          { title: 'Address', key: 'address' },
          { title: 'Actions', key: 'actions', sortable: false },
        ]" :items="restaurant" :items-per-page="10">
          <template #item.actions="{ item }">
            <VBtn color="primary" @click="openEditModal(item)" variant="outlined">Update</VBtn>
            <VBtn color="error" @click="deleteRestaurant(item.id)" style="margin-inline-start: 6px;" variant="outlined">
              Delete
            </VBtn>
          </template>
        </VDataTable>
      </VCardText>
    </VCard>
  </div>
</template>

<style>
td {
  block-size: 40px;
  inline-size: 160px;
}

.addButton {
  display: flex;
  justify-content: end;
  margin-inline-end: 92px;
}
</style>
