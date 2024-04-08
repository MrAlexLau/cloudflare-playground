<script>
  export let title;
  export let url;
</script>

<div class="m-auto mt-10 bg-gray-50 text-align-center sm:w-[600px] rounded-md p-6 flex flex-col border-gray-200 border-2 border-solid drop-shadow-xl">
  <div class="grow text-xl text-gray-600">
    {title}
  </div>

  <div class="flex flex-col justify-center sm:flex-row mt-8 sm:mt-6">
    <div class="sm:pr-6">
      <img src="{url}" width="600px">
    </div>
  </div>
</div>
