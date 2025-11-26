# SummerBio LIMS & PCR Workflow Architecture

**Source:** LIMS Overview Presentation (Last updated: Aug 27, 2021) 
**Context:** Technical overview of the high-throughput, file-based orchestration engine used for COVID-19 PCR testing.

---

## 1. High-Level Architecture
The system relies on a **file-based integration architecture** heavily utilizing **Mirth Connect** as the central orchestration engine. It decouples the digital requisition data from physical sample processing until reconciliation in the LIMS.

* **Middleware:** AWS Mirth Engine (Replaced "Watchman" service in Aug 2021).
* **Storage:** AWS S3 File Server for intermediate file exchange.
* **Core Systems:** Lockbox / LIMS / SFDC.

## 2. Data Ingestion Streams

### Digital Stream (Test Requisition Forms - TRFs)
* **Sources:** PreciseQ, Kayla, Point n Click, LAUSD Microsoft.
* **Protocol:** Partners push TRF batches as **CSV files** via sFTP.
* **Normalization:** Mirth picks up CSVs, converts them to JSON payloads, and injects them into the LIMS.

### Physical Stream (Samples)
* **Format:** Sample tubes with barcodes in 96-well racks.
* **Scanning:**
    * **Accessioning:** Ziath Accessioning Scanners scan racks -> generate CSV.
    * **Consolidation:** Samples re-racked -> Ziath Consolidation Scanner -> generate CSV.
* **Data Capture:** "Watchman" (legacy) or Mirth reads these CSVs to generate JSON payloads for the LIMS.

## 3. PCR Orchestration (The "Wet Lab")
The "Wet Lab" workflow is triggered by file generation and folder monitoring on local servers (`10.50.0.x`).

* **Prep:**
    * System generates barcode files (CSV) and Mirth submits a JSON update to LIMS creating "Plate" and "Racks" entities.
    * Mirth generates a **PLRN file** (thermal cycler instruction) and moves it to a lab-accessible folder: `C:/SB2-Share/PCRStart/`.
* **Hardware:**
    * **Instruments:** 43 distinct CFX384 real-time PCR instruments.
    * **Protocol:** 90-minute run time.
* **Data Generation:**
    * Output: 1 PCRD file + **19 CSV files** generated per plate.
    * Processing: Mirth parses 5 of these CSVs to create a single JSON result payload.

## 4. Logic & Result Analysis
* **Metrics:** Each well has 3 Cq values (N1, N2, RP) calculated by CFX Maestro.
* **Controls:**
    * Located in the lower-right block of the plate (NTC, Negative, Positive).
    * **Rule:** If *any* control fails, the **whole rack** is retested.
* **Sample Status:** LIMS assigns status: Review required, Retest required, or Rack retest required.

## 5. Reporting & Release
* **Human-in-the-Loop:** A Clinical Lab Scientist (CLS) reviews the plate; status changes from "In Process" to "Ready for Review".
* **Latency Buffer:** Generated result CSVs are held on S3 for **30 minutes** to allow PDF reports to generate before release.
* **Public Health:** Results sent to CalReddie via Mirth; acknowledgement received.
