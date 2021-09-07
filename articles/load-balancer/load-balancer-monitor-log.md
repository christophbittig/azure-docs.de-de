---
title: Überwachen von Vorgängen, Ereignissen und Leistungsindikatoren für einen öffentlichen Load Balancer
titleSuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie die Protokollierung für Azure Load Balancer aktivieren.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 9eacbcbb73acffb5625d2f2a7e8594c27992307e
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466607"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Monitor-Protokolle für Azure Load Balancer Standard

Sie können verschiedene Typen von Azure Monitor-Protokollen verwenden, um Azure Load Balancer Standard-Instanzen zu verwalten und Probleme zu beheben. Protokolle können an einen Event Hub oder einen Log Analytics-Arbeitsbereich gestreamt werden. Sie können alle Protokolle aus Azure Blob Storage extrahieren und sie in Tools wie Excel und Power BI anzeigen. 

Protokolltypen in Azure:

* **Aktivitätsprotokolle**: Sie können alle Aktivitäten, die an Ihre Azure-Abonnements übermittelt werden, zusammen mit Ihrem Status anzeigen. Weitere Informationen finden Sie unter [Anzeigen von Aktivitätsprotokollen, um Aktionen für Ressourcen zu überwachen](../azure-resource-manager/management/view-activity-logs.md). Aktivitätsprotokolle sind standardmäßig aktiviert und können im Azure-Portal angezeigt werden. Diese Protokolle sind sowohl für Azure Load Balancer Basic als auch für Load Balancer Standard verfügbar.
* **Load Balancer Standard-Metriken:** Sie können dieses Protokoll verwenden, um die als Protokolle für Load Balancer Standard exportierten Metriken abzufragen. Diese Protokolle sind nur für Load Balancer Standard verfügbar.

