# Home Assistant: SE3 Power Price Forecast

Pulls day-ahead / multi-day SE3 electricity price forecasts from
[eupowerprices.com](https://eupowerprices.com) into Home Assistant, and charts
them with the [apexcharts-card](https://github.com/RomRider/apexcharts-card)
custom Lovelace card.

## What's included

- `packages/se3_power_price.yaml` — REST sensor + template sensors
  - `sensor.se3_power_price_forecast` — current-hour price (EUR/MWh) with the
    full forecast `series` array as an attribute
  - `sensor.se3_price_next_hour` — price one hour from now
  - `sensor.se3_peak_next_24h` / `sensor.se3_low_next_24h` — high/low over the
    next 24 hourly data points
- `lovelace/se3_apexchart.yaml` — an apexcharts-card config that charts the
  full forecast window in ¢/kWh with color-coded price bands
- `secrets.yaml.example` — placeholder for your API key

## Requirements

- A [eupowerprices.com](https://eupowerprices.com) API key with SE3 access
- [HACS](https://hacs.xyz/) with the
  [apexcharts-card](https://github.com/RomRider/apexcharts-card) installed,
  for the chart

## Setup

1. **Add your API key** to your real `secrets.yaml` (do not commit this file):

   ```yaml
   eupowerprices_api_key: "YOUR_API_KEY_HERE"
   ```

2. **Enable packages** in `configuration.yaml`, if not already:

   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```

3. **Copy** `packages/se3_power_price.yaml` into your own `packages/`
   directory (or merge its `sensor:`/`template:` keys into your existing
   config if you don't use packages).

4. **Reload YAML**: Developer Tools → YAML → Reload All YAML Configuration
   (or restart Home Assistant).

5. **Verify**: Developer Tools → States → search
   `sensor.se3_power_price_forecast`. State should be a real EUR/MWh number,
   and the `series` attribute should contain the full forecast array.

6. **Add the chart**: create a new Lovelace card → Manual → paste the
   contents of `lovelace/se3_apexchart.yaml`.

## Notes

- The API's forecast horizon can start a day or two ahead of "now" depending
  on the model run — the sensor always falls back to the first available data
  point rather than showing `unknown` if there's no exact match for the
  current hour.
- The chart converts EUR/MWh → ¢/kWh (÷10) to match the color-threshold scale
  (0–50 ¢/kWh).
- The API currently only returns a forecast/prediction series, not a
  separate "settled/actual" spot price series. If you have a Nordpool
  integration for historical actuals, that could be added as a second
  `series:` entry in the chart.

## License

MIT — do whatever you want with this.
