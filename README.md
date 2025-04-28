# IoT Light Scheduler Dashboard (with Real & Simulated Hardware Support)

This project is a simple but real-world **IoT Light Scheduler**.

You can schedule your **relay/light** to **turn ON or OFF at specific times** from a web browser UI!

Supports:
- ✅ Real Arduino hardware
- ✅ Fake Arduino simulation (no real hardware needed)

---

## 🚀 Project Structure

| File                | Purpose                                              |
|:--------------------|:-----------------------------------------------------|
| `light-scheduler.py` | Main MQTT subscriber + serial writer to Arduino      |
| `web-socket.py`      | WebSocket server to receive browser UI schedules and forward to MQTT |
| `fake-simulation.py` | Simulated Arduino using virtual serial (`loop://`)    |
| `frontend.html`      | Browser UI                                             |

---

## 🛠 Requirements

- Python 3.7+
- `paho-mqtt`
- `pyserial`
- `websockets`
- An MQTT broker (this project uses public IP: **157.173.101.159**, port **1883**)

Install dependencies:

```bash
pip install paho-mqtt pyserial websockets
```

---

## 📦 How To Run (Without Hardware) skip to line 66 if you have hardware

1. **Start the Fake Arduino:**
   ```bash
   python fake-simulation.py
   ```
   ✅ Simulates Arduino at `loop://` port.

2. **Start the MQTT Light Scheduler:**
   ```bash
   python light-scheduler.py
   ```
   ✅ Listens to MQTT messages and sends them to the fake serial.

3. **Start the WebSocket Server:**
   ```bash
   python web-socket.py
   ```
   ✅ Allows browser to communicate with MQTT through WebSocket.

4. **Open your `frontend.html`**
   - ✅ Enter the ON/OFF schedule time and press submit!
   - Make sure it connects to `ws://localhost:8765`

---

## ⚡ How To Run (With Real Arduino)

1. **Change port** in `light-scheduler.py`:

   From:
   ```python
   arduino = serial.serial_for_url('loop://', baudrate=9600, timeout=1)
   ```

   To (example for Windows COM5 or Linux /dev/ttyUSB0):
   ```python
   arduino = serial.Serial('COM5', baudrate=9600, timeout=1)
   ```

2. **Flash your Arduino with this sketch: also provided as 'sched_on_off.ino'**

   ```cpp
    int relayPin = 2;

    void setup() {
    Serial.begin(9600);
    pinMode(relayPin, OUTPUT);
    digitalWrite(relayPin, HIGH); // Relay OFF initially
    }

    void loop() {
    if (Serial.available() > 0) {
        String cmd = Serial.readStringUntil('\n');
        cmd.trim();
        if (cmd == "ON") {
        digitalWrite(relayPin, LOW); // Relay ON
        } else if (cmd == "OFF") {
        digitalWrite(relayPin, HIGH); // Relay OFF
        }
    }
    }
   ```

3. **Then repeat the same steps:**
   - Run `light-scheduler.py`
   - Run `web-socket.py`
   - Open `frontend.html`
   - Schedule ON/OFF times

✅ Now it controls the **real** relay/light!

---

## 🖥 Frontend (Browser UI)

Your frontend must:
- Connect to WebSocket: `ws://localhost:8765`
- Send schedules like: `12:30 ON` or `19:45 OFF`

**Run provided file 'frontend.html':**

---

## 🧐 How it All Works (Simple)

| Step | Description |
|:-----|:------------|
| 1 | UI sends schedule (e.g., `10:30 ON`) to WebSocket server |
| 2 | WebSocket server forwards the message to MQTT broker |
| 3 | `light-scheduler.py` subscribes and saves the schedule |
| 4 | At the correct time, it sends `ON` or `OFF` over Serial |
| 5 | Arduino receives the command and toggles the relay/light |

---

## 💡 Notes

- `loop://` is a virtual serial for simulation (no hardware needed).
- If you don't import `serial.urlhandler.protocol_loop`, `loop://` won't work!
- Scheduling resets automatically every day at **midnight (00:00)**.

---

## 🏁 Final Tip

If anything feels stuck, restart all scripts in this order:
1. `fake-simulation.py` ✅
2. `light-scheduler.py` ✅
3. `web-socket.py` ✅
4. Open `frontend.html` ✅

Everything will sync back properly!

---

# ✍️ Made with 💻 by **MUNEZA Jean Dieudonne**
Feel free to contact me via Email:munezadieudonne2021@gmail.com

