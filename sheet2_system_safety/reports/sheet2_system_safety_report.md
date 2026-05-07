# Sheet 2 – System Safety Report

## Exercise 2.1 – Safety Vocabulary

| Term | Definition | Autonomous Driving Example |
|---|---|---|
| Loss | An unacceptable adverse outcome involving harm to people, property, law, or environment. | A pedestrian is injured by the AV. |
| Hazard | A system state that can lead to a loss under worst-case environmental conditions. | The vehicle continues moving while a pedestrian is within stopping distance. |
| Risk | Combination of hazard likelihood, loss probability given the hazard, and loss severity. | Missed pedestrian detection at 50 km/h has high severity and medium likelihood. |

---

## Exercise 2.2 – ODD Specification

See `docs/odd_definition.md`.

| Dimension | Operating Conditions | Non-Operating Conditions | Runtime Detection |
|---|---|---|---|
| Weather | Clear, cloudy, dry weather | Heavy rain, fog, snow | Weather classifier or camera-based visibility check |
| Lighting | Daytime | Night, extreme glare, low sun angle | Brightness/contrast monitoring |
| Camera Condition | Clean, fixed, forward-facing camera | Dirty lens, occlusion, blur, displaced camera | Image quality and camera calibration checks |
| Scene Type | Urban roads and mapped intersections | Highways, rural roads, unmapped intersections | Map matching and scene classifier |
| Vehicle Speed | 0–50 km/h | >50 km/h | Vehicle speed sensor |

---

## Exercise 2.3 – Losses

| ID | Loss Description | Why Unacceptable |
|---|---|---|
| L-1 | Injury or death of pedestrian | Human harm is unacceptable. |
| L-2 | Collision with another vehicle | May cause fatalities, injuries, and property damage. |
| L-3 | Traffic law violation | Creates unsafe traffic situations and legal liability. |
| L-4 | Loss of vehicle control | May escalate into severe accidents. |
| L-5 | Damage to public infrastructure | Causes economic damage and public safety impact. |

---

## Exercise 2.4 – Hazards

| ID | Hazard | Loss(es) | Likelihood | Severity |
|---|---|---|---|---|
| H-1 | Vehicle moves while pedestrian is in path | L-1 | Medium | High |
| H-2 | Vehicle fails to stop at required intersection | L-1, L-2, L-3 | Medium | High |
| H-3 | Emergency braking triggered unnecessarily | L-2, L-4 | Medium | Medium |
| H-4 | Vehicle operates outside ODD without detection | L-1, L-2, L-4 | High | High |
| H-5 | Human operator fails to intervene in time | L-1, L-2 | Medium | High |
| H-6 | Vehicle continues toward another road user inside stopping distance | L-1, L-2 | Medium | High |

---

## Exercise 2.5 – Control Structure

### Controllers

1. Planning module
2. Human safety operator

### Sensors / Feedback Providers

- Camera
- Pedestrian detector
- Vehicle detector
- Traffic-light detector
- Distance oracle
- Vehicle speed and steering feedback
- Dashboard

### Controlled Process

- Vehicle motion through throttle, brake, and steering actuators

### Control Actions

- Continue driving
- Request deceleration
- Emergency brake
- Human brake override
- Human steering override

### Feedback Signals

- Pedestrian present / absent
- Vehicle present / absent
- Traffic light present / absent
- Distance estimates
- Current speed and steering angle
- Dashboard system state

The traffic-light model only detects traffic-light presence, not the traffic-light state. Therefore, the planner cannot safely know whether a detected traffic light is red, yellow, green, or irrelevant without additional assumptions.

---

## Exercise 2.6 – Unsafe Control Actions

