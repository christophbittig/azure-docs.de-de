---
title: Verwalten eines Azure Data Lake Storage Gen1-Kontos mit REST
description: Mit der WebHDFS-REST-API können Sie Kontoverwaltungsvorgänge für ein Azure Data Lake Storage Gen1-Konto ausführen.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 20d54e138c9928dfb538714483c6d6f805655cbe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597415"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit der REST-API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In diesem Artikel wird beschrieben, wie Sie Kontoverwaltungsvorgänge in Azure Data Lake Storage Gen1 mit der REST-API durchführen. Zu den Kontoverwaltungsvorgängen gehören das Erstellen eines Data Lake Storage Gen1-Kontos, das Löschen eines Data Lake Storage Gen1-Kontos usw. Eine Anleitung, wie Sie Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API durchführen, finden Sie unter [Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)** . Dieser Artikel zeigt anhand von cURL, wie Sie REST-API-Aufrufe eines Data Lake Storage Gen1-Kontos ausführen.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Wie authentifiziere ich mich mithilfe von Azure Active Directory?
Zur Authentifizierung mit Azure Active Directory können Sie zwischen zwei Ansätzen wählen.

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung (interaktiv) finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung (nicht interaktiv) finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Erstellen eines Data Lake Storage Gen1-Kontos
Dieser Vorgang basiert auf dem [hier](/rest/api/datalakestore/accounts/create)definierten REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl. Ersetzen Sie **\<yourstoragegen1name>** durch Ihren Data Lake Storage Gen1-Namen.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

Ersetzen Sie \<`REDACTED`\> im oben angegebenen Befehl durch das zuvor abgerufene Autorisierungstoken. Die Anforderungsnutzlast für diesen Befehl ist in der Datei **input.json** enthalten, die für den oben genannten `-d`-Parameter bereitgestellt wird. Der Inhalt der Datei „input.json“ ähnelt dem folgenden Codeausschnitt:

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Löschen eines Data Lake Storage Gen1-Kontos
Dieser Vorgang basiert auf dem [hier](/rest/api/datalakestore/accounts/delete)definierten REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl zum Löschen eines Data Lake Storage Gen1-Kontos. Ersetzen Sie **\<yourstoragegen1name>** durch Ihren Data Lake Storage Gen1-Kontonamen.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

Die Ausgabe sollte in etwa wie im folgenden Codeausschnitt aussehen:

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>Nächste Schritte
* [Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API](data-lake-store-data-operations-rest-api.md)

## <a name="see-also"></a>Weitere Informationen
* [Azure Data Lake Storage Gen1 – REST-API-Referenz](/rest/api/datalakestore/)
* [Mit Azure Data Lake Storage Gen1 kompatible Open-Source-Big Data-Anwendungen](data-lake-store-compatible-oss-other-applications.md)