---
title: 'Tutorial: Verwenden von FSSPEC zum Lesen/Schreiben von ADLS-Daten in einem serverlosen Apache Spark-Pool in Synapse Analytics'
description: Tutorial zur Verwendung von FSSPEC in einem PySpark-Notebook zum Lesen/Schreiben von ADLS-Daten in einem serverlosen Apache Spark-Pool
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 742aeca5df76558b6dcd81ceb184029f807a1a65
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029593"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>Tutorial: Verwenden von FSSPEC zum Lesen/Schreiben von ADLS-Daten in einem serverlosen Apache Spark-Pool in Synapse Analytics

Hier erfahren Sie, wie Sie die Dateisystemspezifikation (Filesystem Spec, FSSPEC) zum Lesen/Schreiben von Daten in Azure Data Lake Storage (ADLS) mithilfe eines verknüpften Diensts in einem serverlosen Apache Spark-Pool in Azure Synapse Analytics verwenden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Lesen/Schreiben von ADLS-Daten in einer dedizierten Spark-Sitzung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../get-started-analyze-spark.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

In Azure Synapse Analytics definieren Sie in einem verknüpften Dienst Ihre Verbindungsinformationen für andere Dienste. In diesem Abschnitt fügen Sie Azure Synapse Analytics und Azure Data Lake Storage Gen 2 als verknüpfte Dienste hinzu.

1. Öffnen Sie Azure Synapse Studio, und wechseln Sie zur Registerkarte **Verwalten**.
1. Wählen Sie unter **Externe Verbindungen** die Option **Verknüpfte Dienste** aus.
1. Klicken Sie auf **Neu**, um einen verknüpften Dienst hinzuzufügen.
1. Wählen Sie in der Liste die Kachel „Azure Data Lake Storage Gen2“ und dann **Weiter** aus.
1. Geben Sie Ihre Anmeldeinformationen für die Authentifizierung ein. Der Kontoschlüssel wird derzeit als Authentifizierungstyp unterstützt. Klicken Sie auf **Verbindung testen**, um Ihre Anmeldeinformationen zu überprüfen. Klicken Sie auf **Erstellen**.

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="Screenshot: Erstellen eines verknüpften Diensts mit einem ADLS Gen2-Speicherzugriffsschlüssel":::


## <a name="readwrite-data-using-storage-account-name-and-key"></a>Lesen/Schreiben von Daten mithilfe des Speicherkontonamens und -schlüssels

FSSPEC kann ADLS-Daten lesen/schreiben, indem der Speicherkontoname und der Schlüssel direkt angegeben werden.

1. Öffnen Sie in Synapse Studio **Data** > **Linked** > **Azure Data Lake Storage Gen2** (Daten > Verknüpft > Azure Data Lake Storage Gen2). Laden Sie Daten in das Standardspeicherkonto hoch.

1. Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL, den ADLS Gen2-Speichernamen und den -schlüssel in diesem Skript, bevor Sie sie ausführen.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name
   adls_account_key = '' #Provide exact ADLS account key

   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key, mode="wt")
   
   with fsspec_handle.open() as f:
    data.to_csv(f)
   ```

## <a name="readwrite-data-using-linked-service"></a>Lesen/Schreiben von Daten mithilfe des verknüpften Diensts

FSSPEC kann ADLS-Daten lesen/schreiben, indem der Name des verknüpften Diensts angegeben wird.


1. Öffnen Sie in Synapse Studio **Data** > **Linked** > **Azure Data Lake Storage Gen2** (Daten > Verknüpft > Azure Data Lake Storage Gen2). Laden Sie Daten in das Standardspeicherkonto hoch.

1. Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL, den Namen des verknüpften Diensts und den ADLS Gen2-Speichernamen in diesem Skript, bevor Sie sie ausführen.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>)
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="upload-file-from-local-file-system-to-default-adls-storage-account-of-synapse-workspace"></a>Hochladen einer Datei aus dem lokalen Dateisystem in ein ADLS-Standardspeicherkonto des Synapse-Arbeitsbereichs

FSSPEC kann eine Datei aus dem lokalen Dateisystem in ein ADLS-Standardspeicherkonto für den Synapse-Arbeitsbereich hochladen.


Führen Sie den folgenden Code aus.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL in diesem Skript, bevor Sie es ausführen.

   ```PYSPARK
   # Import libraries
   import fsspec
   import os
   
   # Set variables
   local_file_name = "<local_file_name>"
   ADLS_Store_Path = "abfs[s]://<filesystemname>@<account name>.dfs.windows.cor.net/"+local_file_name
   
   # Generate local file for testing 
   with open(local_file_name, mode='w') as f:
       for i in range(1000):
           f.write("Testing local file functionality\n")
   print("Created: " + local_file_name)

   # Upload local file to ADLS 
   fs = fsspec.filesystem('abfs[s]')
   fs.upload(local_file_name, ADLS_Store_Path)
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Pandas in Synapse Analytics mit serverlosem Apache Spark-Pool](tutorial-use-pandas-spark-pool.md)
- [Azure Synapse Analytics](../index.yml)
- [Offiziellen FSSPEC-Dokumentationsseite](https://filesystem-spec.readthedocs.io/en/latest/)
