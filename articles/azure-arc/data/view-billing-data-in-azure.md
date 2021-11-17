---
title: Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal
description: Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: b54d77c5abb5e8043368ad1e8bce3d3865e68fd7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558760"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal




## <a name="connectivity-modes---implications-for-billing-data"></a>Konnektivitätsmodi: Auswirkungen auf Abrechnungsdaten

Es gibt zwei Modi, in denen Sie Ihre Azure Arc-fähigen Datendienste bereitstellen können:

- **Indirekt verbunden**: Es gibt keine direkte Verbindung mit Azure. Daten werden nur über einen Export-/Uploadprozess an Azure gesendet.
- **Direkt verbunden:** In diesem Modus besteht eine Abhängigkeit vom Azure Arc-fähigen Kubernetes-Dienst, um eine direkte Verbindung zwischen Azure und dem Kubernetes-Cluster bereitzustellen, in dem die Azure Arc-fähigen Datendienste bereitgestellt werden. Dies ermöglicht mehr Funktionen von Azure und erlaubt Ihnen auch, das Azure-Portal zu nutzen, um Ihre Azure Arc-fähigen Datendienste so zu verwalten, wie Sie Ihre Datendienste in Azure PaaS verwalten.  

Sie können mehr über den Unterschied zwischen den [Verbindungsmodi](./connectivity.md) erfahren.

Im indirekt verbundenen Modus werden Abrechnungsdaten regelmäßig aus dem Azure Arc-Datencontroller in eine sichere Datei exportiert und dann in Azure hochgeladen und verarbeitet.  Im zukünftigen direkt verbundenen Modus werden die Abrechnungsdaten ungefähr ein Mal pro Stunde automatisch an Azure gesendet, um einen Einblick in die Kosten Ihrer Dienste nahezu in Echtzeit zu ermöglichen. Der Prozess des Exports und Uploads der Daten im indirekt verbundenen Modus kann auch mit Hilfe von Skripts automatisiert werden, oder wir erstellen einen Dienst, der dies für Sie erledigt.

## <a name="upload-billing-data-to-azure---indirectly-connected-mode"></a>Hochladen von Abrechnungsdaten in Azure: indirekt verbundener Modus

> [!NOTE]
> Das Hochladen von Nutzungsdaten (für die Abrechnung) erfolgt im direkt verbundenen Modus automatisch. Die folgenden Anweisungen gelten nur für den indirekt verbundenen Modus. 

Um Abrechnungsdaten in Azure hochzuladen, sollte zunächst Folgendes geschehen:

1. Erstellen Sie bei Bedarf einen Azure Arc-fähigen Datendienst. Erstellen Sie beispielsweise Folgendes:
   - [Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc](create-sql-managed-instance.md)
   - [Erstellen einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe](create-postgresql-hyperscale-server-group.md)
2. Warten Sie nach der Erstellung des Datendiensts mindestens zwei Stunden, damit der Abrechnungstelemetrie-Erfassungsprozess einige Abrechnungsdaten erfassen kann.
3. Führen Sie die unter [Hochladen von Ressourceninventardaten, Nutzungsdaten, Metriken und Protokollen in Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md) beschriebenen Schritte aus, um die Voraussetzungen für das Hochladen von Verbrauchs-/Abrechnungs-/Protokolldaten zu erfüllen, und fahren Sie dann mit den [Hochladen von Nutzungsdaten in Azure](upload-usage-data.md) fort. 


## <a name="view-billing-data-in-azure-portal"></a>Anzeigen von Abrechnungsdaten im Azure-Portal

Gehen Sie wie folgt vor, um Abrechnungsdaten im Azure-Portal anzuzeigen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Geben Sie im Suchfeld oben auf dem Bildschirm **Cost Management** ein, und klicken Sie auf den Cost Management-Dienst.
1. Klicken Sie unter **Cost Management – Übersicht** auf die Registerkarte **Cost Management**.
1. Klicken Sie auf der linken Seite auf die Registerkarte **Kostenanalyse**.
1. Klicken Sie oben in der Ansicht auf die Schaltfläche **Kosten nach Ressource**.
1. Stellen Sie sicher, dass Ihr Bereich auf das Abonnement festgelegt ist, in dem Ihre Datendienstressourcen erstellt wurden.
1. Wählen Sie **Kosten nach Ressource** in der Dropdownliste „Anzeigen“ neben der Auswahl „Bereich“ oben in der Ansicht aus.
1. Stellen Sie sicher, dass der Datumsfilter auf **Dieser Monat** oder einen anderen Zeitbereich festgelegt ist, der hinsichtlich des Erstellungszeitpunkts der Datendienstressourcen sinnvoll ist.
1. Klicken Sie auf **Filter hinzufügen**, um einen Filter nach **Ressourcentyp** = `Microsoft.AzureArcData/<data service type>` hinzuzufügen, wenn Sie nur auf einen Typ von Azure Arc-fähigem Datendienst filtern möchten.
1. Nun wird eine Liste aller Ressourcen angezeigt, die erstellt und in Azure hochgeladen wurden. Da die Verbrauchseinheit 0 USD ausweist, stellen Sie fest, dass die Kosten immer 0 USD betragen.

