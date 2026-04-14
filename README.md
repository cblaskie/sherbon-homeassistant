# Sherbon Home Ops
This repository manages the GitOps deployment for the smart home infrastructure, including Home Assistant VM configurations, Traefik routing, and ESPHome nodes for Mitsubishi HVAC control.

---

## Network Map (The "Site Plan")
All IoT devices are sequestered on the Sherbon_IoT hidden network (VLAN 30).

| IP Address    | Hostname        | Location      | Hardware             | Purpose                  |
| :---          | :---            | :---          | :---                 | :---                     |
| 10.69.30.1    | gateway         | Server Rack   | UniFi UDM Pro        | Network Gateway          |
| 10.69.30.50   | ecobee_main     | Hallway       | Ecobee Smart         | Main Floor HVAC          |
| 10.69.30.51   | sun_porch_ac    | Sun Porch     | XIAO ESP32-S3 Sense  | Mitsubishi CN105 Control |
| 10.69.30.X    | ha_vm           | Unraid        | Home Assistant OS    | Central Intelligence     |

---

## Hardware Standards & "As-Builts"
To maintain consistency across DIY nodes, all HVAC controllers follow these specifications:

### 1. The Controller
* Device: Seeed Studio XIAO ESP32-S3 Sense
* Pinout: 
    * TX: GPIO43 (D6)
    * RX: GPIO44 (D7)
* Protection: A 1k Ohm resistor is soldered in-line with the RX wire to drop the Mitsubishi 5V logic signal to 3.3V.

### 2. The Interface
* Connector: JST PAP-05V-S (5-pin)
* Port: Mitsubishi CN105 (Standard Heat Pump motherboard)

---

## Deployment Workflow (GitOps)
This repository uses a Push-based GitOps model via Komodo and SSH.

1. Edit: Modify YAML configurations in VS Code (Docker).
2. Commit: Push changes to GitHub.
3. Deploy: Komodo triggers a remote sync to the Home Assistant VM via SSH.
4. Compile: The VM compiles and flashes the binary via Over-The-Air (OTA).

---

## Repository Structure
```text
.
├── esphome/
│   ├── common/         # Global standards (WiFi, base.yaml)
│   ├── packages/       # Logic templates (mitsubishi.yaml)
│   └── sun_porch_ac.yaml
├── traefik/            # Routing for ha.sherbon.home
└── secrets.yaml.example # Template for private credentials
