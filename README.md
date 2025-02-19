# Intune + SCCM Device Dashboard

This repository contains a Flask-based web application that displays **managed device** information from **Microsoft Intune** and **SCCM** (Configuration Manager). Data is fetched via the **Microsoft Graph API**.

## Table of Contents
1. [Overview](#overview)  
2. [Prerequisites](#prerequisites)  
3. [Configuration](#configuration)  
4. [Installation](#installation)  
5. [Project Structure](#project-structure)  
6. [Usage](#usage)  
   - [Starting the App](#starting-the-app)  
   - [Refreshing the Cache](#refreshing-the-cache)  
7. [Endpoints and Pages](#endpoints-and-pages)  
   - ["/"](#index---)  
   - ["/summary"](#summary---)  
   - ["/sccm-intune"](#sccm-intune---)  
   - ["/legacy"](#legacy---)  
   - ["/devices"](#devices---)  
   - ["/update-cache"](#update-cache---)  
   - ["/loading-status"](#loading-status---)  
   - ["/reload-devices"](#reload-devices---)  
8. [Logging](#logging)  
9. [Contributing](#contributing)  
10. [License](#license)

---


## Overview

This application retrieves device data from **both** Intune and SCCM using Microsoft Graph API calls. It merges the results into a single cache file for fast lookups. Users can view and filter devices in various ways, including:

- Source (SCCM vs. Intune)  
- Device Model  
- Operating System  
- Country / HQ  
- "Legacy" status  

The application exposes multiple routes (pages) for different views and also uses **Socket.IO** for real-time messaging about the cache-loading progress.

---


## Prerequisites

1. **Python 3.7+**  
   Make sure you have a relatively recent version of Python 3.  
2. **Pipenv or Virtualenv** (optional but recommended)  
   For isolating dependencies.  
3. **Node.js + npm** (only if you want to modify front-end tooling, not strictly required).  

---

## Configuration

1. **Microsoft Graph Credentials**  
   - **Tenant ID** (`TENANT_ID`)  
   - **Client ID** (`CLIENT_ID`)  
   - **Client Secret** (`CLIENT_SECRET`)  

   These must be set in a `.env` file or in your environment variables.  
   For example, in `.env`:

   ```ini
   TENANT_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   CLIENT_ID=yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
   CLIENT_SECRET=ZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZ
   ```

2. **Cache File Storage**  
   - A JSON cache file is used to store fetched device data. By default, it’s in `./cache/devices_cache.json`.  
   - A debug version can also be stored at `./cache/debug_devices_cache.json` if `debug_mode=True`.

---

## Installation

1. Clone this repository:

   ```bash
   git clone https://github.com/your-org/intune-sccm-dashboard.git
   cd intune-sccm-dashboard
   ```

2. Create and activate a virtual environment (recommended):

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

4. Create (or edit) a `.env` file with your Microsoft Graph credentials (see [Configuration](#configuration)).

---

## Project Structure

Below is a simplified project structure:

```
intune-sccm-dashboard/
├─ app_copy.py
├─ get_data2_copy.py
├─ templates/
│   ├─ index.html
│   ├─ summary.html
│   ├─ sccm_intune.html
│   ├─ legacy.html
│   └─ devices.html
├─ static/
│   └─ (CSS, JS, images)
├─ cache/
│   ├─ devices_cache.json
│   └─ debug_devices_cache.json
├─ .env
├─ requirements.txt
└─ README.md
```

- **`app_copy.py`** – Main Flask application.  
- **`get_data2_copy.py`** – Logic for fetching devices from Microsoft Graph (both Intune and SCCM).  
- **`templates/`** – HTML templates for Flask’s `render_template` calls.  
- **`cache/`** – Directory holding the JSON cache files.

---

## Usage

### Starting the App

1. **Ensure** you have valid credentials in `.env`.  
2. **Run** the Flask-SocketIO application:

   ```bash
   python app_copy.py
   ```

3. By default, the app starts on port 5000. Go to `http://localhost:5000` in your browser.  

### Refreshing the Cache

When the app starts, it automatically attempts a **background update** of the cache (fetching both Intune and SCCM devices). You can also manually trigger a refresh:

1. Navigate to `http://localhost:5000/update-cache`, which starts a background job to fetch fresh data.  
2. You can poll `http://localhost:5000/loading-status` to see the status (`updating`, `completed`, `error`, etc.).  

---

## Endpoints and Pages

### **`/`** (Index)
Shows an overview with:
- **SCCM vs Intune** counts  
- **Legacy** vs Non-legacy counts  
- **HQ** distribution  
- Other summary metrics  

### **`/summary`**
Provides a **filterable** page (by HQ, country, model, source, OS) and **pagination** for the devices. Also includes stats on how many unique HQs, models, etc.

### **`/sccm-intune`**
Similar to **`/summary`**, but specifically focuses on showing **SCCM** vs **Intune** device distribution with filters and pagination.

### **`/legacy`**
A dedicated page for **legacy models**. Displays filter/pagination controls plus a chart of how many devices per legacy model.

### **`/devices`**
A more general listing of **all** devices, with filter/pagination controls. You can search by HQ, country, model, source, OS, and user principal name.

### **`/update-cache`**
A **manual** trigger to update the device cache in the background. This calls `update_cache()` from `get_data.py`.

### **`/loading-status`**
Check the **current** loading status:
- `updating` – in progress  
- `completed` – finished successfully  
- `error` – something went wrong  
- `idle` – not currently loading  

### **`/reload-devices`**
Forces the app to **reload** devices from the **existing** cache file without re-fetching from Microsoft Graph.

---

## Logging

- **Log Level**: By default, it’s set to `logging.INFO`. You can change this at the top of `app_copy.py`.  
- **Log Output**: Shown on stdout. If you want to store logs to a file, configure a `FileHandler` in `app_copy.py`.

---

## Contributing

1. **Fork** this repository.  
2. **Create** a feature branch.  
3. **Commit** your changes.  
4. **Open** a pull request.  

Please ensure your code adheres to **PEP 8** style guidelines and has no major linting issues.  

---
