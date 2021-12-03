---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 4957bd3a4b43413a7377e0c9eb019a3bfe4ae105
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529857"
---
Wenn Sie für *eingeschränkte Geräte* entwickeln, können Sie IoT Plug & Play zusammen mit Folgendem verwenden:

- [IoT-Clientbibliotheken des Azure SDK für Embedded C](https://aka.ms/embeddedcsdk)
- [Azure RTOS](/azure/rtos/overview-rtos)

Dieser Artikel enthält Links und Ressourcen für diese eingeschränkten Szenarien.

## <a name="prerequisites"></a>Voraussetzungen

Viele der folgenden Beispiele erfordern ein bestimmtes Hardwaregerät, und die Voraussetzungen für die einzelnen Beispiele unterscheiden sich. Klicken Sie jeweils auf den Link für das entsprechende Beispiel, um ausführliche Anweisungen zu Voraussetzungen, zur Konfiguration und zum Build zu erhalten.

## <a name="use-the-sdk-for-embedded-c"></a>Verwenden des SDK for Embedded C

Das SDK for Embedded C bietet eine schlanke Lösung zum Verbinden von eingeschränkten Geräten mit Azure IoT-Diensten, einschließlich der Verwendung der [IoT Plug & Play-Konventionen](../articles/iot-develop/concepts-convention.md). Die folgenden Links führen zu Beispielen für MPU-basierte Geräte und für Schulungs- und Debugzwecke.

### <a name="use-an-mcu-based-device"></a>Verwenden eines MPU-basierten Geräts

Ein vollständiges End-to-End-Tutorial mit dem SDK für Embedded C, Device Provisioning Service und IoT Plug & Play auf einem MPU-Gerät finden Sie unter [Bereitstellen des Microchip PIC-IoT Wx-Entwicklungsboards für das Herstellen einer Verbindung mit Azure IoT-Diensten](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Beispiele zur Einführung

Das SDK for Embedded C-Repository enthält [mehrere Beispiele](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) zur Verdeutlichung der Verwendung von IoT Plug & Play:

> [!NOTE]
> Diese Beispiele werden zu Schulungs- und Debugzwecken bei der Ausführung unter Windows und Linux gezeigt. In einem Produktionsszenario sind die Beispiele nur für eingeschränkte Geräte vorgesehen.

- [Beispiel für einen Thermostaten mit dem SDK for Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/main/sdk/samples/iot/paho_iot_pnp_sample.c)

- [Beispiel für eine Temperatursteuerung mit dem SDK for Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/main/sdk/samples/iot/paho_iot_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Verwenden von Azure RTOS

Azure-RTOS umfasst eine vereinfachte Schicht, die native Konnektivität zu Azure IoT-Clouddiensten hinzufügt. Diese Schicht bietet einen einfachen Mechanismus zum Verbinden eingeschränkter Geräte mit Azure IoT und verwendet zugleich die erweiterten Funktionen von Azure RTOS. Weitere Informationen finden Sie unter [Was ist Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Toolketten

Die Azure RTOS-Beispiele stehen mit verschiedenen Kombinationen aus IDE und Toolkette zur Verfügung, etwa:

- IAR: [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/)-IDE von IAR
- GCC/CMake: Erstellen von Builds auf der Grundlage des Open-Source-Buildsystems [CMake](https://cmake.org/) und der [GNU Arm Embedded-Toolkette](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: [MCUXpresso-IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) von NXP
- STM32Cube: [STM32Cube-IDE](https://www.st.com/en/development-tools/stm32cubeide.html) von STMicroelectronics
- MPLAB: [MPLAB X-IDE](https://www.microchip.com/mplab/mplab-x-ide) von Microchip

### <a name="samples"></a>Beispiele

Die folgende Tabelle enthält Beispiele, die den Einstieg für verschiedene Geräte mit Azure RTOS und IoT Plug & Play zeigen:

Hersteller | Sicherungsmedium | Beispiele |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2021_11_03.zip) • [MPLAB](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2021_11_03.zip)
| MXCHIP | [AZ3166](../articles/iot-develop/quickstart-devkit-mxchip-az3166.md) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_MIMXRT1060_IAR_Samples_2021_11_03.zip) • [MCUXpresso](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_MIMXRT1060_MCUXpresso_Samples_2021_11_03.zip)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32F746G-DISCO_IAR_Samples_2021_11_03.zip) • [STM32Cube](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32F746G-DISCO_STM32CubeIDE_Samples_2021_11_03.zip)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_IAR_Samples_2021_11_03.zip) • [STM32Cube](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_STM32CubeIDE_Samples_2021_11_03.zip)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_IAR_Samples_2021_11_03.zip) • [STM32Cube](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_STM32CubeIDE_Samples_2021_11_03.zip)
