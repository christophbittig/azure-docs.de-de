---
title: 'Python: Dienst-zu-Dienst-Authentifizierung – Data Lake Storage Gen1'
description: Hier erfahren Sie, wie Sie mithilfe von Azure Active Directory und Python die Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 implementieren.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: 5c4038d1a108df7423986aa47df79833e4f7bd92
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624328"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mithilfe von Python
> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Verwenden des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

In diesem Artikel erfahren Sie, wie Sie mithilfe des Python SDK die Authentifizierung zwischen Diensten bei Azure Data Lake Storage Gen1 durchführen. Informationen zur Authentifizierung von Endbenutzern bei Data Lake Storage Gen1 mithilfe von Python finden Sie unter [Authentifizieren von Endbenutzern bei Data Lake Storage Gen1 mit Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Voraussetzungen

* **Python**. Sie können Python [hier](https://www.python.org/downloads/) herunterladen. In diesem Artikel wird Python 3.6.2 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer Azure Active Directory-Webanwendung.** Sie müssen die Schritte unter [Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) ausgeführt haben.

## <a name="install-the-modules"></a>Installieren der Module

Zum Verwenden von Data Lake Storage Gen1 mit Python müssen Sie drei Module installieren.

* Das Modul `azure-mgmt-resource`, in dem Azure-Module für Active Directory usw. enthalten sind.
* Das Modul `azure-mgmt-datalake-store`, das die Kontoverwaltungsvorgänge für Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum Verwaltungsmodul für Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* Das Modul `azure-datalake-store`, das die Dateisystemvorgänge für Data Lake Storage Gen1 enthält. Weitere Informationen zu diesem Modul finden Sie in der [Referenz zum azure-datalake-store-Dateisystemmodul](/python/api/azure-datalake-store/azure.datalake.store.core/).

Verwenden Sie die folgenden Befehle, um die Module zu installieren:

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Erstellen Sie in der IDE Ihrer Wahl eine neue Python-Anwendung, z.B. **mysample.py**.

2. Fügen Sie den folgenden Codeausschnitt hinzu, um die erforderlichen Module zu importieren:

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Speichern Sie Ihre Änderungen an „mysample.py“.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Dienst-zu-Dienst-Authentifizierung mit Clientgeheimnis für die Kontoverwaltung

Verwenden Sie diesen Codeausschnitt zur Authentifizierung bei Azure AD für Kontoverwaltungsvorgänge bei Data Lake Storage Gen1, z.B. zum Erstellen oder Löschen eines Data Lake Storage Gen1-Kontos. Mit dem folgenden Codeausschnitt können Sie unter Verwendung des Clientgeheimnisses für eine Anwendung bzw. einen Dienstprinzipal einer bestehenden Azure AD-„Web-App“-Anwendung eine nicht interaktive Authentifizierung Ihrer Anwendung durchführen.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Dienst-zu-Dienst-Authentifizierung mit Clientgeheimnis für Dateisystemvorgänge

Verwenden Sie den folgenden Codeausschnitt für die Authentifizierung mit Azure AD für Dateisystemvorgänge in Data Lake Storage Gen1 wie Erstellen von Ordnern, Hochladen von Dateien usw. Mit dem folgenden Codeausschnitt können Sie unter Verwendung des Clientgeheimnisses für eine Anwendung bzw. einen Dienstprinzipal eine nicht interaktive Authentifizierung Ihrer Anwendung durchführen. Verwenden Sie diese Option mit einer vorhandenen Azure AD-Anwendung vom Typ „Web-App“.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung zwischen Diensten verwenden, um sich mit Python bei Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie Python mit Data Lake Storage Gen1 verwenden.

* [Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit Python](data-lake-store-get-started-python.md)
* [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit Python](data-lake-store-data-operations-python.md)