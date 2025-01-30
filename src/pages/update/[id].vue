<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRouter, useRoute } from 'vue-router';

export default {
    name: 'UpdateRestaurant',
    setup() {
        const router = useRouter();
        const route = useRoute();
        const restaurant = ref({
            name: '',
            address: '',
            contact: ''
        });

        const updateRestaurant = async () => {
            try {
                let result = await axios.put(`http://localhost:3000/restaurants/${route.params.id}`, {
                    name: restaurant.value.name,
                    address: restaurant.value.address,
                    contact: restaurant.value.contact,
                });

                if (result.status === 200) {
                    router.push('/');
                }
                console.log('Update restaurant successfully', result);
            } catch (error) {
                console.error('Error updating restaurant', error);
            }
        };

        onMounted(async () => {
            let user = localStorage.getItem('user-info');
            if (!user) {
                router.push('/register');
            }
            try {
                let result = await axios.get(`http://localhost:3000/restaurants/${route.params.id}`);
                restaurant.value = result.data;
                console.log(result, 'result');
            } catch (error) {
                console.error('Error fetching restaurant data', error);
            }
        });

        return {
            restaurant,
            updateRestaurant
        };
    }
};
</script>


<template>
  <div>
    <VCard title="Update Restaurant">
      <VCardText>
        <form class="add">
            <input type="text" name="name" placeholder="Enter Name" v-model="restaurant.name">
            <input type="text" name="address" placeholder="Enter Address" v-model="restaurant.address">
            <input type="text" name="contact" placeholder="Enter Contact" v-model="restaurant.contact">
            <button type="button" @click="updateRestaurant"> Update Restaurant</button>
        </form>
      </VCardText>
  </VCard>
</div>
</template>
