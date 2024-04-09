<script>
  import ComicPanel from '$lib/ComicPanel.svelte';
  import Status from '$lib/Status.svelte';

  let prompt = "Public speaking";
  let status = "";
  let responseBody;
  let skillDetailsResponse;
  let summaryResponse;
  let imageSteps = [
    // { sentence: "foo bar baz e-block bg-green-400 border-green-600 border-2 border-solid hover:bg-accent-medium-blue text-white font-bold py-2 px-4 rounded focus:outline-none cursor", imageResponse: "https://www.akc.org/wp-content/uploads/2017/11/Pembroke-Welsh-Corgi-standing-outdoors-in-the-fall.jpg"},

    // { sentence: "foo", imageResponse: "https://www.akc.org/wp-content/uploads/2017/11/Pembroke-Welsh-Corgi-standing-outdoors-in-the-fall.jpg"},

    // { sentence: "foo", imageResponse: "https://www.akc.org/wp-content/uploads/2017/11/Pembroke-Welsh-Corgi-standing-outdoors-in-the-fall.jpg"}

  ];
  let summaryItem;
  let isLoading;
  let isDone;

  function requestOptions(opts = {}) {
    return {
      method: "POST",
      body: JSON.stringify({ ...opts })
    }
  }

  function resetState() {
    skillDetailsResponse = null;
    summaryResponse = null;
    isLoading = true;
    isDone = false;
    imageSteps = [];
  }

  async function submitPrompt() {
    resetState();

    status = "Researching your topic...";
    skillDetailsResponse = await fetch(
      "https://worker-restless-cake-bbb8.mralexlau.workers.dev",
      requestOptions({ prompt })
    ).then((response) => {
      return response.json();
    });

    status = "Summarizing these findings...";
    summaryResponse = await fetch(
      "https://text-summarizer-2.mralexlau.workers.dev",
      requestOptions({ prompt: skillDetailsResponse[0].response })
    ).then((response) => {
      return response.json();
    });

    summaryItem = summaryResponse[0].summary.split(".").filter((str) => str.trim().length > 0);
    for(let i = 0; i < summaryItem.length; i++) {
      status = `Visualizing your topic... (${i + 1} of ${summaryItem.length})`;
      const sentence = summaryItem[i];

      const imageResponse = await fetch(
        "https://text-to-image.mralexlau.workers.dev",
        requestOptions({ prompt: sentence })
      ).then((response) => {
          return response.blob();
      }).then(function(blob) {
        const objectURL = URL.createObjectURL(blob);
        return objectURL;
      });

      imageSteps = [...imageSteps, { sentence, imageResponse }]
    };

    status = "Done! See your tutorial below."

    isLoading = false;
    isDone = true;
  }
</script>

<h1 class="text-3xl font-bold underline text-center mt-10">
  Tutorial Visualizer
</h1>

<div class="">
  <div class="text-center mt-10">
    What's a skill you would like to learn how to do or improve upon?

    <div>
      <input class="shadow appearance-none border rounded py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline " bind:value={prompt} />

      <a href="" class="button inline-block bg-green-400 border-green-600 border-solid hover:bg-accent-medium-blue text-white font-bold py-2 px-4 rounded focus:outline-none cursor-pointer" on:click={submitPrompt}>
        Go
      </a>
    </div>
  </div>


  {#if status && status.length > 0}
    <div class="flex justify-center items-center mt-10">
      <Status status={status} isLoading={isLoading} isDone={isDone} />
    </div>
  {/if}

  {#if skillDetailsResponse && skillDetailsResponse.length > 0}
    <!-- Remove `display: none` to see more output details -->
    <div style="display: none">
      Here's a lot of details for learning about {prompt}:
      {skillDetailsResponse[0].response || ""}
    </div>
  {/if}

  {#if summaryResponse && summaryResponse.length > 0}
    <!-- Remove `display: none` to see more output details -->
    <div style="display: none">
      Here's a summary for learning about {prompt}:
      {summaryItem || ""}
    </div>
  {/if}

  <div class="w-full">
    {#each imageSteps as imageStep}
      <ComicPanel title={imageStep.sentence} url={imageStep.imageResponse} />
    {/each}
  </div>
</div>

