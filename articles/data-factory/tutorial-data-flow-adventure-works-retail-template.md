---
title: ETL-Vorlage für Das Datenmodell der Einzelhandelsbranche
description: Dieses Tutorial enthält Schritte für die Verwendung der Datenmodellvorlage für den Einzelhandel mithilfe der Adventure Works-Beispieldaten.
author: kromerm
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 10/18/2021
ms.openlocfilehash: 888a6c78572940dd69ef2161fc1ded979259d63f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181629"
---
# <a name="adventureworks-template-documentation"></a>Dokumentation zu AdventureWorks-Vorlagen

In diesem Dokument wird erläutert, wie Sie die AdventureWorks-Pipelinevorlage von Microsoft einrichten und verwenden, um mithilfe von Azure Synapse Analytics und der Retail-Datenbankvorlage mit der Untersuchung des AdventureWorks-Datasets zu beginnen.

## <a name="overview"></a>Übersicht
AdventureWorks ist ein fiktiver Einzelhändler für Sportausrüstung, der zur Demonstration von Microsoft-Anwendungen verwendet wird. In diesem Fall werden sie als Beispiel für die Verwendung von Synapse Pipelines verwendet, um Einzelhandelsdaten der Retail-Datenbankvorlage für die weitere Analyse innerhalb von Azure Synapse zuzuordnen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Synapse-Arbeitsbereich**. [Erstellen Sie einen Azure Synapse-Arbeitsbereich](../storage/common/storage-account-create.md), sofern Sie noch keinen besitzen.

## <a name="find-the-template"></a>Suchen der Vorlage

Führen Sie diese Schritte aus, um die Vorlage zu suchen:

1. Navigieren Sie zu Ihrem Synapse-Arbeitsbereich. Wählen Sie auf der Startseite **Learn** und dann **Katalog durchsuchen** aus. Der Synapse-Katalog wird geöffnet. Sie können beispielsweise nach Datasets, Skripts und Pipelines suchen, die Sie in Ihrem Arbeitsbereich installieren können. 

1. Wählen Sie **Pipelines** aus, und filtern Sie nach den Ergebnisses mit dem Schlüsselwort „AdventureWorks“.

1. Wählen Sie die Vorlage **AdventureWorks** aus und dann **Weiter**.

Mit diesen Schritten wird die Übersichtsseite der Vorlage geöffnet.

## <a name="configure-the-template"></a>Konfigurieren der Vorlage
Die Vorlage ist so konzipiert, dass nur eine minimale Konfiguration erforderlich ist. Auf der Übersichtsseite der Vorlage wird eine Vorschau der anfänglichen Konfiguration der Pipeline angezeigt. Klicken Sie auf **Pipeline öffnen**, um die Ressourcen in Ihrem eigenen Arbeitsbereich zu erstellen. Sie erhalten eine Benachrichtigung, dass alle 31 Ressourcen in der Vorlage erstellt wurden, und können diese überprüfen, bevor Sie sie committen oder veröffentlichen. Sie finden die folgenden Komponenten der Vorlage:

* 17 Pipelines: Diese werden geplant, um sicherzustellen, dass die Daten ordnungsgemäß in die Zieltabellen geladen werden. Sie enthalten eine Pipeline pro Quelltabelle sowie die Planungstabellen.
* 14 Datenflüsse: Diese enthalten die Logik zum Laden aus dem Quellsystem und zum Verschieben der Daten in die Zieldatenbank.

Wenn Sie das AdventureWorks-Dataset in eine andere Datenbank geladen haben, können Sie die Datenflussquellen aktualisieren, um auf dieses Dataset zu verweisen. Führen Sie andernfalls die folgenden Schritte aus, um eine Quell- und Zieldatenbank zu erstellen, die dem in der Vorlage definierten Schema entspricht.


## <a name="dataset-and-sourcetarget-models"></a>Dataset- und Quell-/Zielmodelle
Das AdventureWorks-Dataset im Excel-Format kann von dieser [GitHub-Website](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorks%20Data.zip) heruntergeladen werden. Darüber hinaus können Sie sowohl für die [Quell- als auch für die Zieldatenbank auf die Schemadefinition](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorksSchemas.xlsx) zugreifen. Erstellen Sie mithilfe des Datenbank-Designers in Synapse die Quell- und Zieldatenbanken mit dem Schema in der zuvor heruntergeladenen Excel-Datei. Weitere Informationen zum Datenbank-Designer finden Sie in dieser [Dokumentation](https://aka.ms/SynapseDatabaseDesignerDocumentation).

Stellen Sie mit den erstellten Datenbanken sicher, dass die Dataflows auf die richtigen Tabellen verweisen, indem Sie die Dropdownlisten in den Quell- und Senkeneinstellungen der Arbeitsbereichsdatenbank bearbeiten. Sie können die Daten in das Quellmodell laden, indem Sie die im Beispieldataset bereitgestellten CSV-Dateien in den richtigen Ordnern verschieben, die von den Tabellen angegeben werden. Sobald dieser Schritt durchgeführt wurde, müssen sie nur noch die Pipelines ausführen.

## <a name="troubleshoot-the-pipelines"></a>Problembehandlung bei Pipelines
Wenn die Pipeline nicht erfolgreich ausgeführt werden kann, müssen einige wichtige Komponenten auf Fehler überprüft werden.

* Datasetschema. Stellen Sie sicher, dass die Dateneinstellungen für die CSV-Dateien korrekt sind. Wenn Sie Zeilenheader eingeschlossen haben, stellen Sie sicher, dass die Option „how headers“ in der Datenbanktabelle überprüft ist.
* Datenflussquellen. Wenn Sie andere Spalten- oder Tabellennamen als die im Beispielschema angegebenen verwendet haben, müssen Sie die Datenflüsse durchlaufen, um sicherzustellen, dass die Spalten ordnungsgemäß zugeordnet sind.
* Datenflusssenke. Die Schema- und Datenformatkonfigurationen in der Zieldatenbank müssen mit der Datenflussvorlage übereinstimmen. Diese Elemente müssen auch wie oben ausgerichtet werden, wenn Sie Änderungen vorgenommen haben.

## <a name="next-steps"></a>Nächste Schritte

* Lernen Sie mehr über [Zuordnungsdatenflüsse](concepts-data-flow-overview.md).
* Lernen Sie mehr über [Pipelinevorlagen](solution-templates-introduction.md).
