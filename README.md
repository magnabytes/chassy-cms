# Chassy CMS

### *The zero-dependency, drop-in admin panel that generates pure static HTML directly on virtually any web hosting platform.*

**The Problem:** You built a beautiful, static HTML website for a client. It's fast, custom, and perfect. But then the client asks: *"How do I log in and update the text myself?"* You didn't plan for a CMS, and you definitely don't want to migrate the whole project to WordPress.

**The Solution:** Drop this repository into your project folder. No database, no Node.js, no runtime PHP — just a flat-file content manager that retrofits an elegant admin UI onto any static site. Import your HTML from the dashboard, swap static text for `{{tags}}`, and hand them the keys.

**vs WordPress:** No database, no plugin updates, no PHP executing on your live site. The client edits content in a clean UI; the server just serves files. Nothing to hack, nothing to patch, nothing to break.

**vs Grav, Statamic, or Kirby:** Those are still runtime PHP — every visitor triggers server-side code. This CMS bakes static files once on publish. The public site has zero server-side execution, zero attack surface, and nothing to maintain.

**vs Hugo, Jekyll, or Eleventy:** Static site generators need a build toolchain and have no client-facing editor. This gives you the same pure static output with a browser-based UI your client can use without touching a terminal or a git repo.

> Development happens in a private repository. This repo tracks stable releases only.

---

## Why developers love it

- **True Drop-In Setup:** Copy the files directly into your existing web root. Works on Apache (cPanel, WHM, DirectAdmin, Plesk), nginx (CloudPanel, RunCloud, HestiaCP), OpenLiteSpeed (CyberPanel), and Caddy — out of the box.
- **Visual Template Import:** Go to `/admin`, click **+ Add Page**, and import your existing `index.html`.
- **Auto-Scaffolding:** Replace a paragraph in your code with `{{about_text}}` and hit save. The CMS instantly detects the new tag and scaffolds the client-facing UI.
- **100% Pure Static Output:** When the client edits text and clicks "Publish Live", it physically generates standard `index.html` files. Unbeatable speed, zero runtime vulnerabilities, and bulletproof security.
- **Multi-User Access:** Create editor accounts for clients and admin accounts for developers. Editors can update content; only admins can access templates, settings, and user management.
- **Version History:** Every publish creates a timestamped snapshot. Browse and restore any previous version with one click.

### The Client-Proof Checklist

- **Zero Maintenance:** No WordPress core updates, no broken database connections, no security patches to manage.
- **Unbreakable:** The client cannot accidentally break the layout, mess up the routing, or crash the server. They only have access to the inputs you give them.
- **Infinite Scaling:** Because the front-end is just raw HTML, the site can handle millions of hits on a tiny $5/month hosting plan without breaking a sweat.

---

## License & Commercial Usage

This project is dual-licensed to support individual creators while protecting independent development.

The source code in this repository is publicly available and licensed under the **[PolyForm Noncommercial License 1.0.0](LICENSE.md)**.

### For Hobbyists, Students, and Personal Projects

If you are using this CMS for a personal blog, a portfolio, a hobby project, a non-profit organization, or just to learn and explore, **it is 100% free forever.** You do not need to ask for permission or buy anything. Go build something cool!

### For Agencies, Freelancers, and Commercial Businesses

Under the PolyForm license, you may **not** use this software for commercial purposes — including building websites for paying clients, using it to run a profitable business, or rebranding and reselling it.

If you love the CMS and want to use it commercially, I run a flexible, hassle-free partner pilot:

- **Single-Use License:** Perfect for a single client website deployment.
- **Agency Bulk License:** Perfect for web dev studios looking to use it across multiple projects.

To get a commercial exemption certificate, request a custom volume deal, or clear up any questions, please reach out directly:

**Email:** [licensing @ magnabytes . com]

---

## Quick start (local)

```bash
git clone <repo>
cd chassy-cms
php -c dev-php.ini -S localhost:8080
```

1. Open `http://localhost:8080/admin/` — create your admin account on first load, then log in
2. Click **+ Add Page** to import your HTML or start from scratch
3. Add `{{placeholders}}` in the Template Editor, save, scaffold the fields
4. Click **Publish Live** — static files rebuild instantly

---

## How it works

