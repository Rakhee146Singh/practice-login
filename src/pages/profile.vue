<template>
  <div>
    <VCard title="Edit/View Profile">
      <!-- <VCardText>Profile</VCardText> -->
      <VCardText>
        <VForm>
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
                <VTextField label="Job Title" v-model="form.jobTitle" outlined></VTextField>
              </VCol>
              <VCol cols="12">
                <VTextField label="Location" v-model="form.location" outlined></VTextField>
              </VCol>
              <VCol cols="12">
                <VTextField label="Email" v-model="form.email" outlined></VTextField>
              </VCol>
              <VCol cols="12">
                <VTextField label="Phone" v-model="form.phone" outlined></VTextField>
              </VCol>
              <VCol cols="12" class="text-center">
                <VBtn color="primary">Save Changes</VBtn>
              </VCol>
            </VRow>
          </VContainer>
        </VForm>
      </VCardText>
    </VCard>
  </div>
</template>

<script>
export default {
  name: "UserProfile",
  data() {
    return {
      form: {
        name: "John Doe",
        jobTitle: "Software Developer",
        location: "San Francisco, CA",
        email: "john.doe@example.com",
        phone: "+1 234 567 890"
      }
    };
  }
};
</script>

