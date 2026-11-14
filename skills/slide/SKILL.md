---
name: slide
description: >
  Make presentations with Slide, the AI presentation maker at getslide.pro.
  Use this whenever someone asks for slides, a slide deck, a PowerPoint, a
  pitch deck, a sales deck, a product presentation, a marketing presentation,
  or to turn notes, a brief, a PDF, or a company website into a presentation.
  Connect the Slide MCP server at https://www.getslide.pro/mcp and follow its
  plan, design, and generate steps. Leave the slide writing to Slide.
metadata:
  version: "1.0.0"
  homepage: https://www.getslide.pro
---

# Slide

Slide builds the presentation on its own servers. Your job is to run the tools in order and show the user what Slide returns. The pictures are already on screen in hosts that support them.

Server: `https://www.getslide.pro/mcp`

The user signs in with a Slide account on the first protected call. There is no API key.

## Connect the server

Skip this section when the Slide tools are already available.

Cursor, project or `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "Slide": {
      "url": "https://www.getslide.pro/mcp"
    }
  }
}
```

Other clients, same URL:

- Claude Code: `claude mcp add --transport http slide https://www.getslide.pro/mcp`
- Codex: `codex mcp add --url https://www.getslide.pro/mcp`
- Gemini CLI: `gemini mcp add --transport http slide https://www.getslide.pro/mcp`
- VS Code: `"type": "http"` in `.vscode/mcp.json`
- Claude and ChatGPT: add a custom connector, paste the URL, choose OAuth

After it is connected, start again from the step below that matches what the user asked.

## A new presentation

1. Call `create_deck`, then `plan_deck` with the user's prompt.
2. `plan_deck` waits until the questions are ready, then stops. Show those questions and wait. Call nothing else until the user replies.
3. Call `draft_deck_outline` with their answers. When `questions` is empty, call `draft_deck_outline` immediately. Slide writes the slide list. Wait for that call to finish before you reply.
4. Show the slide list: number, title, bullets. Ask only whether the plan is OK, or whether to continue to the design and templates. Keep the editor link to yourself until `generate_deck`, unless they ask where the presentation is.
5. When they want changes, call `revise_deck_plan`, show the list again, and ask the same question. `get_deck_plan` only reads the list that is already stored.
6. When they choose to continue, ask one question and call nothing yet: built-in templates, or a custom brand kit from the company already in the prompt. When `suggestedBrand.url` is present, name that site. When `suggestedBrand.confirmed` is false, ask them to confirm that site before any scan. When the URL is already confirmed, skip the "which website?" question.

### Templates

Call `list_deck_templates`. The gallery is already on screen, three cards per row. Leave `galleryUrl` in the tool result. The names are already on the cards. Ask which card they want. Then `select_deck_design` with that id and `generate_deck`.

### Custom brand kit

When they want the kit and `suggestedBrand.url` is confirmed, call `create_deck_brand` with that `brandUrl` immediately. Ask for a URL only when `suggestedBrand` is missing. This call waits until the kit is ready. If this deck already has a kit, the same call returns it and does not scan again. When `brand.status` is ready, the brand card is already on screen. Leave the image URL in the tool result. The light and dark looks are one kit. Ask if they like it. If yes, `select_deck_design` with `brand`, then `generate_deck`. If they would rather use a built-in template, switch to `list_deck_templates`.

### After generate

`generate_deck` returns the editor URL and starts the build. Give them that URL. The first build can take about 10 minutes. Tell them to follow the link. Slide emails when the first presentation is ready. Then stop. Leave `get_deck_run` alone.

## Later

- "How is it going?" or "where is the link?": call `get_deck` once. If it is still in progress, say only that, and give `url`. When `galleryUrl` is set, the slides are already on screen. Leave that URL in the tool result. Skip files, tools, phases, and which slides have thumbnails.
- "Find my X presentation": `list_decks` with `query`, then `get_deck`.
- An edit after the first build: `ask_deck`. It returns the editor URL and stops. The edit happens on that page. Use it only after `generate_deck`. A first prompt on a new deck starts at `create_deck`, then `plan_deck`.

`compile_deck` is an in-product tool. Leave it unused from this skill.
