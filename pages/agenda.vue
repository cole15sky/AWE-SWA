<template>
    <div>
        <Navbar />
        <div class="flex justify-center w-screen min-h-50 pt-20 pl-20">
            <div class="w-1/6 p-4">
                <ul class="space-y-1 mt-9">
                    <li v-for="(item, index) in uniqueArray.slice(0, 4)" :key="index">
                        <button @click="selectType(item)"
                            class="relative w-full  text-xl p-2 flex justify-between items-center" :class="{
                                'bg-gradient-to-r from-[#00012D] to-[#03025f] text-white': selectedType === item,
                                'bg-sky-100 text-black': selectedType !== item
                            }">
                            <span>{{ item }}</span>

                            <div v-if="selectedType === item"
                                class="absolute right-[-15px] top-1/2 transform -translate-y-1/2 border-t-[10px] border-b-[10px] border-l-[15px] border-t-transparent border-b-transparent border-l-[#03025f]">
                            </div>
                        </button>
                    </li>
                </ul>
            </div>

            <div class="w-2/4 p-15">
                <div v-if="loading" class="text-center text-gray-500">Loading data...</div>
                <div v-if="error" class="text-center text-red-500">Error: {{ error }}</div>
                <div>

                    <!-- Display items filtered by selectedType and selectedDate -->
                    <div class="flex justify-end space-x-4">
                        <ul class="flex space-x-1 ">
                            <li v-for="(date, index) in uniqueArrayDate.slice(0, 4)" :key="index">
                                <button @click="selectDateType(date)"
                                    class="relative w-full text-xl p-3  justify-between" :class="{
                                        'bg-gradient-to-r from-[#00012D] to-[#03025f] text-white': selectedDateType === date,
                                        ' shadow-lg text-black': selectedDateType !== date
                                    }">
                                    <span>{{ date }}</span>
                                </button>
                            </li>
                        </ul>
                    </div>
                    <!-- Display filtered data with profile images -->
                    <div v-for="(item, index) in filteredData" :key="index" class="p-6 shadow-md">
                        <h3 class="text-[#00012D] font-bold">{{ item.title }}</h3>
                        <p>{{ item.description }}</p>

                        <!-- Rendering speaker profiles -->
                        <div v-for="(speaker, idx) in item.agendaToSpeakers" :key="idx" class="flex items-center mt-2">

                            <img :src="getProfileImage(speaker.speakers.profileImage)" alt="Speaker Profile Image"
                                class="w-16 h-16 rounded-full object-cover mr-4" />
                            <div>
                                <h4 class="font-semibold text-lg">{{ speaker.speakers.name }}</h4>
                            </div>
                        </div>
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

const data = ref(null);
const error = ref(null);
const loading = ref(true);
const selectedType = ref('panelDiscussions');
const selectedDateType = ref(null);

const uniqueArray = ref([]);
const uniqueArrayDate = ref([]);

// Utility function to extract date from a timestamp
const extractDate = (timestamp) => {
    const date = new Date(timestamp);
    return date.toISOString().split('T')[0];  // Extract only the 'YYYY-MM-DD' part
};

// Fetch data from API when component is mounted
const getData = async () => {
    const url = 'https://swa-2024-dev.up.railway.app/api/agenda/web';
    try {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`Response status: ${response.status}`);
        }
        const json = await response.json();
        data.value = json.map(item => ({
            ...item,
            startDate: extractDate(item.startDate)
        }));

        // Extract unique types from the data
        const allTypes = json.map(obj => obj.type);
        uniqueArray.value = Array.from(new Set(allTypes));

        // Extract unique start dates from the data
        const allStartDates = json.map(obj => extractDate(obj.startDate));
        uniqueArrayDate.value = Array.from(new Set(allStartDates));


    } catch (err) {
        error.value = err.message;
    } finally {
        loading.value = false;
    }
};

// Fetch data on component mount
// onMounted(() => {
//     getData();
// });

// Computed property to filter data by selectedType and selectedDateType
const filteredData = computed(() => {
    return data.value
        ? data.value.filter(
            item =>
                item.type === selectedType.value &&
                (!selectedDateType.value || item.startDate === selectedDateType.value)
        )
        : [];
});

const selectType = (item) => {
    selectedType.value = item;
};

const selectDateType = (date) => {
    selectedDateType.value = date;
};

const getProfileImage = (profileImage) => {
    return `https://pub-f9a129ce37b8446bafc8a9b4ca2c4bdb.r2.dev/${profileImage}`;
};

getData();

</script>
