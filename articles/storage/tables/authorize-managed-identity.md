---
title: Autorisieren des Zugriffs auf Tabellendaten mit einer verwalteten Identität (Vorschau)
titleSuffix: Azure Storage
description: Mithilfe von verwalteten Identitäten für Azure-Ressourcen können Sie den Zugriff auf Tabellendaten aus Anwendungen autorisieren, die auf virtuellen Azure-Computern, in Funktions-Apps und anderen ausgeführt werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d6410115f9ba12f0beb5dc1408a076753182797
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859633"
---
# <a name="authorize-access-to-table-data-with-managed-identities-for-azure-resources-preview"></a>Autorisieren des Zugriffs auf Tabellendaten mit verwalteten Identitäten für Azure-Ressourcen (Vorschau)

Azure Table Storage unterstützt die Azure AD-Authentifizierung (Azure Active Directory) mit [verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md). Sie können verwaltete Identitäten für Azure-Ressourcen verwenden, um den Zugriff auf Tabellendaten mithilfe von Azure AD-Anmeldeinformationen aus Anwendungen zu autorisieren, die auf virtuellen Azure-Computern (Virtual Machines, VMs), in Funktions-Apps, VM-Skalierungsgruppen und anderen Diensten ausgeführt werden. Durch Verwendung von verwalteten Identitäten für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden.

In diesem Artikel wird die Autorisierung des Zugriffs auf Tabellendaten über einen virtuellen Azure-Computer mithilfe von verwalteten Identitäten für Azure-Ressourcen erläutert.

> [!IMPORTANT]
> Die Autorisierung mit Azure AD für Tabellen befindet sich derzeit in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivieren von verwalteten Identitäten auf einem virtuellen Computer

Damit Sie verwaltete Identitäten für Azure-Ressourcen zum Autorisieren des Zugriffs auf Tabellen über Ihren virtuellen Computer verwenden können, müssen Sie zuerst verwaltete Identitäten für diese Ressourcen auf dem virtuellen Computer aktivieren. Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen finden Sie in diesen Artikeln:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-Clientbibliotheken](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>Zuweisen einer RBAC-Rolle zu einer verwalteten Identität

Wenn ein Azure AD-Sicherheitsprinzipal auf Daten in einem Azure Storage-Konto zuzugreifen versucht, muss dieser Sicherheitsprinzipal über Berechtigungen für die Datenressource verfügen. Dem Sicherheitsprinzipal muss eine Azure-Rolle zugewiesen werden, die den Zugriff auf Daten in Azure Storage ermöglicht. Dabei spielt es keine Rolle, ob es sich bei dem Sicherheitsprinzipal um eine verwaltete Identität in Azure oder um ein Azure AD-Benutzerkonto handelt, mit dem Code in der Entwicklungsumgebung ausgeführt wird. Weitere Informationen zum Zuweisen von Berechtigungen für den Datenzugriff über Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](assign-azure-role-data-access.md).

> [!NOTE]
> Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder einer Tabelle zuweisen.

## <a name="use-a-managed-identity-to-create-a-table-in-net"></a>Verwenden einer verwalteten Identität zum Erstellen einer Tabelle in .NET

Die Azure Identity-Clientbibliothek vereinfacht das Abrufen eines OAuth 2.0-Zugriffstokens für die Autorisierung mit Azure Active Directory (Azure AD) über das [Azure SDK](https://github.com/Azure/azure-sdk). Wenn Sie die Azure Identity-Clientbibliothek zum Abrufen eines Zugriffstokens verwenden, können Sie das Token mithilfe desselben Codes unabhängig davon abrufen, ob Ihre Anwendung in der Entwicklungsumgebung oder in Azure ausgeführt wird. Weitere Informationen finden Sie unter [Verwenden der Azure Identity-Bibliothek zum Abrufen eines Zugriffstokens für die Autorisierung](../common/identity-library-acquire-token.md).

Erstellen Sie eine Instanz der Klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) zum Abrufen eines Tokens, das in Ihrem Code zum Autorisieren von Anforderungen für Azure Storage verwendet werden kann. Anschließend können Sie mithilfe des Tokens ein Dienstclientobjekt erstellen, das zum Ausführen von Datenvorgängen in Azure Storage autorisiert ist. Weitere Informationen zur Verwendung der Klasse **DefaultAzureCredential** zum Autorisieren einer verwalteten Identität für den Zugriff auf Azure Storage finden Sie unter [Azure Identity-Clientbibliothek für .NET](/dotnet/api/overview/azure/identity-readme).

Das folgende Codebeispiel zeigt, wie Sie die authentifizierten Tokenanmeldeinformationen abrufen, mit ihnen ein Dienstclientobjekt erstellen und anschließend unter Verwendung des Dienstclients eine Tabelle erstellen:

```csharp
public static void CreateTable(string accountName, string tableName)
{
    // Construct the table endpoint from the arguments.
    string tableEndpoint = string.Format("https://{0}.table.core.windows.net/",
                                                accountName);

    // Get a token credential and create a service client object for the table.
    TableClient tableClient = new TableClient(new Uri(tableEndpoint), 
                                                tableName, 
                                                new DefaultAzureCredential());

    try
    {
        // Create the table.
        tableClient.Create();

    }
    catch (RequestFailedException e)
    {
        Console.WriteLine("Exception: {0}", e.Message);
    }
}
```

> [!NOTE]
> Zum Autorisieren von Anforderungen für Tabellendaten mit Azure AD müssen Sie für diese Anforderungen HTTPS verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten](assign-azure-role-data-access.md)
- [Autorisieren des Tabellenzugriffs mithilfe von Azure Active Directory (Vorschauversion)](authorize-access-azure-active-directory.md)