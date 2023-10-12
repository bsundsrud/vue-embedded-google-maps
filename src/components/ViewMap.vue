<script setup>
    import {computed} from 'vue';
    console.log("starting ViewMap setup");
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
        apiKey: {
            type: [String],
            required: true,
        }
    });
    const mapEmbedUrl = computed(() => {
        var params;
        if (props.lat && props.lng) {
            params = `&q=${props.lat},${props.lng}&zoom=${props.zoom}`;
        } else if (props.address) {
            params = `&q=${encodeURIComponent(props.address)}&zoom=${props.zoom}`;
        } else {
            params = `&center=0,0&zoom=1`;
        }
        return `https://www.google.com/maps/embed/v1/place?key=${props.apiKey}&maptype=satellite${params}`;
    });
</script>

<template>
    <div class="embed-map-el">
        <iframe
            style="border:0"
            loading="lazy"
            allowfullscreen
            referrerpolicy="no-referrer-when-downgrade"
            :src="mapEmbedUrl">
        </iframe>
    </div>
</template>

<style scoped>
.embed-map-el, .embed-map-el iframe {
    height: 100%;
    width: 100%;
}

</style>