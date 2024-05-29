<script setup lang="ts">
defineProps({
    image: String,
    name: String,
    species: String,
    status: String,
    location: String,
    episode: String
})
</script>

<template>
    <div class="card">
        <img :src="image" alt="Character">
        <div class="card__content">
            <section>
                <h1 class="text-big">{{ name }}</h1>
                <span class="status text-small">
                    <span class="icon" :class="status"></span>{{ status }} - {{ species }}
                </span>
            </section>
            <section>
                <div class="text-grey text-small">Last known location:</div>
                <div class="location text-normal">{{ location }}</div>
            </section>
            <section>
                <div class="text-grey text-small">First seen in:</div>
                <div class="location text-normal">{{ episode }}</div>
            </section>
        </div>
    </div>
</template>
