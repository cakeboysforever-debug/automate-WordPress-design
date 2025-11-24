# Automation Playbook

This playbook outlines how to deliver the Fiverr packages quickly and consistently using scripts, templates, and checklists.

## Intake & Prep
1. **Client intake form** collects brand assets, palette, fonts, copy bullets, contact emails, and payment provider preference.
2. **Create a project folder** with `/templates/brand-tokens.json` populated from the intake form.
3. **Spin up environment**
   - Local: `wp-env start` or Docker + WP-CLI.
   - Hosting: use host one-click install or `wp core download` + `wp config create`.

## Provision WordPress via WP-CLI
```bash
# scripts/wp-provision.sh (pseudo)
wp core install --url="$SITE_URL" --title="$SITE_NAME" --admin_user="$ADMIN" --admin_password="$PASS" --admin_email="$EMAIL"
wp plugin install elementor seo-by-rank-math wp-mail-smtp wpforms-lite cache-enabler wordfence --activate
wp theme install generatepress --activate
wp theme install generatepress-child.zip --activate
wp option update blogdescription "$TAGLINE"
```

## Page & Content Automation
- **Starter blocks import:** use Elementor/Block Patterns export files. Apply brand tokens via a replace script (e.g., `jq` and `envsubst`).
- **Page scaffolding:**
  ```bash
  wp post create --post_type=page --post_title='Home' --post_status=publish
  wp post create --post_type=page --post_title='About' --post_status=publish
  wp post create --post_type=page --post_title='Contact' --post_status=publish
  ```
- **Menus:** `wp menu create "Main" && wp menu item add-post main home --title="Home"` etc.
- **Forms:** import a JSON preset, then patch notification recipients with `wp option patch`.

## E-commerce (Premium)
- Install WooCommerce and run the setup wizard with defaults; import demo products to visualize layout.
- Configure Stripe/PayPal sandbox, then swap to live keys for final handoff.
- Add performance plugins (cache, image compression) and validate with PageSpeed/GTmetrix.

## QA & Handoff
- Run `wp theme mod get` to confirm brand tokens applied.
- Lighthouse or PageSpeed check for Core Web Vitals; note scores in the delivery report.
- Export backup: `wp db export` + `wp export`. Provide migration steps (All-in-One WP Migration or `wp db import` + `wp search-replace`).
- Rotate admin password and issue editor-level account to client.
- Record a short Loom walkthrough; attach to delivery.

## Delivery Artifacts
- **Delivery checklist** (per-package) with pass/fail boxes.
- **Change log** with plugin versions and revisions applied.
- **Credentials sheet** (temporary admin, editor account, SMTP keys if applicable).

## How to Reuse Templates
- Keep block templates in `/templates/blocks/` and brand token files in `/templates/brand/`.
- Add new industries by cloning tokens + swapping imagery; save presets as `.json` for forms and `.xml` for page exports.
- Version assets in Git; tag releases per package update.
