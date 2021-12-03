---
title: Umfangreiche Sicherheitsanalysen mit Microsoft Sentinel Notebooks und Azure Synapse-Integration
description: In diesem Artikel wird beschrieben, wie Sie Abfragen von großen Datenmengen in Azure Synapse Analytics mit Microsoft Sentinel-Notebooks ausführen.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 10914037b239bf2301881a18c6095ba9364af4ae
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517328"
---
# <a name="large-scale-security-analytics-using-microsoft-sentinel-notebooks-and-azure-synapse-integration-public-preview"></a>Umfangreiche Sicherheitsanalysen mit Microsoft Sentinel-Notebooks und Azure Synapse-Integration (öffentliche Vorschau)

> [!IMPORTANT]
> Die Integration von Microsoft Sentinel-Notebooks mit Azure Synapse Analytics befindet sich derzeit in der VORSCHAU. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

Die Integration von Microsoft Sentinel-Notebooks mit Azure Synapse Analytics ermöglicht umfangreiche Sicherheitsanalysen.

Während KQL und Log Analytics die primären Tools und Lösungen zum Abfragen und Analysieren von Daten in Microsoft Sentinel sind, bietet Azure Synapse zusätzliche Funktionen für die Analyse von Datenmengen mit integriertem Data Lake-Zugriff und der verteilten Verarbeitungs-Engine von Apache Spark.

Die Integration mit Azure Synapse bietet folgendes:

- **Sicherheitsanalyse von Datenmengen** mit kostenoptimiertem, vollständig verwaltetem Azure Synapse Apache Spark-Rechenpool.

- **Kostengünstiger Data Lake-Zugriff** zum Erstellen von Analysen zu historischen Daten über Azure Data Lake Storage Gen2, was eine Reihe von Funktionen speziell für Analysen von Datenmengen darstellt, die auf Azure Blob Storage aufbauen.

- **Flexibilität zur Integration von Datenquellen** aus mehreren Quellen und Formaten in Arbeitsabläufe für Sicherheitsvorgänge.

- **PySpark, eine Python-basierte API** zur Verwendung des Spark-Frameworks in Kombination mit Python, sodass Sie keine neue Programmiersprache erlernen müssen, wenn Sie bereits mit Python vertraut sind.

Sie können beispielsweise Notebooks mit Azure Synapse verwenden, um in Netzwerkfirewallprotokollen nach anormalem Verhalten zu suchen, um potenzielles Netzwerk-Beaconing zu erkennen, oder um Machine Learning-Modelle auf Grundlage von Daten zu trainieren und zu erstellen, die aus einem Log Analytics-Arbeitsbereich gesammelt wurden.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="understand-microsoft-sentinel-notebooks"></a>Sich mit Microsoft Sentinel-Notebooks vertraut machen

Es wird empfohlen, dass Sie sich im Allgemeinen mit Microsoft Sentinel-Notebooks vertraut machen, bevor Sie die Prozeduren in diesem Artikel ausführen.

Zu Beginn sollten Sie einen Blick auf das Kapitel [Jupyter-Notebooks verwenden, um nach Sicherheitsbedrohungen zu suchen](notebooks.md) sowie das [Tutorial: Erste Schritte mit Jupyter-Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md) werfen.

### <a name="required-roles-and-permissions"></a>Erforderliche Rollen und Berechtigungen

Um Azure Synapse mit Microsoft Sentinel-Notebooks zu verwenden, müssen Sie über die folgenden Rollen und Berechtigungen verfügen:

