### Overview

Guide-ify is an app submission for the [Cloudflare AI Challenge](https://dev.to/devteam/join-us-for-the-cloudflare-ai-challenge-3000-in-prizes-5f99). It makes a visual guide for a given life skill that a user would like to learn more about:


![Screen Shot 2024-04-09 at 7 23 19 AM](https://github.com/MrAlexLau/cloudflare-playground/assets/75554/0d67bb22-90a8-4288-b448-097560828e12)

The app does this with a 3-step process:
1. Make a request to research more about the given skill.
2. Summarize these findings.
3. Create a corresponding visual aid for each step in the summary.

These steps each correspond to a different Cloudflare Worker and AI model. You can preview the app [here](https://cloudflare-playground-bn1.pages.dev/
).


### Running the App
This app is made with [sveltekit](https://kit.svelte.dev/) and its installation steps are similar to any other sveltekit project:

```
npm install
npm run dev
```

### Using Custom Cloudflare Workers
By default, this project is making requests against Cloudflare Workers that I've created (eg - all of the urls that end in `mralexlau.workers.dev`). You're welcome to use those, however you can also create your own Workers with custom code if you'd like. See [the workers documentation](workers.md) for more details.

### Deploying to Cloudflare
By default, this app is configured to be deployed to Cloudflare Pages. If you'd like to deploy your own fork of this app, please follow [these directions](https://developers.cloudflare.com/pages/framework-guides/deploy-a-svelte-site/).

Once you've completed these steps, you can deploy your application with `npm run deploy`

