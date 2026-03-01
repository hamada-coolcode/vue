<template>
    <div v-if="loading" class="text-center py-8">Loading...</div>
    <div v-else-if="error" class="text-red-500 py-8">{{ error }}</div>
    <div v-else-if="project" class="space-y-6">
        <div class="flex items-center gap-4">
            <RouterLink to="/projects" class="text-blue-500 hover:underline"
                >← Back to Projects</RouterLink
            >
        </div>

        <div class="border rounded-lg p-6 bg-card shadow-sm">
            <h1 class="text-2xl font-bold mb-4">{{ project.name }}</h1>

            <div class="grid grid-cols-2 gap-4">
                <div>
                    <h3 class="text-sm font-medium text-muted-foreground">Slug</h3>
                    <p class="mt-1">{{ project.slug }}</p>
                </div>

                <div>
                    <h3 class="text-sm font-medium text-muted-foreground">Status</h3>
                    <span
                        :class="[
                            'mt-1 inline-block px-3 py-1 rounded-full text-xs font-medium',
                            project.status === 'in-progress'
                                ? 'bg-yellow-500/10 text-yellow-600 dark:text-yellow-400'
                                : 'bg-green-500/10 text-green-600 dark:text-green-400',
                        ]"
                    >
                        {{ project.status }}
                    </span>
                </div>

                <div>
                    <h3 class="text-sm font-medium text-muted-foreground">Created At</h3>
                    <p class="mt-1">{{ new Date(project.created_at).toLocaleDateString() }}</p>
                </div>

                <div>
                    <h3 class="text-sm font-medium text-muted-foreground">Collaborators</h3>
                    <div class="mt-1 flex flex-wrap gap-1">
                        <template v-if="project.collaborators && project.collaborators.length > 0">
                            <span
                                v-for="collab in project.collaborators"
                                :key="collab"
                                class="px-2 py-1 bg-blue-500/10 text-blue-600 dark:text-blue-400 rounded-full text-xs"
                            >
                                {{ collab }}
                            </span>
                        </template>
                        <span v-else class="text-muted-foreground/50">None</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped></style>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'
import { supabase } from '@/lib/supabaseClient'
import type { Tables } from '../../../database/supabase'

const route = useRoute()
const project = ref<Tables<'projects'> | null>(null)
const loading = ref(true)
const error = ref<string | null>(null)

onMounted(async () => {
    const id = route.params.id
    const { data, error: fetchError } = await supabase
        .from('projects')
        .select()
        .eq('id', id)
        .single()

    if (fetchError) {
        error.value = fetchError.message
        loading.value = false
        return
    }

    project.value = data
    loading.value = false
})
</script>
