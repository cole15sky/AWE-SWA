<script setup>
const links = [{
    label: 'Home',
    to: '/'
},
{
    label: 'Agenda',
    to: '/agenda'
},
{
    label: 'Speakers',
    to: '/speakers'
},
{
    label: 'Sponsors',
    to: '/sponsors'
},
{
    label: 'Exhibitors',
    to: '/exhibitors'
},
{
    label: 'Media center',
    to: '/mediacenter'
},
{
    label: 'Digital Library',
    to: '/digitallibrary'
},
{
    label: 'Contact us',
    to: '/contactus'
}
]

const isOpen = ref(false)
const route = useRoute()

const scheduleText = computed(() => {
    switch (route.path) {
        case '/agenda':
            return 'Event Schedules'
    }
})

</script>
<template>
    <div class="relative bg-[center_top_-4rem] h-80 w-full bg-gradient-to-r from-[#00012D] to-[#03025f] bg-cover text-white font-semibold py-4 px-2 md:px-20 space-y-4 text-sm"
        style="background-image: url('/images/agenda-header.png');">
        <div class="absolute inset-0 bg-black opacity-60 z-10"></div>
        <img src="/images/logo.png" alt="IDWS logo"
            class="absolute top-5 left-5 z-20 h-16 sm:h-20 md:h-24 lg:h-30 xl:h-30" />
        <div class="relative z-20 flex items-center justify-end space-x-4 text-xs">
            <div>
                <NuxtLink to="/register" class="flex items-center md:space-x-2">
                    <span>Register now</span>
                    <UIcon name="heroicons:arrow-long-right-20-solid" class="w-5 h-5" />
                </NuxtLink>
            </div>
            <div>
                <NuxtLink to="/login" class="flex items-center md:space-x-2">
                    <span>Login</span>
                    <UIcon name="material-symbols-light:lock-outline" class="w-5 h-5" />
                </NuxtLink>
            </div>
        </div>
        <div class="relative z-20 flex justify-end">
            <div class="hidden min-[1400px]:flex w-3/4 lg:justify-evenly lg:items-center">
                <div v-for="link in links" :key="link.label">
                    <NuxtLink :to="link.to" active-class="text-[#0058A0]">
                        {{ link.label }}
                    </NuxtLink>
                </div>
                <div class="parellelogram bg-[#0058A0] py-2 px-10 flex items-center space-x-2 cursor-pointer">
                    <UIcon name="ic:baseline-video-camera-front" class="w-5 h-4" />
                    <h1>LIVE STREAMING </h1>
                </div>
            </div>
            <UIcon name='ic:baseline-menu' class="min-[1400px]:hidden w-10 h-10 cursor-pointer"
                @click="isOpen = true" />
        </div>
        <div class="hidden">
            <USlideover v-model="isOpen" prevent-close>
                <UCard class="flex flex-col flex-1"
                    :ui="{ body: { base: 'flex-1' }, ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
                    <template #header>
                        <div class="flex items-center justify-end">
                            <UButton color="gray" variant="ghost" icon="i-heroicons-x-mark-20-solid" class="-my-1"
                                @click="isOpen = false" />
                        </div>
                    </template>

                    <Placeholder class="h-full space-y-2">
                        <div v-for="link in links" :key="link.label">
                            <NuxtLink :to="link.to" active-class="text-[#0058A0]">
                                {{ link.label }}
                            </NuxtLink>
                        </div>

                        <div class="parellelogram bg-[#0058A0] py-2 px-6 flex w-fit cursor-pointer">
                            <UIcon name="ic:baseline-video-camera-front" class="w-6 h-5" />
                            <h1>Live Streaming</h1>
                        </div>
                    </Placeholder>
                </UCard>
            </USlideover>
        </div>
        <p class="hidden md:block md:absolute bottom-7 left-30 font-semibold text-4xl z-20">{{ scheduleText }}</p>
    </div>
</template>


<style scoped>
.parellelogram {
    clip-path: polygon(5% 0%, 100% 0%, 95% 100%, 0% 100%);
}
</style>
