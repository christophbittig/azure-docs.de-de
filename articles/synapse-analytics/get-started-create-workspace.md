---
title: 'Schnellstart: Erste Schritte zum Erstellen eines Synapse-Arbeitsbereichs'
description: In diesem Tutorial erfahren Sie, wie Sie einen Synapse-Arbeitsbereich, einen dedizierten SQL-Pool und einen serverlosen Apache Spark-Pool erstellen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: c99d981c6abf6d7a43a5180360712b665c57f6fe
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324352"
---
# <a name="creating-a-synapse-workspace"></a>Erstellen eines Synapse-Arbeitsbereichs

In diesem Tutorial erfahren Sie, wie Sie einen Synapse-Arbeitsbereich, einen dedizierten SQL-Pool und einen serverlosen Apache Spark-Pool erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

Um die Schritte dieses Tutorials ausführen zu können, benötigen Sie Zugriff auf eine Ressourcengruppe, für die Ihnen die Rolle **Besitzer** zugewiesen wurde. Erstellen Sie den Synapse-Arbeitsbereich in dieser Ressourcengruppe.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Erstellen eines Synapse-Arbeitsbereichs im Azure-Portal

### <a name="start-the-process"></a>Starten des Prozesses
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Geben Sie in der Suchleiste **Synapse** ein, und drücken Sie nicht die EINGABETASTE.
1. Wählen Sie in den Suchergebnissen unter **Dienste** den Eintrag **Azure Synapse Analytics** aus.
1. Wählen Sie **Hinzufügen** aus, um einen Arbeitsbereich zu erstellen.

## <a name="basics-tab--project-details"></a>Registerkarte „Grundlegende Einstellungen“ > „Projektdetails“
Füllen Sie die folgenden Felder aus:

1. **Abonnement**: Wählen Sie ein beliebiges Abonnement.
1. **Ressourcengruppe**: Wählen Sie eine beliebige Ressourcengruppe.
1. **Verwaltete Ressourcengruppe**: Lassen Sie dieses Feld leer.

## <a name="basics-tab--workspace-details"></a>Registerkarte „Grundlegende Einstellungen“ > „Details zum Arbeitsbereich“
Füllen Sie die folgenden Felder aus:

1. **Arbeitsbereichsname**: Wählen Sie einen beliebigen global eindeutigen Namen. In diesem Tutorial wird **myworkspace** verwendet.
1. **Region**: Wählen Sie die Region aus, in der Sie Ihre Clientanwendungen/-dienste (z. B. Azure-VM, Power BI, Azure Analysis Services) und Speicher platziert haben, die Daten enthalten (z. B. Azure Data Lake Storage, Azure Cosmos DB-Analysespeicher).

> [!NOTE]
> Ein Arbeitsbereich, der sich nicht am gleichen Ort wie die Clientanwendungen oder der Speicher befindet, kann die Grundursache zahlreicher Leistungsprobleme sein. Wenn die Daten oder die Clients in mehreren Regionen platziert werden, können Sie separate Arbeitsbereiche in den Regionen erstellen, in denen sich auch Ihre Daten und Clients befinden.

Gehen Sie unter **Data Lake Storage Gen 2 auswählen** wie folgt vor:

1. Wählen Sie unter **Kontoname** die Option **Neu erstellen** aus, und geben Sie für das neue Speicherkonto den Namen **contosolake** oder einen ähnlichen Namen ein. Dieser Name muss eindeutig sein.
1. Wählen Sie unter **Dateisystemname** die Option **Neu erstellen** aus, und geben Sie den Namen **Benutzer** ein. Dadurch wird ein Speichercontainer namens **Benutzer** erstellt. Der Arbeitsbereich verwendet dieses Speicherkonto als primäres Speicherkonto für Spark-Tabellen und Spark-Anwendungsprotokolle.
1. Aktivieren Sie das Kästchen „Mir die Rolle "Mitwirkender an Storage-Blobdaten" für das Data Lake Storage Gen2-Konto zuweisen“. 

## <a name="completing-the-process"></a>Abschließen des Vorgangs
Wählen Sie **Bewerten + erstellen** > **Erstellen** aus. Ihr Arbeitsbereich steht nach wenigen Minuten zur Verfügung.

> [!NOTE]
> Wie Sie Arbeitsbereichsfunktionen für einen vorhandenen dedizierten SQL-Pool (ehemals SQL DW) aktivieren, erfahren Sie unter [Aktivieren von Synapse-Arbeitsbereichsfunktionen für einen dedizierten SQL-Pool (ehemals SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Öffnen von Synapse Studio

Nachdem Ihr Azure Synapse-Arbeitsbereich erstellt wurde, haben Sie zwei Möglichkeiten zum Öffnen von Synapse Studio:

1. Öffnen Sie Ihren Synapse-Arbeitsbereich im [Azure-Portal](https://portal.azure.com), und wählen Sie im Abschnitt **Übersicht** des Synapse-Arbeitsbereichs im Feld „Synapse Studio öffnen“ den Eintrag **Öffnen** aus.
1. Navigieren Sie zu `https://web.azuresynapse.net`, und melden Sie sich bei Ihrem Arbeitsbereich an.

    ![Anmelden beim Arbeitsbereich](./security/media/common/login-workspace.png)

> [!NOTE]
> Um sich bei Ihrem Arbeitsbereich anzumelden, gibt es zwei **Kontoauswahlmethoden**. Eine stammt aus dem **Azure-Abonnement** und die andere ist die **manuelle Eingabe**. Wenn Sie über die Synapse Azure-Rolle oder Azure-Rollen auf höherer Ebene verfügen, können Sie beide Methoden verwenden, um sich beim Arbeitsbereich anzumelden. Wenn Sie nicht über eine der zugehörigen Azure-Rollen verfügen und Ihnen eine RBAC-Rolle von Synapse gewährt wurde, können Sie sich nur mit der **manuellen Eingabe** beim Arbeitsbereich anmelden. Weitere Informationen zur rollenbasierten Zugriffssteuerung für Synapse finden Sie unter [Informationen zu der rollenbasierte Zugriffssteuerung von Synapse (RBAC)](./security/synapse-workspace-synapse-rbac.md).


## <a name="place-sample-data-into-the-primary-storage-account"></a>Platzieren von Beispieldaten im primären Speicherkonto
In diesem Leitfaden zu den ersten Schritten verwenden Sie für viele Beispiele ein kleines Beispieldataset mit 100.000 Zeilen mit NYX Taxi-Daten. Sie beginnen damit, das Dataset im primären Speicherkonto zu platzieren, das Sie für den Arbeitsbereich erstellt haben.

* Laden Sie die folgende Datei auf Ihren Computer herunter: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* Navigieren Sie in Synapse Studio zum Hub „Daten“. 
* Wählen Sie **Mit Link** aus.
* In der Kategorie **Azure Data Lake Storage Gen2** wird ein Element mit einem Namen wie **myworkspace (Primär – contosolake)** angezeigt.
* Wählen Sie den Container mit dem Namen **Benutzer (Primär)** aus.
* Wählen Sie **Hochladen** und dann die von Ihnen heruntergeladene Datei `NYCTripSmall.parquet` aus.

Nach dem Hochladen der Parquet-Datei ist sie über zwei entsprechende URIs verfügbar:
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

Ersetzen Sie in den folgenden Beispielen in diesem Tutorial **contosolake** auf der Benutzeroberfläche durch den Namen des primären Speicherkontos, das Sie für Ihren Arbeitsbereich ausgewählt haben.



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren mithilfe eines serverlosen SQL-Pools](get-started-analyze-sql-on-demand.md)
