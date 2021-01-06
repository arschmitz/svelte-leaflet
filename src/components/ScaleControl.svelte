<script>
    import {getContext, onDestroy, onMount} from 'svelte';

    const {getMap} = getContext('L');

    export let position = 'topright';
    export let options = {};

    let L;
    let scaleControl;

    $: {
        if (L) {
            if (!scaleControl) {
                scaleControl = L.control.scale(options).addTo(getMap());
            }
            scaleControl.setPosition(position);
        }
    }

    onMount(async () => {
        L = await import('leaflet');
    });

    onDestroy(() => {
        if (scaleControl) {
            scaleControl.removeFrom(getMap());
        }
    });

    export function getScaleControl() {
        return scaleControl;
    }
</script>
