
>  New project WeChat robot [based on OpenAI](https://github.com/x-dr/wechat-bot)

### Demo

[https://chatai.451024.xyz](https://chatai.451024.xyz)

> api

```url
https://openai.451024.xyz
```


~~https://openai.1rmb.tk~~


```url
https://openai-proxy-api.pages.dev/api
```

### The demo station is a public service. If you need to use it on a large scale, please deploy it yourself. The demo station is a bit overwhelmed

![worker](./docs/img/worker.png)

## 1、Use Cloudflare Worker to transfer api.openai.com

1. Create a new Cloudflare Worker
2. Copy the code in [cf_worker.js](https://cdn.jsdelivr.net/gh/x-dr/chatgptProxyAPI@main/cf_worker.js) and paste it into Worker and deploy it
3. Bind a domain name to Worker that is not blocked
4. Use your own domain name instead of api.openai.com


**[Detailed tutorial](./docs/cloudflare_workers.md)**


## 2. Use CloudFlare Pages for transfer

**[Detailed tutorial](./docs/cloudflare_proxy_pages.md)**

## 3. Use nextjs edge to transfer to api.openai.com

### Deploy with Cloudflare pages

> [Official Documentation](https://developers.cloudflare.com/pages/framework-guides/deploy-a-nextjs-site/)

1. ~~Fork this project~~ Click the [Use this template](https://github.com/x-dr/chatgptProxyAPI/generate) button to create a new code base.
2. Log in to the console of [Cloudflare](https://dash.cloudflare.com/).
3. From the account home page, select `pages` > `Create a project` > `Connect to Git`
4. Select your Fork's project repository, and in the `Set up builds and deployments` section, select `Next.js` as your framework preset. Your selection will provide the following information.

> ~~General default is enough~~

|  Configuration option	   | Value  |
|  ----  | ----  |
| Production branch  | main |
| Framework preset  | next.js |
| Build command	  | npx @cloudflare/next-on-pages@pre-v1 --experimental-minify |
| Build directory  | .vercel/output/static|


> Add a parameter in `Environment variables (advanced)`

|  Variable name	   | Value  |
|  ----  | ----  |
| NODE_VERSION   | 16 |

5. Click `Save and Deploy` to deploy, then click `Continue to project` to see the access domain name


> Replace `https://api.openai.com` of the official interface with `https://xxx.pages.dev/api` (https://xxx.pages.dev/api is your domain name)

*Note that there is an additional `api` in the path*

**[Detailed tutorial](./docs/cloudflare_pages.md)**

### docker deployment (overseas vps required)

> It seems that sse is not supported, so it is not recommended

<details>

<summary>e.g.</summary>

```bash
docker run -itd --name openaiproxy \
            -p 3000:3000 \
            --restart=always \
           gindex/openaiproxy:latest
```

#### use

*api : http://vpsip:3000/proxy/v1/chat/completions*

```bash
curl --location 'http://vpsip:3000/proxy/v1/chat/completions' \
--header 'Authorization: Bearer sk-xxxxxxxxxxxxxxx' \
--header 'Content-Type: application/json' \
--data '{
   "model": "gpt-3.5-turbo",
  "messages": [{"role": "user", "content": "Hello!"}]
 }'

```

</details>



## Usage


<details>

<summary>JavaScript用fetch</summary>

```javascript
const requestOptions = {
    method: 'POST',
    headers: {
        "Authorization": "Bearer sk-xxxxxxxxxxxx",
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        "model": "gpt-3.5-turbo",
        "messages": [
            {
                "role": "user",
                "content": "hello word"
            }
        ]
    })
};

fetch("https://openai.1rmb.tk/v1/chat/completions", requestOptions)
    .then(response => response.text())
    .then(result => console.log(result))
    .catch(error => console.log('error', error));
  
```

</details>



<details>

<summary>用python</summary>

```python
import requests

url = "https://openai.1rmb.tk/v1/chat/completions"
api_key = 'sk-xxxxxxxxxxxxxxxxxxxx'

headers = {
  'Authorization': f'Bearer {api_key}',
  'Content-Type': 'application/json'
}

payload = {
  "model": "gpt-3.5-turbo",
  "messages": [
    {
      "role": "user",
      "content": "hello word"
    }
  ]
}

try:
    response = requests.post(url, headers=headers, json=payload)
    response.raise_for_status() # Throw an exception if the response code is not 200
    data = response.json()
    print(data)
except requests.exceptions.RequestException as e:
    print(f"Request error: {e}")
except json.JSONDecodeError as e:
    print(f"Invalid JSON response: {e}")
```

</details>



<details>
<summary>Using nodejs chatgpt library</summary>

[transitive-bullshit/chatgpt-api](https://github.com/transitive-bullshit/chatgpt-api)

```javascript
import { ChatGPTAPI } from 'chatgpt'

async function example() {
  const api = new ChatGPTAPI({
    apiKey: "sk-xxxxxxxxxxxxxx",
  // proxy+/v1
    apiBaseUrl:"https://openai.1rmb.tk/v1"


  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}

example()

```

</details>



<details>

<summary>Check balance</summary>

```javascript
    const headers = {
      'content-type': 'application/json',
      'Authorization': `Bearer sk-xxxxxxxxxxxxxxxxx`
    }
    // Check whether to subscribe
    const subscription = await fetch("https://openai.1rmb.tk/v1/dashboard/billing/subscription", {
      method: 'get',
      headers: headers
    })
    if (!subscription.ok) {
      const data = await subscription.json()
      // console.log(data);
      return data
      // throw new Error('API request failed')
    } else {
      const subscriptionData = await subscription.json()
      const endDate = subscriptionData.access_until
      const startDate = new Date(endDate -- 90*24*60*60);
      console.log(formatDate(endDate, "YYYY-MM-DD"));
      console.log(formatDate(startDate, "YYYY-MM-DD"));
      const response = await fetch(`https://openai.1rmb.tk/v1/dashboard/billing/usage?start_date=${formatDate(startDate, "YYYY-MM-DD")}&end_date=${formatDate(endDate, "YYYY-MM-DD")}`, {
        method: 'get',
        headers: headers
      })
      
      const usageData = await response.json();
      // account type
      const plan = subscriptionData.plan.id
      console.log(usageData);
      }

```

</details>

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=x-dr/chatgptProxyAPI&type=Date)](https://star-history.com/#x-dr/chatgptProxyAPI&Date)