| ID | Controller | Control Action | UCA Type | Hazard(s) | Unsafe Scenario |
|---|---|---|---|---|---|
| UCA-1 | Planner | Continue driving | Provided unsafely | H-1, H-6 | Vehicle continues while pedestrian or vehicle is ahead within stopping distance. |
| UCA-2 | Planner | Emergency brake | Not provided | H-1, H-6 | Planner fails to brake when pedestrian or vehicle is ahead. |
| UCA-3 | Planner | Emergency brake | Wrong timing/order | H-1, H-6 | Brake command is issued too late to avoid collision. |
| UCA-4 | Planner | Continue driving | Wrong duration | H-2 | Vehicle continues through an intersection when it should stop. |
| UCA-5 | Planner | Emergency brake | Provided unsafely | H-3 | Planner brakes hard without a real obstacle, creating rear-end collision risk. |
| UCA-6 | Human operator | Manual brake override | Not provided | H-5 | Operator fails to intervene when automation misses a hazard. |
| UCA-7 | Human operator | Steering override | Wrong timing/order | H-4, H-5 | Operator reacts too late during ODD violation. |
| UCA-8 | Human operator | Manual override | Wrong duration | H-4, H-5 | Operator takes over briefly but releases control before the situation is safe. |

---

## Exercise 2.7 – Safety Constraints

| UCA | Safety Constraint | Level | Verification |
|---|---|---|---|
| UCA-1 | Planner must not continue driving when a pedestrian or vehicle is within stopping distance. | System-level | Closed-loop scenario testing |
| UCA-2 | Planner must issue emergency braking when a detected pedestrian or vehicle is inside the critical distance. | System-level | Closed-loop simulation |
| UCA-3 | Perception-to-braking latency must remain below a safe threshold. | Model-level | Latency benchmark |
| UCA-4 | Planner must behave conservatively at intersections when traffic-light state is unknown. | System-level | Intersection simulation |
| UCA-5 | Planner must suppress emergency braking unless a credible stopping condition is present. | System-level | False-positive scenario testing |
| UCA-6 | Operator must receive salient warning information during critical situations. | System-level | Human-factor evaluation |
| UCA-7 | System must detect ODD violations and trigger safe fallback before manual intervention becomes the only barrier. | System-level | ODD/fallback test |
| UCA-8 | Manual takeover must remain active until the vehicle reaches a safe state. | System-level | Operator protocol review and simulation |

---

## Exercise 2.8 – Causal Loss Scenarios

| UCA | Causal Scenario | Root Cause | Related Constraint |
|---|---|---|---|
| UCA-1 | Pedestrian detector misses a pedestrian under shadow or glare, so planner continues. | Training data lacks lighting variation. | Planner must not continue driving near uncertain detections. |
| UCA-2 | Planner never receives a positive pedestrian detection. | False negative classifier output. | Emergency braking constraint. |
| UCA-3 | Correct detection arrives after the vehicle has passed safe stopping distance. | High inference or control latency. | Latency constraint. |
| UCA-4 | Planner treats traffic-light presence as insufficient evidence to stop. | Detector lacks red/yellow/green state awareness. | Conservative intersection handling. |
| UCA-5 | Vehicle detector falsely detects a vehicle ahead and triggers maximum braking. | False-positive perception output. | Credible stopping condition constraint. |
| UCA-6 | Operator does not notice automation failure quickly enough. | Monitoring fatigue during long shift. | Warning and takeover protocol. |
| UCA-7 | Weather changes to rain, but system remains in autonomous mode. | ODD violation not detected. | ODD detection and fallback. |
| UCA-8 | Operator releases override before the vehicle is stable. | Mode confusion or unclear feedback. | Manual takeover persistence constraint. |

---

## Exercise 2.9 – Mapping Constraints to Future Evidence

| Constraint | Future Evidence Source |
|---|---|
| Pedestrian, vehicle, and traffic-light detection reliability | Sheet 4 – Testing I |
| ODD gap analysis | Sheet 3 – Training and ODD gap analysis |
| ODD coverage and refined ODD | Sheet 5 – Testing II |
| Robustness under perturbation | Sheet 6 – Adversarial ML |
| Confidence calibration and uncertainty | Sheet 7 – Uncertainty |
| Explanation of model decisions | Sheet 8 – Explainability |
| OOD violation detection | Sheet 9 – OOD Detection |
| Final argument that constraints are supported by evidence | Sheet 10 – Safety Case |

---

## Key Safety Weaknesses

1. The system has no perception redundancy.
2. The planner receives only binary outputs, losing uncertainty information.
3. The traffic-light detector detects presence but not state.
4. The human fallback is weak because long monitoring causes fatigue and there is no auditory alert.
5. Training data is limited to sunny daytime CARLA data, making ODD violations highly likely.
