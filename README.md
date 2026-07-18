#  Power Price Forecast base on [eupowerprices.com](https://eupowerprices.com) 

Pulls 14 day electricity price forecasts from [eupowerprices.com](https://eupowerprices.com) into Home Assistant, and chartsthem with the [apexcharts-card](https://github.com/RomRider/apexcharts-card) custom Lovelace card.

## What's included

- `packages/power_price.yaml` — REST sensor + template sensors
  - `sensor.power_price_forecast` — current-hour price (EUR/MWh) with the full forecast `series` array as an attribute
  - `sensor.price_next_hour` — price one hour from now
  - `sensor.peak_next_24h` / `sensor.low_next_24h` — high/low over the next 24 hourly data points
- `lovelace/apexchart.yaml` — an apexcharts-card config that charts the full forecast window in ¢/kWh with color-coded price bands
- `secrets.yaml.example` — placeholder for your API key

## Requirements

- A [eupowerprices.com](https://eupowerprices.com) API key with desire Area access 
  Areas:
  -   Nordics & Baltics: 
        **DK1** - Denmark West
        DK2 - Denmark East
        EE - Estonia
        FI - Finland
        NO1 -  Norway East
        NO2 -  Norway South
        NO3 -  Norway Central
        NO4 -  Norway North
        NO5 -  Norway West
        SE1 -  Sweden North
        SE2 -  Sweden North-Central
        SE3 -  Sweden Central
        SE4 -  Sweden South
  -   Central & Western Europe
        AT - Austria
        BE - Belgium
        CZ - Czechia
        DE - Germany
        FR - France
        NL - Netherlands
        PL - Poland
        SK - Slovakia

- [HACS](https://hacs.xyz/) with the   [apexcharts-card](https://github.com/RomRider/apexcharts-card) installed, for the chart

## Setup
0.  **Get your API key from eupowerprices**

  Go to https://eupowerprices.com/api/login/ and write your email. \
  Press Send Sign-in Link and confirm your email.\
  After login go to https://eupowerprices.com/api/portal/ and request access for your Area.\
  Wait until you receive approval. (may be few days) \
  Generate  API Key

1. **Add your API key** to your real `secrets.yaml` (do not commit this file):

   ```yaml
   eupowerprices_api_key: "YOUR_API_KEY_HERE"
   ```

2. **Enable packages** in `configuration.yaml`, if not already:

   ```yaml
    homeassistant:
      packages:
        forecast: !include  Forecast/power_price.yaml
   ```

3. **Copy** `Forecast/power_price.yaml` into your own / Create `Forecast/`
   directory (or merge its `sensor:`/`template:` keys into your existing
   config if you don't use packages).

4. **Reload YAML**: Developer Tools → YAML → Reload All YAML Configuration
   (or restart Home Assistant).

5. **Verify**: Developer Tools → States → search
   `sensor.power_price_forecast`. State should be a real EUR/MWh number,
   and the `series` attribute should contain the full forecast array for
   the next 14 days.

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
