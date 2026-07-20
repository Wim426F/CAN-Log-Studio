# CAN Log Studio

A browser-based tool for analyzing automotive CANbus logs. Load your DBC files and a captured log, decode the signals, and plot them on a multi-axis time graph.

It is a single self-contained HTML file. There is nothing to install and it works offline.

## Features

- Decode DBC-defined signals from SavvyCAN / GVRET CSV logs
- Multi-axis time plot with one vertical axis per unit
- Load multiple DBCs and assign each to a specific bus, with order resolving ID collisions
- Zoom, pan, and an overview navigator for long logs
- Measure tool for reading deltas between two points
- Derived channels built from math expressions (power, energy, rates, and so on)
- Export the plot as PNG
- Handles large logs (millions of frames)

## Getting started

Open `CAN_Log_Studio.html` in any modern browser, either by double-clicking it or dragging it into a tab.

## Loading data

Two inputs are required:

1. One or more DBC files (the signal definitions).
2. A log CSV (a SavvyCAN / GVRET export).

Drag both onto the window, or use the **⚙ DBCs** and **+ log CSV** buttons in the top bar. Once a log is loaded, the sidebar lists the messages and signals found in it.

## Using it

### Plotting signals

The left sidebar lists messages, each expandable into its signals. Click a signal to add it to the plot; click again to remove it. Each signal gets its own color, and signals sharing a unit share a Y axis.

- **Search**: filter the signal list by name.
- **Collapse/expand all**: toggle all message groups at once.
- **Δ button** (on hover): plot the difference between consecutive samples of a signal.

### Multiple DBCs and buses

Click **⚙ DBCs** to open the DBC manager, where you can:

- Add several DBC files.
- Assign each DBC to **All buses** or a specific **bus (0–7)**. Buses present in the loaded log are marked with a dot.
- Reorder DBCs with the ▲▼ arrows. When two DBCs define the same message ID, the one higher in the list wins.

This handles logs where the same ID means different things on different buses, such as an application bus and a drivetrain bus. Bus numbers are assigned manually; nothing is auto-detected. When more than one bus is present, the sidebar groups messages by bus.

A single DBC left on **All buses** decodes everything, with no bus setup needed.

### Navigating the plot

- **Scroll over the plot**: zoom toward the cursor.
- **Scroll over a Y axis**: scale that axis only.
- **Scroll over the time bar**: zoom time only.
- **Drag**: pan.
- **Double-click**: reset the view, based on where you click (plot, single axis, or time bar).
- **Overview strip** (below the plot): drag the highlighted band to move through the log, or drag its edges to resize the view.

### Measure tool

Click **Measure**, then click two points or drag between them. Markers snap to the nearest samples, and the readout shows the time difference and each plotted channel's change between the two points. Markers persist through zoom and pan.

### Derived channels

Click the **＋** in the sidebar to create a calculated channel. Give it a name, an optional unit, and a formula referencing signal names. For example:

```
PackVoltage * PackCurrent                  power
integ(PackVoltage * PackCurrent) / 3600    energy in Wh
deriv(VehicleSpeed)                        acceleration
```

Signals in different messages that share a name can be disambiguated as `MessageName.SignalName`. Derived channels can reference other derived channels.

Available functions:

| Type | Functions |
|---|---|
| Operators | `+  -  *  /  ^` |
| Math | `abs  sqrt  exp  ln  log  sin  cos  tan  floor  ceil  round  sign  min  max  pow  clamp` |
| Time-series | `delta` (difference between samples), `deriv` (rate per second), `integ` (cumulative integral), `cumsum` (running sum) |

### Toolbar

- **Fit all**: show the whole log and auto-fit every axis.
- **Clear plot**: remove all plotted channels.
- **Export PNG**: save the current plot as an image.

## Input format notes

- Log CSVs use the standard SavvyCAN / GVRET export, with columns for timestamp, ID, bus, length, and data bytes. Timestamps in microseconds or seconds are both handled.
- Message IDs are read as hexadecimal.
- Classic CAN frames (up to 8 data bytes) are supported. CAN-FD payloads are truncated to 8 bytes.