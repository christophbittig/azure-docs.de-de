---
title: Verwenden der Azure Identity-Bibliothek zum Abrufen eines Zugriffstokens für die Autorisierung
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie über die Azure Identity-Clientbibliothek ein Zugriffstoken abrufen, mit dem Ihre Anwendungen den Zugriff auf Daten in Azure Storage autorisieren können. Bei der Azure Identity-Bibliothek können Sie denselben Code verwenden, um das Zugriffstoken in der Entwicklungsumgebung oder in Azure abzurufen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/13/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 3de8e679cb6c99db4057ec5e5b0ae48b2b4c3500
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490286"
---
# <a name="use-the-azure-identity-library-to-get-an-access-token-for-authorization"></a>Verwenden der Azure Identity-Bibliothek zum Abrufen eines Zugriffstokens für die Autorisierung

Die Azure Identity-Clientbibliothek vereinfacht das Abrufen eines OAuth 2.0-Zugriffstokens für die Autorisierung mit Azure Active Directory (Azure AD) über das [Azure SDK](https://github.com/Azure/azure-sdk). Die neuesten Versionen der Azure Storage-Clientbibliotheken für .NET, Java, Python, JavaScript und Go sind in die Azure Identity-Bibliotheken für jede dieser Sprachen integriert und bieten so eine einfache, sichere Möglichkeit zum Abrufen eines Zugriffstokens für die Autorisierung von Azure Storage-Anforderungen.

Ein Vorteil der Azure Identity-Clientbibliothek besteht darin, dass Sie das Zugriffstoken mit demselben Code abrufen können, um zu erfahren, ob Ihre Anwendung in der Entwicklungsumgebung oder in Azure ausgeführt wird. Die Azure Identity-Clientbibliothek gibt ein Zugriffstoken für einen Sicherheitsprinzipal zurück. Wenn Ihr Code in Azure ausgeführt wird, kann der Sicherheitsprinzipal eine verwaltete Identität für Azure-Ressourcen, ein Dienstprinzipal oder aber ein Benutzer oder eine Gruppe sein. In der Entwicklungsumgebung stellt die Clientbibliothek ein Zugriffstoken für einen Benutzer oder einen Dienstprinzipal zu Testzwecken zur Verfügung.

Das von der Azure Identity-Clientbibliothek zurückgegebene Zugriffstoken ist in Token-Anmeldeinformationen gekapselt. Sie können dann mithilfe der Token-Anmeldeinformationen ein Dienstclientobjekt abrufen, das zum Ausführen autorisierter Vorgänge für Azure Storage verwendet wird. Eine einfache Möglichkeit zum Abrufen des Zugriffstokens und der Token-Anmeldeinformationen ist die Verwendung der Klasse **DefaultAzureCredential**, die von der Azure Identity-Clientbibliothek bereitgestellt wird. Eine Instanz dieser Klasse versucht, die Token-Anmeldeinformationen auf verschiedene allgemeine Arten abzurufen. Sie funktioniert sowohl in der Entwicklungsumgebung als auch in Azure.

Weitere Informationen zur Azure Identity-Clientbibliothek für Ihre Sprache finden Sie in einem der folgenden Artikel:

- [Azure Identity-Clientbibliothek für .NET](/dotnet/api/overview/azure/identity-readme)
- [Azure Identity-Clientbibliothek für Java](/java/api/overview/azure/identity-readme)
- [Azure Identity-Clientbibliothek für Python](/python/api/overview/azure/identity-readme)
- [Azure Identity-Clientbibliothek für JavaScript](/javascript/api/overview/azure/identity-readme)
- [Azure Identity-Clientbibliothek für Go](/javascript/api/overview/azure/identity-readme)

## <a name="assign-azure-roles-for-access-to-data"></a>Zuweisen von Azure-Rollen für den Datenzugriff

Wenn ein Azure AD-Sicherheitsprinzipal auf Daten in einem Azure Storage-Konto zuzugreifen versucht, muss dieser Sicherheitsprinzipal über Berechtigungen für die Datenressource verfügen. Dem Sicherheitsprinzipal muss eine Azure-Rolle zugewiesen werden, die den Zugriff auf Daten in Azure Storage ermöglicht. Dabei spielt es keine Rolle, ob es sich bei dem Sicherheitsprinzipal um eine verwaltete Identität in Azure oder um ein Azure AD-Benutzerkonto handelt, mit dem Code in der Entwicklungsumgebung ausgeführt wird. Informationen zum Zuweisen von Berechtigungen für den Datenzugriff über Azure RBAC finden Sie in einem der folgenden Artikel:

- [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](../blobs/assign-azure-role-data-access.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](../queues/assign-azure-role-data-access.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten (Vorschau)](../tables/assign-azure-role-data-access.md)

> [!NOTE]
> Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Die können sie dann auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder aber eines Containers, einer Warteschlange oder Tabelle zuordnen.

## <a name="authenticate-the-user-in-the-development-environment"></a>Authentifizieren des Benutzers in der Entwicklungsumgebung

Wenn Ihr Code in der Entwicklungsumgebung ausgeführt wird, erfolgt die Authentifizierung ggf. automatisch. Abhängig von den verwendeten Tools kann aber auch eine Browseranmeldung erforderlich sein. Microsoft Visual Studio und Microsoft Visual Studio Code unterstützen beispielsweise einmaliges Anmelden (SSO), sodass das aktive Azure AD-Benutzerkonto automatisch für die Authentifizierung verwendet wird. Weitere Informationen zu SSO finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../../active-directory/manage-apps/what-is-single-sign-on.md).

