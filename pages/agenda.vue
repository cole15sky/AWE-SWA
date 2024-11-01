<template>
    <div>
        <Navbar />
        <div class="w-full  flex flex-col space-y-4">
            <div class="flex flex-col  md:flex-row justify-center md:pr-18 py-8 md:py-18">
                <div class=" hidden lg:block w-fit md:w-1/7 lg:w-1/7 p-3 md:p-5">
                    <ul class="space-y-1 ml-3 md:ml-5 mt-5">
                        <li v-for="(item, index) in uniqueArray.slice(0, 4)" :key="index" class="flex">
                            <button @click="selectType(item)"
                                class="relative w-full flex-1 text-sm md:text-md p-2 md:p-3 whitespace-normal flex justify-between items-center"
                                :class="{
                                    'bg-gradient-to-r from-[#00012D] to-[#03025f] text-white': selectedType === item,
                                    'bg-gray-100 text-black': selectedType !== item
                                }">
                                <span class="font-semibold">{{ toTitleCase(item) }}</span>
                                <div v-if="selectedType === item"
                                    class="absolute right-[-15px] top-1/2 transform -translate-y-1/2 border-t-[10px] border-b-[10px] border-l-[15px] border-t-transparent border-b-transparent border-l-[#03025f]">
                                </div>
                            </button>
                        </li>
                    </ul>
                </div>

                <div class="shadow-xl p-15 md:w-3/5 md:mx-5 lg:mx-0">
                    <div v-if="loading" class="text-center text-gray-500">Loading data...</div>
                    <div v-if="error" class="text-center text-red-500">Error: {{ error }}</div>

                    <div class="flex flex-col md:flex-row justify-between items-start md:items-center w-full space-x-4">

                        <!-- Dropdown for Mobile Screens -->
                        <div class="block md:hidden w-fit p-3 md:order-1">
                            <select class="w-full p-2 border border-gray-300 rounded-md" v-model="selectedType"
                                @change="selectType(selectedType)">
                                <option disabled selected>Discussion Panel</option>
                                <option v-for="(item, index) in uniqueArray.slice(0, 4)" :key="index" :value="item">
                                    {{ toTitleCase(item) }}
                                </option>
                            </select>
                        </div>


                        <ul class="flex order-2 space-x-1 w-full md:justify-end md:w-full sm:justify-end">
                            <li v-for="(date, index) in uniqueArrayDate.slice(0, 4)" :key="index">
                                <button @click="selectDateType(date)" class="relative w-fit text-xl p-3 justify-between"
                                    :class="{
                                        'bg-gradient-to-r from-[#00012D] to-[#03025f] text-white': selectedDateType === date,
                                        'shadow-lg text-black': selectedDateType !== date
                                    }">
                                    <span class="text-sm font-semibold p-4">{{ formatDate(date) }}</span>
                                </button>
                            </li>
                        </ul>
                    </div>


                    <!-- Content Display -->
                    <div v-for="(item, index) in filteredData" :key="index" class="p-6  shadow-sm">
                        <h3 class="text-[#00012D] font-bold">{{ item.title }}</h3>
                        <p class="text-gray-400">{{ item.description }}</p>
                        <div class="flex items-center gap-2">
                            <UIcon name="hugeicons:clock-01" class="w-3 h-3" />
                            <p class="text-sky-300 text-xs">{{ formatTime(item.startDate) }} - {{
                                formatTime(item.endDate)
                                }}</p>
                        </div>
                        <div class="flex flex-wrap mt-2">
                            <div v-for="(speaker, idx) in item.agendaToSpeakers" :key="idx"
                                class="flex items-center mr-4 mb-4">
                                <img :src="getProfileImage(speaker.speakers.profileImage)" alt="Speaker Profile Image"
                                    class="w-10 h-10 rounded-full object-cover mr-2" />
                                <div>
                                    <h4 class="text-md">{{ speaker.speakers.name }}</h4>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div>
            <Footer />
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
const router = useRouter();
const route = useRoute();

const selectedType = ref(route.query.type || 'panelDiscussions');
const selectedDateType = ref(route.query.date || '2024-10-28');

const uniqueArray = ref([]);
const uniqueArrayDate = ref([]);

// function to format timestamp to display only the time
function formatTime(timestamp) {
    const date = new Date(timestamp);
    return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
}

function formatDate(dateString) {
    const date = new Date(dateString);
    const options = { month: 'short', day: 'numeric' };
    return date.toLocaleDateString('en-US', options);
}

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

    router.push({ query: { ...route.query, type: item } });
};

const selectDateType = (date) => {
    selectedDateType.value = date;


    router.push({ query: { ...route.query, date: date } });
};

const getProfileImage = (profileImage) => {
    return `https://pub-f9a129ce37b8446bafc8a9b4ca2c4bdb.r2.dev/${profileImage}`;
};

function extractDate(timestamp) {
    return new Date(timestamp).toISOString().split('T')[0];
}

getData();

function toTitleCase(text) {
    return text
        .replace(/([a-z])([A-Z])/g, '$1 $2')
        .split(' ')
        .map(word => word.charAt(0).toUpperCase() + word.slice(1).toLowerCase())
        .join(' ');
}

onMounted(() => {
    if (route.query.type) {
        selectedType.value = route.query.type;
    }
    if (route.query.date) {
        selectedDateType.value = route.query.date;
    }
});
</script>
