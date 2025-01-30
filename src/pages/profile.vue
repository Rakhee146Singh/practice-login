<script>
import axios from "axios";

export default {
  name: "Profile",
  data() {
    return {
      form: {
        name: "",
        address: "",
        email: "",
      },
      userId: null
    };
  },
  mounted() {
    setTimeout(() => {
      this.loadUserData(); // Load user data when component is mounted
    }, 1000);
  },
  methods: {
    // Load user data from localStorage or set from a globally available state
    loadUserData() {
      const savedUserData = JSON.parse(localStorage.getItem("user-info"));
      if (savedUserData && Array.isArray(savedUserData) && savedUserData.length > 0) {
        const user = savedUserData[0]; // Assuming there's only one user in the array
        this.form.name = user.name;
        this.form.email = user.email;
        this.form.address = user.address; // Ensure address is fetched as well
        this.userId = user.id; // Set the user ID from the array
        console.log("User loaded from localStorage:", user);
      } else {
        console.error("No user data found in localStorage");
      }
    },

    // Method to update the user data via API
    updateUser() {
      if (!this.userId) {
        console.error("User ID not found", this.userId);
        return;
      }

      const updatedData = {
        name: this.form.name,
        address: this.form.address,
        email: this.form.email
      };

      console.log("Sending update request for user ID:", this.userId);

      axios
        .put(`http://localhost:3000/users/update/${this.userId}`, updatedData)
        .then((response) => {
          console.log("User updated successfully", response.data);

          // Save the updated data back into localStorage (wrap it in an array)
          localStorage.setItem("user-info", JSON.stringify([response.data]));

          // Reload the updated data in case any changes occurred
          this.loadUserData();
        })
        .catch((error) => {
          console.error("Error updating user", error);
        });
    }
  }
};
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
                <VBtn type="submit" color="primary" to="/dashboard">Save Changes</VBtn>
              </VCol>
            </VRow>
          </VContainer>
        </VForm>
      </VCardText>
    </VCard>
  </div>
</template>
