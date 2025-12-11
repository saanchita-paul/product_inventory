<script setup>
import { Link } from '@inertiajs/vue3';

defineProps({
    links: Array,
});
</script>

<template>
    <div v-if="links.length > 3" class="flex flex-wrap justify-center -mb-1 gap-1">
        <template v-for="(link, key) in links" :key="key">
            <div
                v-if="link.url === null"
                class="mr-1 mb-1 px-4 py-2 text-sm leading-4 text-gray-400 border rounded"
                v-html="link.label"
            />
            <Link
                v-else
                class="mr-1 mb-1 px-4 py-2 text-sm leading-4 border rounded hover:bg-gray-100 focus:border-indigo-500 focus:text-indigo-500"
                :class="{ 'bg-gray-800 text-white hover:bg-gray-700': link.active, 'bg-white': !link.active }"
                :href="link.url"
                v-html="link.label"
            />
        </template>
    </div>
</template>
