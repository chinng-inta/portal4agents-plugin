---
name: portal-a2ui-surfaces
description: Fetch, render, and drive the Chinng portal's A2UI v1.0 surfaces over unauthenticated HTTPS GET. Use when an A2UI-capable client needs a ready-made interface for portal news, CVE advisories, government documents, or Agent-Readiness data, or when one request should return both a renderable surface and the records behind it.
---

# Portal A2UI surfaces

The portal publishes the same data three ways: the `portal` MCP server, Markdown and REST, and **A2UI v1.0 surfaces**. This skill covers the A2UI path. Use the MCP server for pure data work — it is cheaper. Reach for A2UI when a human is going to look at the result in an A2UI renderer, or when one call should hand back a renderable interface plus its records.

These endpoints are plain HTTPS GET with no authentication, so this skill needs a fetch capability (`WebFetch`, `curl`, or the client's own HTTP tool), not the MCP connection.

## Endpoints

Base URL: `https://portal.chinng-lab-srv.dev`

| Surface | Endpoint | Parameters |
| --- | --- | --- |
| Top / latest per family | `GET /api/a2ui/top` | `ui_lang`, `per_family` (1–10, default 3) |
| Cross-category BM25 search | `GET /api/a2ui/search` | `ui_lang`, `q`, `category`, `lang`, `limit`, `offset` |
| Government document browser | `GET /api/a2ui/government` | `ui_lang`, `country`, `document_type`, `limit`, `offset` |
| Agent-Readiness directory list | `GET /api/a2ui/surfaces` | `ui_lang`, `domain`, `level`, `sort`, `limit`, `offset` |
| Agent-Readiness detail | `GET /api/a2ui/surfaces/{domain}` | `ui_lang` |

- `ui_lang`: `ja` (default) | `en-us` | `zh-cn`.
- `category`: `news` | `security` | `government`. Anything else is treated as all categories.
- `lang` (article language, search only): `ja` | `en` | `zh`. Anything else is treated as all languages.
- `country`: `jp` | `us` | `eu`. `document_type` is a free-text filter.
- `level`: `0`–`3`. `sort`: `score` (default) | `recent`.
- `limit`: default 20, maximum 100; the government surface is capped at 50 because it reads Markdown per row.
- Search returns no items without `q` — the surface shows a prompt instead.

The response is `application/a2ui+json`: a JSON array of A2UI messages (`agent_to_renderer_list`). Content negotiation also works — sending `Accept: application/a2ui+json` to `/`, `/en-us/`, or `/zh-cn/` returns the top surface for that language instead of HTML. A browser host console for humans lives at `/console/?ui_lang=…`.

## What a response contains

Every surface is three messages, all with `"version": "1.0"`:

1. `createSurface` — `surfaceId: "portal-console"`, `catalogId: "https://a2ui.org/specification/v1_0/catalogs/basic/catalog.json"` (A2UI Basic Catalog).
2. `updateDataModel` — `path: "/"`, `value`: the records and filter state.
3. `updateComponents` — the component tree rooted at the component with `id: "root"`.

Pick the cheaper half for the job:

- **Rendering** — hand the whole array to an A2UI renderer that supports the v1.0 Basic Catalog, in order.
- **Reading data** — parse only the `updateDataModel` value. It holds every record as plain fields; the component tree is layout and runs well over a hundred components. Do not walk the components to extract data.

### Data model paths

| Surface | Paths |
| --- | --- |
| top | `/portal`, `/connect`, `/feeds`, `/news`, `/security`, `/government`, `/surfaces` |
| search | `/portal`, `/search/{q,category,lang,statusLine,items}` |
| government | `/portal`, `/gov/{country,documentType,statusLine,items}` |
| surfaces | `/portal`, `/surfaces/{domain,level,sort,statusLine,items}` |
| surface detail | `/portal`, `/detail/{domain,summary,markdownUrl,apiUrl,llmsTxtUrl,categories,checks}` |

Article-type items (`/news`, `/security`, `/government`, `/search/items`, `/gov/items`) carry `id`, `title`, `metaLine`, `summary`, `portalUrl`, `sourceUrl`, `citationUrl`. `metaLine` is a `·`-joined string of category, source, language, date, document number, document type, and the `link-only` marker — treat it as display text, and go to the MCP server or REST API when the fields are needed separately.

Directory items carry `domain`, `metaLine`, `markdownUrl`, `apiUrl`. In the detail surface, `categories` and `checks` are lists of pre-rendered Markdown `line` strings, not structured scores.

`statusLine` carries the result count, and for search the retrieval mode (`mode=bm25` or the fallback). Use it to decide whether to page rather than inferring totals from the item count.

## Driving the surface

A2UI button events are **client-side navigation only — there is no server action endpoint**. To advance the UI, resolve the event to the next GET and re-fetch:

| Event | Next request |
| --- | --- |
| `open_top` | `/api/a2ui/top` |
| `open_search`, `run_search`, `page_search` | `/api/a2ui/search?q=&category=&lang=&offset=` |
| `open_government`, `run_government`, `page_government` | `/api/a2ui/government?country=&document_type=&offset=` |
| `open_surfaces`, `run_surfaces`, `page_surfaces` | `/api/a2ui/surfaces?domain=&level=&sort=&offset=` |
| `inspect_surface` | `/api/a2ui/surfaces/{domain}` |
| `switch_lang` | the current URL with the new `ui_lang` |

Where the parameters come from:

- Form values live in the data model — `/search/q`, `/search/category`, `/search/lang`, `/gov/country`, `/gov/documentType`, `/surfaces/domain`, `/surfaces/level`, `/surfaces/sort`. `ChoicePicker` values are single-element arrays, so read element 0.
- `offset` (paging) and `domain` (`inspect_surface`) come from the event's `context`.
- Carry `ui_lang` on every request; omitting it falls back to `ja`.
- Treat the sentinel values `all` and empty strings as "parameter omitted" rather than sending them.

Buttons whose action is `openUrl` are external links — `portalUrl`, `sourceUrl`, `citationUrl`, `markdownUrl`. Follow them with ordinary fetches; they are not A2UI surfaces.

### Replacing a rendered surface

Every surface reuses `surfaceId: "portal-console"`, and a renderer rejects a second `createSurface` for a surface it already holds (`INTEGRITY_ERROR`). Before processing a new fetch into a renderer that already has the surface, send `{"version": "1.0", "deleteSurface": {"surfaceId": "portal-console"}}` first, then the three incoming messages.

## Constraints to respect

**`link-only` records expose no summary.** For those items the `summary` field holds a neutral note in the UI language, not a shortened body. Report the item by title, metadata, and source link; never present the note as a summary, and never reconstruct a summary from the title or from another record.

**Headings are Markdown inside `Text`.** A2UI v1.0 allows only `caption` and `body` text variants, so the portal emits headings as Markdown (`## …`) in the text value, and item titles bind through `formatString`. A renderer that does not interpret Markdown shows the `#` characters. When extracting text yourself, strip the heading prefixes.

**An unknown `ui_lang` silently becomes `ja`.** The value is normalized (`en*` → `en-us`, `zh*` → `zh-cn`), and anything unrecognized falls back to Japanese. Check the language of what came back before reporting it as English or Chinese.

**`ui_lang` is not the article filter.** `ui_lang` sets the interface language; the search `lang` parameter filters the records. Summaries are chosen per `ui_lang` where a translation exists, so the record language and the display language can differ.

**An unscanned domain returns HTTP 200.** Unlike `/api/surfaces/{domain}`, which answers 404, the detail surface returns a valid surface whose `/detail/message` says no assessment was found. Report that domain as unscanned — not as a zero score or a Level 0 site.

**Rate limits apply.** Responses carry `RateLimit` headers (60 requests per 20-second window at the time of writing), and `/api/a2ui/search` sits in a stricter search bucket that answers 429 when exceeded. Read `statusLine` and page deliberately instead of fetching pages in a tight loop; on 429, back off and retry.

**Scores and totals are observations with a timestamp.** Directory `metaLine` and detail `summary` include the check date. State it so a historical scan is not read as current behavior.
