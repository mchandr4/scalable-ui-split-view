Rabbit Scalable UI Split Panel (AAOS)

This repository contains a Runtime Resource Overlay (RRO) that enables and configures Scalable UI split-panel layouts in Android Automotive OS (AAOS) by overlaying CarSystemUI resources.

The overlay defines separate app and map panels, assigns panel roles, configures default activities, and applies screen-size–aware bounds — all without modifying SystemUI source code.

Features

Enables Scalable UI in CarSystemUI

Defines navigation (app) and map panels

Configures split layout via Panel XML

Maps panels to default activities

Supports multiple screen sizes using swXXXdp resources

Implemented entirely as a static RRO

Target

Target package: com.android.car.systemui

Overlay type: Static Runtime Resource Overlay (RRO)

Build system: Soong

Platform: Android Automotive OS (AAOS)

Build & Install

The overlay is built automatically as part of the product build:

runtime_resource_overlay {
    name: "RabbitScalableUiSplitPanel",
    resource_dirs: ["res"],
    product_specific: true,
}


Ensure the overlay manifest targets CarSystemUI and the correct overlayable group:

<overlay
    android:targetPackage="com.android.car.systemui"
    android:targetName="<overlayable-name>"
    android:isStatic="true"
    android:priority="900" />

Verification

After flashing the build:

adb shell cmd overlay list --user 0 | grep car.systemui
adb shell dumpsys overlay | grep -i rabbit


You should see the overlay enabled and applied to CarSystemUI.

Notes

Panel roles must reference string-arrays, not strings

Use different default activities for app and map panels during testing

Overlay priority must be higher than existing product/reference overlays
