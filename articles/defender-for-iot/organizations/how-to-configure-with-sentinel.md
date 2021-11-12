---
title: Konfigurieren von Azure Sentinel mit Defender für IoT für Organisationen
description: Erläutert die Konfiguration von Azure Sentinel zum Empfangen von Daten aus Ihrer Defender für IoT-Lösung.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 3b1decfa09120732dce372f7dd5ed1bb50c66bef
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026404"
---
# <a name="connect-your-data-from-defender-for-iot-for-organizations-to-azure-sentinel-public-preview"></a>Verbinden Ihrer Daten aus Defender für IoT für Organisationen mit Azure Sentinel (öffentliche Vorschau)

Mit dem Defender für IoT-Connector können Sie alle Ihre Ereignisse aus Defender für IoT an Azure Sentinel streamen.

Diese Integration ermöglicht Organisationen das schnelle Erkennen mehrstufiger Angriffe, die oft IT- und OT-Grenzen überschreiten. Darüber hinaus ermöglicht die Integration von Defender für IoT in die SOAR-Funktionen (Security Orchestration, Automation and Response = Sicherheitsorchestrierung, Automatisierung und Reaktion) von Azure Sentinel eine automatisierte Reaktion und Prävention mithilfe von integrierten OT-optimierten Playbooks.

## <a name="prerequisites"></a>Voraussetzungen

- **Lese-** und **Schreibberechtigungen** für den Arbeitsbereich, in dem Azure Sentinel bereitgestellt wird.

- **Defender für IoT** muss für Ihre relevanten IoT Hub-Instanzen **aktiviert** sein.

- Sie müssen über die Berechtigungen der Rolle **Mitwirkender** für das **Abonnement** verfügen, für das Sie eine Verbindung herstellen möchten.

## <a name="connect-to-defender-for-iot"></a>Verbinden mit Defender für IoT

1. Klicken Sie in Azure Sentinel auf **Data connectors** (Datenconnectors), und wählen Sie dann im Katalog **Defender für IoT** aus. Möglicherweise heißt diese Option noch „Azure Security Center für IoT“.

1. Wählen Sie unten im rechten Bereich **Connectorseite öffnen** aus.

1. Wählen Sie neben jedem Abonnement, dessen Warnungen und Gerätewarnungen Sie in Azure Sentinel streamen möchten, **Verbinden** aus.

    > [!NOTE]
    > Wenn Defender für IoT nicht für mindestens eine IoT Hub-Instanz in diesem Abonnement aktiviert wurde, wird eine Fehlermeldung angezeigt. Aktivieren Sie in der IoT Hub-Instanz Defender für IoT, um diesen Fehler zu beheben.

1. Sie können entscheiden, ob die Warnungen von Defender für IoT automatisch Incidents in Azure Sentinel generieren sollen. Wählen Sie unter **Create incidents** (Incidents erstellen) **Aktivieren** aus, um die Standardanalyseregel zum automatischen Erstellen von Incidents aus den generierten Warnungen zu aktivieren. Diese Regel kann unter **Analytics** > **Active rules** (Analyse > Aktive Regeln) geändert oder bearbeitet werden.

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

Die Hubdaten stehen ungefähr 15 Minuten nach dem Herstellen einer Verbindung mit einem **Abonnement** in Azure Sentinel zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Defender für IoT mit Azure Sentinel verbinden. Weitere Informationen zu Bedrohungserkennung und Zugriff auf Sicherheitsdaten finden Sie in folgenden Artikeln:

- Informationen zur Verwendung von Azure Sentinel finden Sie im [Schnellstart: Erste Schritte mit Azure Sentinel](../../sentinel/get-visibility.md).
