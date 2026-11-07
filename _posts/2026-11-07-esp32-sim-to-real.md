---
title: Running a Policy from MuJoCo Reinforcement Learning on an ESP32-S3
date: 2026-11-07
categories: [Guides, Robotics]
tags: [esp32, arduino, ai, robotics, reinforcement-learning]     # TAG names should always be lowercase
image: /assets/img/posts/2026-11-07-esp32-sim-to-real/thumb.png


---

One of the most interesting challenges in building a biped robot isn't necessarily getting it to walk. It's getting it to behave like a robot rather than a collection of servos executing predefined movements.

Imagine a robot that can:

- Recover itself when it falls over.
- Stand upright and maintain its balance.
- Walk around its environment.
- Recognize people and turn to look at them.
- Respond to sounds, such as someone clicking their fingers or saying hello.
- Sit down, play dead, look bored or appear curious.
- Combine all these capabilities without compromising its ability to remain upright.

This is the direction I'm exploring with Archie, my modular biped robotics project.

I've already started experimenting with reinforcement learning using MuJoCo and Stable-Baselines3. The next challenge is taking those trained policies out of simulation and running them on the actual robot, which uses an ESP32-S3, SC09 serial bus servos, a BNO055 IMU and, eventually, a Raspberry Pi Compute Module 5 for more demanding processing.

This article explores the architecture I'm considering and how the different pieces could work together.

---

## 1. Training the robot in simulation

The starting point is reinforcement learning.

Rather than manually programming every movement, I can build a representation of Archie in MuJoCo and train a neural network to control its joints.

Using Proximal Policy Optimization (PPO), the robot learns which actions produce desirable outcomes through repeated interaction with its simulated environment.

For example, I can train different objectives:

- Maintain an upright position.
- Recover from a fallen position.
- Avoid falling when disturbed.
- Move towards a target.
- Maintain balance while moving its head.

The important distinction is that I'm not programming a sequence of movements. I'm training a policy that learns how to respond to different observations.

A trained policy essentially implements:

**Observations → Neural network → Actions**

The observations describe the robot's current state, while the actions describe what it should do next.

In MuJoCo, this might include:

- Body orientation.
- Angular velocity.
- Joint positions.
- Joint velocities.
- Foot contact.
- Head contact.
- Target positions.

The policy processes these inputs and produces the next set of joint commands.

The exciting part is that the same underlying approach can be used for much more complicated behaviors.

## 2. Moving a trained policy onto the ESP32-S3

Training is relatively straightforward compared with deploying the resulting model on a microcontroller.

Stable-Baselines3 uses PyTorch, whereas the ESP32-S3 needs a much smaller, embedded inference solution.

Fortunately, Espressif provides a framework designed for this purpose.

### ESP-DL and ESP-PPQ

The two technologies I'm looking at are:

**ESP-DL**

Espressif's lightweight neural network inference framework. It provides a C/C++ API for loading, managing and executing neural network models on supported ESP chips.

**ESP-PPQ**

Espressif's model quantization and conversion toolkit. It takes a compatible model and converts it into the format expected by ESP-DL.

The intended deployment pipeline is:

```
MuJoCo
   |
   v
Stable-Baselines3 (PPO)
   |
   v
PyTorch model
   |
   v
ONNX export
   |
   v
ESP-PPQ
   |
   v
.espdl model
   |
   v
ESP-DL
   |
   v
ESP32-S3
```

ESP-PPQ supports importing ONNX models and exporting Espressif's proprietary `.espdl` format.

ESP-DL then loads and executes that model on the microcontroller.

One important consideration is that not every PyTorch model will necessarily convert directly. The network architecture and its operators must be compatible with ESP-DL. The Stable-Baselines3 policy will also need to be exported correctly, including any observation-normalization operations used during training.

The conversion process needs to be tested with the actual trained model rather than assuming every PPO network will work without modification.

### What ESP-DL actually does

ESP-DL handles neural network inference. It doesn't manage the robot itself.

It won't:

- Read the BNO055 automatically.
- Understand SC09 servo positions.
- Convert normalized actions into servo commands.
- Manage recovery states.
- Decide which behavior the robot should perform.

Those responsibilities remain in the custom firmware.

Think of ESP-DL as an engine that accepts a collection of numbers and returns another collection of numbers.

Everything around that engine is our responsibility.

