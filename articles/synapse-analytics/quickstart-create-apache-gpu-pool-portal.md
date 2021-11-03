---
title: 'Schnellstart: Erstellen eines serverlosen Apache Spark GPU-Pools'
description: Erstellen Sie anhand der Schritte in diesem Leitfaden einen serverlosen Apache Spark GPU-Pool mithilfe des Azure-Portals.
services: synapse-analytics
author: Niharikadutta
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/18/2021
ms.author: nidutta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 439e8b70c20bc07a431b17afdca84690c3cbd61e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101640"
---
# <a name="quickstart-create-an-apache-spark-gpu-enabled-pool-in-azure-synapse-analytics-using-the-azure-portal"></a>Schnellstart: Erstellen eines Apache Spark GPU-fähigen Pools in Azure Synapse Analytics über das Azure-Portal

Ein Apache Spark-Pool bietet Open-Source-Big Data-Computefunktionen, mit denen Daten geladen, modelliert, verarbeitet und für einen schnelleren analytischen Einblick bereitgestellt werden können. Synapse bietet jetzt die Möglichkeit, Apache Spark-Pools zu erstellen, die GPUs im Back-End verwenden, um Ihre Spark-Workloads zur schnelleren Verarbeitung auf GPUs auszuführen.

In dieser Schnellstartanleitung erfahren Sie, wie Sie das Azure-Portal verwenden, um einen Apache Spark GPU-fähigen Pool in einem Azure Synapse Analytics-Arbeitsbereich zu erstellen.

> [!NOTE]
> Azure Synapse GPU-fähige Pools befinden sich derzeit in Public Preview.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Synapse Analytics-Arbeitsbereich](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigieren zum Synapse-Arbeitsbereich 
1. Navigieren Sie zum Synapse-Arbeitsbereich, 0in dem der Apache Spark-Pool erstellt werden soll, indem Sie den Dienstnamen (oder direkt den Ressourcennamen) in die Suchleiste eingeben.
![Suchleiste im Azure-Portal mit der Eingabe „Synapse-Arbeitsbereiche“.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
2. Geben Sie in der Liste der Arbeitsbereiche den Namen (oder einen Teil des Namens) des zu öffnenden Arbeitsbereichs ein. In diesem Beispiel verwenden wir einen Arbeitsbereich namens **contosoanalytics**.
![Auflistung von Synapse-Arbeitsbereichen, die so gefiltert wurden, dass diejenigen, die den Namen „Contoso“ enthalten, angezeigt werden](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-azure-synapse-gpu-enabled-pool"></a>Erstellen eines Azure Synapse GPU-fähigen Pools

1. Wählen Sie in dem Synapse-Arbeitsbereich, in dem Sie den Apache Spark-Pool erstellen möchten, **Neuer Apache Spark-Pool** aus.
    ![Übersicht über den Synapse-Arbeitsbereich mit dem rot umrandeten Befehl zum Erstellen eines neuen Apache Spark-Pools](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen ein:

    |Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    | :------ | :-------------- | :---------- |
    | **Name des Apache Spark-Pools** | Ein gültiger Poolname | Dies ist der Name des Apache Spark-Pools. |
    | **Knotengröße (Familie)** | Hardwarebeschleunigt | Wählen Sie im Dropdownmenü „Hardwarebeschleunigt“ aus. |
    | **Knotengröße** | Groß (16 vCPUs/110 GB/1 GPU) | Legen Sie diese Einstellung auf die kleinste Größe fest, um die Kosten für diesen Schnellstart zu senken. |
    | **Automatische Skalierung** | Disabled | Für diesen Schnellstart ist keine Autoskalierung erforderlich. |
    | **Anzahl von Knoten** | 3 | Verwenden Sie eine kleine Größe, um die Kosten für diesen Schnellstart zu begrenzen. |


    ![Flow für die Apache Spark-Poolerstellung: Registerkarte „Grundeinstellungen“](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-01.png)
    > [!IMPORTANT]
    > Beachten Sie, dass für die Namen, die von Apache Spark-Pools verwendet werden können, bestimmte Einschränkungen gelten. Namen dürfen nur Buchstaben oder Ziffern enthalten und höchstens 15 Zeichen lang sein, müssen mit einem Buchstaben beginnen, dürfen keine reservierten Wörter enthalten und müssen im Arbeitsbereich eindeutig sein.

3. Wählen Sie **Weiter: Zusätzliche Einstellungen** aus, und überprüfen Sie die Standardeinstellungen. Ändern Sie keine Standardeinstellungen. Beachten Sie, dass GPU-Pools **nur mit Apache Spark 3.1 erstellt** werden können.
    ![Screenshot: Seite „Apache Spark-Pool erstellen“, auf der die Registerkarte „Zusätzliche Einstellungen“ ausgewählt ist](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-02.png)

4. Wählen Sie **Weiter: Tags** aus. Fügen Sie keine Tags hinzu.

    ![Flow für die Apache Spark-Poolerstellung: Registerkarte „Zusätzliche Einstellungen“](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Klicken Sie auf **Überprüfen + erstellen**.

6. Vergewissern Sie sich auf Grundlage der vorherigen Angaben, dass die Details korrekt sind, und wählen Sie **Erstellen** aus.
    ![Flow für die Apache Spark-Poolerstellung: Registerkarte zum Überprüfen der Einstellungen](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-03.png)

7. Der Flow für die Ressourcenbereitstellung wird nun gestartet. Er zeigt an, wenn er abgeschlossen ist.
    ![Screenshot: Seite „Übersicht“, auf der die Meldung angezeigt wird, dass die Bereitstellung abgeschlossen wurde](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Wenn Sie nach Abschluss der Bereitstellung zurück zum Arbeitsbereich navigieren, wird ein neuer Eintrag für den neu erstellten Azure Synapse GPU-fähigen Pool angezeigt.
    ![Flow für die Apache Spark-Poolerstellung: Ressourcenbereitstellung](media/quickstart-create-apache-spark-pool/create-spark-gpu-pool-portal-04.png)

9. Zu diesem Zeitpunkt werden keine Ressourcen ausgeführt, und es fallen keine Gebühren für Spark an. Sie haben lediglich Metadaten für die zu erstellenden Spark-Instanzen erstellt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, um den Apache Spark-Pool aus dem Arbeitsbereich zu löschen.
> [!WARNING]
> Wenn Sie einen Apache Spark-Pool löschen, wird die Analyse-Engine aus dem Arbeitsbereich entfernt. Sie können keine Verbindung mehr mit dem Pool herstellen, und Abfragen, Pipelines und Notebooks, die diesen Apache Spark-Pool verwenden, funktionieren nicht mehr.

Gehen Sie zum Löschen des Apache Spark-Pools wie folgt vor:

1. Navigieren Sie im Arbeitsbereich zum Blatt mit den Apache Spark-Pools.
2. Wählen Sie den zu löschenden Apache Spark-Pool aus (in diesem Fall **contosospark**).
3. Klicken Sie auf **Löschen**.

 ![Liste der Apache Spark-Pools, der soeben erstellte Pool ist ausgewählt.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)

4. Bestätigen Sie den Löschvorgang, und klicken Sie auf die Schaltfläche **Löschen**.

 ![Bestätigungsdialogfeld zum Löschen des ausgewählten Apache Spark-Pools](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)

5. Wenn der Vorgang erfolgreich abgeschlossen wurde, wird der Apache Spark-Pool nicht mehr in den Arbeitsbereichsressourcen aufgeführt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Apache Spark-Notebooks für die Ausführung in einem GPU-Pool](spark/apache-spark-rapids-gpu.md).
