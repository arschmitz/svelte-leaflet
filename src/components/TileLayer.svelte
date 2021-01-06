<script>
    import {createEventDispatcher, getContext, onDestroy, onMount} from 'svelte';
    import EventBridge from '../lib/EventBridge';

    const {getMap} = getContext('L');

    export let url;
    export let opacity = 1.0;
    export let zIndex = 1;
    export let options = {};
    export let events = [];

    let L;
    let tileLayer;

    const dispatch = createEventDispatcher();
    let eventBridge;

    $: {
        if (L) {
            if (!tileLayer) {
                tileLayer = L.tileLayer(url, options).addTo(getMap());
                eventBridge = new EventBridge(tileLayer, dispatch, events);
            }
            tileLayer.setUrl(url);
            tileLayer.setOpacity(opacity);
            tileLayer.setZIndex(zIndex);
        }
    }

    onMount(async () => {
        L = await import('leaflet');
    });

    onDestroy(() => {
        if (eventBridge) {
            eventBridge.unregister();
            tileLayer.removeFrom(getMap());
        }
    });

    export function getTileLayer() {
        return tileLayer;
    }
</script>
