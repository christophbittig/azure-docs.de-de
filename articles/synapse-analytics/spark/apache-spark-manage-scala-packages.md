---
title: Verwalten von Scala- und Java-Bibliotheken für Apache Spark
description: Erfahren Sie, wie Sie Scala- und Java-Bibliotheken in Azure Synapse Analytics hinzufügen und verwalten.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 952e0b325fe37345db42d1dcd8d6c521f8235366
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225396"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Verwalten von Scala- und Java-Paketen für Apache Spark in Azure Synapse Analytics

Bibliotheken stellen wiederverwendbaren Code bereit, den Sie in Ihre Programme oder Projekte einschließen können. 

Möglicherweise müssen Sie aus verschiedenen Gründen Ihre serverlose Apache Spark-Poolumgebung aktualisieren. Beispielsweise stellen Sie Folgendes fest:
- Für eine Ihrer Kernabhängigkeiten wurde eine neue Version veröffentlicht.
- Sie benötigen ein zusätzliches Paket zum Trainieren Ihres Machine Learning-Modells oder zum Vorbereiten Ihrer Daten.
- Sie haben ein besseres Paket gefunden und benötigen das ältere Paket nicht mehr.

Wenn Sie Code eines Drittanbieters oder lokal erstellten Code für Ihre Anwendungen verfügbar machen möchten, können Sie eine Bibliothek in einem Ihrer serverlosen Apache Spark-Pools oder in einer Notebook-Sitzung installieren. In diesem Artikel erfahren Sie, wie Scala- und Java-Pakete verwaltet werden.

## <a name="default-installation"></a>Standardinstallation
Apache Spark in Azure Synapse Analytics umfasst eine vollständige Reihe an Bibliotheken für gängige Aufgaben in den Bereichen Datentechnik, Datenaufbereitung, maschinelles Lernen und Datenvisualisierung. Die vollständige Liste der Bibliotheken finden Sie unter [Apache Spark-Versionsunterstützung](apache-spark-version-support.md). 

Wenn eine Spark-Instanz gestartet wird, werden diese Bibliotheken automatisch eingeschlossen. Weitere Scala-/Java-Paket können auf Spark-Pool- und Sitzungsebene hinzugefügt werden.

## <a name="workspace-packages"></a>Arbeitsbereichspakete
Arbeitsbereichspakete können benutzerdefinierte oder private JAR-Dateien sein. Sie können diese Pakete in Ihren Arbeitsbereich hochladen und später einem bestimmten Spark-Pool zuweisen.

So fügen Sie Arbeitsbereichspakete hinzu
1. Navigieren Sie zu **Verwalten** > Registerkarte **Arbeitsbereichspakete**.
2. Laden Sie Ihre JAR-Dateien über die Dateiauswahl hoch.
3. Nachdem die Dateien in den Azure Synapse-Arbeitsbereich hochgeladen wurden, können Sie diese JAR-Dateien einem bestimmten Apache Spark-Pool hinzufügen.

