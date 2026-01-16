

# Scalable UI Split Panel – CarSystemUI RRO

## Overview

This repository provides a **static Runtime Resource Overlay (RRO)** that enables and configures **Scalable UI split-panel layouts** in **Android Automotive OS (AAOS)**.

The overlay targets **CarSystemUI** and defines:

* Multi-panel window states
* App and Map panel roles
* Panel layout bounds and layers
* Default activity mappings

All behavior is configured via resources only—no Java/Kotlin code changes are required.

---

## Target Platform

| Item         | Value                        |
| ------------ | ---------------------------- |
| OS           | Android Automotive OS (AAOS) |
| Target APK   | `com.android.car.systemui`   |
| Overlay Type | Static RRO                   |
| Partition    | `product`                    |
| Build System | Soong                        |

---

## Functional Scope

* Enable Scalable UI framework in CarSystemUI
* Define navigation (app) and map panels
* Support split-panel layouts (e.g., 60/40)
* Allow screen-size-specific customization using resource qualifiers
* Support OEM-specific UI layouts without forking SystemUI

---

## Resource Structure

```
res/
├── values/
│   ├── config.xml          # Scalable UI enablement & defaults
│   ├── arrays.xml          # Panel role definitions
│   ├── dimens.xml          # Default screen bounds
│   └── integers.xml        # Panel layer ordering
├── values-swXXXdp/
│   └── dimens.xml          # Screen-size specific overrides
└── xml/
    ├── app_panel.xml       # Navigation / app panel
    └── map_panel.xml       # Map panel
```

---

## Overlay Configuration

### Soong Module

```bp
runtime_resource_overlay {
    name: "ScalableUiSplitPanelRRO",
    resource_dirs: ["res"],
    product_specific: true,
}
```

### Overlay Manifest

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="vendor.oem.systemui.scalableui.splitpanel"
    android:hasCode="false">

    <overlay
        android:targetPackage="com.android.car.systemui"
        android:targetName="<overlayable-group>"
        android:isStatic="true"
        android:priority="900"/>
</manifest>
```

> `targetName` must match the overlayable group defined in
> `packages/apps/Car/SystemUI/res/values/overlayable.xml`

---

## Panel Roles

Panel roles are defined using **string-arrays** and referenced by Panel XML:

```xml
<string-array name="nav_components">
    <item>app_panel</item>
</string-array>

<string-array name="map_components">
    <item>map_panel</item>
</string-array>
```

---

## Default Activity Mapping

Each panel is mapped to a launchable activity:

```xml
<string-array name="config_default_activities">
    <item>app_panel;com.android.car.ui.paintbooth/.MainActivity</item>
    <item>map_panel;com.android.car.mapsplaceholder/.MapsPlaceholderActivity</item>
</string-array>
```

> Use distinct activities during validation to visually confirm panel separation.

---

## Layer Ordering

```xml
<integer name="app_panel_layer">10</integer>
<integer name="map_panel_layer">5</integer>
```

Lower layer values render behind higher ones.

---

## Build & Integration

1. Add the overlay directory under `vendor/<oem>/overlays/`
2. Include the overlay in the product makefile
3. Build and flash the image
4. Reboot the device

No runtime enablement is required for static overlays.

---

## Validation

### Overlay Status

```bash
adb shell cmd overlay list --user 0 | grep car.systemui
```

### Debug Overlay Resolution

```bash
adb shell dumpsys overlay | grep -i scalable
```

---

## Common Integration Issues

| Issue                            | Cause                            |
| -------------------------------- | -------------------------------- |
| Overlay applied but no UI change | Wrong target package             |
| Overlay ignored                  | Missing or incorrect targetName  |
| Panels overlap                   | Incorrect dp bounds              |
| Overlay overridden               | Lower priority than existing RRO |
| Same app in both panels          | Same default activity mapping    |

---

## Change Management

* Screen layout changes must be implemented via resource qualifiers
* Panel behavior changes require updating panel XML
* Overlay priority changes should be reviewed across product variants

---

## Compliance Notes

* Does not modify AOSP source code
* Compatible with overlayable enforcement
* Safe for OTA updates
* Suitable for multi-variant OEM product lines

---

## Ownership

**Team:** IVI / Android Platform
**Component:** CarSystemUI – Scalable UI
**Maintenance Level:** Product-specific

---

