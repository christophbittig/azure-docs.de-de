---
title: Verknüpfen von Azure-Aktivitätsprotokolldaten mit Azure Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ereignisse des Azure-Aktivitätsprotokolls mit nur einem Klick an Azure Sentinel streamen. Im Aktivitätsprotokolldienst werden Ereignisse auf Abonnementebene für die gesamte Azure-Umgebung erfasst und angezeigt.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2021
ms.author: yelevin
ms.openlocfilehash: 874c4d520b3d41282d74f047b34fbe7148279a65
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606736"
---
# <a name="connect-data-from-azure-activity-log"></a>Verknüpfen von Daten aus dem Azure-Aktivitätsprotokoll

Sie können Ereignisse aus dem Azure-[**Aktivitätsprotokolldienst**](../azure-monitor/essentials/activity-log.md) mit einem einzigen Klick an Azure Sentinel streamen. Das **Aktivitätsprotokoll** ist ein [Plattformprotokoll](../azure-monitor/essentials/platform-logs-overview.md) in Azure, das Einblicke in Ereignisse auf Abonnementebene bietet: von Azure Resource Manager-Betriebsdaten bis hin zu Aktualisierungen von Service Health-Ereignissen. Mit dem **Aktivitätsprotokoll** können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für jeden Schreibvorgang (PUT, POST, DELETE) ermitteln, der für die Ressourcen Ihres Abonnements ausgeführt wurde. Sie können auch den Status des Vorgangs und andere relevante Eigenschaften erfahren. Der Dienst protokolliert keine Lesevorgänge (GET) oder Vorgänge für Ressourcen, die nicht das Azure Resource Manager-Modell (ARM) verwenden.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Log Analytics-Arbeitsbereich.

- Sie müssen über Besitzerberechtigungen für alle Abonnements verfügen, für deren Protokolle Sie das Streamen in Azure Sentinel starten oder beenden möchten.

- Wenn Sie Azure Policy verwenden möchten, um eine Richtlinie zum Protokollstreaming auf Azure-Aktivitätsprotokollabonnements anzuwenden, müssen Sie die Rolle „Besitzer“ für den Zuweisungsbereich der Richtlinie besitzen.

## <a name="data-structure-changes"></a>Änderungen der Datenstruktur

Für diesen Connector wird zurzeit die Methode geändert, die auf dem Back-End für die Erfassung von Aktivitätsprotokollereignissen verwendet wird. Er verwendet jetzt die **Diagnoseeinstellungspipeline**, die von unseren neueren Connectors verwendet wird (z. B. von unseren Azure Key Vault- und Azure Kubernetes Service-Connectors). Wenn Sie weiterhin die Legacymethode für diesen Connector verwenden, wird *dringend empfohlen, ein Upgrade* auf die neue Version durchzuführen, die eine bessere Funktionalität und höhere Konsistenz mit Ressourcenprotokollen bietet. Weitere Informationen finden Sie in den nachstehenden Anweisungen.

