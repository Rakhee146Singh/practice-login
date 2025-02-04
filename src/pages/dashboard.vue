<script setup>
import LineChart from "@/@core/libs/chartjs/components/LineChart";
import { onMounted, ref } from "vue";
import { useRouter } from 'vue-router';

// Router instance for redirection
const router = useRouter();

const name = ref("Guest");
const totalSales = ref(120);
const activeUsers = ref(250);
const conversionRate = ref(75);
const revenue = ref(5000); // New KPI for total revenue
const newUsers = ref(15);  // New KPI for new users this month
const activeSessions = ref(120); // Engagement metric
const selectedPeriod = ref('This Month'); // Period filter for sales chart

const notifications = ref([
  'New user registered: John Doe',
  'Sale made: $150',
  'System update scheduled at 2:00 AM',
]);

const salesChartData = ref({
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
  datasets: [{
    label: 'Sales ($)',
    data: [30, 70, 100, 200, 150, 180],
    fill: false,
    borderColor: '#4CAF50',
    tension: 0.1
  }]
});

const userGrowthData = ref({
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
  datasets: [{
    label: 'Active Users',
    data: [20, 50, 100, 120, 160, 200],
    fill: false,
    borderColor: '#FF9800',
    tension: 0.1
  }]
});

const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    x: {
      beginAtZero: true,
      ticks: {
        color: '#cfcbcb'
      },
    },
    y: {
      beginAtZero: true,
      ticks: {
        color: '#cfcbcb'
      },
    }
  },
  plugins: {
    legend: {
      labels: {
        color: '#cfcbcb'
      }
    }
  }
});

// Update data periodically (simulate real-time)
setInterval(() => {
  totalSales.value += Math.floor(Math.random() * 10);  // Simulate sales increment
  activeUsers.value += Math.floor(Math.random() * 5);  // Simulate user growth
  revenue.value += Math.floor(Math.random() * 100);    // Simulate revenue increment
  activeSessions.value += Math.floor(Math.random() * 5); // Simulate active sessions growth
}, 5000);

// Load user data or redirect if none found
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
    router.push("/register"); // Redirect to register page if no user data
  }
};

// Filter sales data based on selected period
const filterData = () => {
  // Logic to update `salesChartData` based on selected period
  console.log(`Filtering data for: ${selectedPeriod.value}`);
};

// Navigate to different sections
const navigateTo = (section) => {
  router.push(`/${section}`);
};

// Load data on mounted
onMounted(() => {
  loadData();
});
</script>

<template>
  <div class="dashboard-container">
    <!-- Header Card -->
    <VCard :title="`Hello ${name} 🙌, Welcome to the Dashboard.`" class="header-card"></VCard>

    <!-- Filter Section for Sales Data -->
    <div class="filter-section">
      <VSelect :options="['This Week', 'This Month', 'Last 30 Days', 'Custom']" v-model="selectedPeriod"
        class="filter-dropdown" />
      <VBtn @click="filterData" class="filter-button" variant="outlined" color="success">Apply Filter</VBtn>
    </div>

    <!-- Overview Stats Section -->
    <div class="stats-section">
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>{{ totalSales }}</h3>
          <p>Total Sales</p>
        </div>
      </VCard>
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>{{ activeUsers }}</h3>
          <p>Active Users</p>
        </div>
      </VCard>
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>{{ conversionRate }}%</h3>
          <p>Conversion Rate</p>
        </div>
      </VCard>
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>${{ revenue }}</h3>
          <p>Total Revenue</p>
        </div>
      </VCard>
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>{{ newUsers }}</h3>
          <p>New Users This Month</p>
        </div>
      </VCard>
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>{{ activeSessions }}</h3>
          <p>Active Sessions</p>
        </div>
      </VCard>
    </div>

    <!-- Quick Access Section -->
    <div class="quick-access">
      <VBtn @click="navigateTo('restaurant/list')" class="quick-access-button" variant="outlined" color="warning">View
        All
        Restaurants</VBtn>
      <VBtn @click="navigateTo('user/list')" class="quick-access-button" variant="outlined" color="warning">Manage Users
      </VBtn>
      <VBtn @click="navigateTo('settings')" class="quick-access-button" variant="outlined" color="warning">Settings
      </VBtn>
    </div>

    <!-- Graph Section -->
    <div class="graphs-section">
      <VCard class="graph-card">
        <VCardTitle>Sales Overview</VCardTitle>
        <VCardText>
          <LineChart :data="salesChartData" :options="chartOptions" />
        </VCardText>
      </VCard>

      <VCard class="graph-card">
        <VCardTitle>Users Growth</VCardTitle>
        <VCardText>
          <LineChart :data="userGrowthData" :options="chartOptions" />
        </VCardText>
      </VCard>
    </div>

    <!-- Notifications Section -->
    <div class="notifications-section">
      <VCard class="notifications-card">
        <VCardTitle>Recent Notifications</VCardTitle>
        <VCardText>
          <div v-for="(notification, index) in notifications" :key="index" class="notification-item">
            <p>{{ notification }}</p>
          </div>
        </VCardText>
      </VCard>
    </div>
  </div>
</template>

<style scoped>
.dashboard-container {
  padding: 30px;
}

.header-card {
  margin-block-end: 30px;
}

.filter-section {
  display: flex;
  justify-content: flex-start;
  gap: 20px;
  margin-block-end: 30px;
}

.filter-dropdown {
  inline-size: 200px;
}

.filter-button {
  align-content: center;
  border-radius: 5px;
  cursor: pointer;
  font-size: 17px;
  inline-size: 10%;
}

.stats-section {
  display: grid;
  gap: 20px;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  margin-block-end: 40px;
}

.count-card {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(116, 115, 115, 10%);
}

.count-card-content h3 {
  color: #c6c6c6;
  font-size: 2rem;
  margin-block-end: 5px;
}

.count-card-content p {
  color: #888;
  font-size: 1rem;
}

.quick-access {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  margin-block-end: 30px;
}

.quick-access-button {
  border-radius: 5px;
  block-size: 48px;
  cursor: pointer;
  font-size: 17px;
  min-inline-size: 320px;
  padding-block: 10px;
  padding-inline: 20px;
}

.graphs-section {
  display: flex;
  justify-content: space-between;
  gap: 30px;
  margin-block-end: 40px;
}

.graph-card {
  flex: 1;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(116, 115, 115, 10%);
}

.notifications-section {
  margin-block-start: 30px;
}

.notifications-card {
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(116, 115, 115, 10%);
}

.notification-item {
  color: #cfc6c6;
  font-size: 1rem;
  margin-block-end: 10px;
}
</style>
