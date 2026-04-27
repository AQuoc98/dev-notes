# 🟡 JavaScript Mini Logic

**Status:** 🚧 - **Last Updated:** 27th Apr 2026

- [🟡 JavaScript Mini Logic](#-javascript-mini-logic)
  - [Pagination Flow](#pagination-flow)
  - [✅ **1. Common pagination strategies**](#-1-common-pagination-strategies)
  - [✅ **2. Pagination flow (step-by-step)**](#-2-pagination-flow-step-by-step)
  - [✅ **3. Example 1 — Frontend-only pagination (data already in memory)**](#-3-example-1--frontend-only-pagination-data-already-in-memory)
  - [✅ **4. Example 2 — Fetching paginated data from the backend (offset)**](#-4-example-2--fetching-paginated-data-from-the-backend-offset)
  - [✅ **5. Example 3 — Cursor-based pagination (infinite scroll)**](#-5-example-3--cursor-based-pagination-infinite-scroll)
    - [✅ **6. Example 4 — Backend pagination logic (Node/Express)**](#-6-example-4--backend-pagination-logic-nodeexpress)
    - [✅ **7. Best practices \& gotchas**](#-7-best-practices--gotchas)



## Pagination Flow

Pagination splits a large dataset into smaller, manageable "pages" so the client fetches and renders only a slice at a time. This improves performance, reduces memory/bandwidth usage, and gives users a better experience.

## ✅ **1. Common pagination strategies**

| Strategy           | How it works                                                           | Pros                                        | Cons                                             | Best for                      |
| ------------------ | ---------------------------------------------------------------------- | ------------------------------------------- | ------------------------------------------------ | ----------------------------- |
| Offset / Page      | `?page=2&limit=20` — `OFFSET = (page-1)*limit`                         | Simple, can jump to any page, shows totals  | Slow on large tables, duplicates if data changes | Admin tables, small datasets  |
| Cursor / Keyset    | `?cursor=<id_or_timestamp>&limit=20` — `WHERE id > cursor ORDER BY id` | Fast at scale, stable under inserts         | No random page jump, needs a stable sort key     | Feeds, chat, infinite scroll  |
| Infinite Scroll    | UI on top of cursor/offset — fetch next page on scroll                 | Smooth UX, mobile-friendly                  | Hard to deep-link, accessibility concerns        | Social feeds, image galleries |
| Load More Button   | UI on top of cursor/offset — user clicks to fetch next page            | Explicit, accessible                        | More clicks                                      | News, search results          |

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## ✅ **2. Pagination flow (step-by-step)**

1. **Client initializes state** — `page = 1` (or `cursor = null`), `limit = 20`, `items = []`, `hasMore = true`, `loading = false`.
2. **Request data** — call `GET /api/items?page=1&limit=20` (or `?cursor=...`).
3. **Server responds** — returns `{ data, total, nextCursor, hasMore }`.
4. **Client updates state** — append (infinite) or replace (numbered pages) `items`; store `nextCursor`/`page`; toggle `hasMore`.
5. **Render** — show the list + pagination controls or a sentinel for intersection observer.
6. **Next page trigger** — user clicks "Next", clicks a page number, or scrolls near the bottom.
7. **Guard** — skip if `loading` or `!hasMore`; show spinner; handle errors and retry.
8. **Repeat** from step 2 with the new `page`/`cursor`.

```
┌────────┐  request(page/cursor)   ┌──────────┐
│ Client ├────────────────────────►│  Server  │
│ state  │◄────────────────────────┤  DB      │
└────────┘   { data, nextCursor }  └──────────┘
     │
     ├─ append/replace items
     ├─ update cursor / page
     ├─ render list + controls
     └─ on "next" or scroll → repeat
```

**Worked example — tracing the flow for a "Users" table**

Scenario: a dataset of **137 users**, `limit = 10`, `totalPages = Math.ceil(137 / 10) = 14`. The user opens the page, clicks **Next**, then clicks page **5**.

**What happens, step by step**

| Step | User action                | State transition                                        | Network                              | DOM                                  |
| ---- | -------------------------- | ------------------------------------------------------- | ------------------------------------ | ------------------------------------ |
| 1    | Page opens (no `?page`)    | `page=1, loading=true, reqId=1`                         | `GET /api/users?page=1&limit=10`     | "Loading…"                           |
| 2    | Response arrives           | `items=[u1..u10], totalPages=14, loading=false`         | —                                    | Rows 1–10 + `[1] 2 3 … 14`           |
| 3    | User clicks **Next**       | `goTo(2)` → pushes `?page=2`, `reqId=2`, `loading=true` | `GET /api/users?page=2&limit=10`     | "Loading…"                           |
| 4    | Response arrives           | `items=[u11..u20], page=2`                              | —                                    | Rows 11–20 + `1 [2] 3 … 14`          |
| 5    | User quickly clicks **5**  | `goTo(5)` → aborts req #2, `reqId=3`                    | Abort old, `GET ...?page=5&limit=10` | "Loading…"                           |
| 6    | Stale res #2 resolves late | `myReqId (2) !== state.reqId (3)` → dropped             | —                                    | No change                            |
| 7    | Res #3 arrives             | `items=[u41..u50], page=5`                              | —                                    | Rows 41–50 + `1 … 4 [5] 6 … 14`      |
| 8    | User hits browser **Back** | `popstate` → reads `page=2` from URL → `loadPage(2)`    | `GET /api/users?page=2&limit=10`     | Rows 11–20 + `1 [2] 3 … 14`          |

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## ✅ **3. Example 1 — Frontend-only pagination (data already in memory)**

Use when the dataset is small (≤ a few hundred rows) and already loaded on the client. All pagination logic runs locally — no network on page change.

```html
<ul id="list"></ul>
<nav id="pagination" aria-label="Pagination"></nav>
```

```js
// ── Source data (pretend this came from one initial fetch) ───────
const data = Array.from({ length: 53 }, (_, i) => ({ id: i + 1, name: `Item ${i + 1}` }));

const ITEMS_PER_PAGE = 5;
let currentPage = 1;

// ── Pure helpers ─────────────────────────────────────────────────
function getPageData(data, page, size) {
  const start = (page - 1) * size;
  return data.slice(start, start + size); // endIndex = start + size
}

function getPageNumbers(totalPages, currentPage, delta = 2) {
  const pages = [];
  const left = currentPage - delta;
  const right = currentPage + delta;

  for (let i = 1; i <= totalPages; i++) {
    if (i === 1 || i === totalPages || (i >= left && i <= right)) pages.push(i);
  }

  // Insert "..." in gaps
  const result = [];
  let prev = null;
  for (const p of pages) {
    if (prev && p - prev > 1) result.push("...");
    result.push(p);
    prev = p;
  }
  return result;
  // Example: getPageNumbers(20, 10) → [1, "...", 8, 9, 10, 11, 12, "...", 20]
}

// ── Render ───────────────────────────────────────────────────────
function render() {
  const totalPages = Math.ceil(data.length / ITEMS_PER_PAGE);
  const pageData = getPageData(data, currentPage, ITEMS_PER_PAGE);

  document.getElementById("list").innerHTML =
    pageData.map(item => `<li>${item.name}</li>`).join("");

  const pages = getPageNumbers(totalPages, currentPage);
  document.getElementById("pagination").innerHTML = [
    `<button data-page="${currentPage - 1}" ${currentPage === 1 ? "disabled" : ""}>← Prev</button>`,
    ...pages.map(p =>
      p === "..."
        ? `<span>…</span>`
        : `<button data-page="${p}" ${p === currentPage ? 'aria-current="page"' : ""}>${p}</button>`
    ),
    `<button data-page="${currentPage + 1}" ${currentPage === totalPages ? "disabled" : ""}>Next →</button>`,
  ].join(" ");
}

// ── Controller ───────────────────────────────────────────────────
function goTo(page) {
  const totalPages = Math.ceil(data.length / ITEMS_PER_PAGE);
  if (page < 1 || page > totalPages) return; // guard
  currentPage = page;
  render();
  window.scrollTo({ top: 0, behavior: "smooth" });
}

document.getElementById("pagination").addEventListener("click", e => {
  const p = Number(e.target.dataset.page);
  if (p) goTo(p);
});

render(); // boot
```

## ✅ **4. Example 2 — Fetching paginated data from the backend (offset)**

Use when data is too large to load at once. The client asks for one page at a time via a REST endpoint.

**API contract**

```http
GET /api/users?page=2&limit=10
→ 200 OK
{
  "data": [ { "id": 11, "name": "..." }, ... ],
  "page": 2,
  "limit": 10,
  "total": 137,
  "totalPages": 14
}
```

**Client**

```js
const state = {
  page: 1,
  limit: 10,
  totalPages: 0,
  items: [],
  loading: false,
  error: null,
  reqId: 0,          // guards against race conditions
  controller: null,  // cancels stale in-flight fetch
};

async function loadPage(page) {
  // Cancel previous request if still running
  if (state.controller) state.controller.abort();
  const controller = new AbortController();
  const myReqId = ++state.reqId;

  state.loading = true;
  state.error = null;
  state.controller = controller;
  render();

  try {
    const res = await fetch(
      `/api/users?page=${page}&limit=${state.limit}`,
      { signal: controller.signal }
    );
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const { data, totalPages } = await res.json();

    if (myReqId !== state.reqId) return; // ignore stale response

    state.items = data;
    state.page = page;
    state.totalPages = totalPages;
  } catch (err) {
    if (err.name === "AbortError") return;
    state.error = err.message;
  } finally {
    if (myReqId === state.reqId) state.loading = false;
    render();
  }
}

function render() {
  const list = document.getElementById("list");
  if (state.loading)           list.innerHTML = `<p>Loading…</p>`;
  else if (state.error)        list.innerHTML = `<p class="err">${state.error}</p>`;
  else if (!state.items.length) list.innerHTML = `<p>No results.</p>`;
  else list.innerHTML = state.items.map(u => `<li>${u.name}</li>`).join("");

  document.getElementById("pagination").innerHTML = `
    <button data-page="${state.page - 1}" ${state.page <= 1 ? "disabled" : ""}>Prev</button>
    <span>Page ${state.page} / ${state.totalPages}</span>
    <button data-page="${state.page + 1}" ${state.page >= state.totalPages ? "disabled" : ""}>Next</button>
  `;
}

// URL sync — deep-linkable
function goTo(page) {
  const url = new URL(location);
  url.searchParams.set("page", page);
  history.pushState({ page }, "", url);
  loadPage(page);
}

document.getElementById("pagination").addEventListener("click", e => {
  const p = Number(e.target.dataset.page);
  if (p) goTo(p);
});

window.addEventListener("popstate", () => {
  const p = Number(new URLSearchParams(location.search).get("page")) || 1;
  loadPage(p);
});

// Boot from URL
loadPage(Number(new URLSearchParams(location.search).get("page")) || 1);
```

## ✅ **5. Example 3 — Cursor-based pagination (infinite scroll)**

Use for large, frequently changing feeds (social posts, chat). Instead of page numbers, the server returns a `nextCursor` pointing to the next slice. This is **stable under inserts** and **fast at scale** because the DB can use an index seek instead of `OFFSET`.

**API contract**

```http
GET /api/posts?cursor=eyJpZCI6MTIzfQ&limit=20
→ 200 OK
{
  "data": [ ... 20 posts ... ],
  "nextCursor": "eyJpZCI6MTQzfQ",  // null when no more
  "hasMore": true
}
```

**Client**

```js
const state = {
  items: [],
  cursor: null,
  hasMore: true,
  loading: false,
};

async function loadMore() {
  if (state.loading || !state.hasMore) return; // guard
  state.loading = true;

  const url = new URL("/api/posts", location.origin);
  url.searchParams.set("limit", "20");
  if (state.cursor) url.searchParams.set("cursor", state.cursor);

  try {
    const res = await fetch(url);
    const { data, nextCursor, hasMore } = await res.json();
    state.items.push(...data);
    state.cursor = nextCursor;
    state.hasMore = hasMore;
    appendToDOM(data);
  } catch (err) {
    console.error(err);
  } finally {
    state.loading = false;
  }
}

function appendToDOM(items) {
  const frag = document.createDocumentFragment();
  items.forEach(p => {
    const li = document.createElement("li");
    li.textContent = p.title;
    frag.appendChild(li);
  });
  document.getElementById("feed").appendChild(frag);
}

// Trigger next page when the sentinel enters the viewport
const sentinel = document.getElementById("sentinel");
const io = new IntersectionObserver(entries => {
  if (entries[0].isIntersecting) loadMore();
}, { rootMargin: "200px" });
io.observe(sentinel);

loadMore(); // initial load
```

```html
<ul id="feed"></ul>
<div id="sentinel" style="height:1px"></div>
```

### ✅ **6. Example 4 — Backend pagination logic (Node/Express)**

**6.1. Offset pagination** — simple, supports jumping to any page.

```js
// GET /api/users?page=2&limit=10
app.get("/api/users", async (req, res) => {
  const page  = Math.max(1, parseInt(req.query.page)  || 1);
  const limit = Math.min(100, parseInt(req.query.limit) || 20); // cap to prevent abuse
  const offset = (page - 1) * limit;

  const [data, total] = await Promise.all([
    db.user.findMany({
      skip: offset,
      take: limit,
      orderBy: { id: "asc" }, // stable sort key
    }),
    db.user.count(),
  ]);

  res.json({
    data,
    page,
    limit,
    total,
    totalPages: Math.ceil(total / limit),
  });
});
```

Equivalent raw SQL:

```sql
SELECT * FROM users ORDER BY id ASC LIMIT 10 OFFSET 10;
SELECT COUNT(*) FROM users;
```

**6.2. Cursor pagination** — fast at scale, stable under inserts.

```js
// GET /api/posts?cursor=<id>&limit=20
app.get("/api/posts", async (req, res) => {
  const limit  = Math.min(100, parseInt(req.query.limit) || 20);
  const cursor = req.query.cursor ? Number(decodeCursor(req.query.cursor)) : null;

  // Fetch limit + 1 to detect if there's a next page
  const rows = await db.post.findMany({
    where: cursor ? { id: { lt: cursor } } : {}, // descending feed
    orderBy: { id: "desc" },
    take: limit + 1,
  });

  const hasMore = rows.length > limit;
  const data = hasMore ? rows.slice(0, limit) : rows;
  const nextCursor = hasMore ? encodeCursor(String(data[data.length - 1].id)) : null;

  res.json({ data, nextCursor, hasMore });
});

// Opaque base64 cursor so clients don't depend on the format
const encodeCursor = v => Buffer.from(v).toString("base64url");
const decodeCursor = v => Buffer.from(v, "base64url").toString();
```

Equivalent raw SQL (descending by id):

```sql
SELECT * FROM posts
WHERE id < :cursor      -- omit on first page
ORDER BY id DESC
LIMIT 21;               -- limit + 1 to detect hasMore
```

**Why cursor beats offset at scale**

| Query type          | Work the DB must do                                             | Behavior under inserts/deletes              |
| ------------------- | --------------------------------------------------------------- | ------------------------------------------- |
| `OFFSET 10000`      | Scans and discards 10 000 rows before returning `LIMIT` rows    | Can duplicate or skip rows if data changes  |
| `WHERE id < cursor` | Index seek directly to the cursor, then reads `LIMIT` rows      | Stable — new rows don't shift existing ones |

### ✅ **7. Best practices & gotchas**

- **Validate & cap `limit`** on the server (e.g., max 100) to prevent abuse.
- **Prefer cursor pagination at scale** — `OFFSET` gets slower as pages grow because the DB must scan and discard rows.
- **Ensure a stable sort** — always `ORDER BY` a unique column (e.g., `id` or `created_at, id`) to avoid duplicates/skips.
- **Debounce / guard** the "next page" trigger so a fast scroll doesn't fire multiple requests.
- **Use `AbortController`** to cancel stale requests when the user navigates away or changes filters.
- **Cache pages** client-side (e.g., TanStack Query, SWR) keyed by `page`/`cursor` for instant back-navigation.
- **Handle empty / error / end states** explicitly in the UI.
- **Deep linking** — sync `page`/`cursor` to the URL (`history.pushState`) so refresh and share work.
- **Accessibility** — announce page changes with `aria-live`; keep focus management correct after loading more.
- **SEO** — for public content prefer numbered pages with `rel="prev/next"` or server-rendered pages over infinite scroll.
