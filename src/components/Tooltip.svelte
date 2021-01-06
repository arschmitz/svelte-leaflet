<script>
    import {createEventDispatcher, getContext, onDestroy, onMount} from 'svelte';
    import EventBridge from '../lib/EventBridge';

    const {getLayer} = getContext('L.Layer');

    export let events = [];

    let L;
    let tooltip;
    let element;

    const dispatch = createEventDispatcher();
    let eventBridge;

    $: {
        if (L) {
            if (!tooltip) {
                tooltip = L.tooltip();
                eventBridge = new EventBridge(tooltip, dispatch, events);
                getLayer().bindTooltip(tooltip);
            }
            tooltip.setContent(element);
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

    export function getTooltip() {
        return tooltip;
    }
</script>

<div style="display: none;">
    <div bind:this={element}>
        <slot/>
    </div>
</div>