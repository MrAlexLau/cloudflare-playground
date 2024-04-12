See the [Cloudflare documentation](https://developers.cloudflare.com/workers-ai/get-started/workers-wrangler/) for how you can create your own Workers.

# Text-to-Text Worker
```
import { Ai } from './vendor/@cloudflare/ai.js';

export default {
  async fetch(request, env) {
    const tasks = [];
    const ai = new Ai(env.AI);
    const corsHeaders = {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET,HEAD,POST,OPTIONS',
        'Access-Control-Max-Age': '86400', // optional
        'Access-Control-Allow-Headers': 'Content-Type' ,
        'Access-Control-Request-Headers': 'Access-Control-Request-Headers',
    };

    if (
      request.headers.get("Origin") !== null &&
      request.headers.get("Access-Control-Request-Method") !== null &&
      request.headers.get("Access-Control-Request-Headers") !== null
    ) {
      // Handle CORS preflight requests.
      return new Response(null, {
        headers: {
          ...corsHeaders,
          "Access-Control-Allow-Headers": request.headers.get(
            "Access-Control-Request-Headers"
          ),
        },
      });
    }

    const body = await request.json();

    const detailedPrompt = `Tell me everything there is to know when learning about ${body.prompt}. Please provide me with a list of actionable steps I can take and be as detailed as possible with these actions.`
    const params = { prompt: detailedPrompt }

    const modelName = body?.textModel?.length > 0 ? body.textModel : '@cf/mistral/mistral-7b-instruct-v0.1';
    let response = await ai.run(modelName, params);
    tasks.push(response);

    return Response.json(tasks, { headers: corsHeaders});
  }
};
```

# Summarization Worker
```
import { Ai } from './vendor/@cloudflare/ai.js';

export default {
  async fetch(request, env) {
    const tasks = [];
    const ai = new Ai(env.AI);
    const corsHeaders = {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET,HEAD,POST,OPTIONS',
        'Access-Control-Max-Age': '86400', // optional
        'Access-Control-Allow-Headers': 'Content-Type' ,
        'Access-Control-Request-Headers': 'Access-Control-Request-Headers',
    };

    if (
      request.headers.get("Origin") !== null &&
      request.headers.get("Access-Control-Request-Method") !== null &&
      request.headers.get("Access-Control-Request-Headers") !== null
    ) {
      // Handle CORS preflight requests.
      return new Response(null, {
        headers: {
          ...corsHeaders,
          "Access-Control-Allow-Headers": request.headers.get(
            "Access-Control-Request-Headers"
          ),
        },
      });
    }

    const body = await request.json();
    const params = { input_text: body.prompt }
    let response = await ai.run('@cf/facebook/bart-large-cnn', params);
    tasks.push(response);

    return Response.json(tasks, { headers: corsHeaders});
  }
};

```

# Text-to-Image Worker
```
import { Ai } from './vendor/@cloudflare/ai.js';

export default {
  async fetch(request, env) {

    const corsHeaders = {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET,HEAD,POST,OPTIONS',
        'Access-Control-Max-Age': '86400', // optional
        'Access-Control-Allow-Headers': 'Content-Type' ,
        'Access-Control-Request-Headers': 'Access-Control-Request-Headers',
    };

    if (
      request.headers.get("Origin") !== null &&
      request.headers.get("Access-Control-Request-Method") !== null &&
      request.headers.get("Access-Control-Request-Headers") !== null
    ) {
      // Handle CORS preflight requests.
      return new Response(null, {
        headers: {
          ...corsHeaders,
          "Access-Control-Allow-Headers": request.headers.get(
            "Access-Control-Request-Headers"
          ),
        },
      });
    }

    const ai = new Ai(env.AI);

    const body = await request.json();

    const inputs = {
      prompt: body.prompt
    };

    const response = await ai.run(
      '@cf/stabilityai/stable-diffusion-xl-base-1.0',
      inputs
    );

    return new Response(response, {
      headers: {
        'content-type': 'image/png',
        ...corsHeaders
      }
    });
  }
};
```
