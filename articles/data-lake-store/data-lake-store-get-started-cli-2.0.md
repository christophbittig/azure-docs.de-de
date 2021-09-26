---
title: Verwalten eines Azure Data Lake Storage Gen1-Kontos – Azure CLI
description: Verwenden Sie die Azure CLI zum Erstellen eines Data Lake Storage Gen1-Kontos und Ausführen grundlegender Vorgänge.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: normesta
ms.openlocfilehash: 8f94506804f5edc4a728f06234f996ad30509eb6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621752"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Erste Schritte mit Azure Data Lake Storage Gen1 mithilfe der Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)
>
> 

Erfahren Sie, wie Sie mit der Azure CLI ein Azure Data Lake Storage Gen1-Konto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen zu Data Lake Storage Gen1 finden Sie unter [Übersicht über Data Lake Storage Gen1](data-lake-store-overview.md).

Die Azure CLI ist die Befehlszeilenumgebung von Azure und dient zum Verwalten von Azure-Ressourcen. Sie kann unter macOS, Linux und Windows verwendet werden. Weitere Informationen finden Sie in der [Übersicht über Azure CLI](/cli/azure). In der [Referenz für Azure Data Lake Storage Gen1 CLI](/cli/azure/dls) finden Sie außerdem eine vollständige Liste mit Befehlen und Syntax.


## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI**: Entsprechende Anweisungen finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="authentication"></a>Authentifizierung

In diesem Artikel wird ein einfacheres Authentifizierungskonzept mit Data Lake Storage Gen1 verwendet, bei dem Sie sich als Endbenutzer anmelden. Die Zugriffsebene für das Data Lake Storage Gen1-Konto und das Dateisystem hängt dann von der Zugriffsebene des angemeldeten Benutzers ab. Für die Authentifizierung mit Data Lake Storage Gen1 stehen mit **Endbenutzerauthentifizierung** und **Dienst-zu-Dienst-Authentifizierung** aber auch noch andere Konzepte zur Verfügung. Anweisungen und weitere Informationen zur Authentifizierung finden Sie unter [Endbenutzerauthentifizierung](data-lake-store-end-user-authenticate-using-active-directory.md) oder [Dienst-zu-Dienst-Authentifizierung](./data-lake-store-service-to-service-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

    ```azurecli
    az login
    ```

    Sie erhalten einen Code, den Sie im nächsten Schritt benötigen. Verwenden Sie zum Öffnen der Seite https://aka.ms/devicelogin einen Webbrowser, und geben Sie dann zur Authentifizierung den Code ein. Sie werden aufgefordert, sich mit Ihren Anmeldeinformationen anzumelden.

2. Nach der Anmeldung werden in dem Fenster alle mit Ihrem Konto verknüpften Azure-Abonnements aufgelistet. Verwenden Sie den folgenden Befehl, um ein bestimmtes Abonnement zu verwenden:
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Erstellen eines Azure Data Lake Storage Gen1-Kontos

1. Erstellen Sie eine neue Ressourcengruppe. Geben Sie für den folgenden Befehl die Parameterwerte ein, die Sie verwenden möchten. Wenn der Name des Standorts Leerzeichen enthält, setzen Sie ihn in doppelte Anführungszeichen. Beispiel: „USA, Osten 2“. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Erstellen Sie das Data Lake Storage Gen1-Konto.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto

Sie können in Ihrem Azure Data Lake Storage Gen1-Konto Ordner zum Verwalten und Speichern von Daten erstellen. Verwenden Sie den folgenden Befehl, um im Stammverzeichnis des Data Lake Storage Gen1-Kontos einen Ordner mit dem Namen **mynewfolder** zu erstellen.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Der Parameter `--folder` stellt sicher, dass der Befehl einen Ordner erstellt. Ohne diesen Parameter erstellt der Befehl eine leere Datei namens „mynewfolder“ im Stammverzeichnis des Data Lake Storage Gen1-Kontos.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Hochladen von Daten in ein Data Lake Storage Gen1-Konto

Sie können Daten direkt auf die Stammebene von Data Lake Storage Gen1 oder in einen im Konto erstellten Ordner hochladen. Die folgenden Codeausschnitte veranschaulichen das Hochladen von Beispieldaten in den im vorigen Abschnitt erstellten Ordner (**mynewfolder**).

Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)herunterladen. Laden Sie die Datei herunter, und speichern Sie sie in einem lokalen Verzeichnis auf dem Computer, z.B. „C:\sampledata\“.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Als Ziel müssen Sie den vollständigen Pfad einschließlich des Dateinamens angeben.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Auflisten von Dateien in einem Data Lake Storage Gen1-Konto

