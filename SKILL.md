---
name: who-growth-charts
description: Generate WHO child growth charts (height, weight, BMI) with percentile curves. Downloads official WHO reference data on demand. Use when users ask about child growth tracking, percentiles, or want growth charts for their kids.
version: 1.0.0
homepage: https://www.who.int/tools/child-growth-standards
metadata: {"clawdbot":{"emoji":"📈","requires":{"bins":["python3"],"python":["pandas","matplotlib","scipy","openpyxl"]}}}
---

Generate WHO Child Growth Standards charts with percentile curves and child data overlay.

## Features

- **Height-for-age** (0-19 years)
- **Weight-for-age** (0-10 years)
- **BMI-for-age** (0-19 years)
- Supports **boys and girls**
- **Downloads WHO data on demand** from cdn.who.int (cached locally)
- Overlays child's actual measurements with trend line

## Prerequisites

Install Python dependencies:
```bash
pip install pandas matplotlib scipy openpyxl
```

## Usage

### Basic Chart Generation

```bash
python3 {baseDir}/growth_chart.py "Child Name" "DD.MM.YYYY" --sex F --type all
```

Arguments:
- `name`: Child's name (used in chart title)
- `birthdate`: Date of birth in DD.MM.YYYY format
- `--sex` / `-s`: `F` (female) or `M` (male) — default: F
- `--type` / `-t`: `height`, `weight`, `bmi`, or `all` — default: all
- `--data` / `-d`: JSON file with measurement data
- `--output` / `-o`: Output directory for charts

### With Measurement Data

Create a JSON file with height/weight measurements:
```json
{
  "heights": [
    ["2024-01-15T10:00:00", 1.05],
    ["2024-06-20T09:30:00", 1.08]
  ],
  "weights": [
    ["2024-01-15T10:00:00", 17.5],
    ["2024-06-20T09:30:00", 18.2]
  ]
}
```

Heights are in **meters**, weights in **kg**. Dates are ISO format.

```bash
python3 {baseDir}/growth_chart.py "Elise" "06.07.2016" --sex F --data elise_data.json --type all
```

### Integration with Withings

Combine with `withings-family` skill to fetch weight data automatically:
```bash
# Get Withings weight data
python3 ~/clawd/skills/withings-family/withings.py elise body > /tmp/withings.json

# Parse and generate charts (you'll need to transform the JSON format)
```

## WHO Data Sources

Data is automatically downloaded from the WHO Child Growth Standards:
- `cdn.who.int/.../length-height-for-age/...`
- `cdn.who.int/.../weight-for-age/...`
- `cdn.who.int/.../body-mass-index-for-age/...`

Files are cached in `{baseDir}/cache/` after first download.

## Output

Charts are saved as PNG files:
- `{name}_height.png`
- `{name}_weight.png`
- `{name}_bmi.png`

Default output location: `{baseDir}/cache/`

## Chart Features

- **Percentile bands**: 5th, 15th, 50th (median), 85th, 95th
- **Shaded regions** between percentiles for easy reading
- **Child's data points** (red dots)
- **Trend line** (solid red) through measurements
- **Projection** (dashed red) extrapolating future growth
- **Age in years** on x-axis with year markers

## Examples

Generate all charts for a girl:
```bash
python3 {baseDir}/growth_chart.py "Erika" "24.09.2018" --sex F --type all --data erika_merged_data.json
```

Generate only height chart for a boy:
```bash
python3 {baseDir}/growth_chart.py "Max" "15.03.2020" --sex M --type height --data max_data.json
```

## Notes

- WHO weight-for-age standards only go to age 10 (120 months)
- Height and BMI standards extend to age 19 (228 months)
- First run downloads ~200KB of WHO data files
- Subsequent runs use cached data
