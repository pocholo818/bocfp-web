<template>
    <Bar :data="data" :options="options" v-if="loaded" />
</template>
  
<script lang="ts">
import { defineComponent } from 'vue';
import { Bar } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

export default defineComponent({
    name: 'BarChart',
    components: { Bar },
    props: {
        data: {
            type: Object,
            required: true
        },
        options: {
            type: Object,
        }
    },
    data() {
        return {
            loaded: true,
        }
    },
    watch: {
        data: {
            deep: true,
            handler() {
                // refresh upon data changes
                this.loaded = false
                setTimeout(() => this.loaded = true, 0)
            }
        }
    }
})
</script>