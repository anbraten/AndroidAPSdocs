# Freestyle Libre 1

To use your Libre as a CGM that is getting new BG values every 5 minutes without having to scan the sensor, you need to buy an NFC to Bluetooth bridge (commercially available devices, based on the obsolete [LimiTTer](https://github.com/JoernL/LimiTTer) project).

:::{admonition} Libre 2, Libre 1 US and Libre Pro :class: warning Verify the bridge and the app you want to use are compatible with your sensor.  
:::

Several bridges are available on the market:

-   [MiaoMiao Reader](https://www.miaomiao.cool/) (version 1, 2 or 3) also available on AliExpress.
-   [Blucon Nightrider](https://www.ambrosiasys.com/our-products/blucon/) (mind recent firmware versions might not be compatible with all apps, the vendor app does not broadcast data to AAPS)
-   [Bubble (or Bubble Mini)](https://www.bubblesmartreader.com/) from European vendors ([Bubblan](https://www.bubblan.org/), [BubbleShop](https://bubbleshop.eu/)) or for Russian users [here](https://vk.com/saharmonitor/). Also available on AliExpress.
-   Atom for Russian users.

Historically it is possible to use a specific watch, the Sony Smartwatch 3 (SWR50) which has an NFC chip that can be enabled and used as an NFC collector. However the custom NFC to Bluetooth bridges listed above offer a less complex solution and would be used by the majority of those wanting to use their Libre 1 (non-US) as a CGM.

-   Sony Smartwatch 3 (SWR50) <https://github.com/pimpimmi/LibreAlarm/wiki/>

Wenn Du den Libre 1 als BZ-Quelle nutzt, können aus Sicherheitsgründen die Funktionen *Enable SMB always* und *Enable SMB after carbs* für den SMB Algorithmus leider nicht zur Verfügung gestellt werden. Die BZ-Werte des Libre 1 sind für einen sicheren Einsatz dieser Funktionen nicht glatt genug. Näheres hierzu findest du unter [Glättung der Blut-Glukose-Daten](../Usage/Smoothing-Blood-Glucose-Data-in-xDrip.md).

## 1. Using xDrip+

-   xDrip+ supports Miaomiao, Bubble, Blucon, Atom and LibreAlarm.
-   You can safely download the [latest APK (stable)](https://xdrip-plus-updates.appspot.com/stable/xdrip-plus-latest.apk) unless you need recent features, in which case you should use the latest [Nightly Snapshot](https://github.com/NightscoutFoundation/xDrip/releases).
-   Follow setup instructions on [xDrip+ settings page](../Configuration/xdrip.md).
-    You also need [OOP2](https://drive.google.com/file/d/1f1VHW2I8w7Xe3kSQqdaY3kihPLs47ILS/view) for Libre 1 US (and Libre 2 EU).
-   Wähle xDrip+ in der [KONFIGURATION, BZ-Quelle](../Configuration/Config-Builder.md#bg-source) aus.

## 2. Using Glimp

-   Glimp supports Miaomiao, Blucon and Bubble for Libre 1 and Libre 2 EU.
-   Du benötigst Glimp Version 4.15.57 oder neuer. Older versions are not supported.
-   Install [Glimp](https://play.google.com/store/apps/details?id=it.ct.glicemia).
-   Select Glimp in in [ConfigBuilder, BG Source.](../Configuration/Config-Builder.md#bg-source)

## 3. Using Tomato

- Tomato is the vendor app for Miaomiao.
- Install [Tomato](http://tomato.cool/#download_page) and follow the vendor [instructions](http://tomato.cool/how-to-broadcast-data-to-android-aps/tips/).
- Select Tomato in in [ConfigBuilder, BG Source](../Configuration/Config-Builder.md#bg-source).

## 4. Using Diabox

- Diabox is the vendor app for Bubble.
- Install [Diabox](https://t.me/s/DiaboxApp). In Settings, Integration, enable Share data with other apps.

![Diabox](../images/Diabox.png)

- Wähle xDrip+ in der [KONFIGURATION, BZ-Quelle](../Configuration/Config-Builder.md#bg-source) aus.
