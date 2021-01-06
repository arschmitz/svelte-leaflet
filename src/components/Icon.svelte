<script>
    import {getContext, onMount} from 'svelte';

    const {getMarker} = getContext('L.Marker');

    export let options = {};

    let icon;
    let L;

    $: {
        if (L) {
            icon = L.icon(options);
            getMarker().setIcon(icon);
        }
    }

    onMount(async () => {
        L = await import('leaflet');
    });

    export function getIcon() {
        return icon;
    }
</script>
