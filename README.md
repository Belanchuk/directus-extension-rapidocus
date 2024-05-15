# Rapidocus OpenApi Viewer

Rapidoc + Directus = Rapidocus

The extension allows you to view interactive API documentation from OpenAPI Specification of your Directus and customize the appearance.

## Settings

1. Display mode: view | read | focused
2. Show method in navbar: false | as-plain-text | as-colored-text | as-colored-block
3. Navbar active item: left-bar | colored-block

## TODO

1. Add saving settings.
2. Add some settings from the documentation: https://rapidocweb.com/api.html
3. Add saving external OpenAPIs and switching between them.

## Installation

1. Find this module on your Directus Marketplace and install it.
2. Go to Settings -> Project Settings, in the Modules section, enable module Rapidocus.
3. Reload the page.
4. Go to Rapidocus in left menu.

## Note

1. Access to your Directus methods is determined by the current user's access settings.
2. No need to add an access_token.
3. Sandbox enabled, doesn't request scopes.

## Shoutout

Based on the awesome rapidoc component by [@mrin9](https://github.com/mrin9/RapiDoc)

Inspired by https://github.com/u12206050/directus-extension-api-viewer-module