|type  |Details  |
|---------|---------|
|**Microsoft Sentinel**     |- Die Rolle **Microsoft Sentinel-Mitwirkender** zum Speichern und Starten von Notebooks aus Microsoft Sentinel heraus.         |
|**Azure Machine Learning**     |- Eine Rolle **Inhaber** oder **Mitwirkender** auf Ressourcengruppenebene, um bei Bedarf einen neuen Azure Machine Learning-Arbeitsbereich zu erstellen. <br>- Eine Rolle **Mitwirkender** im Azure Machine Learning-Arbeitsbereich, in dem Sie Ihre Microsoft Sentinel-Notebooks ausführen.    <br><br>Weitere Informationen finden Sie unter [Zugriff auf einen Azure Machine Learning-Arbeitsbereich verwalten](/azure/machine-learning/how-to-assign-roles).     |
|**Azure Synapse Analytics**     | - Eine Rolle **Besitzer** auf Ressourcengruppenebene, um einen neuen Azure Synapse-Arbeitsbereich zu erstellen.<br>- Eine Rolle **Mitwirkender** im Azure Synapse-Arbeitsbereich zum Ausführen Ihrer Abfragen. <br>- Eine Rolle Azure Synapse Analytics-**Mitwirkender** in Synapse Studio   <br><br>Weitere Informationen finden Sie unter [Informationen zu den Rollen, die zum Ausführen allgemeiner Aufgaben in Synapse erforderlich sind](/azure/synapse-analytics/security/synapse-workspace-understand-what-role-you-need).     |
|**Azure Data Lake Storage Gen2**     | - Eine Rolle Azure Log Analytics-**Mitwirkender** zum Exportieren von Daten aus einem Log Analytics-Arbeitsbereich<br>– Eine Rolle Azure Blob Storage-Mitwirkender zum Abfragen von Daten aus einem Data Lake  <br><br>Weitere Informationen finden Sie unter [Zuweisen einer Azure-Rolle](/azure/storage/blobs/assign-azure-role-data-access?tabs=portal).|
|     |         |

### <a name="connect-to-azure-ml-and-synapse-workspaces"></a>Herstellen einer Verbindung mit Azure ML- und Synapse-Arbeitsbereichen

Um Microsoft Sentinel-Notebooks mit Azure Synapse zu verwenden, müssen Sie zunächst eine Verbindung mit einem Azure Machine Learning-Arbeitsbereich und einem Azure Synapse-Arbeitsbereich herstellen.

**So erstellen Sie einen Azure Machine Learning-Arbeitsbereich oder stellen eine Verbindung zu ihm her**:

Azure Machine Learning-Arbeitsbereiche sind eine Grundvoraussetzung für die Verwendung von Notebooks in Microsoft Sentinel.

Wenn Sie noch nicht verbunden sind, lesen Sie den Abschnitt [Jupyter-Notebooks verwenden, um nach Sicherheitsbedrohungen zu suchen](notebooks.md).

**So erstellen Sie einen neuen Azure Synapse-Arbeitsbereich**:

Wählen Sie oben auf der Seite **Notebooks** von Microsoft Sentinel die Option **Azure Synapse konfigurieren** und dann **Neuen Azure Synapse-Arbeitsbereich erstellen** aus.

> [!NOTE]
> Azure Data Lake Storage Gen2 ist ein integrierter Data Lake, der in jedem Azure Synapse-Arbeitsbereich enthalten ist. Stellen Sie sicher, dass Sie einen neuen Data Lake auswählen oder erstellen, der sich in derselben Region wie Ihr Microsoft Sentinel-Arbeitsbereich befindet. Dies ist erforderlich, wenn Sie Ihre Daten exportieren, wie weiter unten in diesem Artikel beschrieben.
>

Weitere Informationen finden Sie in der [Azure Synapse-Dokumentation](/azure/synapse-analytics/quickstart-create-workspace).


## <a name="configure-your-azure-synapse-analytics-integration"></a>Ihre Azure Synapse Analytics-Integration konfigurieren

Microsoft Sentinel bietet das integrierte Notebook **Azure Synapse – Konfigurieren von Azure ML und Azure Synapse Analytics**, das Sie durch die Konfigurationen führt, die für die Integration in Azure Synapse erforderlich sind.

> [!NOTE]
> Das Konfigurieren Ihrer Azure Synapse-Integration ist ein einmaliger Vorgang, und Sie müssen dieses Notebook nur einmal für Ihren Microsoft Sentinel-Arbeitsbereich ausführen.
>

**So führen Sie das Azure Synapse – Azure ML- und Azure Synapse Analytics-Notebooks konfigurieren**:

1. Wählen Sie auf der Seite **Notebooks** von Microsoft Sentinel die Registerkarte **Vorlagen** aus und geben Sie **Synapse** in die Suchleiste ein, um das Notebook zu finden.

