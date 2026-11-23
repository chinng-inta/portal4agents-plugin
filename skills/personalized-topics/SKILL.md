---
name: personalized-topics
description: Pick topics from the Chinng AI-Agent Portal that match an interest profile the user declared, ranking and deduplicating on the client side. Use for "what's new for me" requests and recurring personalized digests.
---

# Personalized topics

Use the `portal` MCP server declared by this plugin.

Personalization happens entirely on the client side. The portal is a stateless read-only source: it receives ordinary search queries and stores no profile and no history.

## The profile belongs to the user

Keep a `profile.yaml` in your own persistent local storage, outside the plugin directory so a plugin update cannot overwrite it. This plugin ships no profile. If none exists, ask the user what they want to follow and how much each interest matters, then create one in this shape:

```yaml
# Weights: 3 = core interest, 2 = regular, 1 = occasional.
interests:
  AI agents: 3
  MCP: 3
  vulnerabilities: 2

# Portal category strings to exclude entirely (prefix match).
exclude_categories: []

# Recency window in days.
freshness_days: 14

# Search results to pull per interest per run.
per_interest_limit: 8
```

- Interests are declared by the user, never inferred from what they read, clicked, or asked about.
- Change the profile only when the user asks. Show the diff before saving when the change is more than a single weight.
- Never send the profile, or anything derived from it other than one search keyword per query, to the portal or to any other service.
- Write each interest in the language the user's sources use. An interest is the search query, so a Japanese interest finds Japanese records.

## Fetch the candidates

1. Run one search per interest with the interest as the query and `per_interest_limit` as the limit. Search terms separated by spaces are ANDed, so do not combine several interests into one query.
2. Pool the results by portal item ID. Record, for each item, every interest whose search returned it.
3. Remove items whose category starts with an entry in `exclude_categories`, and items whose ID is in the seen list.

Do not retrieve bodies for ranking. The search results carry everything the steps below need.

If a search returns a rate-limit result instead of items, stop searching, rank what was gathered, and say which interests were not covered.

## Score

Compute each item's score as the sum of three parts:

- **Base** — the sum of the weights of every interest whose search returned the item.
- **Title bonus** — +0.5 for each interest keyword that appears in the title.
- **Recency bonus** — `2.0 × (1 − age_days / freshness_days)` when the item has a publication date inside the window, so +2.0 on the day of publication falling linearly to 0 at `freshness_days`. An item outside the window, or with no publication date, gets 0.

Sort by score, highest first. The score is a sorting aid, not a verdict: it mixes declared weights, title matches and recency, and says nothing about quality.

Treat an item with no publication date as freshness unknown, not as new. When the pool is large enough without them, drop items outside the window.

## Fold same-story duplicates

Several outlets often report one story. Fold them into a single topic before presenting.

1. Normalize each title: remove bracketed text and a trailing outlet name, lowercase, strip punctuation and symbols.
2. Fold two items when the Jaccard coefficient of their normalized titles' character sets is 0.55 or higher.
3. Title similarity misses stories whose titles are written differently, such as a product name in Latin script in one title and in katakana in another. Also compare `entities` and `entities_en`: when two items published within a few days of each other share their main entities, read both titles and fold them if they report the same event. Entity overlap alone is a candidate, not a match; two different stories about the same company share entities too.

Keep the highest-scoring item as the topic's lead, list the others as additional sources, and state how many items were folded.

## Check before presenting

- Drop an item whose only match is a stray keyword in an unrelated title, and say that it was dropped.
- Present 15 topics unless the user asked for a different number.
- Give each topic's matched interests. They are the explanation for why the topic surfaced.
- Mark freshness-unknown topics as such.
- Link each topic with the links its record returned. Do not construct a portal URL from a category and an ID.
- Follow each record's `license_note` and omission metadata. Most external news is `link-only` and carries no summary; present those as dated pointers and do not reconstruct withheld content.

## Remember what was presented

Keep the seen list as a JSON array of portal item IDs beside the profile. Add IDs only after the topics were actually presented to the user, including the IDs folded into a presented topic. A run that failed or was never shown must not mark anything as seen.

## When the topics feel off

- Ask which presented topics missed instead of guessing new interests.
- Prefer adding a missing interest over raising a weight. A search that never runs cannot match at any weight.
