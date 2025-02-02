# Booster Environment 

## Overview

The **Booster Environment** is a simulation framework designed for reinforcement learning applications in aerospace engineering. This environment simulates the flight dynamics of a booster rocket, allowing agents to learn and optimize their control strategies through interactions. The environment includes a detailed action space, observation space, and specific criteria for stability and termination of the simulation.

## Installation

To use the Booster Environment, ensure you have the necessary dependencies installed. 

```bash
pip install -r requirements.txt
```

## Environment Description

### Action Space

The action space consists of three continuous actions that the agent can manipulate:

1. **Main Engine Throttle**: 
   - [-1, 0] (off) 
   - [0, +1] (throttle from 57% to 100% power).
2. **Side Engine Throttle**: 
   - [-1, -0.5]  (left),
   - [-0.5, 0.5] (off)
   - [0.5, +1]   (right).
3. **Delta Gimbal Angle** [-1, 1].

### Observation Space

The observation space provides essential information about the state of the booster. It consists of the following features:

1. **X relative position to the launchpad** [m]
2. **Y relative position to the launchpad** [m]
3. **Velocity in the X direction (Vx)** [m/s]
4. **Velocity in the Y direction (Vy)** [m/s]
5. **Pitch angle (alpha)** [rad].
6. **Angular velocity (w)** [rad/s].
7. **Fuel ratio (Fuel / Initial Fuel)**.
8. **Previous main engine power**  [0, 1].
9. **Previous nozzle angle** [rad].

### Stability Criteria

The booster must meet certain stability criteria during the simulation to ensure safe flight:

1. **X Position Limit**.
2. **Y Position Limit**.
3. **Tilt Angle**.
4. **Angular Velocity Limit**.
5. **Max Steps**.

The limits are described in **constant.py**.

### Termination Criteria

The simulation terminates when any of the following conditions are met:

1. **Landing Velocity**
2. **Horizontal Velocity Limit**
3. **Landing Tilt Angle**
4. **Launch Pad Radius**

The criteria are described in **constant.py**.

## Configuration File

The environment can be configured using the `env.yaml` file, which defines various parameters for the simulation. Here’s an example configuration:

```yaml
drag: 10000 #  The drag force acting on the booster.
turbulence: 0.0 # The turbulence effect (momentum) on the flight
wind: 0.0 # The wind effect (x axis force) on the booster.
max_steps: 2000 #  Maximum number of steps before the episode ends.
render: false # If set to `true`, it enables visual rendering of the environment.

initial_condition:
  Vx: -11 +- 4
  Vy: -150 +- 10
  alpha: -0.045 +- 0.01
  fuel_ratio: 0.2 +- 0.025
  w: 0 +- 0.0
  x: 2530 +- 30
  y: 1200 +- 200

# --------------------------------- REWARD --------------------- #

# Defines the reward system used to evaluate agent performance:
reward_version: v5 # Reward function version

# Reward function constants to fine tune
reward:
  Vx: 250
  Vy: 750
  angle: 1250
  engine_startup: 3
  fuel_penalization: 0
  main_engine_burn: 0.3
  position: 1250
  side_engine_burn: 0.3
  gimbal: 0.5
  termination_reward: 100
  time_penalization: 0
  trajectory_penalization: 0.3
  velocity: 1500
  w: 0
```

### Custom Reward Functions

You can implement your own reward function by following a specific naming pattern in your rewards file. To do this, declare your function with the pattern `_v{version_number}`. For example, if your custom function is designed for version 8, it should be named `_v8`.

In your configuration file (`env.yaml`), you can specify the version of the reward function to be used by setting the `reward_version` parameter to match your custom version. Ensure that the function parameters are correctly defined to match the expected inputs for your implementation.

## Usage

To create and interact with the Booster Environment, follow this example:

```python
import gymnasium as gym
from env import BoosterEnv

# Initialize the environment with the configuration file
env = BoosterEnv(config="path/to/env.yaml")

# Reset the environment to get the initial state
state, _ = env.reset()

# Run a sample episode
for _ in range(1000):
    action = env.action_space.sample()  # Sample a random action
    next_state, reward, done, _, _ = env.step(action)  # Take a step
    if done:
        break

# Close the environment
env.close()
```
