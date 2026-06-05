# Hugo Migration Guide for web192.de

This is a clear step-by-step migration checklist for moving `web192.de` from WordPress to Hugo.

## Quick start commands

Copy and paste these commands in your terminal from the repository root.

```bash
# 1. Install Hugo on Linux
sudo apt update && sudo apt install hugo

# 2. Create a new Hugo site in the repository root
hugo new site .

# 3. Add Hugo Plate as the theme (replace with the actual theme repo URL)
git clone https://github.com/<plate-theme-repo>.git themes/plate

# 4. Start the Hugo development server
hugo server -D
```

> If the repo already contains site files, do not run `hugo new site .` unless the root is empty except for `.git`.

## 0. Should you use Hugo for web192.de?

- Use Hugo if your site is mostly static pages, blog posts, and a few widgets (newsletter, contact, payment).
- Hugo works well with Brevo newsletter forms and Wero payment widgets because they are added as HTML/JS.
- If you want faster performance, simpler hosting, and less WordPress backend maintenance, Hugo is a good fit.
- Keep WordPress if you need frequent non-technical editing, many plugins, user accounts, or custom PHP features.

## 1. Install Hugo and initialize the site

```bash
cd /home/uwe/dev/web192
sudo apt update && sudo apt install hugo
hugo new site .
```

If the repository already has files, instead use a temporary folder:

```bash
mkdir ../web192-hugo
cd ../web192-hugo
hugo new site .
```

## 2. Add Hugo Plate and verify the theme

```bash
cd /home/uwe/dev/web192
git clone https://github.com/<plate-theme-repo>.git themes/plate
```

In `config.toml`, set the theme:

```toml
theme = "plate"
```

If the theme provides `exampleSite/`, copy the example config and content structure:

```bash
cp -r themes/plate/exampleSite/config.toml .
cp -r themes/plate/exampleSite/content ./content
cp -r themes/plate/exampleSite/static ./static
```

Then run:

```bash
hugo server -D
```

Open `http://localhost:1313` and confirm the theme renders.

## 3. Configure the Hugo site for web192.de

In `config.toml` or `config.yaml` update the site metadata:

```toml
baseURL = "https://web192.de/"
languageCode = "de-de"
title = "web192.de"
```

Add these sections if the theme uses them:

```toml
[params]
  description = "Dein Name oder Business"
  author = "web192"
```

## 4. Migrate WordPress content to Hugo

### Export from WordPress

1. In WordPress admin go to `Tools > Export`.
2. Export `All content` and save the XML.

### Convert pages and posts to Markdown

Use a tool or manually convert.

Option A: use a migration tool (replace with actual tool commands):

```bash
# Example using an exporter tool if installed
wordpress-to-hugo-exporter export.xml
```

Option B: create Markdown manually:

```bash
hugo new posts/first-post.md
hugo new page/about.md
```

Add front matter to each file, for example:

```markdown
---
title: "Meine Seite"
date: 2026-06-05
draft: false
tags: [newsletter, kontakt]
---

Hier ist der Text der Seite.
```

## 5. Move images and static files

Copy WordPress uploads into Hugo static files:

```bash
mkdir -p static/images
cp -r /path/to/wordpress/wp-content/uploads/* static/images/
```

Update image links in Markdown to use `/images/...`.

## 6. Use the Hugo Plate structure and homepage

Use the theme's default homepage and sections. If you need to override templates, copy theme files into your root `layouts/`:

```bash
mkdir -p layouts/partials
cp themes/plate/layouts/_default/baseof.html layouts/_default/baseof.html
```

Add a visible contact section on the homepage by editing the theme's homepage template or by adding a partial in `layouts/partials/contact.html`.

## 7. Add Brevo newsletter integration

Place the Brevo form code into a partial or your homepage template.

Example partial file:

```html
<!-- layouts/partials/newsletter.html -->
<form action="https://app.brevo.com/forms/your-form-endpoint" method="post">
  <input type="email" name="EMAIL" placeholder="Deine E-Mail" required>
  <button type="submit">Newsletter abonnieren</button>
</form>
```

Then include it where you want it shown:

```go
{{ partial "newsletter.html" . }}
```

## 8. Add Wero payment integration

Put the Wero checkout widget or button into the page where customers start payment.

Example:

```html
<!-- layouts/partials/payment.html -->
<script src="https://checkout.wero.com/widget.js"></script>
<button id="wero-checkout">Bezahlen</button>
<script>
  document.getElementById('wero-checkout').addEventListener('click', function() {
    Wero.open({
      productId: 'YOUR_PRODUCT_ID',
      amount: 'XX.XX',
      currency: 'EUR'
    });
  });
</script>
```

Then include it in your page or partial.

## 9. Add the contact section

Create a contact partial or static page with visible email, phone, and form link.

Example partial:

```html
<!-- layouts/partials/contact.html -->
<section id="contact">
  <h2>Kontakt</h2>
  <p>Schreib uns an <a href="mailto:kontakt@web192.de">kontakt@web192.de</a></p>
  <p>Oder nutze unser Kontaktformular.</p>
</section>
```

Include this on the homepage or site header.

## 10. Add SEO integration for Google

In `layouts/_default/baseof.html`, add metadata tags:

```html
<title>{{ if .Title }}{{ .Title }} | {{ end }}{{ .Site.Title }}</title>
<meta name="description" content="{{ if .Params.description }}{{ .Params.description }}{{ else }}{{ .Site.Params.description }}{{ end }}">
<meta property="og:title" content="{{ .Title }}">
<meta property="og:description" content="{{ .Params.description }}">
<meta property="og:url" content="{{ .Permalink }}">
```

For Google Search Console, add the verification meta tag from Google directly into the site header.

## 11. Run and verify locally

```bash
hugo server -D
```

Check:
- homepage renders correctly
- contact section is visible
- newsletter form is shown
- Wero payment button appears
- pages and posts load

## 12. Build production files

```bash
hugo
```

Then deploy the contents of `public/` to your hosting.

## Full command list for copy/paste

```bash
# Install Hugo
sudo apt update && sudo apt install hugo

# Initialize site (only if root is empty except .git)
hugo new site .

# Add Hugo Plate theme
git clone https://github.com/<plate-theme-repo>.git themes/plate

# Set theme in config
# (Open config.toml and add theme = "plate")

# Run local preview
hugo server -D

# Create new content files
hugo new posts/first-post.md
hugo new page/about.md

# Copy media from WordPress uploads
mkdir -p static/images
cp -r /path/to/wordpress/wp-content/uploads/* static/images/

# Build production site
hugo
```

## Notes

- Replace `https://github.com/<plate-theme-repo>.git` with the real Hugo Plate repository URL.
- Replace `/path/to/wordpress/wp-content/uploads/` with your actual WordPress upload folder.
- Use `config.toml` or `config.yaml` consistently depending on the theme example.

Good luck. Follow the numbered steps, and use the commands exactly as they appear here.
