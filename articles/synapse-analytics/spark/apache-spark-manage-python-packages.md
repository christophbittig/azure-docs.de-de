---
title: Verwalten von Python-Bibliotheken für Apache Spark
description: Erfahren Sie, wie Sie Python-Bibliotheken hinzufügen und verwalten, die von Apache Spark in Azure Synapse Analytics verwendet werden.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.custom: has-adal-ref
ms.openlocfilehash: c696105fee677e8e8dca71d5515e0dd2374960b0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228447"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Verwalten von Python-Bibliotheken für Apache Spark in Azure Synapse Analytics

Bibliotheken stellen wiederverwendbaren Code bereit, den Sie in Ihre Programme oder Projekte einschließen können. 

Möglicherweise müssen Sie aus verschiedenen Gründen Ihre serverlose Apache Spark-Poolumgebung aktualisieren. Beispielsweise stellen Sie Folgendes fest:
- Für eine Ihrer Kernabhängigkeiten wurde kürzlich eine neue Version veröffentlicht.
- Sie benötigen ein zusätzliches Paket zum Trainieren Ihres Machine Learning-Modells oder zum Vorbereiten Ihrer Daten.
- Sie haben ein besseres Paket gefunden und benötigen das ältere Paket nicht mehr.

Wenn Sie Code eines Drittanbieters oder lokal erstellten Code für Ihre Anwendungen verfügbar machen möchten, können Sie eine Bibliothek in einem Ihrer serverlosen Apache Spark-Pools oder in einer Notebook-Sitzung installieren. In diesem Artikel erfahren Sie, wie Sie Python-Bibliotheken in Ihrem serverlosen Apache Spark-Pool verwalten können.

## <a name="default-installation"></a>Standardinstallation
Apache Spark in Azure Synapse Analytics umfasst eine vollständige Reihe an Bibliotheken für gängige Aufgaben in den Bereichen Datentechnik, Datenaufbereitung, maschinelles Lernen und Datenvisualisierung. Die vollständige Liste der Bibliotheken finden Sie unter [Apache Spark-Versionsunterstützung](apache-spark-version-support.md). 

Wenn eine Spark-Instanz gestartet wird, werden diese Bibliotheken automatisch eingeschlossen. Zusätzliche Python- und benutzerdefinierte Pakete können auf der Ebene des Spark-Pools und auf Sitzungsebene hinzugefügt werden.

## <a name="pool-libraries"></a>Poolbibliotheken
Nachdem Sie die Python-Bibliotheken identifiziert haben, die Sie für Ihre Spark-Anwendung verwenden möchten, können Sie diese Bibliotheken in einem Spark-Pool installieren. Bibliotheken auf Poolebene sind für alle Notebooks und Aufträge verfügbar, die im Pool ausgeführt werden.

Es gibt zwei Hauptmethoden zum Installieren einer Bibliothek auf einem Cluster:
-  Installieren Sie eine Arbeitsbereichsbibliothek, die als Arbeitsbereichspaket hochgeladen wurde.
-  Stellen Sie eine Umgebungsspezifikation *requirements.txt* oder *Conda environment.yml* bereit, um Pakete aus Repositorys wie z. B. PyPI oder Conda-Forge zu installieren.

> [!IMPORTANT]
> - Wenn das Paket, das Sie installieren, groß ist oder seine Installation lange dauert, wirkt sich dies auf die Startzeit der Spark-Instanz aus.
> - Das Ändern der Version von PySpark, Python, Scala/Java, .NET oder Spark wird nicht unterstützt.
> - Das Installieren von Paketen aus externen Repositorys wie PyPI, Conda-Forge oder den Conda-Standardkanälen wird in DEP-fähigen Arbeitsbereichen (Data Exfiltration Protection, Schutz vor Datenexfiltration) nicht unterstützt.

### <a name="install-python-packages"></a>Installieren von Python-Paketen
Python-Pakete können aus Repositorys wie PyPI und Conda-Forge durch Bereitstellen einer Umgebungsspezifikationsdatei installiert werden. 

#### <a name="environment-specification-formats"></a>Formate für Umgebungsspezifikationen