![Screenshot mit Arbeitsbereichspaketen.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Anzeigen von Arbeitsbereichspaketen")

## <a name="pool-libraries"></a>Poolbibliotheken
Nachdem Sie die Scala- und Java-Pakete identifiziert haben, die Sie für Ihre Spark-Anwendung verwenden möchten, können Sie diese Pakete in einem Spark-Pool installieren. Bibliotheken auf Poolebene sind für alle Notebooks und Aufträge verfügbar, die im Pool ausgeführt werden.

Sie können die Spark-Poolbibliotheken aktualisieren, indem Sie zu Synapse Studio oder zum Azure-Portal navigieren. Hier können Sie die zu installierenden Arbeitsbereichsbibliotheken auswählen. 

Nachdem die Änderungen gespeichert wurden, wird die Installation durch einen Spark-Auftrag ausgeführt und die resultierende Umgebung zur späteren Wiederverwendung zwischengespeichert. Sobald der Auftrag fertiggestellt wurde, werden bei neuen Spark-Aufträgen oder Notebook-Sitzungen die aktualisierten Poolbibliotheken verwendet. 

> [!IMPORTANT]
> - Wenn das Paket, das Sie installieren, groß ist oder seine Installation lange dauert, wirkt sich dies auf die Startzeit der Spark-Instanz aus.
> - Das Ändern der Version von PySpark, Python, Scala/Java, .NET oder Spark wird nicht unterstützt.

#### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>Verwalten von Paketen über Synapse Studio oder das Azure-Portal
Spark-Poolbibliotheken können entweder über Synapse Studio oder über das Azure-Portal verwaltet werden. 

So aktualisieren Sie Bibliotheken oder fügen sie einem Spark-Pool hinzu
1. Navigieren Sie im Azure-Portal zu Ihrem Azure Synapse Analytics-Arbeitsbereich.

    Bei Aktualisierung über das **Azure-Portal**:

    - Wählen Sie im Abschnitt **Synapse-Ressourcen** die Registerkarte **Apache Spark-Pools** aus, und wählen Sie einen Spark-Pool aus der Liste aus.
     
    - Wählen Sie im Abschnitt **Einstellungen** des Spark-Pools die Option **Pakete** aus.
  
    ![Screenshot mit Schaltfläche zum Hochladen der Umgebungskonfigurationsdatei.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Hinzufügen von Python-Bibliotheken")
   
    Bei Aktualisierung über **Synapse Studio**:
    - Wählen Sie im Hauptnavigationsbereich **Verwalten** aus und dann **Apache Spark-Pools**.

    - Wählen Sie den Abschnitt **Pakete** für einen bestimmten Spark-Pool aus.
    ![Screenshot mit Option zum Hochladen der Umgebungskonfiguration aus Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Hinzufügen von Python-Bibliotheken aus Studio")
   
2. Um dem Pool JAR-Dateien hinzuzufügen, navigieren Sie zum Abschnitt **Arbeitsbereichspakete**. 
3. Nachdem Sie Ihre Änderungen gespeichert haben, wird ein Systemauftrag ausgelöst, um die angegebenen Bibliotheken zu installieren und zwischenzuspeichern. Durch diesen Vorgang wird die Gesamtstartzeit der Sitzung reduziert. 
4. Sobald der Auftrag erfolgreich abgeschlossen wurde, werden in allen neuen Sitzungen die aktualisierten Poolbibliotheken abgerufen.

> [!IMPORTANT]
> Wenn Sie die Option **Neue Einstellungen erzwingen** aktivieren, beenden Sie damit alle aktuellen Sitzungen für den ausgewählten Spark-Pool. Nachdem die Sitzungen beendet wurden, müssen Sie warten, bis der Pool neu gestartet wird. 
>
> Wenn diese Einstellung deaktiviert ist, müssen Sie warten, bis die aktuelle Spark-Sitzung beendet wird, oder sie manuell beenden. Nachdem die Sitzung beendet wurde, müssen Sie den Pool neu starten.

#### <a name="track-installation-progress"></a>Nachverfolgen des Installationsstatus 
Bei jeder Aktualisierung eines Pools mit einem neuen Satz von Bibliotheken wird ein vom System reservierter Spark-Auftrag initiiert. Mit diesem Spark-Auftrag können Sie den Status der Bibliotheksinstallation überwachen. Wenn bei der Installation aufgrund von Bibliothekskonflikten oder anderen Problemen ein Fehler auftritt, wird der Spark-Pool auf den vorherigen oder den Standardzustand zurückgesetzt. 

Darüber hinaus können Benutzer auch die Installationsprotokolle überprüfen, um Abhängigkeitskonflikte zu identifizieren oder zu ermitteln, welche Bibliotheken während des Poolupdates installiert wurden.

So zeigen Sie diese Protokolle an
1. Navigieren Sie auf der Registerkarte **Überwachen** zur Liste der Spark-Anwendungen. 
2. Wählen Sie den Spark-Systemanwendungsauftrag aus, der Ihrem Poolupdate entspricht. Diese Systemaufträge werden unter dem Titel *SystemReservedJob-LibraryManagement* ausgeführt.
   ![Screenshot mit vom System reservierten Bibliotheksauftrag.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Anzeigen eines Systembibliotheksauftrags")
3. Wechseln Sie zur Anzeige der Protokolle **driver** und **stdout**. 
4. Innerhalb der Ergebnisse werden die Protokolle angezeigt, die sich auf die Installation Ihrer Pakete beziehen.
    ![Screenshot mit Ergebnissen der vom System reservierten Bibliotheksaufträge.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Anzeigen des Status eines Systembibliotheksauftrags")

## <a name="session-scoped-libraries"></a>Sitzungsbezogene Bibliotheken 
Zusätzlich zu den Bibliotheken auf Poolebene können Sie zu Beginn einer Notebook-Sitzung auch sitzungsbezogene Bibliotheken angeben.  Mit sitzungsbezogenen Bibliotheken können Sie JAR-Pakete ausschließlich innerhalb einer Notebook-Sitzung angeben und verwenden. 

Bei der Verwendung sitzungsbezogener Bibliotheken sind folgende Punkte zu berücksichtigen:
   - Wenn Sie sitzungsbezogene Bibliotheken installieren, kann nur das aktuelle Notebook auf die angegebenen Bibliotheken zugreifen. 
   - Diese Bibliotheken wirken sich nicht auf andere Sitzungen oder Aufträge aus, die denselben Spark-Pool verwenden. 
   - Diese Bibliotheken werden auf Basis der zugrunde liegenden Bibliotheken auf Runtime- und Poolebene installiert. 
   - Notebook-Bibliotheken besitzen die höchste Priorität.

Sitzungsbezogene Java- oder Scala-Pakete können über die Option ```%%configure``` angegeben werden:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Wir empfehlen Ihnen, %%configure am Anfang Ihres Notebooks auszuführen. In diesem [Dokument](https://github.com/cloudera/livy#request-body) finden Sie eine vollständige Liste gültiger Parameter.

## <a name="next-steps"></a>Nächste Schritte
- Anzeigen der Standardbibliotheken: [Versionsunterstützung für Apache Spark](apache-spark-version-support.md)
- Beheben von Fehler bei der Bibliotheksinstallation: [Beheben von Fehlern bei der Bibliotheksinstallation](apache-spark-troubleshoot-library-errors.md)