```
Developer (Once)                     Client (Always)
─────────────────────────────────    ─────────────────────────────────
1. Drop repo into public_html/       1. Log in to /admin/
2. Import existing index.html        2. Click a page (Home, About…)
3. Swap text for {{placeholders}}    3. Edit text, upload photos
4. Define fields via UI or PHP       4. Click "Publish Live"
5. Hand client the keys!             5. Site is instantly live.
```

---

## Template syntax

```html
<!-- Scalar value (always HTML-escaped) -->
<h1>{{hero_title}}</h1>

<!-- Image field (relative URL from web root) -->
<img src="{{hero_image}}" alt="Hero photo">

<!-- CSS background image -->
<section style="background-image: url('{{hero_bg}}')">

<!-- Repeatable block -->
{{#menu_items}}
<div class="card">
  <h3>{{name}}</h3>
  <span>{{price}}</span>
  <p>{{description}}</p>
</div>
{{/menu_items}}

<!-- SEO / OG tags — set site_url in Settings for absolute og:image URLs -->
<title>{{page_title}}</title>
<meta name="description" content="{{meta_description}}">
<meta property="og:image" content="{{site_url}}{{og_image}}">
```

**Rules:**

- All values are HTML-escaped automatically — safe everywhere
- Missing keys render as empty string, never as an error
- Key names: letters, numbers, underscores only (`hero_title` not `hero-title`)
- Multi-line textarea content has `nl2br()` applied automatically — no extra CSS needed
- Empty image fields render a grey SVG placeholder — `src=""` never appears in live output

---

## Field types

| Type | Description | Template syntax |
|---|---|---|
| `text` | Single line of text | `{{key}}` |
| `textarea` | Multi-line text; line breaks rendered automatically | `{{key}}` |
| `image` | File upload — JPEG, PNG, WebP, max 5 MB. Four size variants generated automatically (thumbnail 300px, medium 800px, large 1200px, original). Empty fields show a grey SVG placeholder on the live site. | `{{key}}` → `data/uploads/base_1200.jpg` |
| `repeatable` | Array of items with sub-fields | `{{#key}}...{{/key}}` |

---

## Defining fields (`data/fields.php`)

Fields are defined as an associative array. The key is the placeholder name used in templates.

```php
// data/fields.php
return [

    // ── Global Fields (available in every template) ────────
    'phone' => [
        'page'        => 'global',
        'label'       => 'Phone Number',
        'type'        => 'text',
        'placeholder' => '(555) 123-4567',
        'required'    => true,
    ],

    // ── Home Page ──────────────────────────────────────────
    'hero_title' => [
        'page'        => 'index',      // matches index.template.html
        'label'       => 'Hero Headline',
        'type'        => 'text',
        'placeholder' => 'Fresh bread, baked daily',
    ],
    'hero_image' => [
        'page'  => 'index',
        'label' => 'Hero Background Photo',
        'type'  => 'image',
        'hint'  => 'Recommended: 1600×900px, under 2 MB.',
    ],
    'hero_body' => [
        'page'  => 'index',
        'label' => 'Hero Body Text',
        'type'  => 'textarea',
    ],

    // ── Menu Page ──────────────────────────────────────────
    'menu_items' => [
        'page'      => 'menu',
        'label'     => 'Menu Items',
        'type'      => 'repeatable',
        'subfields' => [
            ['key' => 'name',  'label' => 'Dish Name',   'type' => 'text'],
            ['key' => 'price', 'label' => 'Price',        'type' => 'text'],
            ['key' => 'desc',  'label' => 'Description',  'type' => 'textarea'],
        ],
    ],

];
```

### Field options

| Key | Required | Description |
|---|---|---|
| `page` | Yes | Page slug, or `'global'` for global fields |
| `label` | Yes | Human-readable name shown in the admin |
| `type` | Yes | `text`, `textarea`, `image`, or `repeatable` |
| `placeholder` | No | Ghost text inside the input |
| `hint` | No | Small note displayed below the input |
| `required` | No | `true` adds a required marker and HTML validation |
| `subfields` | Repeatable only | Array of `['key', 'label', 'type']` objects |
| `system` | No | `true` prevents deletion via the UI |

---

## Page labels (optional)

Pages are auto-named from their template filename. Override with `data/pages.php`:

