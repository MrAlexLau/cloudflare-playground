<script>
  import Loader from '$lib/Loader.svelte';
  import Checkmark from '$lib/Checkmark.svelte';

  export let status;
  export let isLoading;
  export let isDone;
</script>

{#if isLoading}<Loader />{/if}{#if isDone}<Checkmark />{/if} <span class="p-4">{status || ""}</span>
