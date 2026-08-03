# Galaxy Game - Specification Document: Interplanetary Beamed Power Infrastructure

## 1. Executive Summary
This document outlines late-game **Interplanetary Beamed Power Infrastructure** — a universal megastructure capability enabling power transmission between any two celestial bodies across astronomical units. 

Interplanetary power transmission operates on non-continuous delivery due to astronomical alignment, distance variations, and solar occlusions. Because this is macro-level, background-system infrastructure, management and mitigation are handled entirely by the **AI Manager** via dynamic automation loops rather than manual player mechanics.

---

## 2. Orbital Power Disruption Mechanics

Power transmission between bodies across Astronomical Units ($\text{AU}$) faces two core astronomical constraints:

1. **Solar Occlusion (Superior Conjunction):** 
   * When the Sun sits directly between source and target, direct optical/laser transmission is $100\%$ occluded or severely distorted by solar plasma.
2. **Inverse-Square & Beam Divergence Factors:** 
   * Orbital distances fluctuate dynamically based on each body's position in its orbit. 
   * Delivered power varies dynamically based on distance-induced beam attenuation and array collector efficiency.

---

## 3. Infrastructure Progression & AI Manager Logic

### Progression Tier
* **Tier Status:** **Late-Game Megastructure**
* **Context:** Early and mid-game operations rely strictly on localized power loops (e.g., local surface/orbital solar, nuclear, or imported chemical fuel cells). Interplanetary beaming becomes economically viable only when a body's macro-industrial baseline outstrips local power generation capabilities.

### AI Manager Automation Loops
When direct beaming is interrupted, the AI Manager manages continuous baseline power without manual intervention through three automated routines:

1. **Dynamic Production Shifts (Active vs. Occluded):**
   * **Active Beam Phase:** The AI Manager sets target receiving arrays and heavy processing to *Peak Capacity*. Excess energy is diverted to cryo-liquefaction, local energy buffers, and high-energy volatile synthesis.
   * **Occluded Phase (Conjunction):** Non-essential industrial loads are automatically throttled. Base-load requirements (such as station-keeping and the $L_1$ artificial magnetic shield) switch seamlessly to local volatile storage, $H_2/O_2$ fuel cells, or surface reserves.

2. **Automated Relay Mirror Construction:**
   * If local fuel buffers are projected to dip below safety thresholds during upcoming conjunctions, the AI Manager queues backlog orders for **Lagrange Relay Mirrors** (e.g., Earth-Sun $L_4/L_5$ or Main Belt nodes) to bounce power around the Sun.

3. **Logistics Pre-Buffering:**
   * Prior to known occlusion windows, the AI Manager automatically schedules automated volatile transport runs to pre-fill local stockpiles.

---

## 4. Operational Data Schemas

### A. Power Transmission Node State (`power_transmission_state.json`)
```json
{
  "$schema": "[http://json-schema.org/draft-07/schema#](http://json-schema.org/draft-07/schema#)",
  "title": "InterplanetaryPowerTransmissionState",
  "type": "object",
  "properties": {
    "source_node": { 
      "type": "string"
    },
    "target_node": { 
      "type": "string"
    },
    "transmission_status": { 
      "type": "string", 
      "enum": ["direct_active", "relayed_active", "occluded_blackout"] 
    },
    "orbital_metrics": {
      "current_distance_au": { "type": "number" },
      "line_of_sight_occluded": { "type": "boolean" },
      "beam_efficiency": { "type": "number", "minimum": 0.0, "maximum": 1.0 }
    },
    "ai_automation_policy": {
      "buffer_threshold_percent": { "type": "number", "default": 20.0 },
      "auto_throttle_non_essential": { "type": "boolean", "default": true },
      "failover_power_source": { "type": "string", "default": "local_h2_fuel_cells" }
    }
  }
}
B. Megastructure Backlog Activation Trigger (interplanetary_power_grid.json)
JSON
{
  "tech_tier": "late_game_megastructure",
  "infrastructure_id": "interplanetary_power_grid",
  "prerequisites": [
    "cnt_production_capacity",
    "l1_shade_or_reflector_deployed",
    "magnetosphere_protection_active"
  ],
  "ai_manager_activation_condition": {
    "target_power_deficit_mw": 100000,
    "source_cnt_production_surplus": true
  }
}