```php
// data/pages.php
return [
    'index'   => ['label' => 'Home',     'desc' => 'Hero, story & specialties'],
    'menu'    => ['label' => 'Menu',     'desc' => 'All menu items and pricing'],
    'about'   => ['label' => 'About Us', 'desc' => 'Team & values'],
    'contact' => ['label' => 'Contact'],
];
```

---

## Draft → Preview → Publish

| Action | What happens | Live site? |
|---|---|---|
| **Save Draft** | Saves to `data/draft.php`, builds `_preview/` | No |
| **Publish Live** | Saves to `data/content.php`, rebuilds HTML output, creates history snapshot | **Yes** |
| **Publish Draft Live** | Promotes saved draft without re-editing (amber banner) | **Yes** |

---

## Deployment

### Quick-start

1. Upload the project files to your web root (`public_html/` on cPanel hosts)
2. Visit `https://yourdomain.com/admin/setup-check` to verify the server meets requirements
3. Visit `https://yourdomain.com/admin/` to create your first admin account
4. Go to **Settings** → set **Site URL** for correct SEO/OG image tags

That's it for **Apache** (cPanel, shared hosting, most control panels). For **nginx**, there is one extra step — see the nginx section below.

---

### Before you deploy — server requirements

| Requirement | Notes |
|---|---|
| PHP 8.0+ | Most hosts default to 8.x — set it in your control panel if needed |
| GD extension | Required for image resizing. Enable in cPanel: **Software → Select PHP Version → gd** |
| fileinfo extension | Required for upload MIME validation. Same location in cPanel |
| `data/` writable (755) | The folder PHP writes content, users, and uploads to |
| `data/uploads/` writable (755) | Nested subfolder for media — must also be writable |

**Permissions:** On most cPanel hosts, uploading via File Manager sets correct permissions automatically. If anything fails, set both folders to `755` — in cPanel File Manager: right-click the folder → **Change Permissions → 755**. Via SSH: `chmod 755 data/ data/uploads/`.

> `admin/setup-check.php` checks all of the above in your browser and tells you exactly what to fix.

---

### Apache — cPanel/WHM, Plesk, DirectAdmin, Webmin/Virtualmin, HestiaCP, shared hosting

**No extra configuration needed.** The included `.htaccess` handles everything:
- Blocks direct web access to `data/` (except `data/uploads/` which must be web-accessible for images)
- Disables PHP execution inside the uploads folder
- Sets upload size limits for both mod_php and PHP-FPM stacks

1. Upload all files to your `public_html/` (or vhost document root)
2. Visit `https://yourdomain.com/admin/setup-check` — confirm all green
3. Visit `https://yourdomain.com/admin/` — create your admin account

**Panel notes:**
- **cPanel/WHM** — works with both mod_php and EasyApache PHP-FPM pools. No changes needed.
- **Plesk (Apache + nginx proxy mode)** — `.htaccess` still applies; the nginx proxy passes through to Apache.
- **DirectAdmin** — works with CustomBuild 2 Apache and the default Apache+nginx combo.
- **Webmin/Virtualmin** — works with Virtualmin's default Apache + mod_php or PHP-FPM.
- **HestiaCP** — works in Apache-only and Apache+nginx reverse proxy mode.

If Apache has `AllowOverride None` set (uncommon on shared hosts), `.htaccess` files are ignored. Ask your host to enable `AllowOverride All` for your document root, or check your `httpd.conf`/`apache2.conf`.

---

### nginx — CloudPanel, RunCloud, HestiaCP (nginx-only), Plesk (nginx mode), bare VPS

**One extra step required.** nginx does not read `.htaccess` files — you need to add the security rules to your nginx config.

> **Clean URLs note:** The **Clean URLs** toggle in Settings only modifies `.htaccess` and has no effect on nginx. Clean URLs are handled by the `try_files $uri $uri/ $uri.html =404;` rule already included in `nginx-example.conf` — they work automatically without toggling anything.

The project includes `nginx-example.conf` with the complete `server {}` block. The part you actually need (the security and PHP rules) are the `location {}` blocks inside it.

**Bare VPS (no panel):**
```bash
cp nginx-example.conf /etc/nginx/sites-available/yoursite.conf
# Edit: server_name, root path, and fastcgi_pass socket path
ln -s /etc/nginx/sites-available/yoursite.conf /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

**Panel-managed nginx (CloudPanel, RunCloud, Plesk, HestiaCP):**

Most panels generate their own `server {}` wrapper and expose a field for extra directives. You only need to paste the `location {}` blocks — **not** the outer `server {}` wrapper.

Open `nginx-example.conf` and copy everything between the outer `server { ... }` braces:

```nginx
# Match .htaccess upload limits (also set in .user.ini for PHP-FPM)
client_max_body_size 16M;

