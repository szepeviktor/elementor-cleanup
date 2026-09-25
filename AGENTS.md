# We are doing WordPress plugin cleanup

    remove unnecessary plugin features, e.g. onboarding wizard
    remove advertisements, promotions
    remove remote tracking
    remove feedback
    hide unnecessary menu items
    add missing changelog link
    find root cause of admin notices
    remove license validation
    remove add-on version conflicts

## Current MU Plugin Contract

The implementation is the single-file MU plugin `elementor-cleanup.php`. It is loaded
before the regular Elementor plugin and intentionally uses early class stubs,
WordPress hooks, Elementor hooks, REST endpoint filtering, and narrowly scoped
CSS to suppress non-essential Elementor features.

Treat the following behavior as the current source-confirmed contract:

- keep Elementor usage tracking disabled and mark its tracking notice handled;
- suppress activation redirects, onboarding, welcome popovers, recurring editor
  upgrade notices, announcements, checklists, feedback prompts, and free-trial
  popups;
- disable Elementor AI, Angie/MCP packages, the admin top bar, Apps, cloud
  libraries, Site Navigation, Widget Creation, Pro installation prompts, and the
  Promotions module;
- prevent Elementor One feedback and notification REST behavior, including the
  top-bar feedback flow that can query `ipapi.co`;
- remove remote What's New data, Pro placeholder widgets, dashboard promotions,
  editor-panel upsells, upgrade banners, external home-screen links, promotional
  home cards, and footer rating prompts;
- redirect `elementor-home` and `elementor-one-upgrade` to
  `admin.php?page=elementor`;
- replace the JavaScript-only Elementor One flyout with regular WordPress
  submenus while preserving the capabilities and callbacks of real screens;
- hide the Plugins-page `Go Pro` action and add a `Changelog` row-meta link.

The cleanup must not disable Elementor's page builder, preview iframe, ordinary
widgets, document saving, plugin management, or compatibility checks. Do not
stub Elementor's Compatibility Tag module. Do not add license bypasses or forge
license state; license-related work is limited to removing intrusive validation
failure behavior when a concrete problem has been traced.

Early class stubs are load-order-sensitive. Before adding or changing one,
confirm the class is owned by an optional Elementor feature, compare its public
API with the installed built Elementor version, and run the full browser checks
below. Prefer an official filter or removable hook when it provides equivalent
suppression.

## Local Elementor MU Plugin E2E

Test the MU plugin with a real, built Elementor plugin in a browser.

Use email address: webmaster@appfut.hu

### Elementor Cleanup Item Audit

When the user asks for an "Elementor Cleanup Item Audit", use the E2E
environment with elevated WP-CLI and Chrome DevTools MCP:

- run WP-CLI commands with elevated permissions because the normal sandbox can
  hit bubblewrap limitations around the database socket, plugin files, and local
  service state;
- update the active Elementor plugin to the latest WordPress.org built release
  before testing;
- copy `elementor-cleanup.php` from this repository into the E2E install, or
  confirm the E2E MU-plugin symlink points to this repository copy;
- use Chrome DevTools MCP for browser navigation, snapshots, console messages,
  and network inspection;
- test each cleanup item by observing the behavior with the cleanup disabled and
  enabled, then report only browser-observed differences as E2E evidence.

Do not use the source checkout as the active WordPress plugin for E2E. This repository is the source tree and may not contain built assets. The local E2E WordPress install lives here:

    /home/viktor/chatgpt-is-super

The MU plugin under test is:

    /home/viktor/src/elementor-cleanup/elementor-cleanup.php

It is symlinked into:

    /home/viktor/chatgpt-is-super/wp-content/mu-plugins/elementor-cleanup.php

The E2E site should use the WordPress.org built Elementor package, currently installed as:

    /home/viktor/chatgpt-is-super/wp-content/plugins/elementor

Start the local site from the WordPress install directory:

    wp server --host=127.0.0.1 --port=8888

Open the site in the browser through MCP/DevTools:

    http://127.0.0.1:8888/wp-login.php

Admin login:

    admin / admin

Only count browser-observed behavior as E2E evidence. WP-CLI can be used for setup and environment checks, but feature verification should be done like a real user in the browser.

### Required Browser Checks

