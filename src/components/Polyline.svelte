<script>
    import {createEventDispatcher, getContext, onDestroy, onMount, setContext} from 'svelte';
    import EventBridge from '../lib/EventBridge';

    export let latLngs;
    export let color = '#3388ff';
    export let weight = 3;
    export let opacity = 1.0;
    export let lineCap = 'round';
    export let lineJoin = 'round';
    export let dashArray = null;
    export let dashOffset = null;
    export let options = {};
    export let events = [];

    let L;
    let polyline;

    setContext('L.Layer', {
        getLayer: () => polyline,
    });

    const dispatch = createEventDispatcher();
    let eventBridge;

    const {getMap} = getContext('L');

    $: {
        if (L) {
            if (!polyline) {
                polyline = L.polyline(latLngs, options).addTo(getMap());
                eventBridge = new EventBridge(polyline, dispatch, events);
            }
            polyline.setLatLngs(latLngs);
            polyline.setStyle({
                color: color,
                weight: weight,
                opacity: opacity,
                lineCap: lineCap,
                lineJoin: lineJoin,
                dashArray: dashArray,
                dashOffset: dashOffset,
            });
        }
    }

    onMount(async () => {
        L = await import('leaflet');
    });

    onDestroy(() => {
        if( eventBridge ) {
            eventBridge.unregister();
            polyline.removeFrom(getMap());
        }
    });

    export function getPolyline() {
        return polyline;
    }
</script>

<div>
    {#if polyline}
        <slot/>
    {/if}
</div>