1. Suchen und wählen Sie das Notebook **Azure Synapse – Azure ML und Azure Synapse Analytics konfigurieren** aus, und wählen Sie dann unten rechts **Notebook-Vorlage klonen** aus.

    Ändern Sie im Bereich **Notebook klonen** bei Bedarf den Namen des Notebooks, wählen Sie Ihren [Azure Machine Learning-Arbeitsbereich](#connect-to-azure-ml-and-synapse-workspaces) und dann **Speichern** aus.

1. Nachdem Ihr Notebook bereitgestellt wurde, wählen Sie **Notebook starten**, um es zu öffnen.

    Das Notebook wird in Ihrem Azure ML-Arbeitsbereich in Microsoft Sentinel geöffnet. Weitere Informationen finden Sie unter [Starten eines Notebooks in Ihrem Azure ML-Arbeitsbereich](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace).

1. Führen Sie die Zellen in den ersten Schritten des Notebooks aus, um die erforderlichen Python-Bibliotheken und -Funktionen zu laden und sich bei Azure-Ressourcen zu authentifizieren.

1. Führen Sie die Zellen in Schritt 4, **konfigurieren des Azure Synapse Spark-Pools**, aus, um einen neuen [Azure Synapse Apache Spark-Pool](/azure/synapse-analytics/spark/apache-spark-pool-configurations) zu erstellen, der beim Ausführen Ihrer Abfragen von Datenmengen verwendet wird.

1. Führen Sie die Zellen in Schritt 5, **konfigurieren von Azure ML-Arbeitsbereich und verknüpften Diensten** aus, um sicherzustellen, dass Ihr Azure ML-Arbeitsbereich mit Ihrem Azure Synapse-Arbeitsbereich kommunizieren kann. Weitere Informationen finden Sie unter [Verknüpfen von Azure Synapse Analytics- und Azure Machine Learning-Arbeitsbereichen und Anfügen von Apache Spark-Pools](/azure/machine-learning/how-to-link-synapse-ml-workspaces).

1. Führen Sie die Zellen in Schritt 6 aus, **Daten aus Azure Log Analytics in Azure Data Lake Storage Gen2 exportieren**, um Ihre Daten, die Sie für Ihre Abfragen verwenden möchten, aus Azure Log Analytics in Azure Data Lake Storage zu exportieren.

Nachdem sich Ihre Daten im Azure Data Lake Storage befinden, können Sie Abfragen von Datenmengen mit Azure Synapse ausführen. Weitere Informationen finden Sie unter dem [Log Analytics-Datenexport in Azure Monitor](/azure/azure-monitor/logs/logs-data-export?tabs=portal).

## <a name="hunt-on-historical-data-at-scale"></a>Suche nach historischen Daten in großem Maßstab

Microsoft Sentinel bietet die integrierte **Azure Synapse – Erkennung von potentiellem Netzwerk-Beaconing mithilfe des Apache Spark-Notebooks**. Verwenden Sie dieses Notebook als Vorlage für ein reales Beispielsicherheitsszenario, um die Suche nach Datenmengen mit Microsoft Sentinel und Azure Synapse zu beginnen.

**So erkennen Sie potenzielles Netzwerk-Beaconing mit Microsoft Sentinel und Azure Synapse**:

1. Wählen Sie auf der Seite **Notebooks** von Microsoft Sentinel die Registerkarte **Vorlagen** aus und geben Sie **Synapse** in die Suchleiste ein, um das Notebook zu finden.

1. Suchen und wählen Sie das Notebook **Azure Synapse – Potentielles Netzwerk-Beaconing unter Verwendung von Apache Spark erkennen**  und wählen Sie dann unten rechts **Notebook-Vorlage klonen** aus.

    Ändern Sie im Bereich **Notebook klonen** bei Bedarf den Namen des Notebooks, wählen Sie Ihren [Azure Machine Learning-Arbeitsbereich](#connect-to-azure-ml-and-synapse-workspaces) und dann **Speichern** aus.

1. Nachdem Ihr Notebook bereitgestellt wurde, wählen Sie **Notebook starten**, um es zu öffnen.

    Das Notebook wird in Ihrem Azure ML-Arbeitsbereich aus Microsoft Sentinel heraus geöffnet. Weitere Informationen finden Sie unter [Starten eines Notebooks in Ihrem Azure ML-Arbeitsbereich](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace).

1. Führen Sie die Zellen in den ersten Schritten des Notebooks aus, um die erforderlichen Python-Bibliotheken und -Funktionen zu laden und sich bei Azure-Ressourcen zu authentifizieren.

1. Wenn Sie die Zelle mit der Bezeichnung **Spark-Sitzung starten** erreichen, führen Sie die Zelle aus, um mit der Verwendung Ihrer Azure Synapse-Sitzung zu beginnen, um Ihren Apache Spark-Pool als Compute für Ihre Datenvorbereitungs- und Data-Wrangle-Aufgaben zu verwenden, anstatt Ihren Azure ML-Compute zu nutzen.

1. Führen Sie die nachfolgenden Zellen aus, um Ihre Abfragen für die Daten zu konfigurieren und auszuführen, die jetzt in Ihrem Azure Data Lake Storage gespeichert sind. [Aktualisieren Sie beispielsweise Ihren Lookback-Zeitraum](#define-your-data-lookback-period), um Daten aus einem bestimmten Zeitraum einzubeziehen.

1. Wenn Sie mit Ihrer Abfrage fertig sind, exportieren Sie die Ergebnisse aus Azure Data Lake Storage zurück in Ihren Log Analytics-Arbeitsbereich.

    Der folgende Code, der im Schritt **Ergebnisse aus ADLS exportieren** angezeigt wird, speichert Ihre Abfrageergebnisse als einzelne JSON-Datei. Definieren Sie Ihren Verzeichnisnamen und führen Sie die Zelle aus:

    ```python
    %%synapse
    dir_name = "<dir-name>"  # specify desired directory name
    new_path = adls_path + dir_name
    csl_beacon_pd = csl_beacon_df.coalesce(1).write.format("json").save(new_path)
    ```

1. Nachdem Sie Ihre Daten exportiert haben, können Sie Ihre Spark-Sitzung beenden. Nachdem Sie Ihre Spark-Sitzung beendet haben, werden nachfolgende Abfragen mit dem standardmäßigen Azure ML-Compute ausgeführt, der im Feld **Compute** oben auf der Seite angegeben ist.

    Führen Sie die Zelle im Schritt **Spark-Sitzung beenden** aus:

    ```python
    %synapse stop
    ```

1. Exportieren Sie Ihre JSON-Datei mit Ihren Abfrageergebnissen aus Azure Data Lake Storage in ein lokales Dateisystem.

    Verwenden Sie den Code in den Schritten **Ergebnisse von ADLS in lokales Dateisystem exportieren**, **Dateien von ADLS herunterladen** und **Ergebnisse anzeigen**, um Ihre JSON-Datei lokal zu speichern und anzuzeigen.

1. Nachdem Sie Ihre Ergebnisse lokal gespeichert haben, können Sie diese mit zusätzlichen Daten anreichern und Visualisierungen ausführen. Das Notebook **Azure Synapse – Potenzielles Netzwerk-Beaconing mit Apache Spark erkennen** bietet beispielsweise zusätzliche Schritte, um die folgenden Aktionen auszuführen:

    - Ergebnisse via IP-Adressen-GeoLocation, WhoIs und anderen Threat Intelligence-Daten bereichern, um ein vollständigeres Bild des anomalen Netzwerkverhaltens zu erhalten.
    - MSTICPy-Visualisierungen ausführen, um Standorte zu kartieren, während Sie die Verteilung von Remote-Netzwerkverbindungen oder anderen Ereignissen betrachten.

    Die Ergebnisse können zur weiteren Untersuchung an Microsoft Sentinel zurückgeschrieben werden. Aus den Ergebnissen können Sie beispielsweise benutzerdefinierte Vorfälle, Beobachtungslisten oder Lesezeichen für die Suche erstellen.

    > [!TIP]
    > Verwenden Sie diese Schritte, um potenzielles Netzwerk-Beaconing zu erkennen, oder verwenden Sie sie als Vorlage und passen Sie sie an die Anforderungen Ihrer Organisation an.
    >

## <a name="manage-your-azure-synapse-session-from-microsoft-sentinel"></a>Verwalten Sie Ihre Azure Synapse-Sitzung über Microsoft Sentinel

Wenn Sie sich nicht in einer Azure Synapse-Sitzung befinden, verwendet Microsoft Sentinel standardmäßig den Azure ML-Compute, der im Feld **Compute** oben auf der Seite **Notebooks** ausgewählt ist.

Verwenden Sie den folgenden Code, den Sie von hier aus kopieren können, oder das Notebook **Azure Synapse – Potentielles Netzwerk-Beaconing mit Apache Spark erkennen**, um Ihre Azure Synapse-Sitzung zu starten und zu stoppen.

### <a name="start-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Starten Sie eine Azure Synapse-Sitzung in Microsoft Sentinel

Führen Sie den folgenden Code aus:

```python
%synapse start -w $amlworkspace -s $subscription_id -r $resource_group -c $synapse_spark_compute
```

Starten Sie alle nachfolgenden Codezellen mit `%%synapse`, um die von Ihnen gestartete Synapse-Sitzung zu verwenden.

Beispiel:

```python
%%synapse

# Primary storage info
account_name = '<storage account name>' # fill in your primary account name
container_name = '<container name>' # fill in your container name
subscription_id = '<subscription if>' # fill in your subscription id
resource_group = '<resource group>' # fill in your resource groups for ADLS
workspace_name = '<Microsoft Sentinel/log analytics workspace name>' # fill in your workspace name
device_vendor = "Fortinet"  # Replace your desired network vendor from commonsecuritylogs

# Datetime and lookback parameters
end_date = "<enter date in the format yyyy-MM-dd e.g.2021-09-17>"  # fill in your input date
lookback_days = 21 # fill in lookback days if you want to run it on historical data. make sure you have historical data available in ADLS
```

### <a name="define-your-data-lookback-period"></a>Definieren Sie Ihren Daten-Lookback-Zeitraum

Die Abfragen von Datenmengen in diesem Beispiel-Notebook können mit Daten ab einem vordefinierten Datum mit dem Parameter `end-date` oder einem längeren Zeitraum ausgeführt werden.

Beispiel:

- Wenn Sie an Daten eines bestimmten Datums interessiert sind, geben Sie den 15. November 2021 als aktuelles Datum an. Die Abfrage wird dann nur für Daten ab dem 15. November 2021 ausgeführt. 

- Um einen längeren Zeitraum für Ihre Abfrage zusätzlich zum aktuellen Datum zu definieren, definieren Sie einen Lookback-Parameter. Wenn beispielsweise der Parameter `lookback_days` auf `21` Tage und der Parameter `end_date` auf `2021-11-17` gesetzt ist, betrachtet die Abfrage die Daten für die folgenden 21 Tage ab dem 17. November 2021.

Im Notebook **Azure Synapse – Potenzielles Netzwerk-Beaconing mit Apache Spark erkennen** finden Sie diesen Code im **Datenvorbereitungsschritt**.

Beispiel:

```python
# Datetime and lookback parameters
end_date = "2021-11-17>"  # fill in your input date
lookback_days = "21" # fill in lookback days if you want to run it on historical data. Make sure you have historical data available in ADLS
```

Im obigen Beispiel werden die Abfragen für Daten zwischen dem 28. Oktober und dem 17. November 2021 ausgeführt.

### <a name="stop-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Beenden Sie eine Azure Synapse-Sitzung in Microsoft Sentinel

Führen Sie den folgenden Code aus:

```python
%synapse stop
```

### <a name="switch-azure-synapse-workspaces-in-microsoft-sentinel"></a>Wechseln von Azure Synapse-Arbeitsbereichen in Microsoft Sentinel

Verwenden Sie eine der folgenden Methoden, um einen anderen Synapse-Arbeitsbereich als den, in dem Sie derzeit angemeldet sind, zu verwalten oder auszuwählen:

- **Wenn Sie bereits einen verknüpften Dienst zwischen Ihrem Azure ML und dem neuen Azure Synapse-Arbeitsbereich erstellt haben**:

    1. Geben Sie den Namen für den Parameter `linkservice` in die folgende Codezelle ein und führen Sie die Zelle und die nachfolgenden Zellen erneut aus:

        ```python
        amlworkspace = "<aml workspace name>"  # fill in your AML workspace name
        subscription_id = "<subscription id>" # fill in your subscription id
        resource_group = '<resource group of AML workspace>' # fill in your resource groups for AML workspace
        linkedservice = '<linked service name>' # fill in your linked service created to connect to synapse workspace
        ```

    1. Stellen Sie sicher, dass Sie einen Namen des Azure Synapse Spark-Pools angeben, der registriert und an den verknüpften Dienst angehängt wurde:

        ```python
        synapse_spark_compute = "<synapse spark compute>"
        ```

- **Wenn Sie noch keinen verknüpften Dienst zwischen Ihren Azure ML- und Azure Synapse-Arbeitsbereichen haben**, stellen Sie sicher, dass Sie das Notebook **Azure Synapse – Azure ML und Azure Synapse Analytics konfigurieren** ausführen, um den verknüpften Dienst zu konfigurieren, bevor Sie das **Azure Synapse – Erkennen von potenziellem Netzwerk-Beaconing mit Apache Spark**-Notebook ausführen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

- [Aufspüren von Sicherheitsrisiken mit Jupyter Notebooks](notebooks.md)
- [Tutorial: Erste Schritte mit Jupyter-Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md)
- [Verknüpfen von Azure Synapse Analytics- und Azure Machine Learning-Arbeitsbereichen sowie Anfügen von Apache Spark-Pools (Vorschau)](/azure/machine-learning/how-to-link-synapse-ml-workspaces)