1. Dashboard:
   - Load `/wp-admin/index.php`.
   - Confirm the admin page does not fatal.
   - Check whether Elementor notices, dashboard widgets, news feeds, onboarding, or promotional content are visible.

2. Plugins page:
   - Load `/wp-admin/plugins.php`.
   - Confirm Elementor is active.
   - Confirm the `Go Pro` plugin action link is hidden.
   - Confirm the `Changelog` row-meta link exists.
   - Confirm plugin management still works and the page does not fatal.

3. Elementor admin:
   - Load `/wp-admin/admin.php?page=elementor`.
   - Confirm the Elementor admin shell renders.
   - Check for remaining promo links, upgrade buttons, footer rating prompts, onboarding pointers, and external video/help links.

4. Elementor home:
   - Load `/wp-admin/admin.php?page=elementor-home`.
   - Confirm it redirects to `/wp-admin/admin.php?page=elementor`.
   - Confirm the `Welcome to the new Elementor!` screen is not displayed.
   - Confirm Elementor One prompts, `Add for free`, `Upgrade`, AI, site planner, image optimization, accessibility, and other promotional cards are not displayed.
   - Load `/wp-admin/admin.php?page=elementor-one-upgrade` and confirm it redirects to the same regular Elementor admin screen.

5. New page flow:
   - Load `/wp-admin/post-new.php?post_type=page`.
   - Check whether the WordPress `Choose a pattern` starter wizard appears.
   - Click `Edit with Elementor`.
   - Confirm the Elementor editor loads.

6. Elementor editor:
   - Confirm the preview iframe renders a page instead of a WordPress/theme error.
   - Open the `Add Element` panel.
   - Check for remaining editor-panel upsells such as Pro, Site, Single, WooCommerce, Atomic Form, and `Get more with Elementor Pro`.
   - Check browser console errors and warnings.

7. Safe setup checks:
   - The E2E WordPress install must have a real active theme installed.
   - `/favicon.ico` should return `200`.
   - The Compatibility Tag module should not be stubbed; external compatibility checks should keep using the real Elementor module.

### Additional Regression Checks

- Inspect the Network panel while loading the tested admin screens. Treat calls
  to Elementor marketing, notification, telemetry, feedback, `ipapi.co`, AI,
  Angie, or MCP endpoints as regressions unless they are required functional
  requests and have been traced.
- Confirm ordinary non-Elementor WordPress admin screens do not receive broken
  layouts, missing menus, PHP warnings, or JavaScript errors from cleanup hooks.
- Confirm the regular Elementor submenus remain usable without the Elementor One
  JavaScript flyout and that the current submenu is highlighted correctly.
- Confirm promoted placeholder widgets are absent but ordinary free Elementor
  widgets remain registered and usable.
- Verify both a fresh admin account and the existing admin account when changing
  onboarding, user-meta, popover, AI, or dismissal behavior.

## Performance Baseline

The local benchmark from 2026-07-18 used WordPress 7.0.2, Elementor 4.1.5, the
PHP 7.4 WordPress development server, an authenticated administrator, warmed-up
requests, and exactly 10 repetitions per page and condition.

| Admin page | Cleanup absent | Cleanup active | Net time saved | HTML saved |
| --- | ---: | ---: | ---: | ---: |
| Dashboard | 48.32 ms | 38.11 ms | 10.21 ms (21.1%) | 26,651 bytes |
| Plugins | 41.32 ms | 36.56 ms | 4.77 ms (11.5%) | 11,701 bytes |
| Elementor admin | 41.36 ms | 36.45 ms | 4.91 ms (11.9%) | 14,392 bytes |

The isolated PHP 8.5 no-OPcache parse and hook-registration cost of
`elementor-cleanup.php` was approximately 0.43 ms over an empty include, averaged over
10 fresh processes. Across the three HTTP pages, the cleanup saved 6.63 ms per
request on average after paying its own cost, about a 15x net return relative to
that isolated cost.

This is a local regression baseline, not a production performance guarantee.
Browser asset loading, JavaScript execution, REST calls, and avoided remote
timeouts were not included. When performance is materially changed, repeat each
measurement at least 10 times, report the arithmetic mean, keep the same local
site and authenticated pages, and compare cleanup-active with cleanup-absent
conditions without changing Elementor, WordPress, theme, database, or PHP state
between them.
