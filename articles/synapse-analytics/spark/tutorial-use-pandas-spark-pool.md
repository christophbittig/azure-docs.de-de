---
title: 'Tutorial: Verwenden von Pandas zum Lesen/Schreiben von ADLS-Daten in einem serverlosen Apache Spark-Pool in Synapse Analytics'
description: Tutorial zur Verwendung von Pandas in einem PySpark-Notebook zum Lesen/Schreiben von ADLS-Daten in einem serverlosen Apache Spark-Pool.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4f977ad6a6cc38a269eedbe3e107b166bd74a4f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029588"
---
# <a name="tutorial-use-pandas-to-readwrite-azure-data-lake-storage-gen2-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>Tutorial: Verwenden von Pandas zum Lesen/Schreiben von Azure Data Lake Storage Gen2-Daten in einem serverlosen Apache Spark-Pool in Synapse Analytics

Erfahren Sie, wie Sie Pandas zum Lesen/Schreiben von Daten in Azure Data Lake Storage Gen2 (ADLS) mithilfe eines serverlosen Apache Spark-Pools in Azure Synapse Analytics verwenden. Die Beispiele in diesem Tutorial zeigen, wie Sie CSV-Daten mit Pandas in Synapse sowie Excel- und Parquet-Dateien lesen.

In diesem Tutorial lernen Sie Folgendes:

   > [!div class="checklist"]
   > - Lesen/Schreiben von ADLS Gen2-Daten mithilfe von Pandas in einer Spark-Sitzung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher (oder primärem Speicher) konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Ein serverloser Apache Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../get-started-analyze-spark.md).
- Konfigurieren Sie das [sekundäre Azure Data Lake Storage Gen2-Konto](../../storage/blobs/create-data-lake-storage-account.md) (kein Standardkonto im Synapse-Arbeitsbereich). Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Erstellen verknüpfter Dienste: In Azure Synapse Analytics definiert ein verknüpfter Dienst die Informationen Ihrer Verbindung mit dem Dienst. In diesem Tutorial fügen Sie einen verknüpften Azure Synapse Analytics-Dienst und einen verknüpften Azure Data Lake Storage Gen2-Dienst hinzu.

   1. Öffnen Sie Azure Synapse Studio, und wechseln Sie zur Registerkarte **Verwalten**.
   1. Wählen Sie unter **Externe Verbindungen** die Option **Verknüpfte Dienste** aus.
   1. Klicken Sie auf **Neu**, um einen verknüpften Dienst hinzuzufügen.
   1. Wählen Sie in der Liste die Kachel „Azure Data Lake Storage Gen2“ und dann **Weiter** aus.
   1. Geben Sie Ihre Anmeldeinformationen für die Authentifizierung ein. Kontoschlüssel, Dienstprinzipal (Service Principal, SP), Anmeldeinformationen und verwaltete Dienstidentität (Managed Service Identity, MSI) sind die derzeit unterstützten Authentifizierungstypen. Stellen Sie für SP und MSI sicher, dass ein *Mitwirkender an Storage-Blobdaten* im Speicher zugewiesen ist, bevor Sie einen dieser Typen für die Authentifizierung auswählen. Wählen Sie **Verbindung testen** aus, um Ihre Anmeldeinformationen zu überprüfen. Wählen Sie **Erstellen** aus.

   :::image type="content" source="media/tutorial-use-pandas-spark-pool/create-adls-linked-service.png" alt-text="Screenshot: Erstellen eines verknüpften Diensts mit einem ADLS Gen2-Speicherzugriffsschlüssel.":::


> [!NOTE]
> - Das Pandas-Feature wird in Azure Synapse Analytics in serverlosen Apache Spark-Pools mit **Python 3.8** und **Spark3** unterstützt. 
> - Unterstützung ist für die folgenden Versionen verfügbar: **pandas 1.2.3, fsspec 2021.10.0, adlfs 0.7.7**.
> - Funktionen zur Unterstützung sowohl von **Azure Data Lake Storage Gen2-URIs** (abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path) als auch von **FSSPEC-Kurz-URLs** (abfs[s]://container_name/file_path) sind erforderlich.


## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.


## <a name="readwrite-data-to-default-adls-storage-account-of-synapse-workspace"></a>Lesen/Schreiben von Daten in das ADLS-Standardspeicherkonto des Synapse-Arbeitsbereichs

Pandas kann ADLS-Daten lesen/schreiben, indem der Dateipfad direkt angegeben wird.

Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL in diesem Skript, bevor Sie es ausführen.

   ```PYSPARK
      #Read data file from URI of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://container_name/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path')
   ```

## <a name="readwrite-data-using-secondary-adls-account"></a>Lesen/Schreiben von Daten mithilfe des sekundären ADLS-Kontos

Pandas kann sekundäre ADLS-Kontodaten auf folgende Arten lesen/schreiben:
   - Unter Verwendung eines verknüpften Diensts (mit Authentifizierungsoptionen: Speicherkontoschlüssel, Dienstprinzipal, verwaltete Dienstidentität und Anmeldeinformationen)
   - Unter Verwendung von Speicheroptionen, um Client-ID und Clientgeheimnis, SAS-Schlüssel, Speicherkontoschlüssel und Verbindungszeichenfolge direkt zu übergeben

**Unter Verwendung eines verknüpften Diensts**

Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL und den Namen des verknüpften Diensts in diesem Skript, bevor Sie es ausführen.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'linked_service' : 'linked_service_name'})
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options =       {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'linked_service' :    'linked_service_name'})
   ```


**Unter Verwendung von Speicheroptionen**, um Client-ID und Clientgeheimnis, SAS-Schlüssel, Speicherkontoschlüssel und Verbindungszeichenfolge direkt zu übergeben

Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL, und die Werte für „storage_options“ in diesem Skript, bevor Sie es ausführen.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```


## <a name="example-to-readwrite-parquet-file"></a>Beispiel für das Lesen/Schreiben einer Parquet-Datei 

Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL in diesem Skript, bevor Sie es ausführen.

   ```PYSPARK
      import pandas
      
      #read parquet file
      df = pandas.read_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
      print(df)
      
      #write parquet file
      df.to_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
   ```

## <a name="example-to-readwrite-excel-file"></a>Beispiel für das Lesen/Schreiben einer Excel-Datei 

Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL in diesem Skript, bevor Sie es ausführen.

   ```PYSPARK
      import pandas
      
      #read excel file
      df = pandas.read_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   excel_file_path')
      print(df)
      
      #write excel file
      df.to_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/excel_file_path')
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](../index.yml)