##### <a name="pip-requirementstxt"></a>PIP-Datei „requirements.txt“
Sie können eine Datei *requirements.txt* (Ausgabe des Befehls `pip freeze`) verwenden, um ein Upgrade der Umgebung durchzuführen. Bei der Aktualisierung eines Pools werden die in dieser Datei aufgeführten Pakete aus PyPI heruntergeladen. Die vollständigen Abhängigkeiten werden dann zwischengespeichert und zur späteren Wiederverwendung des Pools gespeichert. 

Der folgende Codeausschnitt zeigt das Format für die Anforderungsdatei. Der Name des PyPI-Pakets wird zusammen mit einer exakten Version aufgeführt. Diese Datei hält das Format ein, das in der Referenzdokumentation zu [pip freeze](https://pip.pypa.io/en/stable/cli/pip_freeze/) beschrieben wird. 

Dieses Beispiel verwendet eine bestimmte Version. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format"></a>YML-Format
Darüber hinaus können Sie auch eine Datei *environment.yml* bereitstellen, um die Poolumgebung zu aktualisieren. Die in dieser Datei aufgeführten Pakete werden aus den Conda-Standardkanälen „Conda-Forge“ und „PyPI“ heruntergeladen. Mithilfe der Konfigurationsoptionen können Sie andere Kanäle angeben oder die Standardkanäle entfernen.

In diesem Beispiel werden die Kanäle und die Conda-/PyPI-Abhängigkeiten angegeben. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Ausführliche Informationen zum Erstellen einer Umgebung aus dieser Datei „environment.yml“ finden Sie unter [Erstellen einer Umgebung aus einer Datei „environment.yml“](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
).

#### <a name="update-python-packages"></a>Aktualisieren von Python-Paketen
Nachdem Sie die Umgebungsspezifikationsdatei oder den Satz von Bibliotheken für die Installation im Spark-Pool identifiziert haben, können Sie die Spark-Poolbibliotheken aktualisieren, indem Sie zu Synapse Studio oder zum Azure-Portal navigieren. Hier können Sie die Umgebungsspezifikation angeben und die zu installierenden Arbeitsbereichsbibliotheken auswählen. 

Nachdem die Änderungen gespeichert wurden, wird die Installation durch einen Spark-Auftrag ausgeführt und die resultierende Umgebung zur späteren Wiederverwendung zwischengespeichert. Sobald der Auftrag fertiggestellt wurde, werden bei neuen Spark-Aufträgen oder Notebook-Sitzungen die aktualisierten Poolbibliotheken verwendet. 

##### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>Verwalten von Paketen über Synapse Studio oder das Azure-Portal
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
   
2. Laden Sie die Umgebungskonfigurationsdatei mithilfe der Dateiauswahl im Abschnitt **Pakete** auf der Seite hoch.
3. Sie können auch zusätzliche **Arbeitsbereichspakete** auswählen, die dem Pool hinzugefügt werden sollen. 
4. Nachdem Sie Ihre Änderungen gespeichert haben, wird ein Systemauftrag ausgelöst, um die angegebenen Bibliotheken zu installieren und zwischenzuspeichern. Durch diesen Vorgang wird die Gesamtstartzeit der Sitzung reduziert. 
5. Sobald der Auftrag erfolgreich abgeschlossen wurde, werden in allen neuen Sitzungen die aktualisierten Poolbibliotheken abgerufen.

> [!IMPORTANT]
> Wenn Sie die Option **Neue Einstellungen erzwingen** aktivieren, beenden Sie damit alle aktuellen Sitzungen für den ausgewählten Spark-Pool. Nachdem die Sitzungen beendet wurden, müssen Sie warten, bis der Pool neu gestartet wird. 
>
> Wenn diese Einstellung deaktiviert ist, müssen Sie warten, bis die aktuelle Spark-Sitzung beendet wird, oder sie manuell beenden. Nachdem die Sitzung beendet wurde, müssen Sie den Pool neu starten.


##### <a name="track-installation-progress"></a>Nachverfolgen des Installationsstatus  
Bei jeder Aktualisierung eines Pools mit einem neuen Satz von Bibliotheken wird ein vom System reservierter Spark-Auftrag initiiert. Mit diesem Spark-Auftrag können Sie den Status der Bibliotheksinstallation überwachen. Wenn bei der Installation aufgrund von Bibliothekskonflikten oder anderen Problemen ein Fehler auftritt, wird der Spark-Pool auf den vorherigen oder den Standardzustand zurückgesetzt. 

Darüber hinaus können Benutzer auch die Installationsprotokolle überprüfen, um Abhängigkeitskonflikte zu identifizieren oder zu ermitteln, welche Bibliotheken während des Poolupdates installiert wurden.

So zeigen Sie diese Protokolle an
1. Navigieren Sie auf der Registerkarte **Überwachen** zur Liste der Spark-Anwendungen. 
2. Wählen Sie den Spark-Systemanwendungsauftrag aus, der Ihrem Poolupdate entspricht. Diese Systemaufträge werden unter dem Titel *SystemReservedJob-LibraryManagement* ausgeführt.
   ![Screenshot mit vom System reservierten Bibliotheksauftrag.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Anzeigen eines Systembibliotheksauftrags")
3. Wechseln Sie zur Anzeige der Protokolle **driver** und **stdout**. 
4. Innerhalb der Ergebnisse werden die Protokolle angezeigt, die sich auf die Installation Ihrer Abhängigkeiten beziehen.
    ![Screenshot mit Ergebnissen der vom System reservierten Bibliotheksaufträge.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Anzeigen des Status eines Systembibliotheksauftrags")

## <a name="install-wheel-files"></a>Installieren von Wheel-Dateien
Python-Wheel-Dateien sind eine gängige Methode zum Erstellen von Paketen aus Python-Bibliotheken. Innerhalb von Azure Synapse Analytics können Benutzer ihre Wheel-Dateien an einen bekannten Speicherort im Azure Data Lake Storage-Konto hochladen oder den Upload über die Azure Synapse-Schnittstelle für Arbeitsbereichspakete durchführen.

### <a name="workspace-packages"></a>Arbeitsbereichspakete 
Arbeitsbereichspakete können benutzerdefinierte oder private Wheel-Dateien sein. Sie können diese Pakete in Ihren Arbeitsbereich hochladen und später einem bestimmten Spark-Pool zuweisen.

So fügen Sie Arbeitsbereichspakete hinzu
1. Navigieren Sie zu **Verwalten** > Registerkarte **Arbeitsbereichspakete**.
2. Laden Sie Ihre Wheel-Dateien über die Dateiauswahl hoch.
3. Nachdem die Dateien in den Azure Synapse-Arbeitsbereich hochgeladen wurden, können Sie diese Pakete einem bestimmten Apache Spark-Pool hinzufügen.

![Screenshot mit Arbeitsbereichspaketen.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Anzeigen von Arbeitsbereichspaketen")

>[!WARNING]
>- Innerhalb von Azure Synapse kann ein Apache Spark-Pool benutzerdefinierte Bibliotheken nutzen, die entweder als Workspace-Pakete oder innerhalb eines bekannten Azure Data Lake Storage-Pfads hochgeladen werden. Allerdings können diese beiden Optionen nicht gleichzeitig innerhalb desselben Apache Spark-Pools verwendet werden. Bei Bereitstellung von Paketen mit beiden Methoden werden nur die in der Liste der Arbeitsbereichspakete angegebenen Wheel-Dateien installiert. 
>
>- Sobald Arbeitsbereichspakete verwendet werden, um Pakete in einem bestimmten Apache Spark-Pool zu installieren, gibt es die Einschränkung, dass Sie keine Pakete mehr mit dem Pfad des Speicherkontos in demselben Pool angeben können.  

### <a name="storage-account"></a>Speicherkonto
Benutzerdefiniert erstellte Wheel-Pakete können im Apache Spark-Pool installiert werden, indem alle Wheel-Dateien in Azure Data Lake Storage (Gen2)-Konto hochgeladen werden, das mit dem Synapse-Arbeitsbereich verknüpft ist. 

Die Dateien sollten in den folgenden Pfad im Standardcontainer des Speicherkontos hochgeladen werden: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> - In manchen Fällen müssen Sie den Dateipfad anhand der obigen Struktur erstellen, wenn er nicht bereits vorhanden ist. Als Beispiel müssen Sie möglicherweise den Ordner ```python``` innerhalb des Ordners ```libraries``` hinzufügen, wenn er nicht bereits vorhanden ist.
> - Diese Methode zum Verwalten von benutzerdefinierten Wheeldateien wird in der Azure Synapse-Runtime für Apache Spark 3.0 nicht unterstützt. Informationen zum Verwalten benutzerdefinierter Wheeldateien finden Sie unter dem Feature Arbeitsbereichspakete.

> [!IMPORTANT]
> Um benutzerdefinierte Bibliotheken über die Azure Data Lake Storage-Methode zu installieren, müssen Sie über die Berechtigung **Mitwirkender an Storage-Blobdaten** oder **Besitzer von Speicherblobdaten** für das primäre Gen2-Speicherkonto verfügen, das mit dem Azure Synapse Analytics-Arbeitsbereich verknüpft ist.


## <a name="session-scoped-packages"></a>Sitzungsbezogene Pakete
Zusätzlich zu den Paketen auf Poolebene können Sie zu Beginn einer Notebook-Sitzung auch sitzungsbezogene Bibliotheken angeben.  Mithilfe sitzungsbezogener Bibliotheken können Sie benutzerdefinierte Python-Umgebungen innerhalb einer Notebook-Sitzung angeben und verwenden. 

Bei der Verwendung sitzungsbezogener Bibliotheken sind folgende Punkte zu berücksichtigen:
   - Wenn Sie sitzungsbezogene Bibliotheken installieren, kann nur das aktuelle Notebook auf die angegebenen Bibliotheken zugreifen. 
   - Diese Bibliotheken wirken sich nicht auf andere Sitzungen oder Aufträge aus, die denselben Spark-Pool verwenden. 
   - Diese Bibliotheken werden auf Basis der zugrunde liegenden Bibliotheken auf Runtime- und Poolebene installiert. 
   - Notebook-Bibliotheken besitzen die höchste Priorität.

So geben Sie sitzungsbezogene Pakete an
1.  Navigieren Sie zum ausgewählten Spark-Pool, und stellen Sie sicher, dass Sie Bibliotheken auf Sitzungsebene aktiviert haben.  Sie können diese Einstellung aktivieren, indem Sie zu **Verwalten** > **Apache Spark-Pool** > Registerkarte **Pakete** navigieren. ![Aktivieren von Sitzungspaketen.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Aktivieren von Sitzungspaketen")
2.  Nachdem die Einstellung angewendet wurde, können Sie ein Notebook öffnen und **Sitzung konfigurieren**> **Pakete** auswählen.
  ![Angeben von Sitzungspaketen.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Aktualisieren der Sitzungskonfiguration")
3.  Hier können Sie eine Conda-Datei *environment.yml* für die Installation oder das Upgrade von Paketen innerhalb einer Sitzung hochladen. Nachdem Sie die Sitzung gestartet haben, werden die angegebenen Bibliotheken installiert. Sobald Ihre Sitzung beendet wurde, sind diese Bibliotheken nicht mehr verfügbar, da sie nur für Ihre Sitzung gelten.

## <a name="verify-installed-libraries"></a>Überprüfen von installierten Bibliotheken
Führen Sie den folgenden Code aus, um zu überprüfen, ob die richtigen Versionen der richtigen Bibliotheken über PyPI installiert werden:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Um die Paketversionen aus Conda anzeigen zu können, müssen Sie in einigen Fällen die Paketversion einzeln überprüfen.

## <a name="next-steps"></a>Nächste Schritte
- Anzeigen der Standardbibliotheken: [Versionsunterstützung für Apache Spark](apache-spark-version-support.md)
- Beheben von Fehler bei der Bibliotheksinstallation: [Beheben von Fehlern bei der Bibliotheksinstallation](apache-spark-troubleshoot-library-errors.md)
- Erstellen eines privaten Conda-Kanals mit Ihrem Azure Data Lake Storage-Konto: [Erstellen eines benutzerdefinierten Conda-Kanals für die Paketverwaltung](./spark/../apache-spark-custom-conda-channel.md).