Verwenden Sie den folgenden Befehl, um die Dateien in einem Data Lake Storage Gen1-Konto aufzulisten.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

Die Ausgabe sollte in etwa wie folgt aussehen:

```output
[
    {
        "accessTime": 1491323529542,
        "aclBit": false,
        "blockSize": 268435456,
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "length": 1589881,
        "modificationTime": 1491323531638,
        "msExpirationTime": 0,
        "name": "mynewfolder/vehicle1_09142014.csv",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "pathSuffix": "vehicle1_09142014.csv",
        "permission": "770",
        "replication": 1,
        "type": "FILE"
    }
]
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Umbenennen, Herunterladen und Löschen von Daten in einem Data Lake Storage Gen1-Konto 

* Verwenden Sie zum **Umbenennen einer Datei** den folgenden Befehl:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Verwenden Sie zum Herunterladen einer Datei** den folgenden Befehl: Stellen Sie sicher, dass der angegebene Zielpfad bereits vorhanden ist.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Falls der Zielordner nicht vorhanden ist, wird er erstellt.
    > 
    >

* Verwenden Sie zum **Löschen einer Datei** den folgenden Befehl:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Wenn Sie den Ordner **mynewfolder** und die Datei **vehicle1_09142014_copy.csv** mit einem einzelnen Befehl löschen möchten, verwenden Sie den Parameter „--recurse“.

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Arbeiten mit Berechtigungen und ACLs für ein Data Lake Storage Gen1-Konto

In diesem Abschnitt erfahren Sie, wie Sie ACLs und Berechtigungen mithilfe der Azure CLI verwalten. Ausführliche Informationen zur Implementierung von ACLs in Azure Data Lake Storage Gen1 finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* Befehl zum **Aktualisieren des Besitzers einer Datei bzw. eines Ordners**:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* Befehl zum **Aktualisieren der Berechtigungen für eine Datei bzw. einen Ordner**:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* Befehl zum **Abrufen der ACLs für einen bestimmten Pfad**:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    Die Ausgabe sollte ähnlich der Folgenden aussehen:

    ```output
    {
        "entries": [
        "user::rwx",
        "group::rwx",
        "other::---"
        ],
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "permission": "770",
        "stickyBit": false
    }
    ```

* Befehl zum **Festlegen eines Eintrags für eine ACL**:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* Befehl zum **Entfernen eines Eintrags für eine ACL**:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* Befehl zum **Entfernen einer vollständigen Standard-ACL**:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* Befehl zum **Entfernen einer vollständigen nicht standardmäßigen ACL**:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Löschen eines Data Lake Storage Gen1-Kontos
Verwenden Sie den folgenden Befehl zum Löschen eines Data Lake Storage Gen1-Kontos.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Geben Sie nach entsprechender Aufforderung **Y** ein, um das Konto zu löschen.

## <a name="next-steps"></a>Nächste Schritte
* [Verwenden von Azure Data Lake Storage Gen1 für Big Data-Anforderungen](data-lake-store-data-scenarios.md) 
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Erstellen von HDInsight-Clustern mithilfe von Azure Data Lake Storage Gen1 im Azure-Portal](data-lake-store-hdinsight-hadoop-use-portal.md)