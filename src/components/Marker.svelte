<script>
    import {createEventDispatcher, getContext, onDestroy, onMount, setContext} from 'svelte';
    import EventBridge from '../lib/EventBridge';

    export let latLng;
    export let zIndexOffset = 0;
    export let icon;
    export let opacity = 1.0;
    export let options = {};
    export let events = [];

    export let rotationAngle = 0;
    export let rotationOrigin = 'center bottom';

    let marker;
    let L;

    setContext('L.Layer', {
        getLayer: () => marker,
    });
    setContext('L.Marker', {
        getMarker: () => marker,
    });

    const dispatch = createEventDispatcher();
    let eventBridge;

    const {getMap} = getContext('L');

    $: {
        if (L) {
            if (!marker) {
                marker = L.marker(latLng, options).addTo(getMap());
                eventBridge = new EventBridge(marker, dispatch, events);
            }
            marker.setLatLng(latLng);
            marker.setZIndexOffset(zIndexOffset);
            marker.setIcon(icon);
            marker.setOpacity(opacity);

            marker.setRotationAngle(rotationAngle);
            marker.setRotationOrigin(rotationOrigin);
        }
    }

    onMount(async () => {
        L = await import('leaflet');

        if (!icon) {
            icon = L.icon({
                iconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon.png',
                iconRetinaUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon-2x.png',
                shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-shadow.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                tooltipAnchor: [16, -28],
                shadowSize: [41, 41],
            });
        }
    });

    onDestroy(() => {
        if (eventBridge) {
            eventBridge.unregister();
            marker.removeFrom(getMap());
        }
    });

    export function getMarker() {
        return marker;
    }
</script>

<div>
    {#if marker}
        <slot/>
    {/if}
</div>
