# vuvi.org — статичный блог про презентации с ИИ

Сайт на чистом HTML: **без сборки, без зависимостей, без JavaScript**.
Канонический адрес — <https://vuvi.org/>, репозиторий — <https://github.com/roxappdev/vuvi>.

## Структура

```
vuvi/
├── index.html                 ← главная (список статей)        → https://vuvi.org/
├── styles.css                 ← один файл стилей для всего сайта
├── 404.html                   ← страница «не найдено»           → https://vuvi.org/404.html
├── robots.txt                 ← правила для краулеров + sitemap
├── sitemap.xml                ← карта сайта (все 11 URL)
├── feed.xml                   ← RSS 2.0, 10 статей
├── llms.txt                   ← краткий индекс для LLM/ИИ-краулеров
├── og-image.png               ← 1200×630, картинка для соцсетей и OG-разметки
├── apple-touch-icon.png       ← 180×180, иконка для iOS
├── CNAME                      ← кастомный домен для GitHub Pages (vuvi.org)
├── .nojekyll                  ← отключает сборку Jekyll на GitHub Pages
├── README.md
└── blog/
    ├── ai-presentation-workflow/index.html      → /blog/ai-presentation-workflow/
    ├── prompt-formula-for-slides/index.html     → /blog/prompt-formula-for-slides/
    ├── ai-presentation-tools-compared/…         → /blog/ai-presentation-tools-compared/
    ├── turn-documents-into-slides/…             → /blog/turn-documents-into-slides/
    ├── prompt-templates-by-type/…               → /blog/prompt-templates-by-type/
    ├── edit-ai-generated-slides/…               → /blog/edit-ai-generated-slides/
    ├── fix-ai-slide-design/…                    → /blog/fix-ai-slide-design/
    ├── ai-speaker-notes-qa-prep/…               → /blog/ai-speaker-notes-qa-prep/
    ├── repurpose-one-deck-many-assets/…         → /blog/repurpose-one-deck-many-assets/
    └── ai-presentation-mistakes/…               → /blog/ai-presentation-mistakes/
```

Каждая статья — это папка с `index.html`, поэтому адрес выглядит как
`vuvi.org/blog/НАЗВАНИЕ_СТАТЬИ/` (любой веб-сервер отдаёт `index.html` для директории автоматически).

## SEO: что где прописано

| Что | Где | Значение |
| --- | --- | --- |
| Канонический адрес | `<link rel="canonical">` в `<head>` каждой страницы | `https://vuvi.org/…` |
| hreflang | `hreflang="en"` и `x-default` | указывают на сам канонический URL |
| Индексация | `<meta name="robots">` | `index, follow, max-image-preview:large` |
| Open Graph | `og:type`, `og:site_name`, `og:title`, `og:description`, `og:url`, `og:image` | картинка `https://vuvi.org/og-image.png` (1200×630) |
| Twitter Card | `twitter:card=summary_large_image` | та же картинка и описание |
| Структурированные данные | `JSON-LD` (`schema.org`) | главная — `WebSite` + `Organization` + `Blog` со списком статей; статьи — `BlogPosting` + `BreadcrumbList` |
| Даты публикации | `article:published_time` + `datePublished`/`dateModified` в JSON-LD | ISO 8601 |
| Карта сайта | `sitemap.xml` | 11 URL с `lastmod`, `changefreq`, `priority` |
| Правила обхода | `robots.txt` | разрешено всё, явно разрешены GPTBot, ClaudeBot, PerplexityBot и др. |
| RSS | `feed.xml` + `<link rel="alternate" type="application/rss+xml">` | 10 статей, даты в RFC 822 |
| Соцсети/превью | `og-image.png`, `apple-touch-icon.png` | абсолютные URL, чтобы работали и из подпапок |
| Тема браузера | `theme-color` | светлая `#0b7a55`, тёмная `#161819` |
| 404 | `404.html` | `noindex, follow` + ссылки на статьи |

После публикации новой статьи обновите три файла: `sitemap.xml` (`<loc>` + `lastmod`),
`feed.xml` (новый `<item>` первым) и `llms.txt` (строка в списке).

## Деплой

### GitHub Pages (текущий вариант)

1. Изменения уходят в ветку `main` репозитория `roxappdev/vuvi`.
2. GitHub Pages публикует её из корня (`/`) — файл `.nojekyll` отключает Jekyll,
   чтобы папки `blog/<slug>/` отдавались как есть.
3. Файл `CNAME` содержит `vuvi.org`: в настройках репозитория
   (Settings → Pages → Custom domain) домен подхватывается автоматически.
4. В DNS у домена должны быть записи:
   - `A` для `vuvi.org` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - `AAAA` (по желанию) → `2606:50c0:8000::153`, `2606:50c0:8001::153`,
     `2606:50c0:8002::153`, `2606:50c0:8003::153`
   - `CNAME` для `www` → `roxappdev.github.io`
5. После проверки домена включите **Enforce HTTPS**.
6. Откройте `https://vuvi.org/robots.txt`, `https://vuvi.org/sitemap.xml`,
   `https://vuvi.org/feed.xml` — все три должны отдавать содержимое, а не 404.

### Свой сервер

1. Скопируйте **содержимое** репозитория в корневую директорию сайта
   (например `/var/www/vuvi.org`, или через панель хостинга / FTP / git).
2. Проверьте, что сервер отдаёт `index.html` для директорий:
   - **nginx** — по умолчанию включено (`index index.html;`);
   - **Apache** — по умолчанию включено (`DirectoryIndex index.html`);
   - **Caddy** — по умолчанию включено.
3. Настройте `404.html` как страницу ошибки:
   - nginx: `error_page 404 /404.html;`
   - Apache: `ErrorDocument 404 /404.html`

## Локальный просмотр

Ссылки на сайте относительные и работают на сервере. Для локальной проверки
из папки сайта запустите:

```
python3 -m http.server 8000
```

и откройте `http://localhost:8000`. При простом открытии `index.html` двойным
кликом (file://) сайт тоже работает, но внутренние ссылки на директории
(`blog/…/`) в некоторых браузерах откроют список файлов вместо страницы.

## Как добавить статью

1. Скопируйте любую папку `blog/<slug>/` под новым именем и отредактируйте
   её `index.html` (заголовок, дата, текст, ссылки «предыдущая/следующая»).
2. Скопируйте блок SEO из `<head>` соседней статьи и замените в нём:
   `canonical`, три `hreflang`, `og:url`, `og:title`, `og:description`,
   `twitter:title`, `twitter:description`, `article:published_time`,
   а в JSON-LD — `headline`, `description`, `url`, `mainEntityOfPage`,
   `datePublished`, `dateModified`, `timeRequired` и хлебные крошки.
3. Добавьте пункт `<li>…</li>` в начало списка `<ol class="post-list">`
   на главной (`index.html`) и `BlogPosting` в JSON-LD этой же страницы.
4. Обновите `sitemap.xml`, `feed.xml`, `llms.txt` и `lastBuildDate` в фиде.
5. Поправьте блок `.post-nav` у соседних статей, чтобы цепочка
   «предыдущая / следующая» осталась непрерывной.

## Правки дизайна

Все стили в одном файле `styles.css` — меняются один раз для всего сайта.
Тёмная тема включается автоматически по настройкам системы
(`prefers-color-scheme`), отдельно делать ничего не нужно.
