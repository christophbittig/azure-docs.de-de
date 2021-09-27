---
title: Erstellen eines Azure Active Directory-Dienstprinzipals über die Azure CLI
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung erstellen wir eine Anwendung und einen Dienstprinzipal für die Authentifizierung bei Azure Communication Services.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.subservice: identity
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: c2b8dd4a40205178b3656477e89189d6f0f0bfeb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635525"
---
# <a name="quickstart-authenticate-using-azure-active-directory-azure-cli"></a>Schnellstart: Authentifizieren mittels Azure Active Directory (Azure CLI)

Mit dem Azure Identity SDK wird die Azure AD-Tokenauthentifizierung (Azure Active Directory) für Azure SDK-Pakete unterstützt. Die neuesten Versionen der Azure Communication Services-SDKs für .NET, Java, Python und JavaScript sind in die Azure-Identitätsbibliothek integriert und stellen so eine einfache und sichere Möglichkeit dar, ein OAuth 2.0-Token für die Autorisierung von Azure Communication Services-Anforderungen abzurufen.

Ein Vorteil der Azure Identity SDKs besteht darin, dass Sie den gleichen Code verwenden können, um in mehreren Diensten zu authentifizieren, ob Ihre Anwendung in der Entwicklungsumgebung oder in Azure ausgeführt wird. 

Das Azure Identity SDK kann mittels vieler Methoden authentifizieren. In der Entwicklung verwenden wir einen Dienstprinzipal, der an eine registrierte Anwendung gebunden ist, mit Anmeldeinformationen, die in Umgebungsvariablen gespeichert sind, was sich für Tests und Entwicklung eignet.

## <a name="prerequisites"></a>Voraussetzungen

 - Azure-Befehlszeilenschnittstelle. [Installationshandbuch](/cli/azure/install-azure-cli)
 - Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Einrichten

Wenn Sie Active Directory für andere Azure-Ressourcen verwenden, sollten Sie verwaltete Identitäten verwenden. Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen finden Sie in diesen Artikeln:

- [Azure portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-SDKs](../../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Authentifizieren einer registrierten Anwendung in der Entwicklungsumgebung

Wenn Ihre Entwicklungsumgebung kein einmaliges Anmelden bzw. keine Anmeldung über einen Webbrowser unterstützt, können Sie für die Authentifizierung aus der Entwicklungsumgebung eine registrierte Anwendung verwenden.

### <a name="creating-an-azure-active-directory-registered-application"></a>Erstellen einer registrierten Azure Active Directory-Anwendung

Zum Erstellen einer registrierten Anwendung über die Azure CLI müssen Sie bei dem Azure-Konto angemeldet sein, in dem die Vorgänge ausgeführt werden sollen. Dazu können Sie den Befehl `az login` verwenden und Ihre Anmeldeinformationen im Browser eingeben. Nachdem Sie sich über die CLI bei Ihrem Azure-Konto angemeldet haben, können Sie den Befehl `az ad sp create-for-rbac` zum Erstellen der registrierten Anwendung und des Dienstprinzipals aufrufen.

In den folgenden Beispielen wird die Azure CLI verwendet, um eine neue registrierte Anwendung zu erstellen.

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

Mit dem Befehl `az ad sp create-for-rbac` wird eine Liste der Dienstprinzipaleigenschaften im JSON-Format zurückgegeben. Kopieren Sie diese Werte, damit Sie sie zum Erstellen der erforderlichen Umgebungsvariablen im nächsten Schritt verwenden können.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Die Azure-Rollenzuweisungen können einige Minuten dauern.

#### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Über das Azure Identity SDK werden zur Laufzeit Werte aus drei Umgebungsvariablen gelesen, um die Anwendung zu authentifizieren. In der folgenden Tabelle sind die für die einzelnen Umgebungsvariablen festzulegenden Werte beschrieben.

| Umgebungsvariable  | value                                    |
| --------------------- | ---------------------------------------- |
| `AZURE_CLIENT_ID`     | Wert `appId` aus dem generierten JSON-Code    |
| `AZURE_TENANT_ID`     | Wert `tenant` aus dem generierten JSON-Code   |
| `AZURE_CLIENT_SECRET` | Wert `password` aus dem generierten JSON-Code |

> [!IMPORTANT]
> Nachdem Sie die Umgebungsvariablen festgelegt haben, schließen Sie das Konsolenfenster, und öffnen Sie es dann erneut. Wenn Sie Visual Studio oder eine andere Entwicklungsumgebung verwenden, müssen Sie möglicherweise einen Neustart vornehmen, damit die neuen Umgebungsvariablen registriert werden.

Nachdem diese Variablen festgelegt wurden, können Sie das Objekt „DefaultAzureCredential“ in Ihrem Code verwenden, um sich beim Dienstclient Ihrer Wahl zu authentifizieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Authentifizierung](../../concepts/authentication.md)

Das könnte Sie auch interessieren:

- [Weitere Informationen zur Azure-Identitätsbibliothek](/dotnet/api/overview/azure/identity-readme)
