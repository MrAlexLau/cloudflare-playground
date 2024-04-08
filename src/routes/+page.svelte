<script>
  let prompt = "Public speaking";
  let status = "";
  let responseBody;
  let skillDetailsResponse;
  let summaryResponse;
  let imageSteps = [];
  let summaryItem;

  function requestOptions(opts = {}) {
    return {
      method: "POST",
      body: JSON.stringify({ ...opts })
    }
  }

  async function submitPrompt() {
    skillDetailsResponse = null;
    summaryResponse = null;

    status = "Finding out more about your topic...";

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

    summaryItem = summaryResponse[0].summary;

    status = "Visualizing your topic...";
    for(let i = 0; i < summaryItem.length; i++) {
      const sentence = summaryItem.split(".")[i];

      const imageResponse = await fetch(
        "https://text-to-image.mralexlau.workers.dev",
        requestOptions({ prompt: sentence })
      ).then((response) => {
          return response.blob();
      }).then(function(blob) {
        const objectURL = URL.createObjectURL(blob);
        console.log('objectURL', objectURL)
        return objectURL;
      });

      imageSteps = [...imageSteps, { sentence, imageResponse }]
    };

  }
</script>

<h1 class="text-3xl font-bold underline">
  Tutorial Visualizer
</h1>

<div>
  <div>
    What's a skill you would like to learn how to do or improve upon?
    <input class="shadow appearance-none border rounded py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" bind:value={prompt} /> <br />

    <a href="" class="button inline-block bg-green-400 border-green-600 border-2 border-solid hover:bg-accent-medium-blue text-white font-bold py-2 px-4 rounded focus:outline-none cursor-pointer" on:click={submitPrompt}>
      Go
    </a>
  </div>

  <div>
    {status || ""}
  </div>

  {#if skillDetailsResponse && skillDetailsResponse.length > 0}
    <!-- Remove `display: none` to see more details -->
    <div style="display: none">
      Here's a lot of details for learning about {prompt}:
      {skillDetailsResponse[0].response || ""}
    </div>
  {/if}

  {#if summaryResponse && summaryResponse.length > 0}
    <!-- Remove `display: none` to see more details -->
    <div style="display: none">
      Here's a summary for learning about {prompt}:
      {summaryItem || ""}
    </div>
  {/if}

  {#each imageSteps as imageStep}
    {imageStep.sentence}
    <img src="{imageStep.imageResponse}" width="200px">
  {/each}
</div>

<style>
</style>

