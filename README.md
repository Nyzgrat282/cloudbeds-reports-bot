# 🍳🛏️ Hotel Ops Extractor: Cloudbeds → F&B & Housekeeping

A **client-side web tool** with a *human-in-the-loop* approach that eliminates manual data entry and Excel cleanup in hotel operations. It solves two daily logistical problems: knowing exactly which guests have breakfast included in their reservation, and, for Housekeeping, interpreting bed-setup requirements written as free-text notes.

> **In production at Up Hoteles (Buenos Aires).**

> **Commercial Impact:** turns a tedious nightly reconciliation + Excel cleanup into a guided **3-minute** run.

---

## 🚀 The Problem vs. The Solution

**The Problem:**
The night auditor had to download the *Hosted Guests Report* from Cloudbeds, manually clean and format the Excel (delete columns, adjust widths, reformat cells), and then review it row by row to determine who had breakfast included and how each room had to be made up. Slow, repetitive, error-prone — every single night.

**The Solution:**
A two-step system that captures the reservation data directly from the Cloudbeds interface while the auditor browses normally, and turns it into two print-ready Excel files — one for **Breakfast (F&B)** and one for **Bedding (Housekeeping)** — in a guided, three-minute flow.

---

## 🔐 Privacy by design

The whole thing runs **entirely in the browser**. Guest data is **never uploaded to any server** — the userscript copies the reservations to the clipboard, you paste them into a self-contained HTML page, and everything is processed locally. Real CSVs and screenshots with personal data are kept out of the repo by design (`.gitignore`). For a tool that touches guest names and stays, this matters.

---

## 🏗️ Architecture (client-side web app)

```
[Cloudbeds Web UI]
       │  Tampermonkey userscript captures reservations
       ▼  → copies CSV to the CLIPBOARD (no file on disk)
[ Self-contained HTML page ]  ← paste with Ctrl+V
       │  JS engine (parsing, breakfast, bedding, blocked rooms)
       │  + ExcelJS (formatting)
       ▼
   [F&B Excel]  and  [Bedding Excel]
```

- **Userscript (Tampermonkey / JS):** injected into the browser; while the auditor browses reservations, it captures each guest's notes and requirements and copies them to the clipboard.

![Floating widget in Cloudbeds](assets/widget_capture.png)

- **Web app (`web/`):** a single self-contained HTML file (built by a Python packer) that embeds the engine (`motor.js`), the Excel generator (`generar_excel.js` on **ExcelJS**), and the UI. The user pastes the data, confirms date and blocked rooms, and clicks **GENERATE** — the two Excel files download locally.

- **Robust parsing:** handles both clipboard formats (`;`-separated from the widget, or TAB when copied from a spreadsheet), Argentine `DD/MM/YYYY` dates, multi-room reservations, and orders everything by room number.

---

## 📋 What it produces

**Breakfast (F&B):** automatically flags which guests have breakfast included based on rate plan / reservation note, so the kitchen gets a clean, print-ready list at the start of the day.

![Breakfast report](assets/output_desayunos.jpeg)

**Bedding (Housekeeping):** interprets the free-text bedding notes to define each room's setup (double, twin, triple…), reconciles check-ins vs check-outs, flags blocked rooms, and lays out occupancy — print-ready.

![Bedding report](assets/output_bedding.jpeg)

---

## ✅ Validated engine

The JavaScript engine is a **port of the original Python engine** (openpyxl) and is **validated against it** — same numbers, same layout. The Python version is kept as a validated reference and console fallback; the web app is what ships to the front desk.

---

## 📊 Commercial Impact

| | Manual process | With this tool |
|---|---|---|
| **Time** | 20–30 min of data entry + cleanup | ~3 minutes, guided |
| **Errors** | Row-by-row omissions | Deterministic processing |
| **Output** | Unformatted Excel, manual layout | Print-ready, two files |
| **Data** | Copied around in loose files | Never leaves the browser |
| **Scalability** | Degrades with more guests | Constant regardless of volume |

Designed to adapt to any property on Cloudbeds by editing a configuration file — no code changes.

---

## 🛠️ Technologies

*   **Browser automation:** Tampermonkey (JavaScript userscript)
*   **Engine & report generation:** vanilla JavaScript + **ExcelJS** (conditional formatting, column widths), 100% client-side
*   **Reference engine:** Python 3.12 + openpyxl (validated baseline)
*   **Distribution:** a single self-contained `.html` file — no install, runs by double-click

---

## 📬 Contact

Do you have an operational workflow you want to automate? I can develop a similar solution for your business.

*   **LinkedIn:** [Sebastián González](https://www.linkedin.com/in/sebasti%C3%A1n-gonz%C3%A1lez-571a18195/)
*   **Email:** [sebag2298@gmail.com](mailto:sebag2298@gmail.com)
