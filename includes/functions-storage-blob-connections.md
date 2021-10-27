---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 04c7d43cfe81cbbf8dcafa11182f27f37158a19c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996431"
---
## <a name="connections"></a>Verbindungen

Die `connection`-Eigenschaft ist ein Verweis auf eine Umgebungskonfiguration, die angibt, wie sich die App mit Azure Blobs verbinden soll. Sie kann angeben:

- Der Name einer Anwendungseinstellung, die einen [Verbindungsstring](#connection-string) enthält
- Der Name eines gemeinsamen Präfixes für mehrere Anwendungseinstellungen, die zusammen eine [identitätsbasierte Verbindung](#identity-based-connections) definieren.

Wenn der konfigurierte Wert sowohl eine genaue Übereinstimmung für eine einzelne Einstellung als auch eine Präfixübereinstimmung für andere Einstellungen ist, wird die genaue Übereinstimmung verwendet.

### <a name="connection-string"></a>Verbindungszeichenfolge

Um eine Verbindungszeichenfolge zu erhalten, folgen Sie den Schritten unter [Verwaltung der Zugriffsschlüssel für Speicherkonten](../articles/storage/common/storage-account-keys-manage.md). Bei der Verbindungszeichenfolge muss es sich um eine Verbindungszeichenfolge für ein allgemeines Speicherkonto (nicht für ein [Blobspeicherkonto](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)) handeln.

Diese Verbindungszeichenfolge sollte in einer Anwendungseinstellung mit einem Namen gespeichert werden, der dem in der Eigenschaft `connection` der Bindungskonfiguration angegebenen Wert entspricht.

Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.

### <a name="identity-based-connections"></a>Identitätsbasierte Verbindungen

Wenn Sie [Version 5.x oder höher der Erweiterung](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) verwenden, können Sie anstelle einer Verbindungszeichenfolge mit einem Geheimnis die Anwendung eine [Azure Active Directory-Identität](../articles/active-directory/fundamentals/active-directory-whatis.md) verwenden lassen. Dazu definieren Sie Einstellungen unter einem gemeinsamen Präfix, das der Eigenschaft `connection` in der Trigger- und Bindungskonfiguration zugeordnet ist.

In diesem Modus benötigt die Erweiterung die folgenden Eigenschaften:

| Eigenschaft                  | Vorlage für Umgebungsvariable                       | BESCHREIBUNG                                | Beispielwert |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Blob-Dienst-URI | `<CONNECTION_NAME_PREFIX>__blobServiceUri`<sup>1</sup>  | Der URI der Datenebene des Blob-Dienstes, mit dem Sie sich verbinden. | <storage_account_name>.blob.core.windows.net |

<sup>1</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` kann als Alias verwendet werden. Wenn beide Aliasnamen angegeben werden, wird die Form `blobServiceUri` verwendet. Die Form `serviceUri` kann nicht verwendet werden, wenn die gesamte Verbindungskonfiguration über Blobs, Warteschlangen und/oder Tabellen hinweg verwendet werden soll.

> [!NOTE]
> Standardmäßig verwendet der Blob-Trigger intern Azure Queues. `<CONNECTION_NAME_PREFIX>__queueServiceUri` kann auch angegeben werden, aber das Standardverhalten ohne diese Angabe ist die Verwendung der durch "AzureWebJobsStorage" definierten Verbindung. Der Trigger benötigt [Storage Queue Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) auf der Verbindung, die für diese Warteschlangen verwendet werden soll.

Zusätzliche Eigenschaften können festgelegt werden, um die Verbindung anzupassen. Siehe [Gemeinsame Eigenschaften für identitätsbasierte Verbindungen](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-blob-permissions](./functions-blob-permissions.md)]