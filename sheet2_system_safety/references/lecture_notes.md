# Lecture Notes Used for Sheet 2

## Safety Engineering

The sheet follows the lecture structure:

1. Hazard analysis
2. Risk analysis
3. Risk mitigation
4. Safety case construction

## STPA Process

The analysis follows the STPA workflow:

1. Define losses, hazards, and ODD
2. Model the control structure
3. Identify unsafe control actions
4. Derive safety constraints
5. Identify causal loss scenarios

## CARLA-Specific Notes

The CARLA exercise system uses:

- one forward-facing camera
- three binary classifiers
- a perfect distance oracle
- a rule-based planner
- human safety operator fallback

The most important weakness is that the planner relies entirely on narrow perception outputs and has no sensor redundancy.
