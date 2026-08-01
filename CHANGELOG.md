# Changelog

## 1.0.0 — 2026-08-01

First release. Chassy CMS is a small admin panel that turns editable content into a fully static website — no database, no server-side app running behind your live site, just HTML/CSS files any host can serve.

**Content editing**
- Text, multi-line text, link, image, and repeatable-list fields, organized per page (plus site-wide "global" fields available on every page)
- Draft → Preview → Publish workflow — make changes and preview them before they go live
- Version history — every publish is snapshotted; restore any previous version in one click
- Paste in your own HTML template and Chassy detects the fields for you automatically

**Media**
- Upload images once and get automatically-sized thumbnail/medium/large/original versions
- Photos are automatically corrected for orientation and stripped of hidden location/camera metadata

**Team access**
- Multi-user accounts with Admin and Editor roles — Editors can update content but not touch settings, templates, or users
- Simple one-time setup on first visit — no default password to change

**Included demo site**
- Ships with a complete sample site ("The Copper Bean," a coffee & bakery demo) so there's something real to explore immediately, not a blank page
- Factory Reset (Settings → Danger Zone) can wipe everything back to blank for your own site, or restore the demo content if you want to see it again

**Reliability & security**
- Every save is protected against cross-site request forgery and lost-write conflicts from editing in multiple tabs
- Uploads are validated by their actual file content, not just the filename
- Your content files are blocked from direct web access at the server level
- A built-in environment checker (`/admin/setup-check.php`) verifies your server is configured correctly, with plain-language fixes for common hosting issues

**Upgrading**
- Upgrading to a future version is just replacing the `admin` folder on your server — your content, images, uploads, and user accounts are never touched, no database migration, no re-running setup.