> [!IMPORTANT]
> Integritätstest- und Load Balancer-Warnungsereignisprotokolle funktionieren zurzeit nicht und werden unter [bekannte Probleme für Azure Load Balancer](whats-new.md#known-issues) aufgeführt. 

> [!IMPORTANT]
> Protokolle sind nur für Ressourcen verfügbar, die über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden. Sie können Protokolle nicht für Ressourcen im klassischen Bereitstellungsmodell verwenden. Weitere Informationen zu diesen Bereitstellungsmodellen finden Sie unter [Grundlegendes zur Bereitstellung über Resource Manager im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Aktivieren der Protokollierung

Die Aktivitätsprotokollierung ist automatisch für alle Resource Manager-Ressourcen aktiviert. Aktivieren Sie die Ereignis- und Integritätstestprotokollierung, um mit der Erfassung von Daten aus diesen Protokollen zu beginnen. Führen Sie die folgenden Schritte durch:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie noch nicht über einen Load Balancer verfügen, [erstellen Sie einen Load Balancer](./quickstart-load-balancer-standard-public-portal.md) , bevor Sie fortfahren.
1. Wählen Sie im Portal die Option **Ressourcengruppen** aus.
2. Wählen Sie den **\<resource-group-name>** der Ressourcengruppe aus, in der sich Ihr Lastenausgleichsmodul befindet.
3. Wählen Sie Ihren Load Balancer aus.
4. Wählen Sie **Aktivitätsprotokoll** > **Diagnoseeinstellungen** aus.
5. Wählen Sie im Bereich **Diagnoseeinstellungen** unter **Diagnoseeinstellungen**, den Eintrag **Diagnoseeinstellung hinzufügen** aus.
6. Geben Sie im Bereich zum Erstellen von **Diagnoseeinstellungen** im Feld **Name** den Wert **myLBDiagnostics** ein.
7. Sie haben die Wahl unter drei Optionen für die **Diagnoseeinstellungen**. Sie können eine Option, zwei oder alle drei Optionen auswählen und jede der Optionen gemäß Ihren Anforderungen konfigurieren:

   * **In einem Speicherkonto archivieren**. Für diesen Vorgang benötigen Sie ein bereits erstelltes Speicherkonto. Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Aktivieren Sie das Kontrollkästchen **In einem Speicherkonto archivieren**.
     2. Wählen Sie **Konfigurieren** aus, um den Bereich **Speicherkonto auswählen** zu öffnen.
     3. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem Ihr Speicherkonto erstellt wurde.
     4. Wählen Sie in der Dropdownliste **Speicherkonto** den Namen Ihres Speicherkontos aus.
     5. Klicken Sie auf **OK**.

   * **An einen Event Hub streamen**. Für diesen Vorgang benötigen Sie einen bereits erstellten Event Hub. Informationen zum Erstellen eines Event Hubs finden Sie unter [Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md).
     1. Aktivieren Sie das Kontrollkästchen **An einen Event Hub streamen**.
     2. Wählen Sie **Konfigurieren** aus, um den Bereich **Event Hub auswählen** zu öffnen.
     3. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem Ihr Event Hub erstellt wurde.
     4. Wählen Sie in der Dropdownliste **Event Hub-Namespace auswählen** den Namespace aus.
     5. Wählen Sie in der Dropdownliste **Event Hub-Richtliniennamen auswählen** den Namen aus.
     6. Klicken Sie auf **OK**.

   * **An Log Analytics senden**. Für diesen Prozess benötigen Sie einen bereits erstellten und konfigurierten Log Analytics-Arbeitsbereich. Eine Anleitung zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/logs/quick-create-workspace.md).
     1. Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**.
     2. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem sich Ihr Log Analytics-Arbeitsbereich befindet.
     3. Wählen Sie in der Dropdownliste **Log Analytics-Arbeitsbereich** den Arbeitsbereich aus.

8. Aktivieren Sie im Abschnitt **METRIK** im Bereich **Diagnoseeinstellungen** das Kontrollkästchen **AllMetrics**.

9. Überprüfen Sie, ob alles richtig aussieht, und klicken Sie oben im Bereich zum Erstellen der **Diagnoseeinstellungen** auf **Speichern**.

## <a name="view-and-analyze-the-activity-log"></a>Anzeigen und Analysieren des Aktivitätsprotokolls

Das Aktivitätsprotokoll wird standardmäßig generiert. Es kann konfiguriert werden, um auf Abonnementebene [gemäß der folgenden Anweisungen in diesem Artikel](../azure-monitor/essentials/activity-log.md) exportiert zu werden. Mehr zu diesen Protokollen können Sie im Artikel [Anzeigen von Aktivitätsprotokollen zur Überwachung von Aktionen in Ressourcen](../azure-resource-manager/management/view-activity-logs.md) erfahren.

Mit einer der folgenden Methoden können Sie die Aktivitätsprotokolldaten anzeigen und analysieren:

* **Azure-Tools:** Rufen Sie Informationen aus dem Aktivitätsprotokoll über Azure PowerShell, über die Azure-Befehlszeilenschnittstelle, mithilfe der Azure-REST-API oder über das Azure-Portal ab. Schrittweise Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI:** Falls Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe der [Azure-Überwachungsprotokolle-Integration mit Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/) können Sie Ihre Daten mit vorkonfigurierten Dashboards analysieren. Sie können Ansichten auch gemäß Ihren Anforderungen anpassen.

## <a name="view-and-analyze-metrics-as-logs"></a>Anzeigen und Analysieren von Metriken als Protokolle
Mit der Exportfunktion in Azure Monitor können Sie Ihre Load Balancer-Metriken exportieren. Diese Metriken generieren für jedes Samplingintervall von einer Minute einen Protokolleintrag.

Der Export von Metriken in Protokolle ist pro Ressourcenebene aktiviert. So aktivieren Sie diese Protokolle:

1. Navigieren Sie zum Bereich **Diagnoseeinstellungen**.
1. Filtern Sie nach Ressourcengruppe, und wählen Sie dann die Load Balancer-Instanz aus, für die Sie den Metrikexport aktivieren möchten. 
1. Wenn die Diagnoseeinstellungenseite für Load Balancer angezeigt wird, wählen Sie **AllMetrics** aus, um geeignete Metriken als Protokolle zu exportieren.

Informationen zu Einschränkungen beim Metrikexport finden Sie im Abschnitt [Einschränkungen](#limitations) dieses Artikels.

Wenn Sie **AllMetrics** in den Diagnoseeinstellungen Ihrer Load Balancer Standard-Instanz aktivieren, wird die Tabelle **AzureMetrics** mit diesen Protokollen aufgefüllt, sofern ein Event Hub oder ein Log Analytics-Arbeitsbereich verwendet wird. 

Zum Exportieren in den Speicher müssen Sie eine Verbindung mit Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge für das Integritätstest- und Ereignisprotokoll abrufen. Nachdem Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, Power BI oder einem anderen Datenvisualisierungstool anzeigen. 

> [!TIP]
> Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von GitHub verwenden.

## <a name="stream-to-an-event-hub"></a>An einen Event Hub streamen
Wenn Diagnoseinformationen an einen Event Hub gestreamt werden, kann er für die zentrale Protokollanalyse in einem SIEM-Tool eines Drittanbieters mit Azure Monitor-Integration verwendet werden. Weitere Informationen finden Sie unter [Streamen von Azure-Überwachungsdaten an einen Event Hub](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>An Log Analytics senden
Sie können Diagnoseinformationen für Ressourcen in Azure direkt an einen Log Analytics-Arbeitsbereich senden. In diesem Arbeitsbereich können Sie komplexe Abfragen für die Informationen zur Problembehandlung und Analyse ausführen. Weitere Informationen finden Sie unter [Erfassen von Azure-Ressourcenprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Einschränkungen
Bei der Verwendung der Funktion zum Exportieren von Metriken in Protokolle für Azure Load Balancer gelten folgende Einschränkungen:
* Metriken werden zurzeit über interne Namen angezeigt, wenn sie als Protokolle exportiert werden. Sie finden die Zuordnung in der folgenden Tabelle.
* Die Dimensionalität von Metriken wird nicht beibehalten. Beispielsweise können Sie mit Metriken wie **DipAvailability** (Integritätsteststatus) die IP-Adresse des Back-Ends nicht aufteilen oder anzeigen.
* Metriken für verwendete SNAT-Ports und zugeordnete SNAT-Ports sind zurzeit nicht für den Export als Protokolle verfügbar.

## <a name="next-steps"></a>Nächste Schritte
* [Überprüfen der verfügbaren Metriken für Ihren Load Balancer](./load-balancer-standard-diagnostics.md)
* [Erstellen und Testen von Abfragen anhand von Azure Monitor-Anweisungen](../azure-monitor/logs/log-query-overview.md)
