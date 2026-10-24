<div align="center">

<img src="assets/logo.png" width="120" alt="Edgelectronix logo: a dark perfume bottle with a circuit-board pattern" />

# S.C.E.N.T

**Smart Customer Experience & Edge AIoT for Inventory & Threats**
A perfume shelf that recommends a fragrance for *you*, notices when a bottle leaves the shelf,
and hears one break, with the AI running on the microcontrollers inside it.

<br />

![Result](https://img.shields.io/badge/🏆_Second_Prize-B8913A?style=for-the-badge)
![Challenge](https://img.shields.io/badge/FPT_IoT_Challenge-2025-16403D?style=for-the-badge)
![Theme](https://img.shields.io/badge/AI_for_Smart_Retail-Silicon_Labs-C0392B?style=for-the-badge)

![EFR32](https://img.shields.io/badge/Silicon_Labs_EFR32xG24_·_xG26_·_MG21-16403D?style=flat-square)
![MLTK](https://img.shields.io/badge/Silicon_Labs_MLTK-16403D?style=flat-square)
![TFLite Micro](https://img.shields.io/badge/TFLite_Micro-16403D?style=flat-square&logo=tensorflow&logoColor=F2F0EA)
![Thread](https://img.shields.io/badge/OpenThread-16403D?style=flat-square)
![Micrium OS](https://img.shields.io/badge/Micrium_OS-16403D?style=flat-square)
![Raspberry Pi](https://img.shields.io/badge/Raspberry_Pi_4-16403D?style=flat-square&logo=raspberrypi&logoColor=F2F0EA)
![Flask](https://img.shields.io/badge/Flask-16403D?style=flat-square&logo=flask&logoColor=F2F0EA)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL_+_TimescaleDB-16403D?style=flat-square&logo=postgresql&logoColor=F2F0EA)
![Redis](https://img.shields.io/badge/Redis-16403D?style=flat-square&logo=redis&logoColor=F2F0EA)

</div>

> [!NOTE]
> Team **Edgelectronix**, FPT IoT Challenge 2025 (*AI for Smart Retail*, with Silicon Labs), May to
> September 2025. **Second Prize** at the final. This repo is a **showcase**; the firmware, gateway and
> dashboard source are private.

<p align="center"><img src="assets/hero.jpg" alt="S.C.E.N.T System banner: a hand reaching for a green perfume bottle, with the Edgelectronix logo" /></p>

<br />

## 🏆 Result

### **Second Prize**, FPT IoT Challenge 2025 ✅

<p align="center"><img src="assets/award.jpg" alt="Team Edgelectronix on stage with the Second Prize board, certificates and flowers, under a 'Congratulations to Edgelectronix' screen" /></p>

<table>
<tr>
<td width="50%" align="center"><img src="assets/pitch.jpg" alt="Pitching S.C.E.N.T on stage in front of the judges" /><br /><sub>Pitching S.C.E.N.T at the final</sub></td>
<td width="50%" align="center"><img src="assets/celebration.jpg" alt="The team celebrating with certificates after the result" /><br /><sub>Right after the result</sub></td>
</tr>
</table>

> *"We joined thinking it would be one more line on our CVs. The deeper we got, the more the
> competition pulled us in."* (the team, in FPT's announcement)

<br />

## 🌸 The problem

Walk into a perfume store and you're on your own: dozens of bottles, notes you can't smell from the
label, and a sales assistant who may or may not be free. The store has its own blind spots: which
bottles are actually being picked up, when a facing runs empty, and what happens when a glass bottle
hits the floor.

S.C.E.N.T turns a regular shelf into one that helps on both sides:

| For the customer | For the store |
| :--- | :--- |
| **A recommendation** from their personality, occasion and taste, with the matching bottle lighting up | **Live stock**: load cells and IR beams see each product leave the shelf |
| **Product info** on a touch screen at the shelf | **A drop alarm**: an on-device sound model hears a bottle break |
| **A voice or text chat** about what they're looking for | **Data** on what customers pick up, ask for and choose |

<br />

## 👥 Team

| | Who | What |
| :--- | :--- | :--- |
| 🖥️ | **Đặng Như Phước** | System architecture, Raspberry Pi gateway and Thread border router, the admin dashboard and the shelf's touch screen, the load-cell and IR sensor firmware |
| 🌸 | **Trương Gia Bảo** (me) | The perfume recommendation model on the EFR32xG24: dataset and features, a classifier and then an embedding model, INT8 deployment, UART link to the Pi |
| 🔊 | **Huỳnh Trọng Khiêm** | The drop-detection sound model on the EFR32xG26: dataset recording and augmentation, model, Micrium OS firmware |
| 🔌 | **Hồ Minh Thao** | Hardware |

Mentor: **Nguyễn Trung Hiếu**

<br />

## 🏗️ How the shelf works

Five pieces of hardware, one Thread mesh.

<p align="center"><img src="assets/slide-overview.jpg" alt="The S.C.E.N.T shelf with its boards, and its six ideas: edge AI models, low-power Thread networking, a friendly UI, customer insight, a cost-effective product, and room to scale" /></p>

<p align="center"><img src="assets/modules.jpg" alt="The five modules: EFR32xG24 for perfume suggestion, EFR32xG26 for drop detection and Si7021, EFR32xG26 for HX711 and IR, a Raspberry Pi 4 with an MG21 as the Thread border router, and the desktop admin" /></p>

```mermaid
flowchart LR
    subgraph SHELF["🧴 The shelf"]
        XG24["EFR32xG24<br/>perfume model<br/>10 product LEDs"]
        XG26A["EFR32xG26 #1<br/>drop-sound model<br/>Si7021 temp / humidity"]
        XG26B["EFR32xG26 #2<br/>2 × load cell · 8 × IR · PIR"]
        LCD["Touch screen"]
    end

    subgraph GW["📡 Raspberry Pi 4"]
        OTBR["OpenThread<br/>border router<br/>(MG21 RCP)"]
        PIWEB["Flask app<br/>shelf screen · NLP"]
    end

    subgraph DESK["🖥️ Store back office"]
        API["Flask + Celery"]
        DB[("PostgreSQL<br/>+ TimescaleDB")]
        R[("Redis")]
        ADMIN["Admin dashboard"]
    end

    XG26A -- "Thread / UDP" --> OTBR
    XG26B -- "Thread / UDP" --> OTBR
    PIWEB <-- UART --> XG24
    OTBR --> PIWEB
    LCD <-- WebSocket --> PIWEB
    PIWEB -- "LAN · MQTT / HTTP" --> API
    API --> DB
    API --> R
    ADMIN <-- WebSocket --> API
```

<p align="center"><img src="assets/shelf-bom.jpg" alt="Shelf bill of materials: the front face with the banner, LCD and product facings, the back face with the load cells, IR modules, the three EFR32 boards, the Raspberry Pi 4 and MG21 stick; 220 V supply through four adapters; up to 13 products, 5 kg per load cell" /></p>

| Part | Count | Role |
| :--- | :---: | :--- |
| **EFR32xG24** dev kit | 1 | Perfume recommendation model, lights the product LED |
| **EFR32xG26** dev kit | 2 | #1: drop-sound model + Si7021 · #2: load cells, IR and PIR |
| **EFR32MG21** USB stick | 1 | Radio (RCP) for the Pi's Thread border router |
| **Raspberry Pi 4** | 1 | Thread border router, shelf screen, local NLP |
| **HX711 + load cell** | 2 | Product platforms, 19 cm wide, up to 5 kg each |
| **IR modules** | 8 | One per product facing |
| **PIR** | 1 | A customer is standing at the shelf |
| **Product LEDs** | 10 | Light up the recommended bottle |

<br />

## 🌸 Recommending a perfume, on a microcontroller

*My part of the project.*

<p align="center"><img src="assets/suggestion.jpg" alt="The perfume suggestion module: Raspberry Pi 4 connected to an EFR32xG24 over UART, and a personal preference going through a neural network to a recommended bottle" /></p>

The customer tells the shelf what they like, by voice or on the screen. The Pi turns that into a
compact preference line and sends it over UART to the **EFR32xG24**, which runs the model locally and
lights the LED under the best match.

```mermaid
sequenceDiagram
    participant C as Customer
    participant P as Raspberry Pi
    participant X as EFR32xG24
    participant L as Product LED
    C->>P: "something floral for date night, long-lasting"
    P->>P: map to vocabulary → PREF line
    P->>X: UART: PREF,2,26,,1:14:39,1,1,2
    X->>X: 260-D multi-hot → INT8 model → 64-D embedding
    X->>X: cosine similarity vs. every perfume on the shelf
    X->>L: light the best match
    X-->>P: top 3 → shown on the screen
```

### First try: pick one of 50

The first model was a straight classifier, trained and profiled with Silicon Labs **MLTK**:

- **74 inputs**: age, 42 scent accords (citrus, vanilla, woody, leather…), gender, 9 personality types
  (bold, classic, romantic…), 10 occasions (work, date night, gym…), and preferred sillage, longevity
  and price
- **74 → 32 → 16 → 50**, fully connected, INT8: **10.4 KB**, **3.7k MACs**, **2 KB of RAM**
- **98.0 % average ROC AUC**, but only **66 % top-1 accuracy**, and 11 of the 50 perfumes were never
  predicted at all

The ranking was good, the single answer wasn't. With 50 classes and a handful of overlapping
preferences, several perfumes are legitimately right for the same person.

### What shipped: an embedding you can search

So the model stopped picking a class and started **describing** a perfume:

<p align="center"><img src="assets/slide-model-steps.jpg" alt="Model pipeline in four steps: a 260-dimension input vector, a Keras network 260 to 256 to 128 to a 64-dimension embedding with a 50-class classifier, contrastive fine-tuning that groups similar perfumes, and deployment of only the 64-dimension embedder in INT8" /></p>

- Each perfume's DNA (gender, brand, top / middle / base notes, main accords, sillage, longevity,
  price) becomes a **260-D multi-hot vector**: 3 genders, 40 brands, 164 notes, 42 accords
- A small network, **260 → 256 → 128 → 64**, is trained as a classifier first, then fine-tuned with a
  **contrastive loss** on L2-normalised embeddings, so the same perfume's variations land close
  together (cosine ≈ 1) and different perfumes stay apart (< 0.2)
- Only the **embedding** part is exported, full INT8 with a calibration set, along with the
  catalogue's precomputed 64-D embeddings as C headers
- On the device: preference → embedding → **cosine similarity against every perfume** → a ranked top
  3. Adding a perfume to the shelf is one more row in the catalogue, not a retrained classifier

<p align="center"><img src="assets/slide-model.jpg" alt="The suggestion model end to end: the preference form on the shelf screen becomes a 260-dimension one-hot input, goes through hidden layers 256, 128 and a 64-dimension embedding, and comes out as a ranked top 3 shown with the bottle on screen" /></p>

<br />

## 🔊 Hearing a bottle break

<p align="center"><img src="assets/drop-detect.jpg" alt="Drop detection module: Raspberry Pi 4 and an EFR32xG26 over Thread, a drop alarm and a temperature/humidity alarm sent to the admin" /></p>

The first **EFR32xG26** listens through its on-board microphone for one sound: a glass perfume bottle
hitting a tile floor.

- **Model**: a temporal-convolution keyword-spotting network from MLTK, retrained for two classes,
  **drop** and **unknown**
- **Data**: about **4,500** recorded drops, against **10,500+** "unknown" clips (claps, mixed
  background, other objects colliding) so a clap doesn't set it off. 1 s clips at 32 kHz, augmented
- **Front end**: 96-channel filterbank from 100 Hz to 16 kHz, with noise reduction and a DC notch
  filter, because the dev board's microphone is noisy at low frequencies
- **Firmware**: three **Micrium OS** tasks and a queue. One records and runs the model continuously,
  one reads the **Si7021** temperature and humidity every 5 s, one owns the Thread stack and sends
  whatever lands in the queue

<p align="center"><img src="assets/micrium-tasks.jpg" alt="Micrium OS tasks: OpenThread, AI drop detect and Si7021 sensor, and a timeline of the three tasks feeding one queue" /></p>

<br />

## 📦 Knowing what left the shelf

<p align="center"><img src="assets/sensors-thread.jpg" alt="Load cell to HX711 to ADC, IR module to a rising-edge trigger, both sent over Thread" /></p>

The second **EFR32xG26** watches the products:

- **8 IR beams**, one per facing, on GPIO interrupts with a 500 ms debounce. A break sends that
  product's code (`P001`, `P045`…)
- **2 load-cell platforms** read through HX711s every 500 ms. A drop of **100 g or more** means a
  bottle was lifted
- A **PIR** sensor says someone is standing at the shelf
- Each event goes out as a small **UDP packet over Thread** to the Pi

```mermaid
sequenceDiagram
    participant S as xG26 sensors
    participant B as Pi · border router
    participant D as Back office
    participant A as Admin / shelf screen
    S->>S: IR beam broken / weight −100 g
    S->>B: UDP over Thread: "P017"
    B->>D: event over the LAN
    D->>D: update stock, log time series
    D-->>A: WebSocket: stock + screen update
```

<br />

## 🖥️ Gateway and back office

<p align="center"><img src="assets/raspi.jpg" alt="Raspberry Pi 4 as the shelf's OpenThread border router and embedded PC" /></p>

- **Raspberry Pi 4**: the **OpenThread border router** (leader of the shelf's Thread mesh, with an
  MG21 USB stick as its radio), the shelf's touch-screen app, and the heavier local models: a
  **BERT-style NLP model** that pulls intent and preferences out of what the customer says
- **Back office**: Flask + Celery, **PostgreSQL with TimescaleDB** for sensor time series, Redis and
  MQTT for realtime events, and a web dashboard for stock, drop alerts and customer insight

<p align="center"><img src="assets/layers.jpg" alt="The five layers: physical (EFR32 boards, Raspberry Pi, sensors), edge processing (perfume, sound and NLP models), network (Thread mesh and LAN), data (PostgreSQL, TimescaleDB, MQTT, Redis, Flask) and application (admin dashboard, shelf screen)" /></p>

<br />

## 🧰 Stack

| Layer | Stack |
| :--- | :--- |
| **Edge AI** | Silicon Labs MLTK, TensorFlow / Keras, contrastive embeddings, TensorFlow Lite Micro (INT8) |
| **Firmware** | Simplicity Studio 5, C, OpenThread, Micrium OS, HX711 / IR / PIR / Si7021 drivers |
| **Network** | Thread mesh (IEEE 802.15.4, 6LoWPAN), OpenThread Border Router on an MG21 RCP, UDP, LAN |
| **Gateway** | Raspberry Pi 4, Flask, WebSocket, local NLP |
| **Back office** | Flask, Celery, PostgreSQL + TimescaleDB, Redis, MQTT, HTML / CSS / JS |

<br />

<div align="center">

| 🏆 Second Prize | 🧠 2 on-device models | 📡 1 Thread mesh | 🧴 13 products |
| :---: | :---: | :---: | :---: |
| **4** Silicon Labs boards | **10.4 KB** first model | **260 → 64** embedding | **~15k** audio clips |

</div>

<br />

---

<div align="center">

**[Bảo Trương](https://github.com/baotrq)** · with Đặng Như Phước, Huỳnh Trọng Khiêm and Hồ Minh Thao · Team Edgelectronix · FPT IoT Challenge 2025

</div>
