<template>
    <div>
        <Navbar />
        <div class="w-full flex flex-col space-y-4">
            <div class="flex md:pr-18 md:flex-row justify-center py-8 md:mt-10">
                <div class="hidden md:mt-10 lg:block w-fit md:w-1/7 lg:w-1/7">
                    <ul class="space-y-1 md:w-fit md:mt-10">
                        <li v-for="(item, index) in uniqueArray.slice(0, 4)" :key="index" class="flex">
                            <button @click="selectType(item)"
                                class="relative md:w-[140px] md:h-[50px] flex-1 text-sm md:text-md p-2 whitespace-normal flex justify-center items-center"
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
                <div class="px-8 w-fit lg:w-3/5">
                    <div v-if="pending" class="text-center text-gray-500">Loading data...</div>
                    <div v-if="error" class="text-center text-red-500">Error: {{ error.message }}</div>

                    <div class="flex flex-col md:flex-row items-start md:items-center w-full space-x-4">
                        <div class="w-full md:w-fit p-3 md:order-1 lg:hidden">
                            <select class="w-fit p-2 border border-gray-300 rounded-md" v-model="selectedType"
                                @change="selectType(selectedType)">
                                <option v-for="(item, index) in uniqueArray.slice(0, 4)" :key="index" :value="item">
                                    {{ toTitleCase(item) }}
                                </option>
                            </select>
                        </div>
                        <div class="flex py-1 sm:justify-end order-2 md:justify-end md:w-full space-x-1">
                            <div class="flex">
                                <div v-for="(date, index) in uniqueArrayDate.slice(0, 4)" :key="index">
                                    <button @click="selectDateType(date)"
                                        class="relative md:w-[140px] h-[60px] text-xl p-2 justify-between"
                                        :class="{
                                            'bg-gradient-to-r from-[#00012D] to-[#03025f] text-white': selectedDateType === date,
                                            'shadow-lg text-black': selectedDateType !== date
                                        }">
                                        <span class="text-sm font-semibold p-4">{{ formatDate(date) }}</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="px-10 md:px-3" style="box-shadow: 0 -1px 10px rgba(0, 0, 0, 0.25);">
                        <div class="p-3 shadow-sm" v-for="(item, index) in filteredData" :key="index">
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
                                    <img :src="getProfileImage(speaker.speakers.profileImage)"
                                        alt="Speaker Profile Image" class="w-10 h-10 rounded-full object-cover mr-2" />
                                    <div>
                                        <h4 class="text-md">{{ speaker.speakers.name }}</h4>
                                    </div>
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

const { data,pending,error } = useFetch('https://swa-2024-dev.up.railway.app/api/agenda/web', {
    transform: (json) => {
        return json.map(item => ({
            ...item,
            startDate: extractDate(item.startDate)
        }));
    }
});

const uniqueArray = computed(() => Array.from(new Set(data.value?.map(obj => obj.type) || [])));
const uniqueArrayDate = computed(() => Array.from(new Set(data.value?.map(obj => extractDate(obj.startDate)) || [])));

const selectedType = ref(useRoute().query.type || 'general');
const selectedDateType = ref(useRoute().query.date || '2024-11-25');

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
    useRouter().push({ query: { ...useRoute().query, type: item } });
};

const selectDateType = (date) => {
    selectedDateType.value = date;
    useRouter().push({ query: { ...useRoute().query, date: date } });
};

const getProfileImage = (profileImage) => {
    return `https://pub-f9a129ce37b8446bafc8a9b4ca2c4bdb.r2.dev/${profileImage}`;
};

function formatTime(timestamp) {
    const date = new Date(timestamp);
    return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
}

function formatDate(dateString) {
    const date = new Date(dateString);
    const options = { month: 'short', day: 'numeric' };
    return date.toLocaleDateString('en-US', options);
}

function extractDate(timestamp) {
    return new Date(timestamp).toISOString().split('T')[0];
}

function toTitleCase(text) {
    return text
        .replace(/([a-z])([A-Z])/g, '$1 $2')
        .split(' ')
        .map(word => word.charAt(0).toUpperCase() + word.slice(1).toLowerCase())
        .join(' ');
}
</script>