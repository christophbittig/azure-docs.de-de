---
title: Datenblatt für Azure Percept DK
description: Das Datenblatt für Azure Percept DK enthält ausführliche Informationen zu Gerätespezifikationen.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 2ec8e46dc881b48e4f885aa2237fae063c9d4305
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222422"
---
# <a name="azure-percept-dk-datasheet"></a>Datenblatt für Azure Percept DK

|Produktspezifikation           |Wert     |
|--------------------------------|--------|
|Produktdesign               |Integrierte Aufnahmen der 80/20 1010-Serie |
|Leistung                     |0,7 TOPS (Azure Percept-Vision)|
|Zielbranchen               |Einzelhandel <br> Fertigung <br> Hotel- und Gaststättengewerbe <br> Intelligente Gebäude <br> Automatisch |
|Hero-Szenarien                  |Weniger als zehn Minuten vom Auspacken bis zu den ersten KI-Frames <br> Weniger als 30 Minuten vom Auspacken bis zum Prototyp <br> Erfassen und Beschriften erster Trainingsdaten <br> Grundlegende KI-Modellanpassung <br> Erweiterte KI-Modellanpassung |
|Lieferumfang                 |1x Azure Percept DK-Trägerplatine  <br> 1x [Azure Percept-Vision](./azure-percept-vision-datasheet.md) <br> 1x RGB-Sensor (Kamera) <br> 1x USB 3.0-Kabel (Typ C) <br> 1x Netzkabel (Gleichstrom) <br> 1x Wandler (Wechselstrom in Gleichstrom) <br> 2x WLAN-Antenne  |
|OS                              |[CBL-Mariner](https://github.com/microsoft/CBL-Mariner)           |
|Verwaltungssteuerungsebene        |Azure Device Update (ADU) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)          |
|Unterstützte Software und Dienste |Azure Device Update <br> [Azure IoT](https://azure.microsoft.com/overview/iot/) <br> [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Central](https://azure.microsoft.com/services/iot-central/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) und [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1) <br> [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) <br> [Azure Mariner-Betriebssystem mit Konnektivität](https://github.com/microsoft/CBL-Mariner) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX-Runtime](https://www.onnxruntime.ai/) <br> [TensorFlow](https://www.tensorflow.org/) <br> [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) <br> IoT Plug & Play <br> [Azure-Gerätebereitstellungsdienst (Device Provisioning Service, DPS)](../iot-dps/index.yml) <br> [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) <br> [Power BI](https://powerbi.microsoft.com/)      |
|Allgemeiner Prozessor               |NXP iMX8m (Azure Percept DK-Trägerplatine)        |
|KI-Beschleunigung                 |1x Intel Movidius Myriad X mit integriertem ISP (Azure Percept-Vision) |
|Sensoren und visuelle Indikatoren   |Sony IMX219-Kamerasensor mit 6P-Objektiv<br>Auflösung: 8 MP bei 30 FPS, Entfernung: 50 cm bis unendlich<br>Sichtfeld: 120 Grad (diagonal), Farbe: WDR (Wide Dynamic Range), Rolling Shutter mit festem Fokus|
|Sicherheit                        |TPM 2.0 Nuvoton NCPT750 |
|Konnektivität                    |WLAN und Bluetooth über Realtek RTL882CE-Single-Chip-Controller     |
|Storage                         |16 GB     |
|Arbeitsspeicher                          |4 GB     |
|Ports                           |1x Ethernet <br> 2x USB-A 3.0 <br> 1x USB-C     |
|Betriebstemperatur           |-0 °C bis 35 °C     |
|Lagertemperatur       |-40 °C bis 85 °C     |
|Relative Luftfeuchtigkeit               |10 % bis 95 %    |
|Zertifizierung                   |CE <br> ACMA <br> FCC <br> IC <br> NCC <br> VCCI und MIC <br> NRTL <br> CB   |
|Stromversorgung                    |19 VDC bei 3,42 A (65 W) |