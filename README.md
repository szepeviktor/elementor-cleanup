# Elementor Cleanup

**Elementor is a page builder, not an advertising platform.**

Elementor Cleanup is a zero-settings WordPress MU plugin that removes Elementor's
AI prompts, tracking, onboarding, upsells, feedback requests, remote promotions,
and admin clutter.

One PHP file. No settings screen. No database configuration. No blanket hiding of
important WordPress notices.

If you maintain Elementor websites for clients, install it and get back to work.
If it saves you time, **star this repository**. Stars tell me it is worth tracking
and removing the next round of distractions after every Elementor release.

## Why this exists

Client websites need a predictable workspace, not a recurring sales funnel.

Elementor adds onboarding flows, AI entry points, upgrade prompts, promotional
widgets, remote news, feedback requests, and navigation that changes between
releases. Individually, each item is small. Across many client sites, they create
confusion, support questions, accidental clicks, visual noise, and maintenance
work.

Elementor Cleanup turns that experience into a quieter, conventional WordPress
admin interface.

## What it removes

The plugin currently targets **38 sources of Elementor admin noise**, including:

- usage tracking and tracking prompts;
- activation redirects, onboarding, welcome popovers, checklists, and free-trial
  prompts;
- Elementor AI, Angie, and MCP packages;
- Elementor One notifications, feedback requests, upgrade screens, and top bar;
- remote What's New data, external home-screen links, and promotional cards;
- Go Pro links, editor-panel upsells, upgrade banners, and Pro placeholder widgets;
- promotional dashboard widgets and footer rating prompts;
- Apps, cloud libraries, Site Navigation, Widget Creation, and other non-essential
  promotional modules;
- the JavaScript-only Elementor One menu flyout.

It also adds a direct **Changelog** link to Elementor's row on the Plugins screen
and restores regular WordPress submenus for real Elementor screens.

## What it deliberately keeps

Cleanup should not mean breaking the product you use.

The plugin does **not** disable:

- the Elementor page builder;
- the preview iframe;
- ordinary free widgets;
- document saving;
- plugin management;
- Elementor compatibility checks;
- genuine WordPress errors and operational warnings.

It does not forge an Elementor license or unlock paid features.

## Why not use a generic notice-hiding plugin?

Generic cleanup plugins usually hide rendered notices using broad CSS, JavaScript,
or text matching. That can conceal warnings you actually need.

Elementor Cleanup is narrow and Elementor-specific. Where possible, it disables
the responsible Elementor hook, module, package, feed, redirect, or REST behavior
at its source. Narrowly scoped CSS is used only for interface elements that cannot
be removed cleanly through an available hook.

There is no control panel to configure on every client site. Install the file and
the same opinionated cleanup is applied consistently.

## Installation

Elementor Cleanup is a must-use plugin. WordPress loads it automatically; there is
no activation step.

From the WordPress root directory:

```bash
mkdir -p wp-content/mu-plugins
curl -fsSL \
  https://raw.githubusercontent.com/szepeviktor/elementor-cleanup/master/elementor-cleanup.php \
  -o wp-content/mu-plugins/elementor-cleanup.php
```

Alternatively, download [`elementor-cleanup.php`](elementor-cleanup.php) and copy it to:

```text
wp-content/mu-plugins/elementor-cleanup.php
```

To uninstall it, remove that file. Elementor's original interface returns on the
next request.

## Measured local impact

Removing admin features can also remove work from admin requests. In a local
authenticated benchmark with WordPress 7.0.2 and Elementor 4.1.5, using ten warmed
runs per page, the following differences were measured:

| Admin page | Cleanup absent | Cleanup active | Net time saved | HTML saved |
| --- | ---: | ---: | ---: | ---: |
| Dashboard | 48.32 ms | 38.11 ms | 10.21 ms (21.1%) | 26,651 bytes |
| Plugins | 41.32 ms | 36.56 ms | 4.77 ms (11.5%) | 11,701 bytes |
| Elementor admin | 41.36 ms | 36.45 ms | 4.91 ms (11.9%) | 14,392 bytes |

These numbers are a regression baseline from one local environment, not a
production performance guarantee. Browser asset loading, JavaScript execution,
remote requests, server configuration, and site-specific plugins will change the
result.

## Compatibility and expectations

Elementor Cleanup requires **Elementor 4.x** and **PHP 7.4 or PHP 8.x**.

Elementor changes frequently, including internal modules and admin markup. Test
updates on a staging site before deploying them across client websites.

If a new promotion appears or an Elementor update causes a regression, open an
issue and include:

- the WordPress, Elementor, Elementor Pro, and PHP versions;
- the affected admin URL;
- a screenshot of the remaining or broken interface;
- relevant browser console or network errors.

Reports with reproducible evidence are much easier to fix.

## Contributing

Pull requests should remain narrowly focused on removing non-essential Elementor
behavior without affecting page building, editing, saving, previewing, ordinary
widgets, plugin management, or compatibility checks.

## Independent project

Elementor Cleanup is an independent community project. It is not affiliated with,
endorsed by, or supported by Elementor Ltd.

---

**Elementor without the sales funnel.**

If that is the admin experience you want for your clients, install it, share it,
and [star the repository](https://github.com/szepeviktor/elementor-cleanup).