# /admin clean URL
location = /admin  { return 301 /admin/; }
location = /admin/ { try_files /admin/index.php =404;
                     fastcgi_pass unix:/run/php/php8.3-fpm.sock;
                     fastcgi_index index.php;
                     include fastcgi_params;
                     fastcgi_param SCRIPT_FILENAME $document_root/admin/index.php; }

# Block PHP execution in uploads (files still served)
location ^~ /data/uploads/ {
    try_files $uri =404;
    location ~* \.(php\d*|phtml|phar|pl|cgi)$ { deny all; }
}

# Block all other data/ paths
location ^~ /data/ { deny all; }

autoindex off;

location ~ \.php$ {
    try_files $uri =404;
    fastcgi_pass unix:/run/php/php8.3-fpm.sock;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}

location / {
    try_files $uri $uri/ $uri.html =404;
}
```

Paste this into your panel's **"Additional nginx configuration"** field for the domain (the exact label varies by panel), then save and reload nginx.

**Finding your PHP-FPM socket path** — change `unix:/run/php/php8.3-fpm.sock` to match your server. Common paths are listed in `nginx-example.conf`. If you're unsure, run this on the server:
```bash
find /run -name "*.sock" 2>/dev/null | grep php
```
Or check your panel's PHP configuration page — it usually displays the active socket or port.

---

### OpenLiteSpeed — CyberPanel

`.htaccess` compatibility in OLS covers all the security rules. Copy files to your vhost document root, then:

1. In CyberPanel go to **Websites → List Websites → Manage → Rewrite Rules** and click **Save & Build** to force OLS to recompile the `.htaccess` rules
2. Upload limits are read from `.user.ini` automatically

---

### Caddy v2

Use the included `Caddyfile`:

```bash
cp Caddyfile /etc/caddy/Caddyfile  # or paste the site block into your existing Caddyfile
# Edit the domain, document root, and fastcgi socket path at the top
systemctl reload caddy
```

Socket path options are listed in the `Caddyfile` header comments.

---

### Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `/admin/` shows a blank page or 500 error | `data/` not writable | `chmod 755 data/` — or check File Manager permissions |
| "Could not write users file" on first setup | `data/` not writable | Same as above |
| Images upload but don't resize | GD extension missing | Enable `gd` in cPanel PHP extensions |
| Images upload but fail silently | fileinfo extension missing | Enable `fileinfo` in cPanel PHP extensions |
| `/data/config.php` is accessible in the browser (shows blank or source) | `.htaccess` not being read by nginx | Add the `location ^~ /data/` block from `nginx-example.conf` |
| `/admin/` works but `/admin` (no slash) gives 404 | nginx missing the clean-URL rule | Add the `location = /admin` redirect from `nginx-example.conf` |
| Upload limit errors on large images | `.user.ini` not loaded | Confirm your PHP runs as FPM — for mod_php stacks the `.htaccess` `php_value` lines handle it |

> Visit `https://yourdomain.com/admin/setup-check` at any time to re-run the environment check.

---

> **Security reminder:** Never commit `data/users.php` or `data/content.php` to a public repository.

---

## File structure

