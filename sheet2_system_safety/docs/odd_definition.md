# Operational Design Domain (ODD)

The ODD defines the conditions under which the CARLA autonomous driving system is intended to operate safely.

| Dimension | Operating Conditions | Non-Operating Conditions | Runtime ODD Violation Detection |
|---|---|---|---|
| Weather | Clear, cloudy, dry weather | Heavy rain, fog, snow | Weather estimation from camera image |
| Lighting | Daytime | Night, extreme glare, sunset darkness | Brightness threshold monitoring |
| Camera Condition | Clean forward-facing camera | Occluded, dirty, blurred lens | Blur/occlusion detection |
| Scene Type | Urban roads, mapped intersections | Rural roads, highways, tunnels | Map mismatch or scene classifier |
| Vehicle Speed | 0–50 km/h | Speeds above 50 km/h | Vehicle speed monitoring |

## Key Assumptions

- Models are trained only on sunny daytime CARLA data.
- No sensor redundancy exists.
- Human operator acts as fallback.
- Planner depends entirely on perception outputs.

## ODD Violations

Outside the ODD:
- model behavior becomes unreliable
- safety guarantees no longer hold
- system must escalate or disengage autonomy
