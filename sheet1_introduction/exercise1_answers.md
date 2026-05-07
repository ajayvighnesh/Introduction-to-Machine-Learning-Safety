# Exercise Sheet 1 — Introduction to Machine Learning Safety

## Exercise 1.1 — Safety vs. Security

### Difference Between Safety and Security

In machine learning systems, safety and security address different types of risks.

**Safety** focuses on preventing unintentional harm caused by accidental failures, design flaws, incorrect predictions, hardware malfunctions, or unexpected environmental conditions. The main concern is whether the system behaves safely even when components fail or produce incorrect outputs.

**Security** focuses on protecting the system against intentional attacks from malicious actors. This includes unauthorized access, adversarial manipulation, data poisoning, model theft, or cyberattacks targeting the ML system.

The lecture slides summarize the distinction as follows:

| Aspect        | Safety                                    | Security                                      |
| ------------- | ----------------------------------------- | --------------------------------------------- |
| Threat Source | Accidental failures, hazards, human error | Malicious actors, intentional attacks         |
| Goal          | Prevent unintentional harm                | Prevent deliberate misuse or compromise       |
| Focus         | Robustness, fail-safe behavior            | Authentication, protection, attack prevention |

### Safety Concern Example

A safety concern for an autonomous vehicle is a pedestrian detector failing to detect a pedestrian during heavy rain or poor lighting conditions. Since the planning module relies completely on the perception model outputs, the vehicle may fail to brake in time, potentially causing injury or death.

### Security Concern Example

A security concern is an adversarial attack in which an attacker modifies a stop sign or traffic light using carefully designed perturbations so that the perception model misclassifies it. This could intentionally cause unsafe vehicle behavior.

---

# Exercise 1.2 — The EU AI Act

## 1. Main Risk Categories of the EU AI Act

The EU AI Act classifies AI systems into four major risk categories.

### 1. Unacceptable Risk

These systems are prohibited because they pose severe threats to human rights or safety.

Examples:

* Social scoring systems
* Manipulative AI systems
* Certain forms of biometric surveillance

### 2. High Risk

These systems are heavily regulated because failures could seriously affect safety or fundamental rights.

Examples:

* Medical AI systems
* Autonomous driving systems
* AI used in transportation or education

### 3. Limited Risk

These systems are allowed but require transparency obligations.

Examples:

* Chatbots
* AI-generated content systems
* Recommendation systems

### 4. Minimal Risk

These systems have little or no regulation because they present low danger.

Examples:

* Spam filters
* Video game AI
* Basic automation tools

---

## 2. Classification of the Given Systems

### a) Autonomous Vehicle Perception System

The autonomous vehicle perception system should clearly be classified as a **High-Risk AI System**.

Reasons:

* The system directly affects physical safety.
* Incorrect predictions can lead to accidents.
* The system operates in public environments.
* Human lives depend on reliable operation.
* The system influences braking and driving decisions.

Failures such as missed pedestrian detections could result in severe injury or fatalities.

### b) ChatBot

A normal chatbot would usually belong to the **Limited Risk** category.

Reasons:

* The primary risk is manipulation or misinformation.
* The chatbot does not usually directly control safety-critical hardware.
* Users mainly require transparency that they are interacting with AI.

However, a chatbot used in medicine, law, or critical infrastructure could become High Risk depending on deployment context.

---

## 3. Obligations for High-Risk AI Systems

The EU AI Act imposes several obligations on providers of high-risk systems.

These include:

* Risk management procedures
* High-quality training and validation data
* Documentation and traceability
* Human oversight mechanisms
* Transparency and explainability
* Robustness and cybersecurity requirements
* Continuous monitoring after deployment
* Safety evaluation before deployment

For autonomous vehicles, providers must demonstrate that the perception system performs reliably under intended operating conditions and that risks are identified and mitigated.

---

# Exercise 1.3 — Build the Right Model vs. Build the Model Right

Two complementary perspectives are important in machine learning quality.

## Build the Right Model

This refers to solving the correct problem.

A failure example:

A pedestrian-detection system is trained only on adults in sunny daytime conditions and performs poorly on children, cyclists, or pedestrians at night.

Even if the model is technically implemented correctly, it does not adequately solve the real-world safety problem because the training objective and dataset are incomplete.

## Build the Model Right

This refers to implementing and deploying the model in a reliable and robust way.

A failure example:

