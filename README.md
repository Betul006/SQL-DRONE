# SQL-DRONE
# 🛸 KAIROS Project — Drone Surveillance & Threat Detection Database

> **Final Project | SQL Server Management Studio 22**
> A relational database system designed for autonomous drone-based surveillance, real-time object detection, threat assessment, and mission management.

---

## 📌 Project Overview

**KAIROS** is a military-grade drone surveillance database system built with **Microsoft SQL Server**. It models the full lifecycle of a drone mission — from user authentication and mission assignment to AI-based object detection, depth analysis, risk scoring, and automated alert dispatching.

The system supports multiple drone units, sensor integration (thermal & high-res cameras), route and obstacle tracking, and detailed system logging.

---

## 🗂️ Project Structure

```
SQL Server Management Studio 22/
│
├── Final_Project phase3+phase2 kod/
│   ├── SQLQuery1.sql              # Main schema + seed data
│   │
│   ├── FUNCTION/
│   │   ├── FUNCTIONS1.sql         # fn_GetRiskLabel
│   │   └── FUNCTIONS2.sql         # fn_GetDroneMissionCount
│   │
│   ├── PROCEDURE/
│   │   ├── PROCEDURE1.sql         # sp_AssignDroneToMission
│   │   └── PROCEDURE2.sql         # Additional stored procedure
│   │
│   ├── TRIGGERS/
│   │   ├── TRIGGER1.sql           # trg_AfterInsertAlert
│   │   ├── TRIGGER2.sql
│   │   ├── TRIGGER3.sql
│   │   ├── TRIGGER4.sql
│   │   └── TRIGGER5.sql
│   │
│   ├── QUERIES/
│   │   ├── Queries1.sql
│   │   ├── Queries2.sql
│   │   ├── Queries3.sql
│   │   ├── Queries4.sql
│   │   └── Queries5.sql
│   │
│   └── EKRAN GORUNTUSU/           # Screenshots (Functions, Procedures, Queries, Triggers)
│
├── EERD DIAGRAM/                  # Enhanced Entity-Relationship Diagram
├── Veri Tabanı Diyagramı/         # Database diagram screenshots
├── README_FINAL_PROJECT.docx      # Project report (Word)
└── REPORT_FINAL_PROJECT1.docx     # Extended project report
```

---

## 🗃️ Database Schema

The database is named **`KAIROS_Project`** and contains **30+ relational tables** across the following functional modules:

### 👤 User & Access Control
| Table | Description |
|---|---|
| `ROLE` | System roles (Interface Operator, Mission Commander, etc.) |
| `USERS` | System users with role assignments |
| `PERMISSION` | Access permissions with security levels |
| `ROLE_PERMISSION` | Many-to-many mapping of roles to permissions |

### 🚁 Drone & Mission Management
| Table | Description |
|---|---|
| `MISSION` | Mission records with status (`Active`, `Completed`, `Cancelled`) |
| `DRONE` | Drone fleet with battery status and assignment state |
| `DRONE_MISSION` | Assignment bridge table between drones and missions |

### 📷 Imaging & Detection
| Table | Description |
|---|---|
| `IMAGE_SENSOR` | Thermal and high-res sensors attached to drones |
| `CAMERA` | Camera units linked to sensors |
| `IMAGE` | Captured frames with timestamps |
| `CLASS` | Object classification categories (Human-Soldier, Vehicle, Animal, etc.) |
| `OBJECT_DETECTION` | AI detection records with confidence scores and bounding boxes |
| `DEPTH_ANALYSIS` | Distance estimation via MiDaS depth model |
| `RISK_ASSESSMENT` | Risk scoring (0–100) per detection |

### 🚨 Alerting & Reporting
| Table | Description |
|---|---|
| `ALERT` | Generated alerts with priority and delivery status |
| `RECEIVER` | Alert receivers (HQ Officers, Field Teams, etc.) |
| `ALERT_RECEIVER` | Alert delivery routing and communication channel |
| `REPORT` | Mission summary reports |
| `REPORT_DETAIL` | Detailed breakdown per report |

### 🗺️ Navigation & Routes
| Table | Description |
|---|---|
| `ROUTE` | Named patrol routes with total distance |
| `LOCATION` | GPS coordinates linked to drones and routes |
| `WAYPOINT` | Ordered waypoints per route |
| `OBSTACLE` | Detected obstacles along waypoints with threat levels |

