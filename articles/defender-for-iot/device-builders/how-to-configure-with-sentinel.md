---
title: Konfigurieren von Microsoft Sentinel mit Defender für IoT für Geräteentwickler
description: In diesem Artikel wird die Konfiguration von Microsoft Sentinel zum Empfangen von Daten aus Ihrer Defender für IoT-Lösung für Geräteentwickler erläutert.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: e64b0db2af1c1b742c3997e616eea43f5c969522
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306166"
---
# <a name="connect-your-data-from-defender-for-iot-for-device-builders-to-microsoft-sentinel-public-preview"></a>Verbinden Ihrer Daten aus Defender für IoT für Geräteentwickler in Microsoft Sentinel (öffentliche Vorschau)

Mit dem Defender für IoT-Connector können Sie alle Ihre Ereignisse aus Defender für IoT in Microsoft Sentinel streamen. 

Diese Integration ermöglicht Organisationen das schnelle Erkennen mehrstufiger Angriffe, die oft IT- und OT-Grenzen überschreiten. Darüber hinaus ermöglicht die Integration von Defender für IoT in die SOAR-Funktionen (Security Orchestration, Automation and Response = Sicherheitsorchestrierung, Automatisierung und Reaktion) von Microsoft Sentinel eine automatisierte Reaktion und Prävention mithilfe von integrierten OT-optimierten Playbooks. 

## <a name="prerequisites"></a>Voraussetzungen

- **Lese-** und **Schreibberechtigungen** für den Arbeitsbereich, in dem Microsoft Sentinel bereitgestellt wird.
- **Defender für IoT** muss für Ihre relevanten IoT Hub-Instanzen **aktiviert** sein.
- Sie müssen über die Berechtigungen der Rolle **Mitwirkender** für das **Abonnement** verfügen, für das Sie eine Verbindung herstellen möchten.

## <a name="connect-to-defender-for-iot"></a>Verbinden mit Defender für IoT

1. Wählen Sie in Microsoft Sentinel **Datenconnectors** und dann im Katalog **Defender für IoT** (früher „Azure Security Center für IoT“) aus.

1. Klicken Sie unten im rechten Bereich auf **Connectorseite öffnen**.

1. Klicken Sie neben jedem IoT Hub-Abonnement, dessen Warnungen und Gerätewarnungen Sie in Microsoft Sentinel streamen möchten, auf **Verbinden**.
    - Wenn Defender für IoT nicht für mindestens eine IoT Hub-Instanz in einem Abonnement aktiviert ist, wird eine Fehlermeldung angezeigt. Aktivieren Sie in der IoT Hub-Instanz Defender für IoT, um diesen Fehler zu beheben.

1. Sie können entscheiden, ob die Warnungen aus Defender für IoT automatisch Incidents in Microsoft Sentinel generieren sollen. Wählen Sie unter **Create incidents** (Incidents erstellen) **Aktivieren** aus, um die Standardanalyseregel zur automatischen Erstellung von Incidents aus den generierten Warnungen zu aktivieren. Diese Regel kann unter **Analytics** > **Active rules** (Analyse > Aktive Regeln) geändert oder bearbeitet werden.

> [!NOTE]
> Das Aktualisieren der **Abonnementliste** nach Verbindungsänderungen kann 10 Sekunden oder länger dauern. 

## <a name="log-analytics-alert-view"></a>Log Analytics-Warnungsansicht

So verwenden Sie das relevante Schema in Log Analytics, um die Azure Defender für IoT-Benachrichtigungen anzuzeigen:

1. Öffnen Sie **Logs** > **SecurityInsights** > **SecurityAlert**, oder suchen Sie nach **SecurityAlert**.

1. Filtern Sie mithilfe des folgenden KQL-Filters, um nur die von Defender für IoT generierten Benachrichtigungen anzuzeigen:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Diensthinweise

Nach dem Herstellen einer Verbindung mit einem **Abonnement** stehen die Hub-Daten in Microsoft Sentinel ungefähr 15 Minuten zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Defender für IoT mit Microsoft Sentinel verbinden. Weitere Informationen zu Bedrohungserkennung und Zugriff auf Sicherheitsdaten finden Sie in folgenden Artikeln:

- Informationen zur Verwendung von Microsoft Sentinel finden Sie im [Schnellstart: Erste Schritte mit Microsoft Sentinel](../../sentinel/get-visibility.md).
- Erfahren Sie, wie Sie auf Ihre [IoT-Sicherheitsdaten zugreifen](how-to-security-data-access.md).