The pedestrian detector achieves high test accuracy but is vulnerable to adversarial perturbations or distribution shift. A tiny change in the input image may completely change the prediction.

In this case, the intended problem is correct, but the implementation and deployment are insufficiently robust for safe operation.

Both perspectives are necessary for safe ML systems.

---

# Exercise 1.4 — First Impressions

## 1. Three Situations Where the Model Should Not Be Trusted

### Situation 1 — Night Driving

The models were trained exclusively on sunny daytime CARLA data.

At night, the visual appearance of pedestrians, vehicles, and traffic lights changes significantly. The model may therefore fail due to distribution shift.

### Situation 2 — Rain or Fog

Rain, fog, or poor weather reduce image quality and visibility.

Since the training data does not include these conditions, the model may produce unreliable predictions or miss important objects.

### Situation 3 — Occlusions or Crowded Urban Scenes

If pedestrians are partially hidden behind vehicles or appear in crowded intersections, the binary classifiers may fail to detect them correctly.

The single-camera setup also increases this risk because there is no sensor redundancy.

---

## 2. Most Safety-Critical Output Label

The most safety-critical label is **pedestrian present**.

Reason:

Missing a pedestrian can directly result in severe injury or death. False negatives in pedestrian detection are therefore extremely dangerous.

While missing vehicles or traffic lights is also serious, pedestrian detection failures often allow less reaction time and involve vulnerable road users.

---

## 3. Additional Information Needed Before Deployment

Before deployment, I would want the following information:

### Dataset Information

* Dataset size
* Label quality
* Diversity of environments
* Weather and lighting coverage
* Real-world vs. simulated data ratio

### Model Performance

* Precision and recall values
* False-negative rates
* Performance under distribution shift
* Robustness against adversarial attacks

### System-Level Safety

* Emergency fallback mechanisms
* Sensor redundancy
* Human intervention response time
* Fail-safe behavior

### Deployment Conditions

* Exact ODD (Operational Design Domain)
* Speed limitations
* Environmental assumptions
* Geographic limitations

### Reliability Evidence

* Calibration results
* Explainability analysis
* OOD detection performance
* Long-term testing results

Without this information, deployment on public roads would be unsafe.

---

# Exercise 1.5 — Course Topics and the Safety Case

A safety case is a structured argument supported by evidence that a system is acceptably safe in a given context.

Each topic in the course contributes evidence to the final safety case.

## Testing and Validation

Testing demonstrates whether the model performs correctly on validation and test datasets.

Metrics such as precision, recall, F1 score, and confusion matrices provide evidence about prediction quality.

Testing also identifies weaknesses and failure modes.

---

## Explainability

Explainability methods such as GradCAM or saliency maps help determine whether the model focuses on relevant objects.

For example, a pedestrian detector should focus on pedestrians rather than background artifacts.

This provides evidence that the model learned meaningful features instead of spurious correlations.

---

## Uncertainty Estimation

Uncertainty estimation evaluates whether model confidence scores are reliable.

A well-calibrated model should not produce high confidence for incorrect predictions.

Calibration methods reduce the risk of overconfident failures.

---

## Anomaly Detection

OOD (Out-of-Distribution) detection identifies unusual inputs such as fog, rain, or nighttime scenes.

This helps the system recognize when it is operating outside the training distribution.

OOD detection provides evidence that the system can identify potentially unsafe conditions.

---

## Adversarial Machine Learning

Adversarial ML evaluates robustness against malicious perturbations.

Testing with attacks such as FGSM helps determine how easily the perception system can be manipulated.

This contributes evidence about security robustness.

---

## Alignment

Alignment focuses on ensuring that the system objectives match human safety goals.

For autonomous driving systems, the planning and perception modules must prioritize human safety over unsafe optimization behavior.

Alignment analysis contributes evidence that the system behavior remains compatible with intended safety objectives.

---

# Conclusion

Machine learning safety involves much more than achieving high prediction accuracy. A safe autonomous driving system must:

* operate reliably under diverse conditions,
* remain robust against attacks and distribution shifts,
* provide trustworthy confidence estimates,
* detect anomalous situations,
* and align with human safety goals.

The CARLA perception system used in this course has several important limitations, especially its dependence on a single camera and sunny daytime training data. Throughout the course, additional evidence will be collected to evaluate whether the system can be considered sufficiently safe for deployment within a restricted operating domain.
