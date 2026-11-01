# n8n Social Media Automation: AI Agent + Unsora (importable workflows)

Two ready-to-import n8n workflows that let n8n's AI Agent schedule and publish social media posts in plain words.
The agent gets one tool, the **MCP Client Tool**, connected to [Unsora](https://tryunsora.com), a social media
scheduler for AI agents. You ask "Schedule this to LinkedIn and Bluesky for tomorrow at 9:00 am", and it does.

| File | Trigger | Use it when |
| --- | --- | --- |
| [`n8n-social-media-automation-chat.json`](n8n-social-media-automation-chat.json) | When chat message received | you want to ask the agent to post, in n8n's chat |
| [`n8n-social-media-automation-weekly.json`](n8n-social-media-automation-weekly.json) | Schedule Trigger (Monday 8 am) | you want it to run on its own every week |

## The short answer

In n8n, add a chat trigger and an AI Agent with a chat model (OpenAI or Anthropic) and Simple Memory. On the Tool
port, add the MCP Client Tool, point it at `https://mcp.tryunsora.com/mcp` with HTTP Streamable and Header Auth
(name: `apiKey`, value: your Unsora key), and select five tools: `get_accounts`, `create_post`, `list_posts`,
`get_post` and `publish_post`. Then ask the agent to post. With a time, Unsora schedules it; with no time, it saves a
draft; "post now" publishes.

## What you need

- **n8n 1.104 or newer** (self-hosted for free, or n8n Cloud). That's the release that added HTTP Streamable to the
  MCP Client Tool.
- **An API key for a chat model**, like OpenAI or Anthropic.
- **An Unsora account** with your social accounts connected (**Accounts** in the sidebar). Unsora has a free trial,
  so you can start with that. Create your key in **Agents → API keys → Create API key**. It starts with `uns_` and is
  shown once.

## Import

1. In n8n, open a new workflow, click **⋯** (top right) → **Import from URL**, and paste one of these:
   - `https://raw.githubusercontent.com/Rezwanulkarim/n8n-social-media-automation/main/n8n-social-media-automation-chat.json`
   - `https://raw.githubusercontent.com/Rezwanulkarim/n8n-social-media-automation/main/n8n-social-media-automation-weekly.json`

   Or download the file and use **Import from File**.
2. Follow the yellow **Setup** note on the canvas:
   - **OpenAI Chat Model:** add your OpenAI credential (or swap in Anthropic).
   - **Unsora** (the MCP Client Tool): create a Header Auth credential. Name: `apiKey`. Value: your Unsora key.
   - **AI Agent → Options → System Message:** set your timezone.
3. Open the chat and ask it to schedule a post.

The files contain no credentials. You add your own keys in n8n.

## Build it yourself (the steps)

1. **Trigger:** On chat message (it shows as "When chat message received").
2. **AI Agent:** Chat Model, Simple Memory, and the MCP Client Tool on the Tool port.
3. **MCP Client Tool:** endpoint `https://mcp.tryunsora.com/mcp`, HTTP Streamable, Header Auth, Tools to Include =
   Selected (the five posting tools above).
4. **Rename the node to Unsora**, so the log reads `Unsora_get_accounts`.
5. **AI Agent → Options → System Message** (below). Keep the `mediaType` line, or images are dropped and the post goes
   out as text only.
6. **Ask:** "Schedule this to LinkedIn and Bluesky for tomorrow at 9:00 am."
7. **To run it on its own:** swap the trigger for a Schedule Trigger (weekly, Monday 8 am), set the prompt source to
   "Define below", and publish the workflow. (That's the weekly file.)
8. **Check it went out:** the Unsora calendar, or ask the agent (`list_posts`, `get_post`).

## System message

```text
You manage social media posts through Unsora.
Always call get_accounts first to find account IDs.
Never publish immediately unless I say post now.
Otherwise, schedule with scheduled_at.
To attach images, set mediaType to slideshow
and put the image URLs in mediaUrls.
My timezone is [your timezone].
After creating a post, reply with the post ID,
the accounts and the scheduled time.
```

## Limits

- Scheduled times must be at least 2 minutes ahead.
- Each platform's own rules still apply: Instagram needs a professional account, TikTok audits apps before posts can
  go public, YouTube is video only, X captions max out at 280 characters, Bluesky's at 300.
- If a scheduled post breaks a platform rule (like a Bluesky caption over 300 characters), Unsora sends it back with
  the issue so the agent can tell you what to fix.
- The agent can't see the images or video it makes, so review them yourself.

Checked against n8n 2.45.6 (AI Agent 3.1, MCP Client Tool 1.4, Chat Trigger 1.3, Schedule Trigger 1.2).

## License

MIT. Use, copy and change these workflows freely.
