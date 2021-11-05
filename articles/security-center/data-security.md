---
title: Datensicherheit in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Daten in Microsoft Defender für Cloud verwaltet und geschützt werden.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/13/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0f09e842fe7a50a9b978d5882405ad6b11576a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101000"
---
# <a name="microsoft-defender-for-cloud-data-security"></a>Datensicherheit in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud erfasst und verarbeitet sicherheitsbezogene Daten (einschließlich Konfigurationsinformationen, Metadaten, Ereignisprotokolle und Ähnliches), um Kunden bei der Vermeidung, Erkennung und Behandlung von Bedrohungen zu unterstützen. Microsoft hält strenge Compliance- und Sicherheitsrichtlinien ein – angefangen bei der Codierung bis hin zum Betreiben von Diensten.

In diesem Artikel erfahren Sie, wie Daten in Defender für Cloud verwaltet und geschützt werden.

## <a name="data-sources"></a>Datenquellen
Defender für Cloud analysiert Daten aus den folgenden Quellen, um über den Sicherheitsstatus zu informieren, Sicherheitslücken zu identifizieren, Gegenmaßnahmen zu empfehlen und aktive Bedrohungen zu erkennen:

- **Azure-Dienste**: Verwendet Informationen zur Konfiguration von Azure-Diensten, die Sie bereitgestellt haben. Hierzu wird mit dem Ressourcenanbieter des Diensts kommuniziert.
- **Netzwerkdatenverkehr:** Verwendet Metadatenstichproben des Netzwerkdatenverkehrs aus der Infrastruktur von Microsoft wie etwa Quelle/Ziel, IP/Port, Paketgröße und Netzwerkprotokoll.
- **Partnerlösungen:** Verwendet Sicherheitswarnungen von integrierten Partnerlösungen (beispielsweise Firewalls und Antischadsoftwarelösungen).
- **Ihre Computer:** Verwendet Konfigurationsdetails und Informationen zu sicherheitsrelevanten Ereignissen, beispielsweise Ereignis- und -Überwachungsprotokolle unter Windows und Syslog-Nachrichten von Ihren Computern.

## <a name="data-protection"></a>Schutz von Daten

### <a name="data-segregation"></a>Trennung von Daten
Daten werden für jede Komponente des Diensts logisch getrennt verwaltet. Sämtliche Daten werden nach Organisation gekennzeichnet. Dieser Kennzeichnung wird während des gesamten Datenlebenszyklus beibehalten und auf jeder Ebene des Diensts erzwungen.

### <a name="data-access"></a>Datenzugriff
Zur Bereitstellung von Sicherheitsempfehlungen sowie bei der Untersuchung potenzieller Sicherheitsrisiken greifen Mitarbeiter von Microsoft unter Umständen auf Informationen zu, die von Azure-Diensten erfasst oder analysiert wurden. Hierzu zählen etwa Prozesserstellungsereignisse und andere Artefakte. Diese können ggf. Kundendaten oder personenbezogene Daten von Ihren Computern enthalten. 

Wir halten uns an den [Microsoft Online Services-Nachtrag zum Schutz von Daten](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880). Darin ist festgelegt, dass Microsoft keine Kundendaten oder daraus abgeleiteten Informationen zu Werbezwecken oder anderen kommerziellen Zwecken verwendet. Wir verwenden Kundendaten nur, wenn dies für die Bereitstellung Ihrer Azure-Dienste erforderlich ist. Dies gilt auch für Zwecke, die mit der Bereitstellung dieser Dienste kompatibel sind. Alle Rechte an den Kundendaten verbleiben bei Ihnen.

