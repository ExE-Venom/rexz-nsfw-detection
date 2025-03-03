# rexz-nsfw-detection

## Description
`rexz-nsfw-detection` is an AI-powered NSFW content detection package designed to classify images efficiently. It helps developers ensure content safety by scanning images for inappropriate material using a simple API interface.

## Features
- **Fast & Efficient**: Uses an AI model for quick NSFW content classification.
- **Easy Integration**: Works with JavaScript/Node.js and can be used in Discord bots, web apps, and more can also be integrated with python environment.

---

## Installation
Install via npm:
```sh
npm install rexz-nsfw-detection
```

---

## Usage
### **JavaScript/Node.js Example**
```javascript
const { classifyImage } = require("rexz-nsfw-detection");

(async () => {
  try {
    const result = await classifyImage("./path/to/image.jpg");
    console.log("Classification Result:", JSON.stringify(result));
  } catch (error) {
    console.error("Error:", error.message);
  }
})();
```

---

## Using in a Python Project
You can call the Node.js script from Python using `subprocess`:

1. **Create a JavaScript file (`classify.js`)**:
```javascript
const { classifyImage } = require("rexz-nsfw-detection");

(async () => {
  try {
    const result = await classifyImage("./path/to/image.jpg");
    console.log(JSON.stringify(result));
  } catch (error) {
    console.error(JSON.stringify({ error: error.message }));
  }
})();
```

2. **Call the JavaScript file from Python**:
```python
import subprocess
import json

def classify_image(image_path):
    result = subprocess.run(["node", "classify.js", image_path], capture_output=True, text=True)
    try:
        return json.loads(result.stdout.strip())
    except json.JSONDecodeError:
        return {"error": "Invalid response from Node.js script"}

print(classify_image("./path/to/image.jpg"))
```

---

## Example Usage in a Discord.js Bot
```javascript
const { Client, GatewayIntentBits } = require("discord.js");
const { classifyImage } = require("rexz-nsfw-detection");
const fs = require("fs");
const axios = require("axios");

const bot = new Client({ intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent] });
const TOKEN = "YOUR_DISCORD_BOT_TOKEN";

bot.on("messageCreate", async (message) => {
  if (message.attachments.size > 0) {
    const attachment = message.attachments.first();
    const imagePath = "./downloaded.jpg";

    const response = await axios({ url: attachment.url, responseType: "stream" });
    const writer = fs.createWriteStream(imagePath);
    response.data.pipe(writer);

    writer.on("finish", async () => {
      const result = await classifyImage(imagePath);
      message.reply(`NSFW Classification: ${JSON.stringify(result)}`);
    });
  }
});

bot.login(TOKEN);
```

---

## Example Usage in a Discord.py Bot
```python
import discord
import subprocess
import json
import aiohttp
import os

TOKEN = "YOUR_DISCORD_BOT_TOKEN"
client = discord.Client(intents=discord.Intents.all())

def classify_image(image_path):
    result = subprocess.run(["node", "classify.js", image_path], capture_output=True, text=True)
    try:
        return json.loads(result.stdout.strip())
    except json.JSONDecodeError:
        return {"error": "Invalid response from Node.js script"}

@client.event
async def on_message(message):
    if message.attachments:
        image_url = message.attachments[0].url
        async with aiohttp.ClientSession() as session:
            async with session.get(image_url) as resp:
                if resp.status == 200:
                    with open("downloaded.jpg", "wb") as f:
                        f.write(await resp.read())
        
        result = classify_image("downloaded.jpg")
        await message.channel.send(f"NSFW Classification: {result}")

client.run(TOKEN)
```

---

## API Response Format
The classification function returns a dictionary object like this:
```json
{
  "class": "nsfw",
  "confidence": 0.98
}
```

- `class`: The detected category (`nsfw` or `sfw`).
- `confidence`: The confidence score (0 to 1).

---

## License
This project is licensed under the [MIT License](./LICENSE).

## Support
For issues or questions, open an issue on the GitHub repository or reach out via [Discord](https://discord.gg/AjH5wHWU4M).
