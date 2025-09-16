# AICA (AI Controlled Autonomy) System

This directory now contains both the original PCA (Patient Controlled Analgesia) system and the new AICA (AI Controlled Autonomy) system, based on the PCA model but adapted for autonomous AI control.

## AICA System Overview

The AICA system implements AI-controlled autonomy using the same infrastructure as the PCA system but with different semantics:

### Component Mapping

| PCA Component | AICA Component | Purpose |
|---------------|----------------|---------|
| Patient | AI Agent | Primary user interface for autonomous actions |
| Clinician | Supervisor | Human oversight and emergency controls |
| Temperature Sensor | System Load Sensor | Monitors system resource utilization |
| Pulse Sensor | Decision Confidence Sensor | Monitors AI decision confidence levels |
| Drug Pump Actuator | Action Executor | Executes autonomous actions |
| Safety Monitor | Decision Validator | Validates AI decisions and actions |
| Medical Database | Event/Decision Log | Records all autonomous decisions and actions |

### Key Features

1. **Autonomous Mode Toggle**: The AI can be switched between manual and autonomous operation
2. **Decision Confidence Monitoring**: System tracks confidence levels for autonomous decisions
3. **Action Limiting**: Configurable limits on number and intensity of autonomous actions
4. **Emergency Stop**: Human supervisor can immediately halt autonomous operations
5. **Event Logging**: All decisions and actions are logged for audit and analysis

### Running the AICA System

To run the AICA system instead of the PCA system:

```bash
# Build the sensor and actuator binaries
make

# Start MongoDB (if using database logging)
# For macOS with brew:
brew services start mongodb-community@7.0
# Or with Docker:
docker run -tp 27017:27017 --rm mongo

# Run the AICA system
python main.py --aica
```

### AICA Configuration

The AICA system uses the following default configuration:

- **Action Execution Rate**: Every 2 seconds (configurable)
- **Maximum Actions per Round**: 10 actions (configurable)
- **Round Duration**: 60 seconds (configurable)
- **Confidence Threshold**: 70% for autonomous actions
- **Database**: MongoDB at localhost:27017/aicadb
- **Sensors**: System load (port 8001), Decision confidence (port 8002)

### GUI Interfaces

1. **AI Agent Control Window**:
   - Displays remaining actions in current round
   - Shows time to next allowed action
   - Shows time to next round reset
   - Shows current autonomous mode status
   - Provides manual "Execute Action" button
   - Provides "Toggle Autonomy" button

2. **Supervisor Control Window**:
   - Displays real-time system load
   - Shows AI decision confidence levels
   - Allows configuration of action count and intensity
   - Provides "Emergency Stop" button
   - Displays system warnings and status

### Autonomous Decision Logic

The AICA system makes autonomous decisions based on:

1. **Time Constraints**: Respects minimum time between actions
2. **Resource Limits**: Will not exceed configured action limits per round
3. **Confidence Threshold**: Only acts when decision confidence > 70%
4. **Safety Checks**: All actions are validated before execution

### Testing

Basic tests are available in `../spec/aica-checks/`:

- `basic-init.maude`: Tests system initialization
- `action-limits.maude`: Verifies action limit enforcement

Run tests with:
```bash
cd ../spec
# If maude binary is available:
maude aica-checks/basic-init.maude
```

### Compatibility

The AICA system maintains full compatibility with the existing PCA infrastructure:

- Uses the same sensor and actuator interfaces
- Compatible with existing MongoDB setup
- Reuses GUI framework and networking components
- Can be run alongside PCA system (different databases/ports)

## Switching Between PCA and AICA

- **PCA System**: `python main.py` (default)
- **PCA System v2**: `python main.py --two`
- **AICA System**: `python main.py --aica`

Both systems can coexist and use the same underlying infrastructure with different configurations.