### <a name="data-use"></a>Datennutzung
Microsoft nutzt mandantenübergreifende Muster und Informationen zu Bedrohungen (Threat Intelligence), um die Funktionen für Prävention und Erkennung zu verbessern. Dies erfolgt in Übereinstimmung mit den in unserer [Datenschutzerklärung](https://privacy.microsoft.com/privacystatement) beschriebenen Datenschutzzusagen.

## <a name="manage-data-collection-from-machines"></a>Verwalten der Datensammlung von Computern
Wenn Sie Defender für Cloud in Azure aktivieren, wird die Datensammlung für alle Ihre Azure-Abonnements aktiviert. Sie können die Datensammlung für Ihre Abonnements auch in Defender für Cloud aktivieren. Wenn die Datensammlung aktiviert ist, stellt Defender für Cloud den Log Analytics-Agent auf allen vorhandenen unterstützten virtuellen Azure-Computern sowie auf allen neuen virtuellen Computern bereit, die erstellt werden.

Der Log Analytics-Agent sucht in ETW-Ablaufverfolgungen ([Event Tracing for Windows, Ereignisablaufverfolgung für Windows](/windows/win32/etw/event-tracing-portal)) nach verschiedenen sicherheitsbezogenen Konfigurationen und Ereignissen. Außerdem löst das Betriebssystem während der Ausführung des Computers Ereignisprotokollereignisse aus. Beispiele für Daten dieser Art: Betriebssystemtyp und -version, Betriebssystemprotokolle (Windows-Ereignisprotokolle), ausgeführte Prozesse, Computername, IP-Adressen, angemeldeter Benutzer und Mandanten-ID. Der Log Analytics-Agent liest Ereignisprotokolleinträge und ETW-Ablaufverfolgungen und kopiert diese zur Analyse in Ihren Arbeitsbereich bzw. in Ihre Arbeitsbereiche. Der Log Analytics-Agent ermöglicht auch Prozesserstellungsereignisse und die Befehlszeilenüberwachung.

Wenn Sie die erweiterten Sicherheitsfeatures von Microsoft Defender für Cloud nicht verwenden, können Sie die Datensammlung von virtuellen Computern auch in der Sicherheitsrichtlinie deaktivieren. Die Datensammlung ist für Abonnements erforderlich, die durch die erweiterten Sicherheitsfeatures geschützt werden. Momentaufnahmen von VM-Datenträgern sowie die Artefaktsammlung sind auch bei deaktivierter Datensammlung aktiviert.

Sie können den Arbeitsbereich und die Region angeben, in dem bzw. der die von Ihren Computern gesammelten Daten gespeichert werden. Standardmäßig werden auf Ihren Computern gesammelte Daten im nächstgelegenen Arbeitsbereich gespeichert, wie in der folgenden Tabelle gezeigt:

| Geografischer Raum VM                                      | Geografischer Raum Arbeitsbereich  |
|---------------------------------------------|----------------|
| USA, Brasilien, Südafrika         | USA  |
| Canada                                      | Canada         |
| Europa (außer Vereinigtes Königreich)           | Europa         |
| United Kingdom                              | United Kingdom |
| Asien (außer Indien, Japan, Korea, China) | Asien-Pazifik   |
| Korea                                       | Asien-Pazifik   |
| Indien                                       | Indien          |
| Japan                                       | Japan          |
| China                                       | China          |
| Australien                                   | Australien      |
|                                             |                |

> [!NOTE]
> **Microsoft Defender für Storage** speichert Artefakte entsprechend dem Standort der verknüpften Azure-Ressource regional. Weitere Informationen finden Sie unter [Einführung in Microsoft Defender für Storage](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Nutzung der Daten
Kunden können auf Daten im Zusammenhang mit Defender für Cloud über die folgenden Datenströme zugreifen:

| STREAM                                                                                | Datentypen                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md)                       | Alle Sicherheitswarnungen, von Defender für Cloud genehmigte [Just-In-Time](just-in-time-access-usage.md)-Zugriffsanforderungen und alle von [adaptiven Anwendungssteuerungen](adaptive-application-controls.md) generierte Warnungen|
| [Azure Monitor-Protokolle](../azure-monitor/data-platform.md)                      | Alle Sicherheitswarnungen                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | Sicherheitswarnungen, Sicherheitsempfehlungen, Ergebnisse der Sicherheitsrisikobewertung, sichere Bewertungsinformationen, Status von Konformitätsprüfungen und mehr                                                                       |
| [Microsoft Defender für Cloud-REST-API](/rest/api/securitycenter/) | Sicherheitswarnungen, Sicherheitsempfehlungen und vieles mehr                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Daten in Microsoft Defender für Cloud verwaltet und geschützt werden. 

Weitere Informationen zu Microsoft Defender für Cloud finden Sie unter [Was ist Microsoft Defender für Cloud?](defender-for-cloud-introduction.md).
