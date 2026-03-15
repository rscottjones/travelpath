# Travelpath for Micro.blog

Interactive flight-path maps and travel routes, powered by Leaflet. Supports journey mode (multi-leg trips) and hub-and-spoke mode (all routes radiating from a central point).

---

## Installation

Copy the `travelpath/` folder into your Micro.blog plugin directory. The plugin follows Hugo v0.91 conventions.

---

## Quick start

1. Create `data/travelpath/mytrip.yml` (settings) and `data/travelpath/mytrip-segments.yml` (stops).
2. Add `{{< travelpath "mytrip" >}}` to any post.

---

## File structure

```
travelpath/
├── layouts/shortcodes/travelpath.html          ← shortcode
├── layouts/partials/travelpath-styles.html     ← CSS (emitted once per page)
├── layouts/partials/travelpath-shapes.html     ← shared JS: basemaps, icons, loaders
├── layouts/partials/travelpath-media-styles.html ← lightbox/video CSS
├── static/travelpath/                          ← (empty — all deps loaded from CDN)
└── data/travelpath/
    ├── iceland.yml                  ← example: simple journey
    ├── iceland-segments.yml
    ├── california.yml               ← example: rich journey with legend
    ├── california-segments.yml
    ├── flights-from-phx.yml         ← example: hub-and-spoke
    └── flights-from-phx-segments.yml
```

---

## Settings file (mapname.yml)

All fields are optional. Hub mode is activated when `hub_lat` + `hub_lon` are both present.

```yaml
title: "Japan Trip 2023"
description: "Two weeks across Honshu"

# Hub mode — omit for journey mode
hub_lat: 33.4373
hub_lon: -112.0078
hub_label: "Phoenix"
hub_icon: plane
hub_color: "#3b82f6"

# Map display
basemap: osm           # osm | voyager | positron | topo | satellite | streets | …
height: 500px
center_lat: 36.5       # optional — auto-fits if omitted
center_lon: 137.0
zoom: 6

# Lines
line_style: great-circle  # great-circle | straight | dashed | dotted | chevron
                           # combinable: great-circle-dashed, straight-chevron, …
line_color: "#3b82f6"
line_weight: 3
line_opacity: 0.7

# Pins
pins: true
pin_color: "#3b82f6"
pin_icon: circle       # circle | pin | star | plane | train | car | boat | fa-plane | …

# Stats
stats: true
stats_unit: km         # km | mi

# Grid
grid: true
tiles: name            # name | short
arrows: true           # show › between journey tiles
```

---

## Segments file (mapname-segments.yml)

```yaml
- order: 1
  label: Tokyo
  lat: 35.6762
  lon: 139.6503
  pin: true
  icon: train
  color: "#ef4444"
  line_style: straight
  description: "4 nights in Shinjuku"
  url: https://myblog.com/tokyo
  url_text: "Tokyo post"
  legend: Rail
  short: TYO
```

| Field | Notes |
|-------|-------|
| `label` | Required. Display name. |
| `lat`, `lon` | Required. |
| `order` | Sort order. Uses YAML position if absent. |
| `pin` | Override global pins setting per-stop. |
| `icon` | Pin icon (same set as Maps/Tracks). |
| `color` | Line color FROM this stop TO the next. |
| `line_style` | Line style FROM this stop TO the next. |
| `description` | Popup body text. |
| `url` | Popup link. Auto-detects image/video/audio by extension. |
| `url_text` | Link label (default: "Read more →"). |
| `url_type` | Force: `image` / `video` / `audio` / `link`. |
| `legend` | Groups stops into a legend category. |
| `short` | Abbreviated label for compact tiles. |

**Outgoing model:** A segment's `color` and `line_style` apply to the line drawn *from that stop to the next*. The last segment is terminal — it draws no outgoing line.

---

## Shortcode parameters

```
{{< travelpath map="japan-2023" >}}
{{< travelpath "japan-2023" >}}
{{< travelpath map="japan-2023" height="600px" line_color="#ef4444" >}}
{{< travelpath map="japan-2023" grid="no" stats="no" >}}
```

| Param | Default | Notes |
|-------|---------|-------|
| `map` / positional 0 | required | Name of YAML settings file |
| `height` | `500px` | CSS height |
| `basemap` | `osm` | Basemap style |
| `line_color` | `#3b82f6` | Default line color (does not override per-segment colors) |
| `line_style` | `great-circle` | Default line style |
| `line_weight` | `3` | Line width in px |
| `stats` | `yes` | `yes` / `no` |
| `stats_unit` | `km` | `km` / `mi` |
| `grid` | `yes` | `yes` / `no` |
| `pins` | `yes` | `yes` / `no` |
| `arrows` | `yes` | `yes` / `no` — journey › arrows |

---

## Basemaps

| Key | Style |
|-----|-------|
| `osm` | OpenStreetMap (default) |
| `voyager` | Carto Voyager |
| `positron` | Carto Positron (minimal) |
| `topo` | OpenTopoMap |
| `cycle` | CyclOSM |
| `satellite` | Esri World Imagery |
| `streets` | Esri World Street Map |
| `esri-topo` | Esri World Topo |
| `esri-relief` | Esri Shaded Relief |
| `natgeo` | National Geographic |
| `usgs-topo` | USGS Topo (US only) |
| `usgs-hybrid` | USGS Imagery + Topo (US only) |

---

## Icons

Built-in shapes: `pin`, `pin-small`, `circle`, `disc`, `bullseye`, `star`, `heart`, `camera`, `video`, `image`, `gallery`, `mountain`, `landmark`, `utensils`, `droplet`, `binoculars`, `microphone`, `flag-checkered`, `triangle-exclamation`, `point`

Transport aliases: `plane`, `train`, `car`, `boat`, `bus`, `hike`, `bike`

Numbered circles: `circle-0` through `circle-99`

Font Awesome: any `fa-` icon name (e.g. `fa-plane`, `fa-ship`)

Emoji: any unicode character (e.g. `✈`, `🚄`)

---

## URL / media handling in popups

| Extension | Behavior |
|-----------|----------|
| `.jpg .png .gif .webp` | Lightbox |
| `.mp4 .mov .webm .m3u8` | Video overlay |
| `.mp3 .m4a .aac .wav` | Inline audio player |
| Anything else | Opens in new tab |

Override with `url_type: image / video / audio / link` per segment.

---

## Dependencies (CDN, loaded on demand)

| Library | Loaded when |
|---------|-------------|
| Leaflet 1.9.4 | Always |
| Leaflet.Geodesic 2.7.1 | `great-circle` line style |
| leaflet-polylinedecorator 1.6.0 | `chevron` line style |
| Font Awesome Free 6.5.1 | Any `fa-` icon |
| hls.js | `.m3u8` video URLs |

---

## Compatibility

Targets Hugo v0.91 (Micro.blog). Uses `.Page.Scratch`, `index` for hyphenated data keys, `findRE` instead of `strings.Contains`, and `jsonify` + `safeJS` for data injection.
