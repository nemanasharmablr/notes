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

To understand Drive Control Block (DCB) and the Power Block, think of a Variable Speed Drive as a computer with a high-voltage power output. For a .NET developer, this is essentially the "Backend Logic" vs. the "Hardware Infrastructure."

1. The Power Block (The Infrastructure)
The Power Block is the physical hardware that handles the conversion of electricity. In software terms, this is your Hardware Layer or the Execution Environment.

How it works: It takes incoming AC power, converts it to DC (the DC Bus), and then "chops" it back into a simulated AC wave using IGBTs (Insulated Gate Bipolar Transistors) to spin the motor.

The Developer Analogy: Think of the Power Block as the CLR (Common Language Runtime). It’s the engine that actually executes the instructions and manages the "memory" (current/voltage). You don't usually code the CLR, but your code is limited by what the CLR can handle.

2. Drive Control Block (DCB) Logic (The Code)
DCB is the "Application Layer" inside the drive. It allows you to write custom logic that runs locally on the drive’s processor, independent of an external PLC (Programmable Logic Controller).

The Concept: Instead of just responding to a "Start" command, the drive can make decisions.

The Logic: It uses Function Block Diagram (FBD) logic. For a .NET developer, this is like Reactive Programming or LINQ. Data flows from an Input (a sensor), through a Logic Gate (an AND, OR, or Timer), to an Output (turning the motor or a relay).

Example Use Case: A "Pump Jam" logic.

Logic: IF (Motor Torque > 90%) AND (Speed < 10Hz) FOR (5 seconds) THEN (Reverse Motor for 2 seconds).

This prevents the need for a central server (PLC) to constantly monitor the pump; the drive "handles its own exceptions."

3. How SoMove Utilizes Both
SoMove acts as your IDE (Integrated Development Environment), similar to Visual Studio, to bridge the gap between your logic and the power hardware.

For the Power Block:

Configuration: You use SoMove to set the "Hardware Constraints." You define the maximum current (I 
max
​	
 ) the Power Block is allowed to output to prevent it from "crashing" (tripping).

Thermal Monitoring: SoMove gives you a "Diagnostic" view of the Power Block’s temperature. If the IGBTs get too hot, SoMove shows you the thermal state, much like monitoring CPU/RAM usage in a profiler.

For the DCB Logic:

Graphical Programming: Schneider provides a "Logic Builder" tab within SoMove (for supported drives like the ATV340 or ATV900). You don't write C#; you drag and drop blocks and "wire" them together.

Compilation & Deployment: Once you’ve built your logic, you click Write to Device. This is your Deployment/Build step. The logic is compiled into a format the drive's microprocessor understands and is stored in non-volatile memory.

Live Debugging: While the motor is running, SoMove highlights the "wires" in the logic diagram. If a line is Green, that boolean value is True. If Black, it's False. This is exactly like Runtime Breakpoints or Data Tips in Visual Studio.

Summary Comparison
Concept	.NET Equivalent	SoMove's Role
Power Block	Server Hardware / CLR	Resource monitoring and safety limits.
DCB Logic	Middleware / Business Logic	The IDE where you write and "debug" the flow.
Parameters	appsettings.json	The key-value pairs that define behavior.
Inputs/Outputs	API Endpoints / Events	The "triggers" for your logic to execute.


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
