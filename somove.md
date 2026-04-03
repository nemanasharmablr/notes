# Schneider SoMove: Technical Reference for Developers

## 1. Overview
**SoMove** is the Integrated Development Environment (IDE) used to configure, commission, and maintain Schneider Electric motor control devices (Variable Speed Drives, Soft Starters, and Servos). 

For a developer, think of SoMove as **Visual Studio for Hardware**. It replaces manual keypad entry with a graphical interface for managing "Hardware APIs."

---

## 2. Tool Architecture & Details
| Feature | Description | Developer Analogy |
| :--- | :--- | :--- |
| **FDT/DTM** | The "Frame" (SoMove) and the "Drivers" (DTMs). | **IDE vs. SDKs/NuGet Packages**. You need the specific DTM for your drive model to communicate. |
| **Connectivity** | Modbus (USB-RJ45), Ethernet, Bluetooth. | **Connection Strings**. Establishing the communication protocol to the "Remote Server" (the Drive). |
| **Project File** | `.smv` file extension. | **appsettings.json / .csproj**. Stores all key-value pair parameters and logic. |
| **Cost** | Free Download. | **Open Tooling**. |

---

## 3. The Hardware Stack: Power Block vs. DCB

### The Power Block (Infrastructure Layer)
* **Definition:** The physical power electronics (Rectifier, DC Bus, Inverter/IGBTs) that convert electricity to motion.
* **Developer Analogy:** The **CLR (Common Language Runtime)** or the **Server Hardware**. It handles the "low-level" execution and memory (current) management.
* **SoMove Role:** Used to set "Environment Variables" (Max Voltage, Thermal Limits) and monitor hardware health (CPU/Thermal temperature).

### Drive Control Block - DCB (Application Layer)
* **Definition:** An embedded logic engine within the drive that allows for custom programming using Function Block Diagrams (FBD).
* **Developer Analogy:** **Middleware / Lambda Functions**. It allows for event-driven logic (e.g., `if (Sensor_A) then (Stop_Motor)`).
* **SoMove Role:** Acts as the **Graphical Code Editor**. It allows you to "wire" inputs to outputs and perform **Live Debugging** by watching logic paths change color during execution.

---

## 4. The Commissioning Process (The "Deployment" Pipeline)

### Phase 1: Offline Configuration (Coding)
* **Define Properties:** Enter motor nameplate data (kW, Voltage, Amps).
* **Set Defaults:** Define ramp times (Accel/Decel) and speed limits.

### Phase 2: Connection & Transfer (Deployment)
* **Scan:** Discover the device on the COM port.
* **Store to Device:** Equivalent to **`dotnet publish`**. Sends your local config to the drive's non-volatile memory.
* **Load from Device:** Equivalent to **Decompiling**. Pulls the current settings from the hardware into your IDE.

### Phase 3: Tuning & Testing (Integration Testing)
* **Auto-Tuning:** The "Hardware Handshake." The drive performs a discovery on the motor's electrical signature (Resistance/Inductance) to optimize the control loop.
* **Command Panel:** The **Postman/Swagger UI**. Allows you to manually trigger "Run/Stop" commands to verify the "Endpoint" (Motor) responds correctly.
* **Oscilloscope:** The **Profiler/Debugger**. A real-time trace of variables (Current, Torque, Speed) to find bottlenecks or mechanical "exceptions."

---

## 5. Summary Cheat Sheet for .NET Developers

* **Parameters** = Private Variables / Properties.
* **Auto-Tune** = Object Initialization / Reflection.
* **Faults/Errors** = Runtime Exceptions (e.g., `OvercurrentException`).
* **Store to Device** = Deployment to Production.
* **FBD Logic** = Reactive Programming / Event Listeners.
