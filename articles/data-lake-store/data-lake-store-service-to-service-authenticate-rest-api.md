---
title: REST – Dienst-zu-Dienst-Authentifizierung – Data Lake Storage Gen1 – Azure
description: Hier erfahren Sie, wie Sie mithilfe der REST-API Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 und Azure Active Directory durchführen.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: d125e1c92ae64749c28703430fa62f63efd05a93
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649417"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mithilfe der REST-API
> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Verwenden des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

In diesem Artikel erfahren Sie, wie Sie mithilfe der REST-API die Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 durchführen. Informationen zur Authentifizierung von Endbenutzern bei Data Lake Storage Gen1 mithilfe der REST-API finden Sie unter [Authentifizieren von Endbenutzern bei Azure Data Lake Storage Gen1 mit der REST-API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer Azure Active Directory-Webanwendung.** Sie müssen die Schritte unter [Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) ausgeführt haben.

## <a name="service-to-service-authentication"></a>Dienst-zu-Dienst-Authentifizierung

In diesem Szenario stellt die Anwendung eigene Anmeldeinformationen zum Durchführen der Vorgänge bereit. Hierzu müssen Sie eine POST-Anforderung wie im folgenden Codeausschnitt gezeigt ausgeben:

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

Die Ausgabe dieser Anforderung enthält ein Autorisierungstoken (gekennzeichnet durch `access-token` in der folgenden Ausgabe), das Sie anschließend mit Ihren REST-API-Aufrufen übergeben. Speichern Sie das Authentifizierungstoken in einer Textdatei. Sie benötigen es, wenn Sie REST-Aufrufe an Data Lake Storage Gen1 richten.

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

In diesem Artikel wird der **nicht interaktive** Ansatz verwendet. Weitere Informationen zur nicht interaktiven Authentifizierung (Dienst-zu-Dienst-Aufrufe) finden Sie unter [Aufrufe zwischen Diensten mithilfe von Clientanmeldeinformationen](/previous-versions/azure/dn645543(v=azure.100)).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung zwischen Diensten verwenden, um sich mit der REST-API bei Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie die REST-API mit Data Lake Storage Gen1 verwenden.

* [Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit der REST-API](data-lake-store-get-started-rest-api.md)
* [Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API](data-lake-store-data-operations-rest-api.md)