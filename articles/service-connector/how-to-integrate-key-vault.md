---
title: Integration von Azure Key Vault mit Service Connector
description: Integrieren Sie Azure Key Vault mit Service Connector in Ihre Anwendung
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b46f9c0080fb13f178ec1dca96fb49316c2f617
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021529"
---
# <a name="integrate-azure-key-vault-with-service-connector"></a>Integration von Azure Key Vault mit Service Connector

Diese Seite zeigt die unterstützten Authentifizierungstypen und Clienttypen von Azure Key Vault mit Service Connector. Möglicherweise können Sie auch in anderen Programmiersprachen eine Verbindung zu Azure Key Vault herstellen, ohne Service Connector zu verwenden. Diese Seite zeigt auch den Namen und den Wert der Standard-Umgebungsvariablen (oder die Spring Boot-Konfiguration), die Sie erhalten, wenn Sie die Dienstverbindung erstellen. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net | | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Java | | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Java - Spring Boot | | | | ![Ja-Symbol](./media/green-check.png) |
| Node.js | | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Python | | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>Namen von Standard-Umgebungsvariablen oder Anwendungseigenschaften

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**Systemseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Ihr Azure RBAC-Bereich | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Ihr Key Vault Endpunkt | `https://{yourKeyVault}.vault.azure.net/` |

**Benutzerseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Ihr Azure RBAC-Bereich | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Ihr Key Vault Endpunkt | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | Ihre Kunden-ID | `{yourClientID}` |

**Dienstprinzipal**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Ihr Azure RBAC-Bereich | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Ihr Key Vault Endpunkt | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | Ihre Kunden-ID | `{yourClientID}` |
| AZURE_KEYVAULT_CLIENTSECRET | Ihr Kundengeheimnis | `{yourClientSecret}` |
| AZURE_KEYVAULT_TENANTID | Ihre Mieter-ID | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

**Dienstprinzipal**
| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| azure.keyvault.uri | Ihre Key Vault-Endpunkt-URL | `"https://{yourKeyVaultName}.vault.azure.net/"` |
| azure.keyvault.client-id | Ihre Kunden-ID | `{yourClientID}` |
| azure.keyvault.client-key | Ihr Kundengeheimnis | `{yourClientSecret}` |
| azure.keyvault.tenant-id |  Ihre Mieter-ID | `{yourTenantID}` |
| azure.keyvault.scope | Ihr Azure RBAC-Bereich | `https://management.azure.com/.default` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
