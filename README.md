Programming Open Distributed Systems in Maude
=============================================

This repository contains a [Maude](https://maude.cs.illinois.edu) specification and implementation of both:

1. **PCA System**: A [*Patient Controlled Analgesia*](https://en.wikipedia.org/wiki/Patient-controlled_analgesia) (PCA) unit, as a case study for the specification of open distributed systems in Maude. PCA is a widely used method for relieving pain efficiently and safely in hospitalized patients.

2. **AICA System**: An *AI Controlled Autonomy* (AICA) system based on the PCA model, demonstrating autonomous decision-making and action execution with human oversight.

## System Comparison

| Feature | PCA System | AICA System |
|---------|------------|-------------|
| **Primary Control** | Patient button press | AI autonomous decisions |
| **Human Role** | Patient + Clinician | AI Agent + Human Supervisor |
| **Safety Mechanism** | Dose limits + timing | Action limits + confidence thresholds |
| **Monitoring** | Medical sensors (pulse, temp) | System sensors (load, confidence) |
| **Actions** | Drug administration | Configurable autonomous actions |
| **Emergency Control** | Clinician override | Supervisor emergency stop |


The content of this repository is organized in three directories:

* [`spec`](spec) contains self-contained Maude specifications of both PCA and AICA units suitable for formal verification. Some safety checks are executed using Maude bounded search. These checks revealed some flaw that let the patient receive an overdose of analgesia, which is fixed in a second version (by replacing the file [`pca.maude`](spec/pca.maude) with [`pca2.maude`](spec/pca2.maude)). The AICA system includes similar safety checks for autonomous action limits.
* [`impl`](impl) replaces the external-world components (sensors, actuators, databases, GUI, etc.) of the specification in [`spec`](spec) with Maude external objects connecting to external hardware or software outside Maude. Both PCA and AICA systems can be run from this directory.

Instructions on how to run these examples can be found in their corresponding directories. [Here](https://youtu.be/6nHAEaIZ4JE) is a video showing the behavior of the open system implementation.


Open system components
----------------------

Both PCA and AICA systems are [object-oriented systems](https://maude.lcc.uma.es/maude-manual/maude-manualch6.html) that result from the interactions via asynchronous messages of several objects belonging to different classes, each implemented in a separate file:

### PCA System Components:

* `Pca` ([`pca.maude`](spec/pca.maude) and [`pca2.maude`](spec/pca2.maude)) is the class of the main object of the PCA specification. It controls and maintains the state of the system, and interacts with all other components.
* `Gui` ([`gui.maude`](spec/gui.maude)) is in charge of the interaction with the users (patient or clinician) through a graphical user interface.
* `Database` ([`database.maude`](spec/database.maude) or [`mongodb.maude`](impl/mongobd.maude)) maintains the logs of the system events for traceability and runtime monitoring using a database.
* `Monitor` ([`monitor.maude`](spec/monitor.maude)) monitors system events through checks on the database to detect undesired behavior and notify the `Pca` object. In particular, the absence of overdose is checked on a sliding time window.
* `Sensor` ([`sensor.maude`](spec/sensor.maude)) provides on demand measurements of the vitals of the patient (heart rate and temperature).
* `Actuator` ([`actuator.maude`](spec/actuator.maude)) controls the PCA pump that administers analgesia to the patient.

### AICA System Components:

* `Aica` ([`aica.maude`](spec/aica.maude) and [`impl/aica.maude`](impl/aica.maude)) is the main class that controls the autonomous system and makes decisions based on sensor input and confidence levels.
* `Gui` - Provides two interfaces: AI Agent control and Human Supervisor oversight.
* `Database` - Logs all autonomous decisions, confidence levels, and actions taken.
* `Monitor` - Validates autonomous decisions and enforces safety constraints.
* `Sensor` - System load sensor (reuses temperature sensor) and decision confidence sensor (reuses pulse sensor).
* `Actuator` - Executes autonomous actions (reuses the same actuator infrastructure).

## Running the Systems

### PCA System:
```bash
cd impl
make                    # Build sensor/actuator binaries
python main.py          # Run PCA v1
python main.py --two    # Run PCA v2 (fixed version)
```

### AICA System:
```bash
cd impl
make                    # Build sensor/actuator binaries  
python main.py --aica   # Run AICA system
```

See the respective README files in each directory for detailed setup instructions.

Following the Maude convention, `Pca` will create most objects dynamically by sending a message to a manager object, `guiManager`, `databaseManager`, `monitorManager`, `sensorManager`, or `actuatorManager`. These objects will be fully implemented in Maude in the specification, while they will be or interact with external objects in the implementation. However, the `Pca` class (as well as the `Monitor` class) is shared between both variants.

Constants and common declarations for all these objects are kept in [`open-system.maude`](spec/open-system.maude). Other files, like [`configuration.maude`](configuration.maude), [`json.maude`](json.maude), or [`real-time.maude`](real-time.maude), provide the equational infrastructure for the specification.

This [3 minutes video](https://youtu.be/6nHAEaIZ4JE) shows how to run the open system.
