# Chart schema — chronotherapy SVG

Inline visualization of the full trip's day-by-day schedule. Rendered via `mcp__visualize__show_widget` after the markdown plan.

## Layout

- **One row per day** of the protocol (typically 8–14 rows: 2 pre-flight + travel + adaptation + pre-return + travel + recovery).
- **X-axis**: 24-hour timeline, 00:00–24:00 in destination clock time *during destination phases*, home clock time *during pre-flight and recovery phases*. Mark zone changes with a dashed vertical line and a "TZ→ {{tz}}" label.
- **Y-axis**: stacked rows, each labeled `{{day_name}} {{date}} D±{{n}}`.
- **Block widths**: proportional to duration on the 24-h axis.

## Color palette

| Block type | Fill color | Border | Pattern |
|---|---|---|---|
| Sleep | `#1a2540` (dark navy) | none | solid |
| Strong-seek light | `#f59e0b` (amber) | none | solid |
| Mild-seek light | `#fde68a` (light yellow) | `#f59e0b` thin | solid |
| Soft-avoid light | `#9ca3af` (medium grey) | `#6b7280` thin | dashed border |
| Full-avoid light | `#374151` (dark grey) | none | solid |
| Caffeine OK | `#92400e` (brown) | none | hatched (//) |
| No-caffeine | `#fef3c7` (cream) | `#92400e` thin | solid |
| Wind-down | `#cbd5e1` (light slate) | none | solid |
| Melatonin dose marker | `#7c3aed` (purple) | white outline | small filled circle, 8 px diameter |
| DLMO line | `#10b981` (emerald) | none | thin curve overlay |
| Travel block | `#0ea5e9` (sky blue) | none | striped |

Use semantic CSS variables when rendering — the visualize MCP exposes design tokens.

## Required overlays

1. **Departure and arrival markers**: vertical dashed lines at the exact times, labeled `↑ Depart {{flight_number}}` and `↓ Arrive {{airport}}`.
2. **DLMO drift overlay**: a thin curve drawn across the rows tracking the modeled DLMO time across the trip. Helps the user see *why* doses and light windows shift each day.
3. **CBT_min markers (optional, helpful)**: small downward-pointing triangles at the modeled CBT_min on each day.

## Layout dimensions

- Total width: 800 px (desktop) or 360 px (mobile — derived from `read_me` platform parameter)
- Row height: 32 px
- Row spacing: 4 px
- Left label column: 120 px (day label)
- Top header: 30 px (24-hour scale)
- Bottom legend: 60 px

## Example minimal SVG skeleton

```svg
<svg viewBox="0 0 800 480" xmlns="http://www.w3.org/2000/svg">
  <!-- Header: 24h scale -->
  <g transform="translate(120, 0)">
    <line x1="0" y1="20" x2="680" y2="20" stroke="#cbd5e1"/>
    <!-- Hour ticks every 60 min, label every 3h -->
  </g>

  <!-- Day rows -->
  <g transform="translate(0, 30)">
    <!-- D-2 row -->
    <text x="0" y="20" font-size="12">Sun 10 May (D−2)</text>
    <g transform="translate(120, 0)">
      <!-- sleep block -->
      <rect x="0" y="6" width="180" height="20" fill="#1a2540"/>
      <!-- mild-seek -->
      <rect x="180" y="6" width="240" height="20" fill="#fde68a" stroke="#f59e0b"/>
      <!-- strong-seek -->
      <rect x="540" y="6" width="80" height="20" fill="#f59e0b"/>
      <!-- caffeine ok -->
      <rect x="180" y="26" width="220" height="6" fill="#92400e" opacity="0.5"/>
      <!-- melatonin dose -->
      <circle cx="600" cy="16" r="4" fill="#7c3aed" stroke="white"/>
    </g>

    <!-- More day rows here -->
  </g>

  <!-- DLMO drift overlay -->
  <path d="M120,16 L800,80" stroke="#10b981" stroke-width="1.5" fill="none"/>

  <!-- Legend -->
  <g transform="translate(40, 420)">
    <!-- color squares + labels -->
  </g>
</svg>
```

## Reading the chart

The user should be able to glance at the chart and see:
- **Sleep blocks drifting** in the protocol direction across the trip
- **Strong-seek light** sitting at the leverage windows (biological evening for delays, biological morning for advances)
- **Melatonin dose markers** clustered in the appropriate window for the direction
- **Caffeine windows shrinking** on the days closest to a long-haul flight
- **The DLMO curve sliding** across the trip from origin phase to destination phase

This visual encodes the entire protocol in one image. For a 9-day trip the chart fits in one screen and replaces ~3 pages of prose.

## Mobile adaptation

When `platform == "mobile"` from `read_me`, set viewBox width to 360 px and rotate day labels 90° if needed. Reduce hour-tick density to every 6 h.

## Title and footer

- Title (above SVG): `{{home_tz}} → {{dest_tz}} — {{magnitude}}h {{direction}} — {{trip_dates}}`
- Footer: `Algorithm: jet-lag-protocol skill. Plan saved at {{plan_file_path}}.`
