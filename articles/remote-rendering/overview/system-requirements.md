---
title: Systemanforderungen
description: Listet die Systemanforderungen für Azure Remote Rendering auf.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: 320f9ef1285f16a3f7b38b3b4be3e5c51eff269f
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729798"
---
# <a name="system-requirements"></a>Systemanforderungen

In diesem Kapitel werden die Mindestsystemanforderungen für das Arbeiten mit *Azure Remote Rendering* (ARR) aufgeführt.

## <a name="development-pc"></a>Entwicklungs-PC

* Windows 10, Version 1903 oder höher.
* Aktuelle Grafiktreiber.
* Optional: [H265-Hardwarevideodecoder](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7), wenn Sie eine lokale Vorschau von remote gerendertem Inhalt verwenden möchten (z. B. in Unity).

> [!IMPORTANT]
> Windows Update stellt nicht immer die neuesten GPU-Treiber bereit. Überprüfen Sie die Website Ihres GPU-Herstellers auf die neuesten Treiber:
>
> * [AMD-Treiber](https://www.amd.com/en/support)
> * [Intel-Treiber](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-Treiber](https://www.nvidia.com/Download/index.aspx)

In der folgenden Tabelle wird aufgeführt, welche GPUs H265-Hardwarevideodecodierung unterstützen.

| GPU-Hersteller | Unterstützte Modelle |
|-----------|:-----------|
| NVIDIA | Überprüfen Sie die **NVDEC-Unterstützungsmatrix** [unten auf dieser Seite](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Ihre GPU benötigt die Angabe „Ja“ in der Spalte **H.265 4:2:0 8-Bit**. |
| AMD | GPUs mit mindestens Version 6 des [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6) von AMD. |
| Intel | Skylake und neuere CPUs |

Obwohl der richtige H265-Codec möglicherweise installiert ist, können Sicherheitseigenschaften für die Codec-DLLs Fehler bei der Codecinitialisierung verursachen. Im [Leitfaden zur Problembehandlung](../resources/troubleshoot.md#h265-codec-not-available) werden Schritte zum Beheben dieses Problems beschrieben. Das DLL-Problem kann nur auftreten, wenn der Dienst in einer Desktopanwendung verwendet wird, z. B. in Unity.

## <a name="devices"></a>Geräte

Azure Remote Rendering unterstützt derzeit nur **HoloLens 2** und Windows-Desktops als Zielgerät. Weitere Informationen finden Sie im Abschnitt [Plattformeinschränkungen](../reference/limits.md#platform-limitations).

Es ist wichtig, den neuesten HEVC-Codec zu verwenden, da neuere Versionen beträchtliche Verbesserungen bei der Latenz aufweisen. So überprüfen Sie, welche Version auf Ihrem Gerät installiert ist:

1. Starten Sie den **Microsoft Store**.
1. Klicken Sie oben rechts auf die Schaltfläche mit den Auslassungspunkten ( **„...“** ).
1. Wählen Sie **Downloads und Updates** aus.
1. Suchen Sie in der Liste nach **HEVC-Videoerweiterungen vom Gerätehersteller**. Wenn dieser Punkt nicht unter Updates aufgeführt ist, ist die aktuellste Version bereits installiert.
1. Stellen Sie sicher, dass der aufgeführte Codec mindestens Version **1.0.21821.0** aufweist.
1. Klicken Sie auf die Schaltfläche **Updates abrufen**, und warten Sie, bis die Updates installiert wurden.

## <a name="network"></a>Netzwerk

Eine stabile Netzwerkverbindung mit geringer Latenz ist wichtig für eine gute Benutzererfahrung.

Weitere Informationen dazu finden Sie im speziellen Kapitel zu den [Netzwerkanforderungen](../reference/network-requirements.md).

Informationen zum Behandeln von Netzwerkproblemen finden Sie im [Problembehandlungsleitfaden](../resources/troubleshoot.md#unstable-holograms).

### <a name="network-firewall"></a>Netzwerkfirewall

### <a name="sdk-version--0176"></a>SDK-Version >= 0.1.76

Beim Rendering per Remotezugriff von virtuellen Computern werden freigegebene IP-Adressen aus folgenden IP-Adressbereichen verwendet:

| Name             | Region         | IP-Präfix         |
|------------------|:---------------|:------------------|
| Australien (Osten)   | australiaeast  | 20.53.44.240/28   |
| East US          | eastus         | 20.62.129.224/28  |
| USA (Ost) 2        | eastus2        | 20.49.103.240/28  |
| Japan, Osten       | japaneast      | 20.191.165.112/28 |
| Nordeuropa     | northeurope    | 52.146.133.64/28  |
| USA Süd Mitte | southcentralus | 20.65.132.80/28   |
| Asien, Südosten   | southeastasia  | 20.195.64.224/28  |
| UK, Süden         | uksouth        | 51.143.209.144/28 |
| Europa, Westen      | westeurope     | 20.61.99.112/28   |
| USA, Westen 2        | westus2        | 20.51.9.64/28     |

Stellen Sie sicher, dass Ihre Firewalls (auf dem Gerät, in Routern usw.) diese IP-Adressbereiche und die folgenden Ports nicht blockieren:

| Port              | Protocol  | Allow    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | Ausgehend |

#### <a name="sdk-version--0176"></a>SDK-Version < 0.1.76

Stellen Sie sicher, dass Ihre Firewalls (auf dem Gerät, in Routern usw.) die folgenden Ports nicht blockieren:

| Port              | Protocol | Allow    | Beschreibung |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Ausgehend | Anfängliche Verbindung (HTTP-Handshake) |
| 8266              | UDP      | Ausgehend | Datenübertragung |
| 5000, 5433, 8443  | TCP      | Ausgehend | Erforderlich für das [ArrInspector-Tool](../resources/tools/arr-inspector.md)|

## <a name="software"></a>Software

Die folgende Software muss installiert sein:

* Aktuellste Version von **Visual Studio 2019** [(Download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio-Tools für Mixed Reality](/windows/mixed-reality/install-the-tools). Insbesondere sind die folgenden *Workloadinstallationen* obligatorisch:
  * **Desktopentwicklung mit C++**
  * **Entwicklung für die universelle Windows-Plattform (UWP)**
* **Windows SDK 10.0.18362.0** [(Download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(Download)](https://git-scm.com/downloads)
* Optional: Um den Videostream vom Server auf einem Desktop-PC anzuzeigen, benötigen Sie die **HEVC-Videoerweiterungen** [(Microsoft Store-Link)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Stellen Sie sicher, dass die neueste Version installiert ist, indem Sie im Store nach Updates suchen.

## <a name="unity"></a>Unity

Installieren Sie für die Entwicklung mit Unity eine unterstützte Version von Unity. Diese können Sie [hier](https://unity3d.com/get-unity/download) herunterladen. Sie sollten zum Verwalten von Installationen den Unity Hub verwenden.
Schließen Sie unbedingt folgende Module in Ihre Unity-Installation ein:
* **UWP**: Buildunterstützung für Universelle Windows-Plattform
* **IL2CPP**: Windows-Buildunterstützung (IL2CPP)

### <a name="unity-2019"></a>Unity 2019

Für Unity 2019 werden die Versionen 2019.3 und 2019.4 LTS unterstützt. Wenn Sie allerdings die OpenXR-Version des Plug-Ins verwenden möchten, benötigen Sie eine 2020-basierte Version.

ARR für Unity 2019 unterstützt sowohl die **integrierte XR-Legacyintegration** für Windows Mixed Reality als auch das neue Plug-In-Framework des **XR SDK**.

### <a name="unity-2020"></a>Unity 2020

Verwenden Sie für Unity 2020 die neueste Version von Unity (2020.3).

> [!IMPORTANT]
> Bei Verwendung der OpenXR-Version des Plug-Ins muss die *universelle Renderpipeline* (URP) mindestens die Version 10.5.1 haben. Öffnen Sie zur Überprüfung den Paket-Manager im Unity-Menü *Windows*, und sehen Sie sich den Abschnitt *Universal RP* (Universelle Renderpipeline) an: ![Version der universellen Renderpipeline](./media/unity-universal-rp-version-10-5-1.png)

> [!IMPORTANT]
> Beim **WMR-Plug-In (Windows Mixed Reality) für Unity 2020.3** kommt es derzeit zu einer Leistungsbeeinträchtigung mit ARR. Für ein besseres Ergebnis empfiehlt es sich, entweder Unity 2019.X zu verwenden oder zur OpenXR-Version zu wechseln.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Rendern eines Modells mit Unity](../quickstarts/render-model.md)