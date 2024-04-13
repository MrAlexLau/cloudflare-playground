<script>
  import ComicPanel from '$lib/ComicPanel.svelte';
  import Status from '$lib/Status.svelte';

  let prompt = "Public speaking";
  let status = "";
  let responseBody;
  let skillDetailsResponse;
  let summaryResponse;
  let imageSteps = [];
  let summaryItem;
  let isLoading;
  let isDone;
  let hideSettings = true;

  let selectedTextModel = "@cf/tiiuae/falcon-7b-instruct";

  const textModels = [
    "@cf/meta/llama-2-7b-chat-fp16",
    "@cf/meta/llama-2-7b-chat-int8",
    "@cf/mistral/mistral-7b-instruct-v0.1",
    "@cf/tiiuae/falcon-7b-instruct",
    "@hf/google/gemma-7b-it",
    "@hf/nousresearch/hermes-2-pro-mistral-7b",
  ]

  function toggleSettings() {
    hideSettings = !hideSettings;
  }

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

  function filterToMinLength(items, minLength) {
    return items.filter((str) => str.trim().length > minLength);
  }

  async function summarizeSteps(text) {
    // Attempt to parse out a text response that contains a numeric list in it.
    let steps = text.split(/\d\./);
    if (text.indexOf("1.") >= 0 && filterToMinLength(steps, 0).length > 1) {
      if (text.indexOf("1.") === 0) {
        return filterToMinLength(steps, 10)
      }

      // Pluck off the first item since it's usually something like:
      // "Sure, I'd love to tell you about public speaking... etc."
      const [first, ...restOfSteps] = steps;
      return filterToMinLength(restOfSteps, 10);
    }

    const response = await fetch(
      "https://text-summarizer-2.mralexlau.workers.dev",
      requestOptions({ prompt: text })
    ).then((response) => {
      return response.json();
    });
    const summaryBySentence = response[0].summary.split(".");

    return filterToMinLength(summaryBySentence, 0);
  }

  async function submitPrompt() {
    resetState();

    status = "Researching your topic...";
    skillDetailsResponse = await fetch(
      "https://worker-restless-cake-bbb8.mralexlau.workers.dev",
      requestOptions({ prompt, textModel: selectedTextModel })
    ).then((response) => {
      return response.json();
    });

    status = "Summarizing these findings...";
    const steps = await summarizeSteps(skillDetailsResponse[0].response)

    for(let i = 0; i < steps.length; i++) {
      status = `Visualizing your topic... (${i + 1} of ${steps.length})`;
      const sentence = steps[i];

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

<div class="text-center bg-teal-600 p-12">
  <div class="text-4xl font-bold text-center text-white">
    Guide-ify
  </div>

  <div class="mt-4">🖼️ 📘 <span class="text-white">Create a visual guide for any skill!</span> 📘 🖼️</div>
</div>



<div class="mt-4 text-right pr-2">
  <a
    href=""
    class="mt-6 underline cursor-pointer text-gray-700"
    on:click={toggleSettings}
  >Settings</a> ⚙️
</div>

<div id="settings-container" class="text-right p-2" class:hidden={hideSettings}>
  <form>
    Text model:
    <select
      bind:value={selectedTextModel}
    >
      {#each textModels as textModel}
        <option value={textModel}>
          {textModel}
        </option>
      {/each}
    </select>
  </form>

  <div class="p-2 pt-10">
    <hr />
  </div>
</div>


<div class="">
  <div class="text-center mt-6">
    What's a life skill you would like to learn how to do or improve upon?

    <div>
      <input class="shadow appearance-none border rounded py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline " bind:value={prompt} />

      <a href="" class="button inline-block bg-teal-600 border-green-600 border-solid hover:bg-accent-medium-blue text-white font-bold py-2 px-4 rounded focus:outline-none cursor-pointer" on:click={submitPrompt}>
        Go
      </a>
    </div>
  </div>

  {#if status && status.length > 0 && isLoading}
    <div class="flex justify-center items-center mt-10">
      <Status status={status} />
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

