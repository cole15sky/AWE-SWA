<script setup>
    const footerLinks = [
        [
            {
                label: 'Home',
                to: '/'
            },
        ],
        [
            {
                label: 'Agenda',
                to: '/Agenda'
            },
            {
                label: 'Speakers',
                to: '/'
            },
            {
                label: 'Sponsors',
                to: '/'
            },
            {
                label: 'Add poll',
                to: '/'
            },
        ],
        [
            {
                label: 'News',
                to: '/'
            },
            {
                label: 'Gallery',
                to: '/'
            },
            {
                label: 'Archive',
                to: '/'
            },
        ],
        [
            {
                label: 'Terms of Use',
                to: '/'
            },
            {
                label: 'Privacy policy',
                to: '/'
            },
        ]
    ]

    const socialLinks = [
        {
            label: 'Instagram',
            icon: 'tabler:brand-instagram',
            to: '/'
        },
        {
            label: 'Facebook',
            icon: 'tabler:brand-facebook',
            to: '/'
        },
        {
            label: 'X',
            icon: 'tabler:brand-x',
            to: '/'
        },
    ]
</script>

<template>
    <div class="max-[600px]: text-sm bg-gradient-to-r from-[#00012D] to-[#03025f] text-white font-semibold py-20 px-20 space-y-40 min-[320]">
        <div class="md:flex flex-wrap md:justify-between space-y-10 md:space-y-0 gap-10">
            <div>
                <img class="h-20 w-20" src="/public/images/IDWS-logo.jpeg" alt="idws logo">
            </div>

            <div class="flex-wrap md:flex md:justify-between w-3/5 gap-10">
                <div v-for="links in footerLinks" class="space-y-2">
                    <div v-for="link in links" :key="link.label">
                        <NuxtLink :to="link.to">
                            {{ link.label }}
                        </NuxtLink>
                    </div>
                </div>
            </div>

            <div class="flex space-x-4">
                <div v-for="link in socialLinks" :key="link.label">
                    <NuxtLink :to="link.to">
                        <UIcon :name="link.icon" class="h-5 w-5"/>
                    </NuxtLink>
                </div>
            </div>
        </div>
        <div>
            <p>Copyright &copy; Innovation Driven Water Sustainability 2024, All rights reserved.</p>
        </div>
    </div>
</template>
