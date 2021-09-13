---
title: Verbinden von sämtlichen Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank mit Azure Sentinel
description: Erfahren Sie, wie Sie mithilfe von Azure Policy die Verbindung der Azure SQL-Datenbank-Diagnoseprotokolle und Sicherheitsüberwachungsprotokolle mit Azure Sentinel erzwingen können.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0f1cc19aac18f22d86e0ed56eded168c78a26631
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346215"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Verbinden von Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank

Azure SQL ist eine vollständig verwaltete PaaS-Datenbank-Engine (Platform-as-a-Service), bei der die meisten Funktionen für die Datenbankverwaltung ohne Benutzereingriff erfolgen, z. B. Upgrades, Patches, Sicherungen und Überwachung. 

Mit dem Azure SQL-Datenbank-Connector können Sie die Überwachungs- und Diagnoseprotokolle Ihrer Datenbanken in Azure Sentinel streamen, sodass Sie die Aktivitäten in allen Instanzen kontinuierlich überwachen können.

- Das Verbinden von Diagnoseprotokollen ermöglicht Ihnen das Senden von Datenbankdiagnoseprotokollen mit unterschiedlichen Datentypen an Ihren Azure Sentinel-Arbeitsbereich.

- Durch das Verbinden von Überwachungsprotokollen können Sie Sicherheitsüberwachungsprotokolle aus allen Azure SQL-Datenbanken auf Serverebene streamen.

Erfahren Sie mehr über [Azure SQL-Datenbank Diagnosetelemetriedaten](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) und über [Azure SQL-Server-Überwachung](../azure-sql/database/auditing-overview.md).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Zum Verbinden von Überwachungsprotokollen müssen Sie über Lese- und Schreibberechtigungen für die Azure SQL Server-Überwachungs Einstellungen verfügen.

- Wenn Sie Azure Policy verwenden möchten, um eine Richtlinie zum Protokollstreaming in Azure SQL-Datenbank-Ressourcen anzuwenden, müssen Sie die Rolle „Besitzer“ für den Zuweisungsbereich der Richtlinie besitzen.

## <a name="connect-to-an-azure-sql-database"></a>Herstellen einer Verbindung mit einer Azure SQL-Datenbank

Dieser Connector verwendet Azure Policy, um eine einzelne Konfiguration zum Azure SQL-Protokollstreaming auf eine Sammlung von Instanzen anzuwenden, die als Bereich definiert sind. Der Azure SQL-Datenbank Connector sendet zwei Arten von Protokollen an Azure Sentinel: Diagnoseprotokolle (aus SQL-Datenbanken) und Überwachungsprotokolle (auf SQL Server-Ebene). Die in Azure SQL-Datenbanken und -Server erfassten Protokolltypen werden links auf der Connectorseite unter **Datentypen** angezeigt.

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie im Katalog für Datenconnectors **Azure SQL-Datenbanken** und dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Beachten Sie im Abschnitt **Konfiguration** der Connectorseite die beiden Kategorien von Protokollen, die Sie verbinden können.

### <a name="connect-diagnostics-logs"></a>Verbinden von Diagnoseprotokollen

1. **Streamen Sie Diagnoseprotokolle von Ihren Azure SQL-Datenbanken großflächig** erweitern.

1. Wählen Sie die Schaltfläche **Azure Policy-Zuweisungs-Assistent starten** aus.

    Der Richtlinienzuweisungs-Assistent wird geöffnet, und Sie können eine neue Richtlinie mit dem Namen **Bereitstellen: Diagnoseeinstellungen für Azure SQL-Datenbanken in Protokollanalyse-Arbeitsbereich konfigurieren** erstellen.

    1. Klicken Sie auf der Registerkarte **Grundlagen** unter **Bereich** auf die Schaltfläche mit den drei Punkten, um Ihr Abonnement (und optional eine Ressourcengruppe) auszuwählen. Sie können auch eine Beschreibung hinzufügen.

    1. Lassen Sie auf der Registerkarte **Parameter** die ersten beiden Einstellungen unverändert. Wählen Sie in der Dropdownliste **Log Analytics-Arbeitsbereich** Ihren Azure Sentinel-Arbeitsbereich aus. Die übrigen Dropdownfelder stellen die verfügbaren Diagnoseprotokolltypen dar. Behalten Sie für alle Protokolltypen, die erfasst werden sollen, die Markierung „True“ bei.

    1. Die Richtlinie wird auf Ressourcen angewendet, die in Zukunft hinzugefügt werden. Wählen Sie zum Anwenden der Richtlinie auf Ihre vorhandenen Ressourcen die Registerkarte **Wartung** aus, und aktivieren Sie das Kontrollkästchen **Wartungstask erstellen**.

    1. Klicken Sie auf der Registerkarte **Überprüfen + erstellen** auf **Erstellen**. Damit ist die Richtlinie dem ausgewählten Bereich zugewiesen.

### <a name="connect-audit-logs"></a>Verbinden von Überwachungsprotokollen

1. Erweitern Sie, nachdem Sie wieder auf der Connector-Seite sind, **Stream-Auditing-Protokolle von Ihren Azure SQL-Datenbanken auf Serverebene großflächig**.

1. Wählen Sie die Schaltfläche **Azure Policy-Zuweisungs-Assistent starten** aus.

    Der Richtlinienzuweisungs-Assistent wird geöffnet, und Sie können eine neue Richtlinie mit dem Namen **Bereitstellen: Auditingeinstellungen für Azure SQL-Datenbanken in Protokollanalyse-Arbeitsbereich konfigurieren** erstellen.

    1. Klicken Sie auf der Registerkarte **Grundlagen** unter **Bereich** auf die Schaltfläche mit den drei Punkten, um Ihr Abonnement (und optional eine Ressourcengruppe) auszuwählen. Sie können auch eine Beschreibung hinzufügen.

    1. Wählen Sie auf der Registerkarte **Parameter** Ihren Azure Sentinel-Arbeitsbereich in der Dropdownliste **Log Analytics-Arbeitsbereich** aus. Lassen Sie die Einstellung **Effekt** unverändert.

    1. Die Richtlinie wird auf Ressourcen angewendet, die in Zukunft hinzugefügt werden. Wählen Sie zum Anwenden der Richtlinie auf Ihre vorhandenen Ressourcen die Registerkarte **Wartung** aus, und aktivieren Sie das Kontrollkästchen **Wartungstask erstellen**.

    1. Klicken Sie auf der Registerkarte **Überprüfen + erstellen** auf **Erstellen**. Damit ist die Richtlinie dem ausgewählten Bereich zugewiesen.

> [!NOTE]
>
> Mit diesem speziellen Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten 14 Tage erfasst wurden. Wenn 14 Tage ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie die Azure Policy verwenden, um Azure SQL-Datenbankdiagnose und Audit-Protokolle mit Azure Sentinel zu verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
