<script setup>
import { Loader } from '@googlemaps/js-api-loader';
    import {onMounted, ref } from 'vue';
    console.log("starting EditMap setup");
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


    const emit = defineEmits(["change"]);

    const loader = new Loader({
        apiKey: props.apiKey,
        version: "weekly",
    });
    
    const mapEl = ref(null);



    onMounted(async () => {
        const { LatLng } = await loader.importLibrary("core");
        const { Map } = await loader.importLibrary("maps");
        const { AdvancedMarkerElement } = await loader.importLibrary("marker");
        const { PlacesService } = await loader.importLibrary("places");

        const map = new Map(
            mapEl.value,
            {
                mapId: 'picker',
                mapTypeId: "hybrid",
                zoom: props.zoom,
            }
        );

        var marker = undefined;
        
        const createMarker = (position) => {
            const m = new AdvancedMarkerElement({
                position,
                map,
            });

            const el = m.content;
            el.classList.add("pin");
            el.style.opacity = '1';
            el.addEventListener("animationend", () => {
                el.classList.remove("pin-drop");
                el.style.opacity = '1';
            });
            m.addListener("click", (e) => {
                infoWindowContent();
                e.stop();
            });
            el.classList.add("pin-drop");
            return m;
        };
        
        if (props.lat !== undefined && props.lng !== undefined) {
            const latLng = new LatLng(props.lat, props.lng); 
            map.setCenter(latLng);          
            marker = createMarker(latLng);
        } else if (props.address !== undefined) {
            const placesService = new PlacesService(map);
            placesService.findPlaceFromQuery({query: props.address, fields: ["geometry.location"]}, (result, status) => {
                if (result === undefined) {
                    return;
                }
                if (result?.length === 0) {
                    return;
                }
                const r = result[0];
                
                map.setCenter(r.geometry.location);
            });
        }
        
        
        
        const click = (e) => {
            const { lat, lng } = e.latLng;
            if (marker === undefined) {
                marker = createMarker(e.latLng);
            } else {
                marker.position = e.latLng;
                const el = marker.content;
                el.classList.add("pin-drop");
            }
            emit("change", lat(), lng());
        };

        map.addListener("click", click);
    
    })
</script>

<template>
    <div class="container">
        <div ref="mapEl" class="google-map-el"></div>
    </div>
</template>

<style scoped>
.google-map-el, .container {
    height: 100%;
    width: 100%;
}


@keyframes pin-drop {
  0% {
    transform: translateY(-100px) scaleY(0.9);
    opacity: 0;
  }
  100% {
    transform: translateY(0px) scaleY(1);
    opacity: 1;
  }
}

.google-map-el :deep(.pin-drop) {
    animation: pin-drop 0.2s linear forwards 0s;
}
</style>