```
/
├── .htaccess                    ← blocks data/ from web access (except data/uploads/)
│
├── admin/
│   ├── index.php                ← Dashboard — pages list
│   ├── page.php                 ← Per-page field editor (?p=index, ?p=about, etc.)
│   ├── global-fields.php        ← Alias: redirects to page.php?p=global
│   ├── settings.php             ← Admin branding & site URL (admin-only)
│   ├── users.php                ← User management — list, create, edit, delete (admin-only)
│   ├── help.php                 ← Developer & user guide
│   ├── media.php                ← Media library — upload, browse, delete images
│   ├── media-api.php            ← GET JSON: image picker modal data
│   ├── media-delete.php         ← POST: delete image + clear content refs + rebuild
│   ├── media-upload.php         ← POST: direct upload to library
│   ├── image-variant.php        ← POST: generate/regenerate a single image variant
│   ├── save.php                 ← POST: save content + trigger build
│   ├── publish.php              ← POST: promote draft to live
│   ├── scaffold.php             ← POST: scaffold new page fields (admin-only)
│   ├── field-edit.php           ← GET/POST: edit field label/placeholder/required
│   ├── field-delete.php         ← POST: delete field + remove content references
│   ├── template.php             ← Template editor (admin-only)
│   ├── history.php              ← Version history list
│   ├── history-restore.php      ← POST: restore a history snapshot
│   ├── reset.php                ← POST: factory-reset site content (admin-only)
│   ├── login.php / logout.php
│   ├── admin.css                ← Shared admin stylesheet
│   ├── _auth.php                ← Session auth guard; auth_is_admin() / auth_require_admin()
│   ├── _csrf.php                ← csrf_token() / csrf_verify()
│   ├── _content.php             ← content_read() / content_write() / atomic_write()
│   ├── _fields.php              ← fields_write() — rewrites fields.php from array
│   ├── _image.php               ← Image resize, variants, thumbnail helpers
│   ├── _history.php             ← history_snapshot() / history_list() / history_restore()
│   ├── _shell_head.php          ← App shell: sidebar nav + breadcrumb header
│   ├── _shell_foot.php          ← App shell: closes content + footer chrome (pair with _shell_head.php)
│   ├── _footer.php              ← Shared page footer
│   ├── _users.php               ← users_read() / users_write() / users_find_by_username()
│   ├── _admin_settings.php      ← Loads $admin_brand, $admin_color, $admin_site_url
│   └── build/
│       └── builder.php          ← build_site() — template compiler
│
├── data/
│   ├── config.php               ← Path constants
│   ├── fields.php               ← Field definitions
│   ├── pages.php                ← Optional page labels/descriptions
│   ├── content.php              ← Live content (PHP-wrapped JSON — never commit publicly)
│   ├── draft.php                ← Unpublished draft (transient, same format)
│   ├── users.php                ← User accounts (PHP-wrapped JSON — never commit publicly)
│   ├── admin_settings.php       ← Admin branding (site_name, site_url, brand_color)
│   ├── templates/               ← *.template.html files
│   ├── uploads/                 ← Image uploads (web-accessible; PHP execution blocked)
│   └── history/                 ← Timestamped content snapshots
```

---

## User roles

| Role | Can do |
|---|---|
| **Admin** | Everything: content, templates, settings, user management, factory reset |
| **Editor** | Content editing and publishing only — no access to templates, settings, or users |

---

## Requirements

- PHP 8.0+ with `fileinfo` and `gd` extensions
- **Apache**, **nginx**, **OpenLiteSpeed**, or **Caddy v2** (all supported — see Deployment below)
- Write permissions on `data/` and `data/uploads/`

---

## Security

- Passwords stored as bcrypt hashes in `data/users.php`
- Multi-user: admin and editor roles with enforced page-level access control
- All POST handlers call `csrf_verify()` before any processing
- Image uploads validated by MIME type via `finfo`, never by file extension; re-encoded through GD on receipt to strip embedded metadata and payloads
- PHP execution disabled in `data/uploads/` via `.htaccess`
- All `data/` files that aren't PHP-wrapped are either blocked by server config (Apache: `.htaccess`; nginx: `nginx-example.conf`) or use `<?php die('Forbidden'); ?>` as line 1 — `content.php`, `draft.php`, `users.php`, `admin_settings.php`, and `login_attempts.php` are protected at the PHP level and safe on any server
- `data/` (including `data/templates/` and `data/history/`) blocked from public web access via `.htaccess` (Apache) or the provided nginx/Caddy config — `data/uploads/` is the only exception, so images remain web-accessible

### Trust model

**Admin role = developer trust.** The template editor lets admins write raw HTML, CSS, and JavaScript that is baked directly into the public site. This is intentional — it is how you set up and customise the site. An admin account should only be given to the developer or someone with equivalent trust.

**Editor role = client trust.** Editors can only edit content within the fields you define. They cannot access templates, field definitions, settings, or user management. They cannot inject JavaScript into the site.

**Recovering a locked-out account.** If an admin password is forgotten and no other admin exists, run this from the server command line (SSH, cPanel Terminal):

```bash
php admin/reset-password.php <username> <new-password>
# List all users:
php admin/reset-password.php --list
```

---

## Credits

Built by [ma-tt](https://github.com/ma-tt)
