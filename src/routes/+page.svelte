<script>
  let prompt = "Public speaking";
  let status = "";
  let responseBody;
  let skillDetailsResponse;
  let summaryResponse;

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

    // responseBody = skillDetailsResponse[0].response;
    status = "Summarizing these findings...";
    summaryResponse = await fetch(
      "https://text-summarizer-2.mralexlau.workers.dev",
      requestOptions({ prompt: skillDetailsResponse[0].response })
    ).then((response) => {
      return response.json();
    });
  }
</script>

<h2>Concept Visualizer</h2>

<div>
  <div>
    What's a skill you would like to learn how to do or improve upon?
    <input bind:value={prompt} /> <br />

    <a href="#" on:click={submitPrompt}>
      Go
    </a>
  </div>

  <div>
    {status || ""}
  </div>

  {#if skillDetailsResponse && skillDetailsResponse.length > 0}
    <div>
      Here's a lot of details for learning about {prompt}:
      {skillDetailsResponse[0].response || ""}
    </div>
  {/if}

  {#if summaryResponse && summaryResponse.length > 0}
    <div>
      Here's a summary for learning about {prompt}:
      {summaryResponse[0].summary || ""}
    </div>
  {/if}
</div>

<style>
</style>

