# Python Discord Bot

A simple Discord bot built with `discord.py`. It shows basic skills: commands, events, role checks, message filtering, simple polls, direct messages, and logging.

## Features

- Command prefix: `!`
- Events
  - `on_ready`: prints a startup line
  - `on_member_join`: sends a welcome DM
  - `on_message`: ignores the bot itself, deletes a message if it contains a banned word, then passes control to the command handler
- Commands
  - `!hello` — greets the caller
  - `!assign` — gives the caller the role named in `secret_role`
  - `!remove` — removes that role from the caller
  - `!dm <text>` — sends you a DM with your text echoed back
  - `!reply` — replies to your message in the same channel
  - `!poll <question>` — posts a poll embed with 👍 and 👎 reactions
  - `!secret` — only members with the role in `secret_role` can use this
- Logging: writes a `discord.log` file at DEBUG level

## Repo contents

- `main.py` — the bot code
- `.env` — holds `DISCORD_TOKEN` (do not commit real tokens)
- `requirements.txt` — Python package list
- `README.md` — this guide

## Quick start

1) **Create a bot on Discord**
- Go to the Discord Developer Portal, create an application, add a Bot user.
- Copy the **Bot Token**. You will paste it into `.env`.
- Under **Bot** settings, enable **Message Content Intent** and **Server Members Intent**.

2) **Set up locally**
```bash
# Python 3.10+ recommended
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
```

3) **Configure environment**
Create a `.env` file next to `main.py`

```env
DISCORD_TOKEN="paste-your-token-here"
```

4) **Invite the bot to your server**
- Use the OAuth URL with the **bot** scope.
- Give the bot these permissions at minimum:
  - Send Messages
  - Read Message History
  - Add Reactions
  - Manage Messages (needed to delete messages when moderating)
  - Manage Roles (needed for `!assign` and `!remove`)
- Make sure the bot’s role is **above** the role you want it to assign.

5) **Run it**
```bash
python main.py
```

You should see a log line like: `We are ready to go in, <botname>`.

## Configuration

- `secret_role` in `main.py` controls the gated role name. Default is `"Gamer"`:
  ```python
  secret_role = "Gamer"
  ```
  Change it to match an existing role in your server.

- **Banned word** check is very simple:
  ```python
  if "shit" in message.content.lower():
      await message.delete()
      await message.channel.send(f"{message.author.mention} - dont use that word!")
  ```
  Edit this for your own policy. Consider a list of words or a regex. Add error handling so the bot does not crash if it lacks permissions.

- **Intents**:
  ```python
  intents = discord.Intents.default()
  intents.message_content = True
  intents.members = True
  bot = commands.Bot(command_prefix='!', intents=intents)
  ```
  These must also be enabled on the **Developer Portal** for the bot.

- **Logging** writes to `discord.log` at DEBUG level:
  ```python
  handler = logging.FileHandler(filename='discord.log', encoding='utf-8', mode='w')
  bot.run(token, log_handler=handler, log_level=logging.DEBUG)
  ```

## Commands and examples

- `!hello` — “Hello @you!”
- `!assign` — gives you the `secret_role`
- `!remove` — removes that role from you
- `!dm hello there` — the bot DMs you “You said hello there”
- `!reply` — the bot replies under your message
- `!poll Should we start now?` — embed with 👍 👎 reactions
- `!secret` — only works if you have the `secret_role`

## Notes and limitations

- The filter deletes a message if it contains one exact word. This is only a demo. Improve the list, avoid false positives, and add permission checks.
- `on_member_join` tries to DM every new member. Some users block DMs. You should wrap this in a try/except.
- `!assign` and `!remove` act only on the caller. Change them if you need admin tools that target other users, and protect them with role checks.
- The bot needs the right guild permissions to delete messages, add reactions, and manage roles.
- Tokens are secret. Never commit real tokens. Rotate a token if it leaks.

## Development ideas

- Convert to slash commands with `discord.app_commands`
- Use cogs to split commands by feature
- Add multi-option polls
- Add persistent config per guild
- Add structured logging and better error handling
- Add tests with `pytest` and mocks for Discord API calls

## Licence

MIT or similar. Update this line with your chosen licence.
