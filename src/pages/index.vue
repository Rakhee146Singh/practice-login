<script>
import axios from 'axios';
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

export default {
  name: 'HomePage',
  setup() {
    const router = useRouter();
    const name = ref('');
    const restaurant = ref([]);

    const deleteRestaurant = async (id) => {
      try {
        let result = await axios.delete(`http://localhost:3000/restaurants/${id}`);
        console.log(result, 'Deleted Successfully');

        if (result.status === 200) {
          loadData();
        }
      } catch (error) {
        console.error('Error deleting restaurant:', error);
      }
    };

    const loadData = async () => {
      let user = localStorage.getItem('user-info');
      if (user) {
        try {
          const userInfo = JSON.parse(user);
          if (Array.isArray(userInfo) && userInfo.length > 0) {
            name.value = userInfo[0]?.name || 'Guest';
          } else {
            name.value = 'Guest';
          }
        } catch (e) {
          console.error('Error parsing user-info:', e);
          name.value = 'Guest';
        }
      } else {
        router.push('/register');
      }

      try {
        let result = await axios.get('http://localhost:3000/restaurants');
        console.log(result, 'home page');
        restaurant.value = result.data;
      } catch (error) {
        console.error('Error fetching restaurants:', error);
      }
    };

    onMounted(() => {
      loadData();
    });

    return {
      name,
      restaurant,
      deleteRestaurant,
      loadData
    };
  }
};
</script>


<template>
  <div>
    <VCard title="List Of Restaurant">
      <VCardText>Hello {{ name }}, Welcome to the Home Page</VCardText>
      <VCardText>
        <div class="restaurantTable">
          <table border="1">
            <tr>
              <td> Id </td>
              <td> Name </td>
              <td> Contact </td>
              <td> Address </td>
              <td> Actions </td>
            </tr>
            <tr v-for="item in restaurant" :key="item.id">
              <td> {{ item.id }} </td>
              <td>{{ item.name }}</td>
              <td>{{ item.contact }}</td>
              <td>{{ item.address }}</td>
              <td>
                <router-link :to="'/update/' + item.id">
                  Update
                </router-link>
                <button v-on:click="deleteRestaurant(item.id)">Delete</button>
              </td>
            </tr>
          </table>
        </div>
      </VCardText>
    </VCard>
  </div>
</template>

<style>
td {
  block-size: 40px;
  inline-size: 160px;
}
</style>
