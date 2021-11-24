---
title: Aktualisieren von Threat Intelligence-Daten
description: Das Threat Intelligence-Datenpaket wird mit jeder neuen Defender für IoT-Version oder bei Bedarf zwischen den Releases bereitgestellt.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: ac743c9e8c53e40743bda0c50017885f40381971
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293191"
---
# <a name="threat-intelligence-research-and-packages"></a>Forschung und Pakete zur Bedrohungsanalyse #
## <a name="overview"></a>Übersicht ##

Sicherheitsteams in Microsoft führen proprietäre Forschungen von ICS-Bedrohungsanalyse und Sicherheitsrisiken aus. Zu diesen Teams gehören MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (Digital Crimes Unit) und Section 52 (IoT/OT/ICS-Domänenexperten, die ICS-spezifische Zero-Days, Reverse-Engineering-Schadsoftware, Kampagnen und Angreifer verfolgen).

Die Teams bieten Sicherheitsermittlung, -analyse und -reaktion für folgende Komponenten von Microsoft:

- Cloudinfrastruktur und Dienste.
- Herkömmliche Produkte und Geräte.
- Interne Unternehmensressourcen.

Sicherheitsteams profitieren von folgenden Vorteilen:

- Schutz vor bekannten und relevanten Bedrohungen.
- Einblicke, die Ihnen bei der Triage und Prioritätensetzung helfen.
- Ein Verständnis für den vollen Kontext von Bedrohungen, bevor sie betroffen sind.
- Relevantere, präzisere und handlungsrelevante Daten.

Diese Intelligenz bietet kontextbezogene Informationen, um die Analysen der Microsoft-Plattform zu bereichern, und unterstützt die verwalteten Dienste des Unternehmens bei der Reaktion auf Vorfälle und der Untersuchung von Sicherheitsverletzungen. Threat Intelligence-Pakete enthalten Signaturen (einschließlich Schadsoftwaresignaturen), CVEs und andere Sicherheitsinhalte.

## <a name="when-are-packages-delivered"></a>Wann werden Pakete übermittelt? ##

Threat Intelligence-Pakete werden ungefähr einmal im Monat oder bei Bedarf häufiger bereitgestellt. Ankündigungen zu neuen Paketen sind unter https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT verfügbar.

Sie können das aktuellste gelieferte Paket vom **Threat Intelligence-Aktualisierungsbereich** der Seite **Aktualisierungen** auf dem Defender für IoT im Azure-Portal abrufen.

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Aktualisieren von Threat Intelligence-Paketen auf Ihren Sensoren ##

Es gibt drei Optionen zum Aktualisieren von Threat Intelligence-Paketen auf Ihren Sensoren:

- Automatisches Pushen der Pakete an die Sensoren, sobald sie von Defender für IoT übermittelt werden
- Manuelles Pushen von Threat Intelligence-Paketen bei Bedarf an die Sensoren
- Herunterladen eines Pakets und anschließendes Hochladen auf einen oder mehrere Sensoren

Benutzer mit den Berechtigungen „Sicherheitsleseberechtigter“ für Defender für IoT können Pakete automatisch und manuell auf Sensoren pushen.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Automatisches Pushen von Threat Intelligence-Updates auf Sensoren ###

