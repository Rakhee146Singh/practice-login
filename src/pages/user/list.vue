<script setup>
import AddRestaurant from "@/components/AddRestaurant.vue";
import axios from "axios";
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
import { VBtn, VCard, VCardText, VDataTable, VDialog } from "vuetify/components";

const router = useRouter();
const name = ref("Guest");
const user = ref([]);
const dialog = ref(false);
const isEdit = ref(false);
const selectedUser = ref(null);

// **Load user Data**
const loadData = async () => {
  let userInfoString = localStorage.getItem("user-info"); // Avoid reusing 'user'
  if (userInfoString) {
    try {
      const userInfo = JSON.parse(userInfoString);
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
    let result = await axios.get("http://localhost:3000/users");
    user.value = result.data;
  } catch (error) {
    console.error("Error fetching users:", error);
  }
};


// **Delete user**
const deleteUser = async (id) => {
  try {
    let result = await axios.delete(`http://localhost:3000/users/${id}`);
    if (result.status === 200) {
      user.value = user.value.filter((r) => r.id !== id); // Remove deleted item
    }
  } catch (error) {
    console.error("Error deleting user:", error);
  }
};

// **Update user List Immediately Without Reload**
const updateUserList = (updatedUser) => {
  if (isEdit.value) {
    // Update existing user
    const index = user.value.findIndex((r) => r.id === updatedUser.id);
    if (index !== -1) {
      user.value[index] = updatedUser;
    }
  } else {
    // Add new user
    user.value.push(updatedUser);
  }
  dialog.value = false; // Close modal
};

// **Open Modal for Adding**
const openAddModal = () => {
  isEdit.value = false;
  selectedUser.value = null;
  dialog.value = true;
};

// **Open Modal for Updating**
const openEditModal = (item) => {
  isEdit.value = true;
  selectedUser.value = { ...item };
  dialog.value = true;
};

// **Load Data on Mounted**
onMounted(() => {
  loadData();
});
</script>

<template>
  <div>
    <VCard title="List Of Users">
      <VCardText>Hello {{ name }}, Welcome to the User List Page</VCardText>

      <div class="addButton">
        <VBtn @click="openAddModal" color="success" variant="outlined">Add User</VBtn>
      </div>

      <VDialog v-model="dialog" max-width="600px">
        <VCard>
          <AddRestaurant
            :userData="selectedUser"
            :isEdit="isEdit"
            :formType="'User'"
            @dataUpdated="updateUserList"
          />
        </VCard>
      </VDialog>

      <VCardText>
        <VDataTable :headers="[
          { title: 'ID', key: 'id' },
          { title: 'Name', key: 'name' },
          { title: 'Email', key: 'email' },
          { title: 'Password', key: 'password' },
          { title: 'Actions', key: 'actions', sortable: false }
        ]" :items="user" :items-per-page="10">
          <template #item.actions="{ item }">
            <VBtn color="primary" @click="openEditModal(item)" variant="outlined">Update</VBtn>
            <VBtn color="error" @click="deleteUser(item.id)" style="margin-inline-start: 6px;" variant="outlined">
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
  margin-inline-end: 70px;
}
</style>
