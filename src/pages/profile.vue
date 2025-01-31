<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const form = ref({
  name: "",
  address: "",
  email: "",
});
const userId = ref(null);

const loadUserData = () => {
  const savedUserData = JSON.parse(localStorage.getItem("user-info"));
  if (savedUserData && Array.isArray(savedUserData) && savedUserData.length > 0) {
    const user = savedUserData[0];
    form.value.name = user.name;
    form.value.email = user.email;
    form.value.address = user.address;
    userId.value = user.id;
    console.log("User loaded from localStorage:", user);
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
  };

  console.log("Sending update request for user ID:", userId.value);

  axios
    .put(`http://localhost:3000/users/${userId.value}`, updatedData)
    .then((response) => {
      console.log("User updated successfully", response.data);

      localStorage.setItem("user-info", JSON.stringify([response.data]));

      loadUserData();

      router.push("dashboard");
    })
    .catch((error) => {
      console.error("Error updating user", error);
    });
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
                <VAvatar size="100" class="mb-4" color="grey lighten-2">
                  <VIcon size="100">tabler-user</VIcon>
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
