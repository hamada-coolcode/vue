<script setup lang="ts">
type LinkProps = {
    title: string
    to: string
    icon: string
}
interface Props {
    links: LinkProps[]
}
const props = defineProps<Props>()
</script>

<style scoped>
.router-link-active {
    background-color: black;
}
</style>

<template>
    <RouterLink
        v-for="link in links"
        :key="link.title"
        :to="link.to"
        class="flex items-center gap-3 px-4 py-2 mx-2 transition-colors rounded-lg hover:text-primary justify-center lg:justify-normal text-muted-foreground"
    >
        <iconify-icon :icon="link.icon"></iconify-icon>
        <span class="hidden lg:block text-nowrap">{{ link.title }}</span>
    </RouterLink>
</template>
