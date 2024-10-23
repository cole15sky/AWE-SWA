<template>
    <div>
        <Navbar />
        <div class="flex  justify-center min-h-50">

            <!-- Sidebar displaying 4 unique items from uniqueArray -->
            <div class="w-1/6 p-4 ">

                <!--  Loading Unique elements from Array -->
                <ul>
                    <li v-for="(item, index) in uniqueArray.slice(0, 4)" :key="index">
                        <button @click="selectType(item)" class="w-full text-left text-xl p-2" :class="{
                            'bg-gradient-to-r from-[#00012D] to-[#03025f] text-white': selectedType === item,
                            'bg-sky-100 text-black': selectedType !== item
                        }">
                            {{ item }}
                        </button>
                    </li>
                </ul>


            </div>

            <div class="w-2/4 p-15">

                <div v-if="loading" class="text-center text-gray-500">Loading data...</div>
                <div v-if="error" class="text-center text-red-500">Error: {{ error }}</div>

                <div >
                    <!-- Display items filtered by selectedType -->
                    <div v-for="(item, index) in filteredData" :key="index" class="p-6 border rounded-xl  shadow">
                        <h3 class="text-[#00012D] font-bold">{{ item.title }}</h3>
                        <p>{{ item.description }}</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script setup>
definePageMeta({
    colorMode: 'light',
})

// Define reactive variables
const data = ref(null);
const error = ref(null);
const loading = ref(true);
const selectedType = ref('null');


// Fetch data from API when component is mounted
const getData = async () => {
    const url = 'https://swa-2024-dev.up.railway.app/api/agenda/web';
    try {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`Response status: ${response.status}`);
        }
        const json = await response.json();
        data.value = json;

        // Extract unique types from the data
        const allTypes = json.map(obj => obj.type);

        uniqueArray.value = Array.from(new Set(allTypes));
        debugger

    } catch (err) {
        error.value = err.message;
    } finally {
        loading.value = false;
    }
};

// Fetch data on component mount
onMounted(() => {
    getData();
});

// Computed property to filter data by the selected type
const filteredData = computed(() => {
    return data.value ? data.value.filter(item => item.type === selectedType.value) : [];
});

// Define uniqueArray as reactive to store unique types
const uniqueArray = ref([]);

const selectType = (item) => {
    selectedType.value = item;  // Update the selected type
};

</script>
