<script setup>
import LineChart from "@/@core/libs/chartjs/components/LineChart";
import { onMounted, ref } from "vue";
import { useRouter } from 'vue-router'; // Import router for redirection

// Router instance for redirection
const router = useRouter();

const name = ref("Guest");

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

// const chartOptions = ref({
//   responsive: true,
//   maintainAspectRatio: false,
//   scales: {
//     x: {
//       beginAtZero: true
//     },
//     y: {
//       beginAtZero: true
//     }
//   }
// });

const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    x: {
      beginAtZero: true,
      ticks: {
        color: '#cfcbcb'  // Set color of x-axis labels
      },
    },
    y: {
      beginAtZero: true,
      ticks: {
        color: '#cfcbcb'  // Set color of y-axis labels
      },
    }
  },
  plugins: {
    legend: {
      labels: {
        color: '#cfcbcb'  // Set color of dataset labels (legend)
      }
    }
  }
});


// Load data on mounted
onMounted(() => {
  loadData();
});
</script>

<template>
  <div class="dashboard-container">
    <!-- Header Card -->
    <VCard :title="`Hello ${name} 🙌, Welcome to the Dashboard.`" class="header-card"></VCard>

    <!-- Overview Stats Section -->
    <div class="stats-section">
      <!-- Count Card 1 -->
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>120</h3>
          <p>Total Sales</p>
        </div>
      </VCard>

      <!-- Count Card 2 -->
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>250</h3>
          <p>Active Users</p>
        </div>
      </VCard>

      <!-- Count Card 3 -->
      <VCard class="count-card">
        <div class="count-card-content">
          <h3>75%</h3>
          <p>Conversion Rate</p>
        </div>
      </VCard>
    </div>

    <!-- Graph Section -->
    <div class="graphs-section">
      <!-- Sales Graph -->
      <VCard class="graph-card">
        <VCardTitle>Sales Overview</VCardTitle>
        <VCardText>
          <LineChart :data="salesChartData" :options="chartOptions" />
        </VCardText>
      </VCard>

      <!-- Users Growth Graph -->
      <VCard class="graph-card">
        <VCardTitle>Users Growth</VCardTitle>
        <VCardText>
          <LineChart :data="userGrowthData" :options="chartOptions" />
        </VCardText>
      </VCard>
    </div>
  </div>
</template>

<style scoped>
.dashboard-container {
  padding: 20px;
}

.header-card {
  margin-block-end: 30px;
}

.stats-section {
  display: flex;
  justify-content: space-between;
  margin-block-end: 30px;
}

.count-card {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(116, 115, 115, 10%);
  inline-size: 30%;
  text-align: center;
}

.count-card-content h3 {
  font-size: 2rem;
  margin-block-end: 5px;
}

.count-card-content p {
  color: #cfcbcb;
  font-size: 1rem;
}

.graphs-section {
  display: flex;
  justify-content: space-between;
  gap: 20px;
}

.graph-card {
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(116, 115, 115, 10%);
  inline-size: 48%;
}

.graph-card h3 {
  font-size: 1.2rem;
  margin-block-end: 15px;
}
</style>
