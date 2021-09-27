---
title: 'Authentifizierung: Data Lake Storage Gen1 mit Azure AD'
description: Erfahren Sie, wie Sie die Authentifizierung bei Azure Data Lake Storage Gen1 mit Azure Active Directory ausführen.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 2a83393f00234b3a8d1a2de066e41d0d6070bac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571121"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Authentifizierung bei Azure Data Lake Storage Gen1 mit Azure Active Directory

Azure Data Lake Storage Gen1 verwendet Azure Active Directory für die Authentifizierung. Vor dem Erstellen einer Anwendung, die mit Data Lake Storage Gen1 arbeitet, müssen Sie entscheiden, wie Sie Ihre Anwendung bei Azure Active Directory (Azure AD) authentifizieren möchten.

## <a name="authentication-options"></a>Authentifizierungsoptionen

* **Endbenutzerauthentifizierung**: Die Azure-Anmeldeinformationen eines Endbenutzers werden für die Authentifizierung bei Data Lake Storage Gen1 verwendet. Die Anwendung, die Sie zum Arbeiten mit Data Lake Storage Gen1 erstellen, fordert diese Benutzeranmeldeinformationen an. Folglich ist dieser Authentifizierungsmechanismus *interaktiv*, und die Anwendung wird im Kontext des angemeldeten Benutzers ausgeführt. Weitere Informationen und Anweisungen finden Sie unter [Authentifizierung von Endbenutzern bei Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Authentifizierung zwischen Diensten**: Verwenden Sie diese Option, wenn eine Anwendung sich selbst bei Data Lake Storage Gen1 authentifizieren soll. In solchen Fällen erstellen Sie eine Azure Active Directory-Anwendung (AD) und verwenden den Schlüssel aus der Azure AD-Anwendung für die Authentifizierung bei Data Lake Storage Gen1. Folglich ist dieser Authentifizierungsmechanismus *nicht interaktiv*. Weitere Informationen und Anweisungen finden Sie unter [Authentifizierung zwischen Diensten bei Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Die folgende Tabelle verdeutlicht, wie Endbenutzerauthentifizierung und Authentifizierung zwischen Diensten für Data Lake Storage Gen1 unterstützt werden. Lesen Sie die Tabelle wie im Folgenden beschrieben.

* Das Symbol ✔* gibt an, dass die Authentifizierungsoption unterstützt wird, und ist mit einem Artikel verlinkt, in dem die Verwendung der Authentifizierungsoption veranschaulicht wird. 
* Das Symbol ✔ gibt an, dass die Authentifizierungsoption unterstützt wird. 
* Leere Zellen bedeuten, dass die Authentifizierungsoption nicht unterstützt wird.


|Verwenden Sie diese Authentifizierungsoption mit...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Endbenutzer (ohne MFA\*\*)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)** (veraltet)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Endbenutzer (mit MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Zwischen Diensten (mit Clientschlüssel)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Zwischen Diensten (mit Clientzertifikat) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klicken Sie auf das Symbol <b>✔\*</b>. Es ist ein Link.</i><br>
<i>\*\* MFA steht für Multi-Factor Authentication (mehrstufige Authentifizierung)</i>

Weitere Informationen zur Authentifizierung mit Azure Active Directory finden Sie unter [Authentifizierungsszenarien für Azure AD](../active-directory/develop/authentication-vs-authorization.md).

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizierung von Endbenutzern](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Dienst-zu-Dienst-Authentifizierung](data-lake-store-service-to-service-authenticate-using-active-directory.md)