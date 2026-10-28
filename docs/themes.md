# Interface themes

A theme is a JSON file that sets every colour the interface draws, plus its spacing and text
sizes. Light and Dark ship with RustGIS; users add their own to the themes folder. The map is
drawn by the renderer, which takes its light or dark defaults from the theme's `dark` flag.

## Where themes live

| Theme | File |
|---|---|
| Built in | `crates/ui-egui/themes/*.json`, compiled into the app |
| User | `<config>/themes/*.json`, read at startup, when a file changes, and by Reload Themes |

The config folder is `%APPDATA%\RustGIS` on Windows, `~/Library/Application Support/RustGIS` on
macOS and `$XDG_CONFIG_HOME/rustgis` elsewhere. `RUSTGIS_CONFIG_DIR` overrides it.

A user file whose `name` matches a built-in replaces it: a reworked Light sits in the folder as
`light.json`, and the menu lists one Light.

## Picking one

Settings › Theme lists Follow the System and every theme that loaded. A choice is applied at once
and saved for the next start; 80% to 200% of interface scale sits beside it.

The quickest way to begin is to take a theme file from anywhere else with **Load a Theme from
File…**, which keeps a copy in the themes folder: edit that copy and the app follows it. A file
that does not load is listed in red at the bottom of the same menu, with the token at fault.

A saved file is picked up within a second, without pressing **Reload Themes**: the app watches the
folder's files while it runs. A file that was in use and stops loading leaves the window on the
nearest built-in until it loads again, with the reason listed in the menu.

## A short theme

`extends` names a theme that ships, and the file then sets only what it changes:

```json
{
  "schema": 1,
  "name": "Sunset",
  "extends": "Dark",
  "colours": {
    "pane": "#241710",
    "ribbon": "#35221A",
    "text": "#FFE7D1",
    "accent": "#F2A65A",
    "on_accent": "#2A1B12"
  }
}
```

Colours, brightness and sizes it leaves out come from the theme it extends. Without `extends`
every colour is required, as in the full file below: that is the reference, a short file is the
better start.

## The file

```json
{
  "schema": 1,
  "name": "Sunset",
  "dark": true,
  "colours": {
    "header": "#2A1B12",
    "ribbon": "#35221A",
    "tab_strip": "#2A1B12",
    "canvas": "#1A1009",
    "pane": "#241710",
    "pane_header": "#35221A",
    "border": "#5A3B2A",
    "border_strong": "#7A5138",
    "text": "#FFE7D1",
    "text_dim": "#D3A985",
    "text_disabled": "#8A6B52",
    "icon": "#F5D3B3",
    "hover": "#4A2F20",
    "pressed": "#6B3F26",
    "checked": "#5C3A24",
    "accent": "#F2A65A",
    "accent_text": "#FFC98B",
    "on_accent": "#2A1B12",
    "input": "#1F130D",
    "input_border": "#6B4632",
    "group_label": "#C99871",
    "status_bar": "#20140E",
    "selection": "#7A4A2B",
    "menu": "#2E1D14",
    "shadow": "#0000006E",
    "green": "#7FC29B",
    "red": "#EF7A6A",
    "amber": "#F2C14E",
    "context": "#4FB3A5",
    "context_tint": "#1E3C39",
    "control": "#4A3020"
  },
  "metrics": {
    "item_spacing": [7.0, 6.0],
    "button_padding": [10.0, 4.0],
    "row_height": 24.0,
    "corner_radius": 8,
    "body": 13.0,
    "button_text": 13.0,
    "small": 11.0,
    "heading": 18.0,
    "mono": 12.5
  }
}
```

Every colour is required unless the file extends a theme, and an unknown token stops the file from
loading with the token named. Colours are `#RRGGBB`, or `#RRGGBBAA` where alpha matters, as in
`shadow`. `schema` is 1; a file written for a later build is refused by number.

`dark` is the brightness the theme reports to egui, which sets its default widget shapes and the
map's light or dark defaults. It is not inferred from the colours.

`metrics` are in points: item spacing, button padding, row height, corner radius and text sizes,
required unless the file extends a theme. Sizes are held to what a window can draw, and a file over
64 KB is not read.

## Tokens

Surfaces: `header`, `ribbon`, `tab_strip`, `canvas`, `pane`, `pane_header`, `menu`, `status_bar`,
`input`.

Lines and text: `border`, `border_strong`, `input_border`, `text`, `text_dim`, `text_disabled`,
`icon`, `group_label`, `on_accent`.

Interaction: `hover`, `pressed`, `checked`, `selection`, `control` (slider rails and checkbox
wells, which must stay visible on white).

Meaning: `accent`, `accent_text`, `context`, `context_tint`, `green`, `red`, `amber`, `shadow`.

## Commands

| Command | Effect |
|---|---|
| `ui.themeFile` with `name` | Uses that theme; no name follows the system again |
| `ui.themeLoad` | Opens a theme file from anywhere, keeps it in the themes folder and uses it |
| `ui.theme` with `dark` | Switches brightness and leaves the theme list |
| `ui.scale` with `factor` | Interface scale, held between 0.75 and 2.0 |
| `ui.themeReload` | Reads the themes folder again |

## What a theme cannot change

The map is drawn by the renderer, so a theme's colours stop at the map view; `canvas` is the
surface behind it. Icon shapes stay as drawn, taking their ink and accent from the theme. Plugin
panels use the host's widgets and follow it.
