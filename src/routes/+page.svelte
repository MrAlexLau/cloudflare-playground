<script>
  let prompt = "Eclipses";
  let responseBody;

  function requestOptions(opts = {}) {
    return {
      method: "POST",
      body: JSON.stringify({ ...opts })
    }
  }

  async function submitPrompt() {
    responseBody = "Summarizing your topic...";

    const fullBody = await fetch(
      "https://worker-restless-cake-bbb8.mralexlau.workers.dev",
      requestOptions({ prompt })
    ).then((response) => {
      return response.json();
    });

    responseBody = fullBody[0].response;
  }
</script>

<h2>Concept Visualizer</h2>

<div>
  <div>
    What's a concept you would like to learn more about?
    <input bind:value={prompt} /> <br />

    <a href="#" on:click={submitPrompt}>
      Go
    </a>
  </div>

  <div>
    {responseBody || ""}
  </div>
</div>

<style>
</style>

