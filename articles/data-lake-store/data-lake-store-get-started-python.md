---
title: Verwalten eines Azure Data Lake Storage Gen1-Kontos mit Python
description: Hier erfahren Sie, wie Sie das Python SDK für Azure Data Lake Storage Gen1-Kontoverwaltungsvorgänge verwenden.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.custom: devx-track-python
ms.openlocfilehash: b2941f2b81826577f0dd2e6d9138ed6bc88d3187
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580336"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Es wird beschrieben, wie Sie das Python SDK für Azure Data Lake Storage Gen1 verwenden, um grundlegende Kontoverwaltungsvorgänge durchzuführen, z.B. ein Data Lake Storage Gen1-Konto erstellen, Data Lake Storage Gen1-Konten auflisten usw. Eine Anleitung, wie Sie Dateisystemvorgänge in Data Lake Storage Gen1 mit Python durchführen, finden Sie unter [Dateisystemvorgänge in Data Lake Storage Gen1 mit Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Python**. Sie können Python [hier](https://www.python.org/downloads/) herunterladen. In diesem Artikel wird Python 3.6.2 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Eine Azure-Ressourcengruppe**. Anweisungen finden Sie unter [Verwalten von Azure-Ressourcen über das Portal](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Installieren der Module

Zum Verwenden von Data Lake Storage Gen1 mit Python müssen Sie drei Module installieren.

* Das Modul `azure-mgmt-resource`, in dem Azure-Module für Active Directory usw. enthalten sind.
* Das Modul `azure-mgmt-datalake-store`, das die Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum Verwaltungsmodul für Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* Das Modul `azure-datalake-store`, das die Dateisystemvorgänge für Azure Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum azure-datalake-store-Dateisystemmodul](/python/api/azure-datalake-store/azure.datalake.store.core/).

Verwenden Sie die folgenden Befehle, um die Module zu installieren:

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Erstellen Sie in der IDE Ihrer Wahl eine neue Python-Anwendung, z.B. **mysample.py**.

2. Fügen Sie den folgenden Codeausschnitt hinzu, um die erforderlichen Module zu importieren:

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Speichern Sie Ihre Änderungen an „mysample.py“.

## <a name="authentication"></a>Authentifizierung

In diesem Abschnitt werden die unterschiedlichen Möglichkeiten zur Authentifizierung mit Azure AD beschrieben. Die verfügbaren Optionen sind:

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über Python](data-lake-store-end-user-authenticate-python.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Erstellen eines Clients und eines Data Lake Storage Gen1-Kontos

Der folgende Codeausschnitt erstellt zunächst den Data Lake Storage Gen1-Kontoclient. Er verwendet das Clientobjekt zum Erstellen eines Data Lake Storage Gen1-Kontos. Schließlich erstellt der Codeausschnitt ein Dateisystem-Clientobjekt.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Auflisten der Data Lake Storage Gen1-Konten

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Löschen des Data Lake Storage Gen1-Kontos

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Nächste Schritte
* [Dateisystemvorgänge in Data Lake Storage Gen1 mit Python](data-lake-store-data-operations-python.md)

## <a name="see-also"></a>Weitere Informationen

* [Referenz zu azure-datalake-store Python (Dateisystem)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Mit Azure Data Lake Storage Gen1 kompatible Open-Source-Big Data-Anwendungen](data-lake-store-compatible-oss-other-applications.md)