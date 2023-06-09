# watfordGPT Telegram Bot
A [Telegram bot](https://core.telegram.org/bots/api) that integrates with OpenAI, [DALL·E](https://openai.com/product/dall-e-2) and [Whisper](https://openai.com/research/whisper) APIs to provide answers. Ready to use with minimal configuration required.

## Features
- [x] Support markdown in answers
- [x] Reset conversation with the `/reset` command
- [x] Typing indicator while generating a response
- [x] Access can be restricted by specifying a list of allowed users
- [x] Docker and Proxy support
- [x] Image generation using DALL·E via the `/image` command
- [x] Transcribe audio and video messages using Whisper (may require [ffmpeg](https://ffmpeg.org))
- [x] Automatic conversation summary to avoid excessive token usage
- [x] Track token usage per user
- [x] Get personal token usage statistics and cost per day/month via the `/stats` command 
- [x] User budgets and guest budgets 
- [x] Stream support
- [x] GPT-4 support - If you have access to the GPT-4 API, simply change the `OPENAI_MODEL` parameter to `gpt-4`
- [x] Localized bot language - Available languages :gb: :de: :ru: :tr: :it: :finland: :es: :indonesia: :netherlands: :cn: :taiwan: :vietnam: :iran: :brazil:
- [x] Improved inline queries support for group and private chats - To use this feature, enable inline queries for your bot in BotFather via the `/setinline` [command](https://core.telegram.org/bots/inline)

## Prerequisites
- Python 3.9+
- A [Telegram bot](https://core.telegram.org/bots#6-botfather) and its token (see [tutorial](https://core.telegram.org/bots/tutorial#obtain-your-bot-token))
- An [OpenAI](https://openai.com) account (see [configuration](#configuration) section)

## Getting started

### Configuration
Customize the configuration by copying `.env.example` and renaming it to `.env`, then editing the required parameters as desired:

| Parameter                   | Description                                                                                                                                                                                                                   |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `OPENAI_API_KEY`            | Your OpenAI API key, you can get it from [here](https://platform.openai.com/account/api-keys)                                                                                                                                 |
| `TELEGRAM_BOT_TOKEN`        | Your Telegram bot's token, obtained using [BotFather](http://t.me/botfather) (see [tutorial](https://core.telegram.org/bots/tutorial#obtain-your-bot-token))                                                                  |
| `ADMIN_USER_IDS`            | Telegram user IDs of admins. These users have access to special admin commands, information and no budget restrictions. Admin IDs don't have to be added to `ALLOWED_TELEGRAM_USER_IDS`. **Note**: by default, no admin (`-`) |
| `ALLOWED_TELEGRAM_USER_IDS` | A comma-separated list of Telegram user IDs that are allowed to interact with the bot (use [getidsbot](https://t.me/getidsbot) to find your user ID). **Note**: by default, *everyone* is allowed (`*`)                       |

### Optional configuration
The following parameters are optional and can be set in the `.env` file:

#### Budgets
| Parameter             | Description                                                                                                                                                                                                                                                                                                                                                                               | Default value      |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| `BUDGET_PERIOD`       | Determines the time frame all budgets are applied to. Available periods: `daily` *(resets budget every day)*, `monthly` *(resets budgets on the first of each month)*, `all-time` *(never resets budget)*.                                                                  																											    | `monthly`          |
| `USER_BUDGETS`        | A comma-separated list of $-amounts per user from list `ALLOWED_TELEGRAM_USER_IDS` to set custom usage limit of OpenAI API costs for each. For `*`- user lists the first `USER_BUDGETS` value is given to every user.  																																									| `*`                |
| `GUEST_BUDGET`        | $-amount as usage limit for all guest users. Guest users are users in group chats that are not in the `ALLOWED_TELEGRAM_USER_IDS` list. Value is ignored if no usage limits are set in user budgets (`USER_BUDGETS`=`*`).																																								    | `100.0`            |
| `TOKEN_PRICE`         | $-price per 1000 tokens used to compute cost information in usage statistics. Source: https://openai.com/pricing                                                                                                                                                                                                                                                                          | `0.002`            |
| `IMAGE_PRICES`        | A comma-separated list with 3 elements of prices for the different image sizes: `256x256`, `512x512` and `1024x1024`. Source: https://openai.com/pricing                                                                                                                                                                                                                                  | `0.016,0.018,0.02` |
| `TRANSCRIPTION_PRICE` | USD-price for one minute of audio transcription. Source: https://openai.com/pricing                                                                                                                                                                                                                                                                                                       | `0.006`            |

#### Additional optional configuration options
| Parameter                          | Description                                                                                                                                                                                                                                            | Default value                      |
|------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| `ENABLE_QUOTING`                   | Whether to enable message quoting in private chats                                                                                                                                                                                                     | `true`                             |
| `ENABLE_IMAGE_GENERATION`          | Whether to enable image generation via the `/image` command                                                                                                                                                                                            | `true`                             |
| `ENABLE_TRANSCRIPTION`             | Whether to enable transcriptions of audio and video messages                                                                                                                                                                                           | `true`                             |
| `PROXY`                            | Proxy to be used for OpenAI and Telegram bot (e.g. `http://localhost:8080`)                                                                                                                                                                            | -                                  |
| `OPENAI_MODEL`                     | The OpenAI model to use for generating responses                                                                                                                                                                                                       | `gpt-3.5-turbo`                    |
| `ASSISTANT_PROMPT`                 | A system message that sets the tone and controls the behavior of the assistant                                                                                                                                                                         | `You are a helpful assistant.`     |
| `SHOW_USAGE`                       | Whether to show OpenAI token usage information after each response                                                                                                                                                                                     | `false`                            |
| `STREAM`                           | Whether to stream responses. **Note**: incompatible, if enabled, with `N_CHOICES` higher than 1                                                                                                                                                        | `true`                             |
| `MAX_TOKENS`                       | Upper bound on how many tokens the watfordGPT API will return                                                                                                                                                                                             | `1200` for GPT-3, `2400` for GPT-4 |
| `MAX_HISTORY_SIZE`                 | Max number of messages to keep in memory, after which the conversation will be summarised to avoid excessive token usage                                                                                                                               | `15`                               |
| `MAX_CONVERSATION_AGE_MINUTES`     | Maximum number of minutes a conversation should live since the last message, after which the conversation will be reset                                                                                                                                | `180`                              |
| `VOICE_REPLY_WITH_TRANSCRIPT_ONLY` | Whether to answer to voice messages with the transcript only or with a watfordGPT response of the transcript                                                                                                                                              | `false`                            |
| `VOICE_REPLY_PROMPTS`              | A semicolon separated list of phrases (i.e. `Hi bot;Hello chat`). If the transcript starts with any of them, it will be treated as a prompt even if `VOICE_REPLY_WITH_TRANSCRIPT_ONLY` is set to `true`                                                | -                                  |
| `N_CHOICES`                        | Number of answers to generate for each input message. **Note**: setting this to a number higher than 1 will not work properly if `STREAM` is enabled                                                                                                   | `1`                                |
| `TEMPERATURE`                      | Number between 0 and 2. Higher values will make the output more random                                                                                                                                                                                 | `1.0`                              |
| `PRESENCE_PENALTY`                 | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far                                                                                                                                       | `0.0`                              |
| `FREQUENCY_PENALTY`                | Number between -2.0 and 2.0. Positive values penalize new tokens based on their existing frequency in the text so far                                                                                                                                  | `0.0`                              |
| `IMAGE_SIZE`                       | The DALL·E generated image size. Allowed values: `256x256`, `512x512` or `1024x1024`                                                                                                                                                                   | `512x512`                          |
| `GROUP_TRIGGER_KEYWORD`            | If set, the bot in group chats will only respond to messages that start with this keyword                                                                                                                                                              | -                                  |
| `IGNORE_GROUP_TRANSCRIPTIONS`      | If set to true, the bot will not process transcriptions in group chats                                                                                                                                                                                 | `true`                             |
| `BOT_LANGUAGE`                     | Language of general bot messages. Currently available: `en`, `de`, `ru`, `tr`, `it`, `fi`, `es`, `id`, `nl`, `zh-cn`, `zh-tw`, `vi`, `fa`, `pt-br`.  [Contribute with additional translations](https://github.com/n3d1117/watfordGPT-telegram-bot/discussions/219) | `en`                               |

Check out the [official API reference](https://platform.openai.com/docs/api-reference/chat) for more details.

### Installing
Clone the repository and navigate to the project directory:

```shell
git clone https://github.com/zpseudo/watfordgpt-telegram-bot.git
cd watfordgpt-telegram-bot
```

#### From Source
1. Create a virtual environment:
```shell
python -m venv venv
```

2. Activate the virtual environment:
```shell
# For Linux or macOS:
source venv/bin/activate

# For Windows:
venv\Scripts\activate
```

3. Install the dependencies using `requirements.txt` file:
```shell
pip install -r requirements.txt
```

4. Use the following command to start the bot:
```
python bot/main.py
```


## Disclaimer
This is a personal project and is not affiliated with OpenAI in any way.

## License
This project is released under the terms of the GPL 2.0 license. For more information, see the [LICENSE](LICENSE) file included in the repository.