## <a name="download-billing-data"></a>Herunterladen von Abrechnungsdaten

Sie können Abrechnungszusammenfassungsdaten direkt aus dem Azure-Portal herunterladen.

1. Klicken Sie in der gleichen Ansicht **Kostenanalyse > Nach Ressourcentyp anzeigen**, die Sie durch Befolgen der oben beschriebenen Anweisungen geöffnet haben, oben auf die Schaltfläche „Herunterladen“.
1. Wählen Sie den Dateityp (Excel oder CSV) für den Download aus, und klicken Sie auf die Schaltfläche **Daten herunterladen**.
1. Öffnen Sie die Datei in einem für den ausgewählten Dateityp geeigneten Editor.

## <a name="export-billing-data"></a>Exportieren von Abrechnungsdaten

Sie können auch in regelmäßigen Abständen **detaillierte** Nutzungs- und Abrechnungsdaten automatisch in einen Azure Storage-Container exportieren, indem Sie einen Abrechnungsexportauftrag erstellen. Dies ist nützlich, wenn Sie die Details Ihrer Abrechnung untersuchen möchten, z. B. wie viele Stunden einer bestimmten Instanz im Abrechnungszeitraum in Rechnung gestellt wurden.

Führen Sie die folgenden Schritte aus, um einen Abrechnungsexportauftrag einzurichten:

1. Klicken Sie auf der linken Seite auf **Exporte**.
1. Klicken Sie auf **Hinzufügen**.
1. Geben Sie einen Namen und die Exporthäufigkeit ein, und klicken Sie auf „Weiter“.
1. Erstellen Sie ein neues Speicherkonto, oder verwenden Sie ein vorhandenes Speicherkonto, und füllen Sie das Formular aus, um das Speicherkonto, den Container und den Verzeichnispfad für den Export der Abrechnungsdatendateien anzugeben. Klicken Sie anschließend auf „Weiter“.
1. Klicken Sie auf **Erstellen**.

Die Exportdateien für Abrechnungsdaten stehen in ungefähr vier Stunden zur Verfügung und werden nach dem von Ihnen beim Erstellen des Abrechnungsexportauftrags angegebenen Zeitplan exportiert.

Führen Sie die folgenden Schritte aus, um die zu exportierenden Abrechnungsdatendateien anzuzeigen:

Sie können die Abrechnungsdatendateien im Azure-Portal überprüfen. 

> [!IMPORTANT]
> Nachdem Sie den Abrechnungsexportauftrag erstellt haben, warten Sie vier Stunden, bevor Sie mit den folgenden Schritten fortfahren.

1. Geben Sie im Suchfeld oben im Portal **Speicherkonten** ein, und klicken Sie auf **Speicherkonten**.
3. Klicken Sie auf das Speicherkonto, das Sie beim Erstellen des Auftragsexportauftrags weiter oben angegeben haben.
4. Klicken Sie auf der linken Seite auf „Container“.
5. Klicken Sie auf den Container, den Sie beim Erstellen des Abrechnungsexportauftrags weiter oben angegeben haben.
6. Klicken Sie auf den Ordner, den Sie beim Erstellen des Abrechnungsexportauftrags weiter oben angegeben haben.
7. Führen Sie einen Drilldown in die generierten Ordner und Dateien aus, und klicken Sie auf eine der generierten CSV-Dateien.
8. Klicken Sie auf die Schaltfläche **Herunterladen**, um die Datei in Ihrem lokalen Ordner „Downloads“ zu speichern.
9. Öffnen Sie die Datei mit einem CSV-Dateiviewer, z. B. in Excel.
10. Filtern Sie die Ergebnisse, um nur die Zeilen mit dem **Ressourcentyp** = `Microsoft.AzureArcData/<data service resource type` anzuzeigen.
11. In der Spalte UsageQuantity sehen Sie die Anzahl der Stunden, die die Instanz im aktuellen 24-Stunden-Zeitraum verwendet wurde.
