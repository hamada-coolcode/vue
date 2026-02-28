<template>
    <h1>Task {{ route.params?.id }}</h1>
</template>

<style scoped></style>

<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
</script>