Die Methode **Diagnoseeinstellungen** sendet dieselben Daten, die die Legacymethode aus dem Aktivitätsprotokolldienst gesendet hat, obwohl es einige [Änderungen an der Struktur](../azure-monitor/essentials/activity-log.md#data-structure-changes) der **AzureActivity**-Tabelle gegeben hat.

Im Folgenden finden Sie einige der wichtigsten Verbesserungen, die sich aus dem Umstieg auf die Diagnoseeinstellungspipeline ergeben:
- Verbesserte Erfassungslatenz (Ereigniserfassung innerhalb von 2 bis 3 Minuten nach dem Auftreten anstelle von 15 bis 20 Minuten).
- Verbesserte Zuverlässigkeit.
- Diese Ebene bietet eine verbesserte Leistung.
- Unterstützung für alle Kategorien von Ereignissen, die vom Aktivitätsprotokolldienst protokolliert werden (der Legacymechanismus unterstützt nur eine Teilmenge, z. B. gibt es keine Unterstützung für Service Health-Ereignisse).
- Verwaltung im großen Stil mit Azure Policy.

In der [Azure Monitor-Dokumentation](../azure-monitor/logs/data-platform-logs.md) finden Sie eine ausführlichere Behandlung des [Azure-Aktivitätsprotokolls](../azure-monitor/essentials/activity-log.md) und der [Diagnoseeinstellungspipeline](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="set-up-the-azure-activity-log-connector"></a>Einrichten des Azure-Aktivitätsprotokollconnectors

Das Einrichten des neuen Azure-Aktivitätsprotokollconnectors erfolgt in zwei Phasen:
1. Trennen der vorhandenen Abonnements von der Legacymethode.

1. Verbindung aller relevanten Abonnements mit der neuen Diagnoseeinstellungspipeline unter Verwendung von **Azure Policy**.

### <a name="disconnect-from-old-pipeline"></a>Trennen der Verbindung mit der alten Pipeline

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors). Klicken Sie in der Liste mit Connectors auf **Azure-Aktivität** und dann unten rechts auf die Schaltfläche **Connectorseite öffnen**.

1. Überprüfen Sie unter der Registerkarte **Anweisungen** im Abschnitt **Konfiguration** in Schritt 1 die Liste Ihrer vorhandenen Abonnements, die mit der alten Methode verbunden sind (damit Sie wissen, welche Abonnements Sie der neuen Methode hinzufügen müssen), und trennen Sie diese alle auf einmal, indem Sie unten auf die Schaltfläche **Alle trennen** klicken.

### <a name="connect-to-new-pipeline"></a>Herstellen einer Verbindung mit der neuen Pipeline

1. Wählen Sie unter Schritt 2 die Schaltfläche **Azure Policy-Zuweisungs-Assistent starten** aus. Der Richtlinienzuweisungs-Assistent wird geöffnet und kann eine neue Richtlinie mit dem Namen **Konfigurieren von Azure-Aktivitätsprotokollen zum Streamen an den angegebenen Log Analytics-Arbeitsbereich** erstellen.

1. Klicken Sie auf der Registerkarte **Grundlagen** unter **Bereich** auf die Schaltfläche mit den drei Punkten, um den Bereich **Bereich** zu öffnen,in dem Sie Ihr Abonnement (und optional eine Ressourcengruppe) auszuwählen. Sie können auch eine Beschreibung hinzufügen.

1. Lassen Sie auf der Registerkarte **Parameter** die Felder **Auswirkung** und **Protokolle aktivieren** unverändert. Wählen Sie in der Dropdownliste **Log Analytics-Arbeitsbereich** Ihren Azure Sentinel-Arbeitsbereich aus.

1. Die Richtlinie wird auf Ressourcen angewendet, die in Zukunft hinzugefügt werden. Wählen Sie zum Anwenden der Richtlinie auf Ihre vorhandenen Ressourcen die Registerkarte **Wartung** aus, und aktivieren Sie das Kontrollkästchen **Wartungstask erstellen**.

1. Klicken Sie auf der Registerkarte **Überprüfen + erstellen** auf **Erstellen**. Damit ist die Richtlinie dem ausgewählten Bereich zugewiesen.

> [!NOTE]
>
> Mit diesem speziellen Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten 14 Tage erfasst wurden. Wenn 14 Tage ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

## <a name="find-your-data"></a>Suchen von Daten

Geben Sie `AzureActivity` oben im Abfragefenster ein, um das relevante Schema in Log Analytics nach Azure-Aktivitätswarnungen abzufragen.

Auf der Registerkarte **Nächste Schritte** auf der Connectorseite finden Sie einige nützliche Beispielabfragen und Arbeitsmappenvorlagen, mit deren Hilfe Sie Ihre Daten analysieren und visualisieren können.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie das Azure-Aktivitätsprotokoll mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit dem Erkennen von Bedrohungen mit Azure Sentinel mithilfe von [integrierten](detect-threats-built-in.md) oder [benutzerdefinierten](detect-threats-custom.md) Regeln.