### 🧍 Human Classification (Inheritance)
| Table | Description |
|---|---|
| `HUMAN` | Base entity (height, weight, class) |
| `SOLDIER` | Extends HUMAN with unit and rank info |
| `CIVILIAN` | Extends HUMAN with national ID and profession |
| `PERSONNEL` | Extends HUMAN with department and registration |

### ⚙️ System & Hardware
| Table | Description |
|---|---|
| `HARDWARE` | Physical drone components |
| `SOFTWARE_MODULE` | Software modules per hardware component |
| `VERSION` | Version history per software module |
| `SYSTEM_SETTING` | Per-drone configuration parameters |
| `PARAMATER` | Limit values for system settings |
| `BACKUP` | Database backup records |
| `SYSTEM_LOG` | Audit log for all system actions |

---

## ⚙️ Functions

### `fn_GetRiskLabel(RiskLevel)`
Converts a numeric risk score (0–100) into a human-readable label.

| Score Range | Label |
|---|---|
| ≥ 80 | `Critical` |
| ≥ 60 | `High` |
| ≥ 40 | `Middle` |
| < 40 | `Low` |

```sql
SELECT Assessment_ID, Risk_Level, dbo.fn_GetRiskLabel(Risk_Level) AS Risk_Label
FROM RISK_ASSESSMENT;
```

---

### `fn_GetDroneMissionCount(DroneID)`
Returns the total number of missions a given drone has been assigned to.

```sql
SELECT Drone_ID, Serial_No, dbo.fn_GetDroneMissionCount(Drone_ID) AS Total_Missions
FROM DRONE;
```

---

## 🔧 Stored Procedures

### `sp_AssignDroneToMission`
Safely assigns a drone to an active mission with full validation:
- Checks that the drone exists and its status is `Ready`
- Checks that the mission exists and its status is `Active`
- Inserts into `DRONE_MISSION` and updates the drone's status to `On Mission`
- Returns an output message with success or error detail

```sql
DECLARE @msg VARCHAR(255);
EXEC dbo.sp_AssignDroneToMission
    @DroneID        = 20,
    @MissionID      = 3,
    @AssignmentTime = '2026-06-01 09:00:00',
    @ResultMessage  = @msg OUTPUT;
SELECT @msg AS Result;
```

---

## ⚡ Triggers

### `trg_AfterInsertAlert`
Fires **after INSERT** on the `ALERT` table. Automatically writes an audit entry to `SYSTEM_LOG` for every new alert, including the Alert ID and priority level.

> Additional triggers handle further audit and integrity enforcement across the system (see `TRIGGERS/` folder).

---

## 🚀 Getting Started

### Prerequisites
- Microsoft SQL Server 2019 or later
- SQL Server Management Studio (SSMS) 22

### Setup

1. Create the database:
```sql
CREATE DATABASE KAIROS_Project;
USE KAIROS_Project;
```

2. Run the main schema and seed script:
```
Final_Project phase3+phase2 kod/SQLQuery1.sql
```

3. Apply functions, procedures, and triggers in order:
```
FUNCTION/FUNCTIONS1.sql
FUNCTION/FUNCTIONS2.sql
PROCEDURE/PROCEDURE1.sql
PROCEDURE/PROCEDURE2.sql
TRIGGERS/TRIGGER1.sql → TRIGGER5.sql
```

4. Run sample queries from the `QUERIES/` folder to verify the setup.

---

## 🧪 Sample Data

The database is pre-loaded with:
- **3 users** across 3 roles
- **3 drones** (KAIROS-ESP32-001/002/003) in various statuses
- **5 missions** (border security, hazard analysis, threat detection, etc.)
- **3 object detections** with depth analysis and risk assessments
- **3 alerts** routed to mission commanders and operations centers
- **3 patrol routes** with waypoints and obstacles

---

## 👩‍💻 Authors

| Name | Role |
|---|---|
| Betül Alkan | Interface Operator / Developer |
| Beyzanur Alkan | Mission Commander / Developer |
| Bilal Alkan | System Manager / Developer |

---

## 📄 License

This project was developed as an academic final project. All rights reserved by the authors.
