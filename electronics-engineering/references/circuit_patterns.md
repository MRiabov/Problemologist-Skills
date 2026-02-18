# Electromechanical Circuit Patterns

## 1. Motor with Direct PSU
Simple connection for always-on motors.
- **PSU**: V+ -> Motor: +
- **PSU**: 0 -> Motor: -

## 2. Motor with Relay/Switch
For gated control.
- **PSU**: V+ -> Relay: in
- **Relay**: out -> Motor: +
- **PSU**: 0 -> Motor: -

## 3. High-Current Parallel Supply
For multiple high-load actuators.
- **PSU**: V+ -> Splitter/Node
- **Node**: 1 -> Motor 1: +
- **Node**: 2 -> Motor 2: +
- **Motor 1/2**: - -> PSU: 0

## 4. Component Terminal Mapping
- **Motor**: `+`, `-` (or `a`, `b`)
- **Switch/Relay**: `in`, `out`
- **Power Supply**: `supply_v+`, `0` (or `v+`, `0`)