Threat Intelligence-Pakete können auf mit der *Cloud verbundenen* Sensoren automatisch aktualisiert werden, sobald sie von Defender für IoT veröffentlicht werden. Sie stellen die automatischen Paketupdates sicher, indem Sie beim Onboarding eines mit der Cloud verbundenen Sensors die Option **Automatic Threat Intelligence Updates** (Automatische Threat Intelligence-Updates) aktivieren. Weitere Informationen finden Sie unter [Integrieren eines Sensors](getting-started.md#onboard-a-sensor).

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Manuelles Pushen von Threat Intelligence-Updates auf Sensoren ###

Ihre *mit der Cloud verbundenen* Sensoren können automatisch mit Threat Intelligence-Paketen aktualisiert werden. Wenn Sie jedoch einen konservativeren Ansatz verfolgen möchten, können Sie Pakete auch nur dann aus dem Portal von Defender für IoT auf Sensoren pushen, wenn Sie dies für erforderlich halten. Dadurch können Sie steuern, wann ein Paket installiert wird, ohne es herunterladen und dann auf Ihre Sensoren hochladen zu müssen.

**So pushen Sie Pakete manuell**

1. Navigieren Sie in Microsofte Defender für IoT zur Seite **Standorte und Sensoren**.
1. Wählen Sie die Auslassungspunkte (...) für einen Sensor und dann **Threat Intelligence-Update pushen** aus. Im Feld **Status des Threat Intelligence-Updates** wird der Updatestatus angezeigt.

#### <a name="change-the-threat-intelligence-update-mode"></a>Ändern des Modus von Threat Intelligence-Updates ####

Sie können den Threat Intelligence-Updatemodus für einen Sensor nach dem ersten Onboarding ändern.

**So ändern Sie den Updatemodus**

1. Wählen Sie die Auslassungspunkte (...) für einen Sensor und dann **Bearbeiten** aus.
1. Aktivieren oder deaktivieren Sie den Umschalter **Automatic Threat Intelligence Updates** (Automatische Threat Intelligence-Updates).

### <a name="download-packages-and-upload-to-sensors"></a>Herunterladen von Paketen und Hochladen auf Sensoren ###

Pakete können im Azure-Portal heruntergeladen und manuell in einzelne Sensoren hochgeladen werden. Wenn Ihre Sensoren über die lokale Verwaltungskonsole verwaltet werden, können Sie Threat Intelligence-Pakete auf die Verwaltungskonsole herunterladen und sie an mehrere Sensoren gleichzeitig übertragen.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Laden Sie Updates im Azure-Portal herunter.":::

Diese Option ist sowohl für *mit der Cloud verbundene* als auch für *lokal verwaltete* Sensoren verfügbar.

**So führen Sie einen Upload auf einen einzelnen Sensor aus**

1. Navigieren Sie zur Microsoft Defender für IoT-Seite **Updates**.

2. Laden Sie das **Threat Intelligence**-Paket herunter, und speichern Sie es.

3. Melden Sie sich bei der Sensorkonsole an.

4. Wählen Sie im Seitenmenü **Systemeinstellungen** aus.

5. Wählen Sie **Threat Intelligence-Daten** und dann **Update** aus.

6. Laden Sie das neue Paket hoch.

**So führen Sie einen Upload auf mehrere Sensoren gleichzeitig aus**

1. Navigieren Sie zur Microsoft Defender für IoT-Seite **Updates**.

2. Laden Sie das **Threat Intelligence**-Paket herunter, und speichern Sie es.

3. Melden Sie sich bei der Verwaltungskonsole an.

4. Wählen Sie im Seitenmenü **Systemeinstellungen** aus.

5. Wählen Sie im Abschnitt **Sensor-Engine-Konfiguration** die Sensoren aus, die die aktualisierten Pakete erhalten sollen.  

6. Wählen Sie im Abschnitt **Threat Intelligence-Daten auswählen** das Pluszeichen ( **+** ) aus.

7. Hochladen des Pakets

## <a name="review-package-update-status-on-the-sensor"></a>Überprüfen des Paketupdatestatus auf dem Sensor ##

Der Updatestatus und die Versionsinformationen zu einem Paket werden in den **Systemeinstellungen** des Sensors im Abschnitt **Threat Intelligence** angezeigt.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Überprüfen von Paketinformationen für mit der Cloud verbundene Sensoren ##

Überprüfen Sie die folgenden Informationen zu Threat Intelligence-Paketen für Ihre mit der Cloud verbundenen Sensoren:

- Installierte Paketversion
- Modus des Threat Intelligence-Updates
- Status des Threat Intelligence-Updates

So überprüfen Sie Threat Intelligence-Informationen

1. Navigieren Sie in Microsofte Defender für IoT zur Seite **Standorte und Sensoren**.
1. Überprüfen Sie die installierte **Threat Intelligence-Version** auf jedem Sensor. Die Benennung der Versionen basiert auf dem Tag, an dem das Paket von Defender für IoT erstellt wurde.
1. Überprüfen Sie den **Threat Intelligence-Modus**. *Automatisch* gibt an, dass neu verfügbare Pakete automatisch auf Sensoren installiert werden, sobald sie von Defender für IoT veröffentlicht werden. *Manuell* gibt an, dass Sie neu verfügbare Pakete bei Bedarf direkt an Sensoren pushen können.
1. Überprüfen Sie den **Status des Threat Intelligence-Updates**. Die folgenden Status werden möglicherweise angezeigt:

- Fehler
- Vorgang wird ausgeführt
- Update verfügbar
- OK,

Wenn cloudbasierte Threat Intelligence-Updates zu Fehlern führen, überprüfen Sie auf der Seite **Standorte und Sensoren** die Verbindungsinformationen in den Spalten **Sensorstatus** und **Letzte Verbindung (UTC)** . 

## <a name="see-also"></a>Weitere Informationen

[Integrieren eines Sensors](getting-started.md#onboard-a-sensor)

[Verwalten von Sensoren über die Verwaltungskonsole](how-to-manage-sensors-from-the-on-premises-management-console.md)
