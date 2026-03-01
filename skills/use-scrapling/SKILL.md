---
name: use-scrapling
description: Scrape websites using the scrapling library. Use when the user wants to extract data from a website, crawl pages, parse HTML, or fetch web content — especially when the site may use JavaScript rendering or anti-bot protection. Trigger on keywords like scrape, crawl, extract data from a website, fetch web page, or parse HTML.
---

# SKILL: use-scrapling

Scrapling is an adaptive Python web scraping library that handles static pages, JavaScript-rendered content, and anti-bot protected sites through a unified API.

## Running scrapling

Always run scripts with `uv run --with scrapling` — no installation, venv, or `pyproject.toml` required. Works in any project.

```bash
uv run --with scrapling python scrape.py
```

Write the scraping logic to a `.py` file, then execute it this way.

## Step 1: Choose the right fetcher

Select based on the target site's characteristics:

| Site type | Fetcher | When to use |
|-----------|---------|-------------|
| Static HTML | `Fetcher` | Fast, no JS needed, no bot protection |
| Anti-bot / Cloudflare | `StealthyFetcher` | Bot detection, Cloudflare, fingerprinting |
| JavaScript-rendered | `DynamicFetcher` | Content only visible after JS executes |
| Multi-page with login | `*Session` variants | Cookie persistence across requests |

**If unsure**, start with `Fetcher`. If you get blocked or see empty content, escalate to `StealthyFetcher`, then `DynamicFetcher`.

## Step 2: Fetch the page

### Static HTML — `Fetcher`

```python
from scrapling.fetchers import Fetcher

page = Fetcher.get('https://example.com', stealthy_headers=True)
print(page.status)  # 200
```

### Anti-bot protected — `StealthyFetcher`

```python
from scrapling.fetchers import StealthyFetcher

page = StealthyFetcher.get('https://example.com', headless=True)
print(page.status)
```

### JavaScript-rendered — `DynamicFetcher`

```python
from scrapling.fetchers import DynamicFetcher

page = DynamicFetcher.get(
    'https://example.com',
    wait_selector='.target-element',  # wait for element before parsing
    headless=True,
)
print(page.status)
```

### Session (cookies / login flows)

```python
from scrapling.fetchers import FetcherSession  # or StealthySession, DynamicSession

with FetcherSession(impersonate='chrome') as session:
    session.post('https://example.com/login', data={'user': '...', 'pass': '...'})
    page = session.get('https://example.com/dashboard')
```

## Step 3: Extract data

All fetchers return the same `Selector` object — extraction syntax is identical regardless of which fetcher was used.

### CSS selectors

```python
# Single value
title = page.css('h1::text').get()

# All matching values
links = page.css('a::attr(href)').getall()

# Element with nested access
for item in page.css('.product'):
    name  = item.css('.name::text').get()
    price = item.css('.price::text').get()
    print(name, price)
```

### XPath

```python
title = page.xpath('//h1/text()').get()
rows  = page.xpath('//table/tr').getall()
```

### Text search

```python
# Find element containing specific text
el = page.find_by_text('Add to Cart', first_match=True)
```

### Structured output

```python
import json

results = []
for item in page.css('.result'):
    results.append({
        'title': item.css('h2::text').get(),
        'url':   item.css('a::attr(href)').get(),
        'desc':  item.css('p::text').get(),
    })

print(json.dumps(results, indent=2))
```

## Step 4: Multi-page crawl (Spider)

Use `Spider` when scraping more than a handful of pages.

```python
from scrapling.spiders import Spider, Response

class MySpider(Spider):
    name = "my_spider"
    start_urls = ["https://example.com/listings"]

    async def parse(self, response: Response):
        for item in response.css('.listing'):
            yield {
                'title': item.css('h2::text').get(),
                'price': item.css('.price::text').get(),
                'url':   item.css('a::attr(href)').get(),
            }

        # Follow pagination
        next_page = response.css('a.next::attr(href)').get()
        if next_page:
            yield response.follow(next_page, callback=self.parse)

result = MySpider().start()
result.items.to_json('output.json')
print(f"Scraped {len(result.items)} items → output.json")
```

Run with:

```bash
uv run --with scrapling python scrape.py
```

## Common options

```python
# HTTP method
page = Fetcher.post('https://api.example.com/data', json={'key': 'value'})

# Custom headers / proxy
page = Fetcher.get(
    'https://example.com',
    headers={'Accept-Language': 'en-US'},
    proxy='http://user:pass@proxy:8080',
)

# DynamicFetcher: scroll before capturing
page = DynamicFetcher.get('https://example.com', scroll_down=True)

# DynamicFetcher: execute JS before capturing
page = DynamicFetcher.get(
    'https://example.com',
    wait_selector='.loaded',
    execute_js="window.scrollTo(0, document.body.scrollHeight)",
)
```

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Empty selectors / missing content | Switch to `DynamicFetcher` — page likely needs JS |
| 403 / CAPTCHA / bot block | Switch to `StealthyFetcher` |
| Inconsistent results across runs | Use `find_by_text` or similarity matching instead of brittle CSS paths |
| Need to stay logged in | Use a `*Session` variant |
| Large crawl is slow | Increase `Spider` concurrency settings |
