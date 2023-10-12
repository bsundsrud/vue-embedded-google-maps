<script setup>
    import { computed } from 'vue';
    import ViewMap from './ViewMap.vue';
    import EditMap from './EditMap.vue';

    const API_KEY = "API_KEY";
    
    const props = defineProps({
        lat: {
            type: [Number],
            required: false,
        },
        lng: {
            type: [Number],
            required: false,
        },
        address: {
            type: [String],
            required: false,
        },
        zoom: {
            type: [Number],
            required: false,
            default: 17
        },
        edit: {
            type: [Boolean],
            required: false,
            default: false
        },
    });

    const showMap = computed(() => {
        if (props.lat && props.lng) {
            return true;
        } else if (props.address) {
            return true;
        } else {
            return false;
        }
    });
//
</script>

<template>
    <div class="container" v-if="showMap">
        <EditMap v-if="edit" @change="(lat, lng) => $emit('change', lat, lng)"  v-bind="props" :api-key="API_KEY"/>
        <ViewMap v-else v-bind="props" :api-key="API_KEY"/>
    </div>
</template>

<style scoped>
.container {
    width: 100%;
    height: 100%;
}

</style>