Sie können auch einen Dienstprinzipal erstellen und Umgebungsvariablen festlegen, die von der Entwicklungsumgebung zur Laufzeit gelesen werden können.

## <a name="authenticate-a-service-principal-in-the-development-environment"></a>Authentifizieren des Dienstprinzipals in der Entwicklungsumgebung

Wenn Ihre Entwicklungsumgebung kein einmaliges Anmelden bzw. keine Anmeldung über einen Webbrowser unterstützt, können Sie für die Authentifizierung aus der Entwicklungsumgebung einen Dienstprinzipal verwenden. Nachdem Sie den Dienstprinzipal erstellt haben, fügen Sie die dafür zurückgegebenen Werte zu Umgebungsvariablen hinzu.

### <a name="create-the-service-principal"></a>Erstellen des Dienstprinzipals

Wenn Sie einen Dienstprinzipal über die Azure-Befehlszeilenschnittstelle erstellen und eine Azure-Rolle zuweisen möchten, rufen Sie den Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) auf. Geben Sie eine Azure Storage-Datenzugriffsrolle an, die dem neuen Dienstprinzipal zugewiesen werden soll. Geben Sie außerdem den Bereich für die Rollenzuweisung an. Weitere Informationen zu den für Azure Storage bereitgestellten integrierten Rollen finden Sie unter [In Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md).

Wenn Sie nicht über ausreichende Berechtigungen zum Zuweisen einer Rolle zum Dienstprinzipal verfügen, müssen Sie möglicherweise den Kontobesitzer oder den Administrator bitten, die Rollenzuweisung vorzunehmen.

Im folgenden Beispiel wird ein neuer Dienstprinzipal über die Azure-Befehlszeilenschnittstelle erstellt und ihm im Speicherkontobereich die Rolle **Mitwirkender an Speicherblobdaten** zugewiesen:

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
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

Weitere Informationen zum Erstellen eines Dienstprinzipals finden Sie in einem der folgenden Artikel:

- [Erstellen Sie eine Azure AD-App und Dienstprinzipal im Portal](../../active-directory/develop/howto-create-service-principal-portal.md)
- [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann mehrere Minuten dauern.

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Über die Azure Identity-Clientbibliothek werden zur Laufzeit Werte aus drei Umgebungsvariablen gelesen, um den Dienstprinzipal zu authentifizieren. In der folgenden Tabelle sind die für die einzelnen Umgebungsvariablen festzulegenden Werte beschrieben.

|Umgebungsvariable|value
|-|-
|`AZURE_CLIENT_ID`|Die App-ID für den Dienstprinzipal
|`AZURE_TENANT_ID`|Die Azure AD-Mandanten-ID des Dienstprinzipals
|`AZURE_CLIENT_SECRET`|Das für den Dienstprinzipal generierte Kennwort

> [!IMPORTANT]
> Nachdem Sie die Umgebungsvariablen festgelegt haben, schließen Sie das Konsolenfenster, und öffnen Sie es dann erneut. Wenn Sie Visual Studio oder eine andere Entwicklungsumgebung verwenden, müssen Sie möglicherweise die Entwicklungsumgebung neu starten, damit die neuen Umgebungsvariablen registriert werden.

Weitere Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="get-an-access-token-for-authorization"></a>Abrufen eines Zugriffstokens für die Autorisierung

Die Azure Identity-Clientbibliothek stellt Klassen bereit, mit deren Hilfe Sie ein Zugriffstoken zum Autorisieren von Anforderungen bei Azure AD abrufen können. Die Azure Storage-Clientbibliotheken enthalten Konstruktoren für Dienstclientobjekte, die Token-Anmeldeinformationen als Parameter verwenden. Sie können beide zusammen verwenden, um Azure Storage-Anforderungen mit Azure AD-Anmeldeinformationen auf einfache Weise zu autorisieren.

Die Verwendung der Klasse **DefaultAzureCredential-Klasse** wird für die meisten einfachen Szenarien empfohlen, bei denen Ihre Anwendung sowohl in der Entwicklungsumgebung als auch in Azure ein Zugriffstoken abrufen muss. Viele verschiedene Typen von Tokenanmeldeinformationen stehen auch für andere Szenarien zur Verfügung.

Das folgende Beispiel zeigt, wie Sie mithilfe von „DefaultAzureCredential“ ein Token in .NET abrufen. Die Anwendung erstellt dann mithilfe des Tokens einen neuen Dienstclient, der anschließend zum Erstellen eines Blobcontainers verwendet wird. Obwohl in diesem Beispiel .NET und der Blob Storage-Dienst verwendet werden, verhält sich die Klasse **DefaultAzureCredential** ähnlich wie andere Sprachen und andere Azure-Dienste.

```csharp
static void CreateBlobContainer(string accountName, string containerName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a token credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                  new DefaultAzureCredential());

    // Create the container if it does not exist.
    containerClient.CreateIfNotExists();
}
```  

Weitere Informationen zur Verwendung der Klasse „DefaultAzureCredential“ zum Autorisieren einer verwalteten Identität für den Zugriff auf Azure Storage finden Sie unter [Azure Identity-Clientbibliothek für .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="see-also"></a>Siehe auch

- [Apps und Dienstprinzipale in Azure AD](../../active-directory/develop/app-objects-and-service-principals.md)
- [Microsoft Identity Plattform: Authentifizierungsbibliotheken](../../active-directory/develop/reference-v2-libraries.md)