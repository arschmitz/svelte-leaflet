<script>
    import {createEventDispatcher, getContext, onDestroy, onMount} from 'svelte';
    import EventBridge from '../lib/EventBridge';

    const {getLayer} = getContext('L.Layer');

    export let events = [];

    let L;
    let popup;
    let element;

    const dispatch = createEventDispatcher();
    let eventBridge;

    $: {
        if (L) {
            if (!popup) {
                popup = L.popup();
                eventBridge = new EventBridge(popup, dispatch, events);
                getLayer().bindPopup(popup);
            }
            popup.setContent(element);
        }
    }

    onMount(async () => {
        L = await import('leaflet');
    });

    onDestroy(() => {
        if (eventBridge) {
            eventBridge.unregister();
        }
    });

    export function getPopup() {
        return popup;
    }
</script>

<div style="display: none;">
    <div bind:this={element}>
        <slot/>
    </div>
</div>