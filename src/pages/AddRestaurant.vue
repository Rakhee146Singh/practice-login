<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

export default {
    name: 'AddRestaurant',
    setup() {
        const router = useRouter();
        const restaurant = ref({
            name: '',
            address: '',
            contact: ''
        });

        const addRestaurant = async () => {
            try {
                let result = await axios.post('http://localhost:3000/restaurants', {
                    name: restaurant.value.name,
                    address: restaurant.value.address,
                    contact: restaurant.value.contact,
                });

                if (result.status === 201) {
                    router.push('/');
                }
                console.log('Add restaurant successfully', result);
            } catch (error) {
                console.error('Error adding restaurant', error);
            }
        };

        onMounted(() => {
            let user = localStorage.getItem('user-info');
            if (!user) {
                router.push('/register');
            }
        });

        return {
            restaurant,
            addRestaurant
        };
    }
};
</script>

<template>
  <div>
    <VCard title="Add Restaurant">
      <VCardText>
        <form class="add">
            <input type="text" name="name" placeholder="Enter Name" v-model="restaurant.name">
            <input type="text" name="address" placeholder="Enter Address" v-model="restaurant.address">
            <input type="text" name="contact" placeholder="Enter Contact" v-model="restaurant.contact">
            <button type="button" @click="addRestaurant">Add New Restaurant</button>
        </form>
    </VCardText>
  </VCard>
</div>
</template>

