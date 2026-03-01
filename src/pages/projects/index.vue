<template>
    <div>
        <h1>Projects Page</h1>
        <DataTable :columns="columns" :data="projectsData" />
    </div>
</template>

<style scoped></style>

<script setup lang="ts">
import { h, onMounted, ref } from 'vue'
import type { ColumnDef } from '@tanstack/vue-table'
import type { Tables } from '../../../database/supabase'
import DataTable from '@/components/ui/data-table/DataTable.vue'
import { supabase } from '@/lib/supabaseClient'

const projectsData = ref<Tables<'projects'>[]>([])

onMounted(async () => {
    const { data, error } = await supabase.from('projects').select()
    if (error) {
        console.error('Error fetching projects:', error)
        return
    }
    projectsData.value = data || []
})

const columns: ColumnDef<Tables<'projects'>>[] = [
    {
        accessorKey: 'name',
        header: () => 'Name',
        cell: ({ row }) => {
            return h(
                'button',
                {
                    class: 'text-left hover:underline text-blue-500 dark:text-blue-400 font-medium',
                    onClick: () => {
                        window.location.href = `/projects/${row.original.id}`
                    },
                },
                row.getValue('name') as string,
            )
        },
    },
    {
        accessorKey: 'slug',
        header: () => 'Slug',
    },
    {
        accessorKey: 'status',
        header: () => 'Status',
        cell: ({ row }) => {
            const status = row.getValue('status') as string
            const statusColors: Record<string, string> = {
                'in-progress': 'bg-yellow-500/10 text-yellow-600 dark:text-yellow-400',
                completed: 'bg-green-500/10 text-green-600 dark:text-green-400',
            }
            const colorClass = statusColors[status] || 'bg-muted text-muted-foreground'
            return h(
                'span',
                { class: `px-2 py-1 rounded-full text-xs font-medium ${colorClass}` },
                status,
            )
        },
    },
    {
        id: 'collaborators',
        accessorFn: (row: Tables<'projects'>) => (row as any).collaborators,
        header: () => 'Collaborators',
        cell: ({ row }) => {
            const collaborators = (row.original as any).collaborators as string[]
            if (!collaborators || collaborators.length === 0) {
                return h('span', { class: 'text-muted-foreground/50' }, 'None')
            }
            return h(
                'div',
                { class: 'flex flex-wrap gap-1' },
                collaborators.map((collab) =>
                    h(
                        'span',
                        {
                            class: 'px-2 py-1 bg-blue-500/10 text-blue-600 dark:text-blue-400 rounded-full text-xs',
                        },
                        collab,
                    ),
                ),
            )
        },
    },
]
</script>
