<script setup>
import { Head, Link } from '@inertiajs/vue3';

defineProps({
    canLogin: {
        type: Boolean,
    },
    canRegister: {
        type: Boolean,
    },
    laravelVersion: {
        type: String,
        required: true,
    },
    phpVersion: {
        type: String,
        required: true,
    },
});
</script>

<template>
    <Head title="Welcome"/>

    <div
        class="bg-gray-50 text-black/70 dark:bg-zinc-950 dark:text-white/70 min-h-screen flex flex-col selection:bg-[#FF2D20] selection:text-white">

        <img
            class="absolute -left-20 top-0 max-w-[877px] opacity-20 dark:opacity-10 pointer-events-none"
            src="https://laravel.com/assets/img/welcome/background.svg"
            alt="Background"
        />

        <div class="relative flex flex-1 flex-col items-center justify-center">
            <div class="w-full max-w-2xl px-6 lg:max-w-7xl flex flex-col items-center text-center">

                <div class="mb-8 flex justify-center">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                         stroke="currentColor" class="w-20 h-20 text-[#FF2D20]">
                        <path stroke-linecap="round" stroke-linejoin="round"
                              d="M20.25 7.5l-.625 10.632a2.25 2.25 0 01-2.247 2.118H6.622a2.25 2.25 0 01-2.247-2.118L3.75 7.5M10 11.25h4M3.375 7.5h17.25c.621 0 1.125-.504 1.125-1.125v-1.5c0-.621-.504-1.125-1.125-1.125H3.375c-.621 0-1.125.504-1.125 1.125v1.5c0 .621.504 1.125 1.125 1.125z"/>
                    </svg>
                </div>

                <h1 class="text-4xl font-bold tracking-tight text-black dark:text-white sm:text-6xl">
                    Product Inventory System
                </h1>

                <p class="mt-6 text-lg leading-8">
                    Efficiently manage your stock, track products, and streamline your inventory operations in one
                    secure place.
                </p>

                <div class="mt-10 flex items-center justify-center gap-x-6" v-if="canLogin">

                    <template v-if="$page.props.auth.user">
                        <Link
                            :href="route('dashboard')"
                            class="rounded-md bg-[#FF2D20] px-6 py-3 text-lg font-semibold text-white shadow-sm hover:bg-[#FF2D20]/80 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#FF2D20]"
                        >
                            Go to Dashboard <span aria-hidden="true">→</span>
                        </Link>
                    </template>

                    <template v-else>
                        <Link
                            :href="route('login')"
                            class="rounded-md bg-[#FF2D20] px-6 py-3 text-lg font-semibold text-white shadow-sm hover:bg-[#FF2D20]/80 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#FF2D20]"
                        >
                            Log in
                        </Link>

                        <Link
                            v-if="canRegister"
                            :href="route('register')"
                            class="text-lg font-semibold leading-6 text-black dark:text-white hover:underline"
                        >
                            Register <span aria-hidden="true">→</span>
                        </Link>
                    </template>

                </div>
            </div>
        </div>

        <footer class="py-10 text-center text-sm opacity-70">
            Laravel v{{ laravelVersion }} (PHP v{{ phpVersion }})
        </footer>
    </div>
</template>
