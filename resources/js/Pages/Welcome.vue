<script setup>
defineProps({
    laravelVersion: String,
    phpVersion: String,
});
</script>

<template>
    <div class="min-h-screen flex items-center justify-center bg-gray-50 text-gray-900">
        <div class="max-w-xl text-center space-y-4 px-6">
            <h1 class="text-3xl font-bold">Laravel + Vue 3 + Inertia</h1>
            <p class="text-gray-600">
                Running Laravel {{ laravelVersion }} with PHP {{ phpVersion }}.
            </p>
            <p class="text-sm text-gray-400">
                Edit <code>resources/js/Pages/Welcome.vue</code> to get started.
            </p>
        </div>
    </div>
</template>
