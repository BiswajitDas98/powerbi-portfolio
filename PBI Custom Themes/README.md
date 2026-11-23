# Glass themes for Power BI

Four Power BI report themes in a frosted-glass style: a soft gradient canvas, translucent cards with rounded corners,
clean Segoe UI text and an 8-colour chart palette that colour-blind readers can tell apart. Each comes in a dark and a
light version.

## Themes

| File | Name in Power BI | Look |
|---|---|---|
| `Survey_Analytics_Steel_Blue_Dark.json` | Steel Blue Glass - Dark | Near-black canvas with soft steel-blue glows |
| `Survey_Analytics_Steel_Blue_Light.json` | Steel Blue Glass - Light | Pale blue-grey canvas with soft steel-blue glows |
| `Survey_Analytics_Glacier_Grey_Teal_Dark.json` | Glacier Grey-Teal Glass - Dark | Near-black canvas with soft grey-teal glows |
| `Survey_Analytics_Glacier_Grey_Teal_Light.json` | Glacier Grey-Teal Glass - Light | Pale grey-teal canvas with soft grey-teal glows |

Each theme has its background image built in. The images are also in `../Dashboard_background/` as 1920×1080 JPGs.

## Apply a theme

1. Open the report in Power BI Desktop.
2. Go to **Design → Import Themes → Browse for themes**.
3. Select one of the JSON files.

A theme changes default formatting only. A visual that was formatted by hand keeps its own settings until you select
**Reset to default** in its format pane.

## What the themes set

| Area | Dark themes | Light themes |
|---|---|---|
| Page | Background image, scaled to fill a 16:9 page; canvas colour around it | Same |
| Visuals | White at 94% transparency (frosted glass), thin light border, 18 px rounded corners | White at 38% transparency, thin grey border, 18 px rounded corners |
| Headline numbers | White, Segoe UI Semibold 28 pt | Dark ink, Segoe UI Semibold 28 pt |
| Titles and headers | Light text, Segoe UI Semibold 12 pt | Dark text, Segoe UI Semibold 12 pt |
| Labels, axes, legends | Soft grey-white and grey | Slate and grey |
| Tables and matrices | Dark rows with alternate shading, subtle gridlines | White rows with alternate shading, subtle gridlines |
| Slicers | Dark items, light text | White items, dark text |
| Status colours (good / neutral / bad) | `#6EE7B7` / `#FCD34D` / `#FCA5A5` | `#157F47` / `#8A5300` / `#B42323` |

### Colour values per theme

| | Steel Blue Dark | Steel Blue Light | Glacier Dark | Glacier Light |
|---|---|---|---|---|
| Canvas | `#080C11` | `#E6EBF1` | `#060C0E` | `#E7EEF0` |
| Accent | `#6B9AC4` | `#3F74A6` | `#7FB3BF` | `#2F6B78` |
| Text | `#E8ECF0` | `#0F1B2A` | `#E9F1F3` | `#0B1A1E` |
| Secondary text | `#C8D0D9` | `#334155` | `#C6D3D6` | `#2C3F44` |
| Muted text | `#97A3B0` | `#5B6B7D` | `#93A6AA` | `#56696E` |
| Card / alternate row | `#171B1F` / `#12161B` | `#F6F7FA` / `#F1F4F7` | `#151B1C` / `#101618` | `#F6F9F9` / `#F2F6F7` |
| Gridlines | `#212429` | `#DFE2E8` | `#1F2426` | `#DFE4E7` |
| Border | `#2B2E32` | `#D1D8DF` | `#292E30` | `#D2DADE` |

The accent colours the table highlights, links and the middle of the low-to-high colour scale.

## Chart colours

All four themes use the same eight hues, set slightly brighter on dark canvases and slightly deeper on light ones.

| # | Hue | Dark themes | Light themes |
|---|---|---|---|
| 1 | Blue | `#3987E5` | `#2A78D6` |
| 2 | Orange | `#D95926` | `#EB6834` |
| 3 | Aqua | `#199E70` | `#1BAF7A` |
| 4 | Yellow | `#C98500` | `#EDA100` |
| 5 | Pink | `#D55181` | `#E87BA4` |
| 6 | Green | `#008300` | `#008300` |
| 7 | Violet | `#9085E9` | `#6250D6` |
| 8 | Red | `#E66767` | `#E34948` |

- Power BI gives the colours to series in this order. To keep a category in the same colour across visuals, sort it
  the same way everywhere, or set its colour once under **Format → Colors**.
- Neighbouring colours stay distinct for colour-blind readers. Still, give charts with many series data labels or a
  legend, so colour is never the only cue.
- On the light themes, orange, aqua, yellow and pink are fainter against the white cards. Keep data labels on in charts
  that use them.
- More than eight series is hard to read in any palette. Group the smallest into "Other".

## Full-page web content

If a page shows a web page or dashboard through an HTML content visual that covers the whole page, turn off
**Background** and **Border** on that visual. Otherwise the theme's card frame shows around it.

## Changing a theme

- **Colours and fonts:** edit the JSON file in any text editor, then import it again.
- **Background image:** it is stored in the JSON as a base64 JPG under `visualStyles.page.*.background[0].image.url`.
  - To use another image, replace that value with your own data URI (`data:image/jpeg;base64,…`).
  - Or delete the `"page"` block and set the image in Power BI under **Format → Canvas background → Image**, with
    **Image fit: Fill** and transparency 0%.

## Troubleshooting

| Problem | Fix |
|---|---|
| "We couldn't import your theme" | Delete the `"page"` block from the JSON, import it again, then add the matching JPG from `../Dashboard_background/` under Format → Canvas background → Image (Fill, 0% transparency). |
| A visual doesn't change | It was formatted by hand. Use **Reset to default** in its format pane. |
| A category's colour differs between visuals | Sort it the same way everywhere, or set its colour under Format → Colors. |
| A frame shows around a full-page web visual | Turn off Background and Border on that visual. |

| Output |
<img width="1312" height="738" alt="THEM1" src="https://github.com/user-attachments/assets/736addce-7f04-46bf-944e-d4677e2e1807" />
<img width="1318" height="750" alt="THEM1_L" src="https://github.com/user-attachments/assets/ce3d5bb4-76ae-4362-9024-eb7ff57ca8cf" />
<img width="1315" height="741" alt="THEM2" src="https://github.com/user-attachments/assets/fd14cb5e-a56d-494f-a499-43d57bbc9bf4" />
<img width="1314" height="742" alt="THEM2_L" src="https://github.com/user-attachments/assets/9cc03217-faee-4eea-82ff-c4aa5677c236" />