Official documentation: [https://docs.espressif.com/projects/esp-dl/en/latest/](https://docs.espressif.com/projects/esp-dl/en/latest/)

## 3. Building the hardware abstraction layer

This is arguably one of the most important parts of the implementation.

The simulation and the physical robot need to agree on what the numbers being passed between them actually mean.

For example, the simulator might represent a joint action as a normalized value between -1 and +1.

The physical SC09 servo, however, expects a position command expressed in its own position units.

We need a translation layer between the two.

I'd implement this as a dedicated C++ hardware abstraction layer.

### Input translation

The policy expects observations in exactly the same structure and units it encountered during training.

The hardware layer is responsible for constructing those observations.

For example:

| Policy input       | Physical hardware source           |
| ------------------ | ---------------------------------- |
| Body pitch         | BNO055 orientation                 |
| Body roll          | BNO055 orientation                 |
| Angular velocity   | BNO055 gyroscope                   |
| Left hip position  | SC09 feedback                      |
| Right hip position | SC09 feedback                      |
| Knee positions     | SC09 feedback                      |
| Joint velocities   | Calculated from servo feedback     |
| Foot contact       | Physical sensor or estimated state |

All measurements need to be converted into the same units, coordinate system and conventions used in MuJoCo.

If the simulation uses radians, the physical hardware layer should provide radians.

If the simulator considers a positive angle to mean forward movement, the physical servo mapping must use the same convention.

### Output translation

The policy produces normalized actions.

The hardware layer converts these into physical servo commands.

For example:

```
PPO output:          0.45
       |
       v
Joint angle mapping
       |
       v
Apply zero offset
       |
       v
Apply direction correction
       |
       v
Convert radians to SC09 position units
       |
       v
Apply joint limits
       |
       v
Send serial servo command
```

This layer also handles:

- Servo IDs.
- Calibration offsets.
- Direction inversions.
- Position limits.
- Speed and acceleration constraints.
- Serial bus communication.
- Feedback reading.

The benefit is that the neural network doesn't need to know anything about the physical servo hardware.

It simply operates in the same conceptual joint space as the simulator.

This also makes it much easier to replace a servo or change its calibration without having to retrain the policy.

### Starting position matters

The initial position of the physical robot must match the assumptions made during training.

If MuJoCo expects the robot to begin with straight legs and its head facing forward, the physical robot should initialize into that same configuration.

Otherwise, the policy receives observations that don't represent the state it expects.

I'd implement a startup sequence that:

1. Initializes the servos.
2. Reads their actual positions.
3. Moves the robot into a known starting pose.
4. Initializes and calibrates the IMU.
5. Builds the initial observation vector.
6. Starts policy inference.

This is particularly important when deploying recovery and balance policies.

## 4. Using real IMU data when training in MuJoCo

One question that initially seemed complicated was how to train a policy using an IMU that physically exists on the robot but doesn't exist in the simulation.

The solution is to simulate the measurements rather than the physical sensor.

MuJoCo already knows the robot's orientation, angular velocity and joint positions.

We can use those simulated values to construct the observation vector that the real hardware will eventually provide.

For example:

```
MUJOCO TRAINING

Simulated robot orientation
Simulated angular velocity
Simulated joint positions
          |
          v
Observation vector
          |
          v
        PPO
```

And on the real robot:

```
PHYSICAL ROBOT

BNO055 orientation
BNO055 gyroscope
SC09 servo feedback
          |
          v
Observation vector
          |
          v
     ESP-DL policy
```

Both systems should produce the same observation structure.

### What about sensor imperfections?

Real sensors aren't perfect.

The BNO055 will have measurement noise, calibration errors and filtering characteristics that won't necessarily be present in the simulator.

We can improve robustness by introducing sensor noise, offsets and other imperfections during training.

This is an example of domain randomization.

The aim is to prevent the policy from becoming dependent on unrealistically perfect simulator measurements.

### Servo feedback is equally important

The SC09 servos provide actual position feedback.

This is extremely useful because the policy should ideally know where the joints actually are, rather than simply where it asked them to go.

We can also estimate joint velocity by comparing successive position measurements.

This allows the policy to understand whether a joint is moving, stationary or struggling to reach its commanded position.

---

## 5. The head-mounted IMU problem

An interesting complication with Archie is that the BNO055 is mounted in the head rather than the main body.

It's also mounted at approximately 45 degrees.

This creates an additional challenge because the head will move independently of the torso.

For example, the vision system might command the head to turn towards somebody standing on the left.

The IMU will measure that head movement as well as the movement of the body.

We don't want the balance policy to mistake head movement for body movement.

### Separating head and body orientation

Fortunately, we know the position of the neck servos.

This means we can combine:

- BNO055 orientation.
- Neck joint positions.
- Known IMU mounting orientation.
- Neck joint geometry.

Using quaternion-based coordinate transformations, we can estimate the torso orientation from the head-mounted IMU.

Conceptually:

```
BNO055 orientation
        +
Neck servo positions
        +
45-degree mounting calibration
        |
        v
Torso orientation estimate
        |
        v
Balance policy
```

The same principle applies to angular velocity, although compensating for rotational motion requires accounting for the neck's motion and geometry.

There are some limitations. Rapid head movements introduce additional acceleration, and the BNO055's accelerometer cannot distinguish gravity from all forms of linear acceleration perfectly.

Nevertheless, this is a workable approach.

I'd also consider randomizing head positions and movements during MuJoCo training so that the balance policy learns to remain stable while the head moves.

An important longer-term consideration would be adding a second IMU to the torso. That would provide an independent body measurement and simplify the estimation problem considerably.

For the initial prototype, however, the head-mounted arrangement is worth experimenting with.

---

## 6. Contact sensors: are they necessary?

During training, I've also been using information about whether the feet or the front of the head are touching the ground.

The physical robot doesn't currently have contact sensors.

Whether that's a problem depends on how the contact information is used.

There are two possibilities.

**Contact information used in the reward function**

If MuJoCo uses contact information only to calculate rewards, teaching the robot what good and bad contact looks like, then the physical robot doesn't necessarily need those sensors.

**Contact information used in the observation vector**

If the policy actually receives foot-contact or head-contact flags as inputs, then removing those inputs on the real robot will cause a mismatch.

We would need to provide equivalent measurements, estimate the contact state or retrain the policy without those inputs.

For Archie, I'd initially favour using contact information in the simulation's reward calculations without making it a mandatory input to the balance policy.

This would reduce hardware complexity.

We can revisit physical foot pressure sensors later if they prove useful.

---

## 7. Moving beyond balance: giving Archie skills

Once balance and recovery are working, the next challenge is giving Archie more interesting behaviors.

This is where reinforcement learning becomes particularly exciting.

Rather than training one enormous policy to do absolutely everything, I'm considering a hierarchical architecture with specialised skills.

There are three broad approaches.

### Approach A: One command-conditioned policy

A single policy receives additional commands describing what it should do.

For example:

- Stand upright.
- Walk forwards.
- Walk backwards.
- Sit down.
- Play dead.

The policy learns how to execute those commands.

This is similar to the approach used in many modern legged robotics systems.

### Approach B: Multiple specialised policies

Different policies handle different tasks.

For example:

| Policy              | Responsibility                  |
| ------------------- | ------------------------------- |
| Recovery            | Get upright after falling       |
| Standing            | Maintain balance                |
| Walking             | Locomotion                      |
| Sitting             | Controlled sitting and standing |
| Expressive movement | Character and animations        |

A higher-level behavior manager selects the appropriate policy.

This has the advantage that individual skills can be trained and improved independently.

### Approach C: A hybrid approach

This is probably the most interesting direction for Archie.

Use a combination of command-conditioned policies, specialised skills and a higher-level behavior engine.

For example:

```
          BEHAVIOR ENGINE
                 |
          Desired behavior
                 |
          POLICY MANAGER
                 |
       +---------+---------+
       |         |         |
    Recovery  Balance   Locomotion
       |         |         |
       +---------+---------+
                 |
          Hardware layer
                 |
             SC09 servos
```

The policy manager determines which skill should be active, while the hardware layer ensures the commands are translated correctly.

A separate safety supervisor can override normal behavior whenever a fall or other unsafe condition is detected.

The advantage is that we can develop new behaviors without constantly breaking existing ones.

---

## 8. Adding computer vision and face tracking

Archie already has a computer vision component planned around the ESP32-S3 Sense, with more demanding processing potentially handled by the Raspberry Pi CM5.

The vision system could identify faces and send detection information to the main controller.

For example:

```
Camera
  |
  v
Face detection
  |
  v
Bounding boxes
  |
  v
Target selection
  |
  v
Head tracking command
```

The target-selection algorithm could simply choose the largest detected face, which is a reasonable starting point when deciding who Archie should look at.

The detection information could also be made available to the reinforcement learning policy.

For example, the observation vector could include:

- Horizontal position of the target.
- Vertical position of the target.
- Apparent face size.
- Detection confidence.
- Whether a face is currently visible.

The policy could then learn to maintain balance while orienting towards the target.

### Could face tracking be part of the balance policy?

Absolutely.

We could introduce a reward for maintaining a face near the center of the camera image.

However, I wouldn't initially combine everything into one policy.

I'd first get the balance policy working reliably, then introduce a separate head-tracking controller.

Eventually, a command-conditioned policy could receive the desired head direction and learn to achieve it while maintaining balance.

This creates a useful separation between deciding what to look at and controlling the physical robot.

### What about turning the body?

Head tracking is relatively straightforward.

Body turning is more complicated because it affects the robot's overall balance and potentially requires stepping.

I'd introduce this later, once locomotion is reliable.

The behavior manager can decide that a target is outside the head's comfortable range and request a body turn.

The locomotion controller then handles the actual movement.

---

## 9. Responding to sound

Another interesting possibility is giving Archie the ability to turn towards sounds.

With two I2S microphones mounted on opposite sides of the head, we could investigate sound source localization.

The microphones could provide information about the direction from which a sound originates.

For example:

- Someone clicks their fingers.
- Someone says hello.
- A noise occurs to the left.
- A noise occurs to the right.

The system could estimate the direction of the sound and pass that information to the behavior controller.

The process might look like this:

```
Left microphone  +  Right microphone
                |
                v
       Sound localization
                |
                v
       Estimated direction
                |
                v
        Head orientation
```

One important detail is that two microphones don't automatically provide reliable directional information.

We would need synchronized sampling and suitable signal processing, potentially using time-difference-of-arrival calculations.

The physical distance between the microphones will also affect the accuracy.

Nevertheless, this is a relatively accessible feature to experiment with.

Eventually, the same command-conditioned control system could receive either a visual target direction or a sound direction.

Archie could then turn towards whichever stimulus is considered most interesting.

---

## 10. Giving Archie a personality

This is where the project starts becoming particularly interesting.

I don't just want Archie to balance, walk and respond to commands.

I'd like it to appear to have some personality.

For example:

- When nobody is around, it gets bored.
- When someone enters the room, it becomes curious.
- When someone speaks, it turns towards them.
- When it recognises a familiar face, it becomes interested.
- When nothing happens for a while, it looks around or shifts its weight.

These behaviors don't necessarily need to be trained using reinforcement learning.

In fact, I'd separate personality and behavior selection from the physical control system.

### A behavior engine

A relatively simple behavior engine could run on the Raspberry Pi CM5 or the main ESP32.

It would maintain an internal state representing what Archie should be doing.

Possible states might include:

| State      | Example behavior                       |
| ---------- | -------------------------------------- |
| Idle       | Stand and occasionally look around     |
| Bored      | Lower head and shift weight            |
| Curious    | Raise head and look towards a stimulus |
| Interested | Track a face                           |
| Listening  | Turn towards a sound                   |
| Sleepy     | Slowly lower head                      |
| Alert      | Raise head and orient towards activity |

The behavior engine decides what to do.

The motion controller decides how to execute it.

This means we don't need to hardcode every individual servo movement.

### Keyframe-driven behaviors

One particularly useful approach is to define keyframes for different poses.

For example, a bored behavior might have:

1. Normal standing position.
2. Head slowly lowering.
3. Slight body weight shift.
4. Head turning to one side.
5. Return to normal standing.

These keyframes can be stored as configuration data rather than embedded in the firmware.

The behavior engine sends the desired pose to the motion controller.

The controller then produces smooth movements between those poses.

Eventually, the desired pose could become part of the policy's observation vector, allowing the neural network to learn how to reach different poses while maintaining balance.

This would make it possible to create many new behaviors without training a completely new policy for each one.

---

## 11. Running everything on the ESP32-S3

The remaining challenge is making sure that all these capabilities can run without interfering with the real-time control loop.

The ESP32-S3 has two processor cores and supports FreeRTOS task scheduling.

This provides an opportunity to separate time-critical control from less critical processing.

I'd structure the firmware around several tasks.

| Task                | Responsibility                                                | Priority |
| ------------------- | ------------------------------------------------------------- | -------- |
| Control loop        | Sensor reading, observation construction and policy execution | Highest  |
| Servo communication | SC09 commands and feedback                                    | High     |
| Sensor processing   | IMU filtering and state estimation                            | High     |
| Policy management   | Skill selection and model management                          | Medium   |
| Display             | TFT updates and status information                            | Low      |
| Communications      | Commands and telemetry                                        | Low      |

The actual task arrangement will depend on the timing requirements and the ESP-IDF configuration.

### Protecting the control loop

The most important task is the control loop.

It needs a predictable execution period.

Rather than relying on an ordinary loop with arbitrary delays, I'd use a periodic FreeRTOS task with `vTaskDelayUntil()`.

This provides a more consistent execution schedule.

The control loop should:

1. Read the latest sensor measurements.
2. Construct the observation vector.
3. Execute the selected policy.
4. Translate the actions into servo commands.
5. Repeat at the required frequency.

Other tasks should not be allowed to block this process.

We can also use the ESP32-S3's two cores to isolate time-critical work from background activities where appropriate.

One important qualification: ESP-DL has some built-in dual-core scheduling support for computationally intensive operators, but this does not mean every neural network automatically receives a performance boost from both cores.

For a relatively small PPO network, the main benefit will probably come from carefully scheduling the inference task alongside the rest of the firmware.

We should also measure actual execution time rather than assume the system will meet its deadlines.

A GPIO connected to a logic analyzer is a useful way to measure control-loop timing and jitter.

The goal is not simply to achieve a good average execution time. We need to understand the worst-case execution time.

---

## 12. The proposed overall architecture

Putting everything together, this is the architecture I'm considering for Archie.

```
                 RASPBERRY PI CM5
               Computer vision
               Face recognition
               Behavior engine
               Higher-level logic
                       |
                       |
                Main ESP32-S3
                       |
          +------------+------------+
          |                         |
     Policy manager           Sensor processing
          |                         |
    ESP-DL inference             BNO055
          |                    SC09 feedback
          |                         |
          +------------+------------+
                       |
                Hardware layer
                       |
                 SC09 servos
                       |
                  Archie robot
```

A second ESP32-S3 Sense board can handle camera processing, audio recording and microphone acquisition, communicating relevant results to the main controller.

This keeps the responsibilities relatively independent.

The CM5 handles more demanding processing, while the ESP32 remains responsible for real-time physical control.

The architecture also allows individual components to be upgraded without redesigning the entire system.

---

## 13. My proposed development roadmap

Rather than attempting to implement everything simultaneously, I'd approach the project incrementally.

| Stage | Development objective                                            |
| ----- | ---------------------------------------------------------------- |
| 1     | Export the existing PPO policy and run inference on the ESP32-S3 |
| 2     | Implement the SC09 hardware abstraction layer                    |
| 3     | Integrate BNO055 measurements and servo feedback                 |
| 4     | Reproduce the MuJoCo observation vector on real hardware         |
| 5     | Test the standing and balance policy                             |
| 6     | Implement and test recovery                                      |
| 7     | Introduce head movement and torso orientation compensation       |
| 8     | Add computer vision and face tracking                            |
| 9     | Add two-microphone sound localization                            |
| 10    | Introduce command-conditioned locomotion and additional skills   |
| 11    | Add the behavior engine and expressive keyframe movements        |

The most important milestone is getting the existing balance and recovery policies running reliably on the physical robot.

Everything else builds on that foundation.

I also want to preserve a reliable recovery mechanism throughout development, so that experimenting with new behaviors doesn't compromise the robot's ability to get itself upright.

---

## Conclusion

What started as an experiment in training a biped robot to stand upright is developing into something much more ambitious.

The interesting realization is that we don't necessarily need to train one enormous neural network to manage every aspect of the robot.

Instead, we can combine several technologies:

- Reinforcement learning for physical control.
- MuJoCo for simulation and training.
- ESP-DL for embedded neural network inference.
- ESP-PPQ for model conversion and quantization.
- FreeRTOS for managing real-time firmware.
- Computer vision for understanding the environment.
- Audio processing for sound localization.
- A behavior engine for personality and interaction.
- Keyframe-driven movements for expressive animation.

The result could be a robot that doesn't just execute predefined movements but reacts to its surroundings, maintains its balance, responds to people and expresses different behaviors.

And importantly, much of this can run on relatively accessible hardware.

The next big challenge is getting that first trained policy out of MuJoCo and onto the real ESP32-S3.

Once that's working, we have a foundation on which to build everything else.

*This is an evolving architecture, and I'll be documenting the implementation as Archie develops.*

### Technical references

The key Espressif resources for the implementation are:

- [ESP-DL documentation](https://docs.espressif.com/projects/esp-dl/en/latest/) — inference APIs, model deployment and profiling.
- [ESP-PPQ model conversion](https://docs.espressif.com/projects/esp-dl/en/latest/tutorials/how_to_quantize_model.html) — exporting quantized `.espdl` models.
- [ESP-DL supported operators](https://github.com/espressif/esp-dl/blob/master/operator_support_state.md) — important when checking whether the exported PPO network is compatible.
