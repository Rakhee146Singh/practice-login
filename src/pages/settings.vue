<template>
  <div class="flex min-h-screen p-6">
    <VCard>
      <!-- Sidebar -->
      <div class="w-1/4 bg-gray-100 p-4 rounded-xl sidebar">
        <h2 class="text-lg font-semibold mb-4">Settings</h2>
        <ul>
          <li
            v-for="tab in tabs"
            :key="tab.key"
            class="tab-item"
            :class="{ 'active-tab': activeTab === tab.key }"
            @click="activeTab = tab.key"
          >
            <span class="tab-icon">{{ tab.icon }}</span> {{ tab.name }}
          </li>
        </ul>
      </div>

      <!-- Content -->
      <div class="w-3/4 p-6 content-area">
        <div class="bg-white p-6 rounded-xl shadow-md">
          <div v-if="activeTab === 'security'">
            <h2 class="text-xl font-semibold mb-4">Security Settings</h2>
            <button class="danger-button">Change Password</button>
          </div>
          <div v-if="activeTab === 'notifications'">
            <h2 class="text-xl font-semibold mb-4">Notification Settings</h2>
            <div class="flex justify-between items-center">
              <span>Email Notifications</span>
              <input
                type="checkbox"
                v-model="notifications.email"
                class="toggle-switch"
              />
            </div>
          </div>
          <div v-if="activeTab === 'preferences'">
            <h2 class="text-xl font-semibold mb-4">Preferences</h2>
            <button class="reset-button">Reset Preferences</button>
          </div>
        </div>
      </div>
    </VCard>
  </div>
</template>

<script setup>
import { ref } from "vue";

const activeTab = ref("security");
const notifications = ref({ email: true });

const tabs = [
  { name: "Security", icon: "🔒", key: "security" },
  { name: "Notifications", icon: "🔔", key: "notifications" },
  { name: "Preferences", icon: "⚙️", key: "preferences" },
];
</script>
