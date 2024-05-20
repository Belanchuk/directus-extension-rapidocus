# Rapidocus OpenApi Viewer

Rapidoc + Directus = Rapidocus

View any OpenAPI Specification in your Directus and customize the appearance.

## Settings

### General

- Display mode: view | read | **focused**\*
- Allow try: **true**\* | false

### Navigation

- Show method in navbar: false | as-plain-text | as-colored-text | **as-colored-block**\*
- Navbar active item: **left-bar**\* | colored-block
- Show info: true | **false**\*
- Allow server selection: true | **false**\*
- Allow authentication: true | **false**\*
- Persist auth: true | **false**\*
- Allow search: **true**\* | false
- Allow advanced search: **true**\* | false
- Item spacing: compact | **default**\* | relaxed
- Font size: **default**\* | large | largest
- Use path in navbar: true | **false**\*

\* default value

## TODO

1. Add Api Viewer Module Compatibility.

## Installation

1. Find this module on your Directus Marketplace and install it.
2. If Api Viewer Module is installed, disable it.
3. Go to Settings -> Project Settings, in the Modules section, enable module Rapidocus.
4. Reload the page.
5. Go to Rapidocus in left menu.

## Note

1. Access to your Directus methods is determined by the current user's access settings.
2. No need to add an access_token.
3. Sandbox enabled, doesn't request scopes.
4. Incompatible with the Api Viewer Module.
5. If during local development you have a duplicate link to the server, then set the Directus variable to PUBLIC_URL=http://localhost:8055. Correct the link if necessary.

## Shoutout

Based on the awesome rapidoc component by [@mrin9](https://github.com/mrin9/RapiDoc)

Inspired by https://github.com/u12206050/directus-extension-api-viewer-module
