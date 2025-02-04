<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const form = ref({
  name: "",
  address: "",
  email: "",
  image: "",
});
const userId = ref(null);

// Function to load user data from localStorage and fetch it from the backend
const loadUserData = () => {
  const savedUserData = JSON.parse(localStorage.getItem("user-info"));
  if (savedUserData && Array.isArray(savedUserData) && savedUserData.length > 0) {
    userId.value = savedUserData[0].id; // Get user ID from localStorage

    // Fetch user details from the backend using the user ID
    axios
      .get(`http://localhost:3000/users/${userId.value}`)
      .then((response) => {
        const user = response.data;
        form.value.name = user.name;
        form.value.email = user.email;
        form.value.address = user.address;
        form.value.image = user.image || ""; // Load image if available
        console.log("User details loaded from API:", user);
      })
      .catch((error) => {
        console.error("Error fetching user details from API:", error);
      });
  } else {
    console.error("No user data found in localStorage");
  }
};

const updateUser = () => {
  if (!userId.value) {
    console.error("User ID not found", userId.value);
    return;
  }

  const updatedData = {
    name: form.value.name,
    address: form.value.address,
    email: form.value.email,
    image: form.value.image,
  };

  console.log("Sending update request for user ID:", userId.value);

  axios.put(`http://localhost:3000/users/${userId.value}`, updatedData)
    .then((response) => {
      console.log("User updated successfully", response.data);

      // Store only necessary user details
      const userInfo = {
        id: response.data.id,
        name: response.data.name,
        email: response.data.email,
        address: response.data.address
      };

      localStorage.setItem("user-info", JSON.stringify([userInfo]));

      loadUserData();

      router.push("dashboard");
    })
    .catch((error) => {
      console.error("Error updating user", error);
    });
};

// Image Upload Handler
const handleImageUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => {
      form.value.image = reader.result;
    };
  }
};

onMounted(loadUserData);
</script>

<template>
  <div>
    <VCard title="Edit/View Profile">
      <VCardText>
        <VForm @submit.prevent="updateUser">
          <VContainer>
            <VRow>
              <VCol cols="12" class="text-center">
                <!-- Display existing profile image or default icon -->
                <VAvatar size="100" class="mb-4" color="grey lighten-2">
                  <img v-if="form.image" :src="form.image" alt="Profile Image" width="100" height="100" />
                  <VIcon v-else size="100">tabler-user</VIcon>
                </VAvatar>
              </VCol>
              <VCol cols="12">
                <VTextField label="Full Name" v-model="form.name" outlined></VTextField>
              </VCol>
              <VCol cols="12">
                <VTextField label="Address" v-model="form.address" outlined></VTextField>
              </VCol>
              <VCol cols="12">
                <VTextField label="Email" v-model="form.email" outlined></VTextField>
              </VCol>

              <!-- Image Upload -->
              <VCol cols="12" class="text-center">
                <input type="file" @change="handleImageUpload" accept="image/*" />
              </VCol>

              <VCol cols="12" class="text-center">
                <VBtn type="submit" color="primary">Save Changes</VBtn>
              </VCol>
            </VRow>
          </VContainer>
        </VForm>
      </VCardText>
    </VCard>
  </div>
</template>
