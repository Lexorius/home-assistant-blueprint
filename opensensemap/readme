# OpenSenseMap Environmental Data Upload — Home Assistant Blueprint

Upload your Home Assistant sensor values to [OpenSenseMap](https://opensensemap.org) every 5 minutes.  
All sensors are optional—configure only the ones you actually have.

---

## Features
- Sends selected sensor readings to OpenSenseMap every 5 minutes
- Works with many sensor types (temperature, humidity, pressure, wind, rain, UV/solar, particulate matter, gases, battery/RSSI, etc.)
- Optional **data validation** (placeholder for future logic) and **debug logging**
- Skips uploads when no valid values are available

---

## Requirements
- Home Assistant (recent version)
- An OpenSenseMap **senseBox ID** and an **Access Token**
- (Recommended) Proper units on your HA sensors (e.g., °C, %, hPa, Lux)

---

## Installation
1. Save the blueprint file in your HA config folder, e.g.:
   ```
   config/blueprints/automation/opensensemap/opensensemap_upload.yaml
   ```
2. In Home Assistant, go to **Settings → Automations & Scenes → Blueprints** and click **Import/Reload** to make it appear.

---

## Configuration

### 1) Create an automation from the blueprint
When you create an automation from this blueprint, you’ll be asked for:

- **SenseBox ID** (required)  
  The ID of your box on OpenSenseMap.

- **Access Token** (required)  
  Your OSeM access token.

- **Per-sensor mapping (optional)**  
  For each metric you want to upload:
  - Select the **Home Assistant entity** (e.g., `sensor.outdoor_temperature`)
  - Paste the **OpenSenseMap Sensor ID** that receives this value

Tip: You can start simple (e.g., just temperature + humidity) and add more later.

- **Options**  
  - **Enable validation** (default: on). Placeholder for additional plausibility checks.
  - **Enable debug** (default: off). Writes payload and box info to **Settings → System → Logs**.

The automation runs every **5 minutes**.

---

## 2) Add the REST command to `configuration.yaml` (Required)

The blueprint uses a REST command to send the payload. Add this snippet to your `configuration.yaml` (or a file you include from there):

```yaml
rest_command:
  opensensemap_upload:
    method: POST
    url: "{{ url }}"
    headers:
      # If your OSeM token is a Bearer token, use this:
      # Authorization: "Bearer {{ token }}"

      # If your OSeM expects the token directly, use this:
      Authorization: "{{ token }}"

      Content-Type: "application/json"
    payload: "{{ payload }}"
```

> After editing `configuration.yaml`, **restart Home Assistant** or **reload YAML configuration** so the `rest_command` becomes available.

---

## How it works
- Every 5 minutes, the automation builds a JSON payload from the selected sensors.
- Only sensors with **valid values** (not `unknown/unavailable/none`) are included.
- If the payload is **empty**, no request is sent.
- The request is a `POST` to:
  ```
  https://api.opensensemap.org/boxes/<your-sensebox-id>/data
  ```
  with a JSON body mapping the **OpenSenseMap Sensor IDs** to their current **values**.

> Note: OpenSenseMap deployments may differ in token handling. If you receive `401 Unauthorized`, switch the `Authorization` header in the config to **`Bearer {{ token }}`**.

---

## Example (minimal)
- **HA entity:** `sensor.outdoor_temperature`  
- **OSeM Sensor ID (temperature):** `5f1e...abcd`

Fill those in under **Temperature** inputs. That’s it—the temperature will start uploading every 5 minutes.

---

## Debugging & Logs
- Turn on **Debug mode** in the blueprint inputs to log the built payload and box ID to **Settings → System → Logs**.
- The automation also writes a short summary to the **Logbook** after each upload.

---

## Troubleshooting

**Blueprint doesn’t show up**  
- File path must be under `config/blueprints/automation/...` and end with `.yaml`  
- No tabs or BOM in the YAML (use spaces)  
- Reload the Blueprints page or restart Home Assistant

**“Message malformed: invalid time_pattern value … minutes”**  
- Ensure the trigger uses `"/5"` (not `"*/5"`):
  ```yaml
  trigger:
    - platform: time_pattern
      minutes: "/5"
  ```

**Upload not happening**  
- Make sure you added the **`rest_command`** snippet to `configuration.yaml` and restarted HA  
- Check that at least one selected sensor has a valid numeric value  
- Look for `401 Unauthorized` → try `Authorization: "Bearer {{ token }}"` in `configuration.yaml`  
- Check **Settings → System → Logs** with **Debug mode** enabled

**OSeM sensor IDs**  
- Each metric must be mapped to the **exact Sensor ID** in your OSeM box  
- If a value isn’t arriving, double-check that the **ID matches** the intended OSeM sensor

---

## Notes
- Units are not converted beyond simple rounding; ensure your HA sensors report in the units your OSeM sensors expect.
- The “Enable validation” flag is provided for future extensions (range checks, unit guards, etc.). You can leave it on.

---

## License
Use, adapt, and share freely within your project. If you publish a modified version, please document your changes for others.
