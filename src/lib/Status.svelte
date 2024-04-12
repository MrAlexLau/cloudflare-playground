<script>
  import Loader from '$lib/Loader.svelte';

  export let status;
</script>

<Loader /><span class="p-4">{status || ""}</span>
