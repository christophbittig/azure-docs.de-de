---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 965a2abb8a56894efc700cea75097526daf4e544
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529970"
---
## <a name="connections"></a>Verbindungen

Die Eigenschaft `connection` ist ein Verweis auf die Umgebungskonfiguration, die angibt, wie die App eine Verbindung mit Azure Queues herstellen soll. Folgendes kann angegeben werden:

- Der Name einer Anwendungseinstellung, die eine [Verbindungszeichenfolge](#connection-string) enthält.
- Der Name eines gemeinsam genutzten Präfixes für mehrere Anwendungseinstellungen, die zusammen eine [identitätsbasierte Verbindung](#identity-based-connections) definieren.

Wenn der konfigurierte Wert sowohl eine genaue Übereinstimmung für eine einzelne Einstellung als auch eine Präfix-Übereinstimmung für andere Einstellungen ist, wird die genaue Übereinstimmung verwendet.

### <a name="connection-string"></a>Verbindungszeichenfolge

Um eine Verbindungszeichenfolge abzurufen, führen Sie die Schritte unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../articles/storage/common/storage-account-keys-manage.md) aus.

Diese Verbindungszeichenfolge sollte in einer Anwendungseinstellung mit einem Namen gespeichert werden, der mit dem von der `connection`-Eigenschaft der Bindungskonfiguration angegebenen Wert übereinstimmt.

Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.

### <a name="identity-based-connections"></a>Identitätsbasierte Verbindungen

Wenn Sie die [Erweiterungsversion 5.x oder höher](../articles/azure-functions/functions-bindings-storage-queue.md#storage-extension-5x-and-higher) verwenden, kann die App anstelle einer Verbindungszeichenfolge mit einem Geheimnis eine [Azure Active Directory-Identität](../articles/active-directory/fundamentals/active-directory-whatis.md) verwenden. Dazu definieren Sie Einstellungen unter einem gemeinsamen Präfix, das der Eigenschaft `connection` in der Trigger- und Bindungskonfiguration entspricht.

In diesem Modus erfordert die Erweiterung die folgenden Eigenschaften:

| Eigenschaft                  | Vorlage für Umgebungsvariable                       |BESCHREIBUNG                                | Beispielwert |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Warteschlangendienst-URI | `<CONNECTION_NAME_PREFIX>__queueServiceUri`<sup>1</sup>  | Dies ist der URI der Datenebene des Warteschlangendiensts, mit dem Sie mithilfe des HTTPS-Schemas eine Verbindung herstellen. | https://<Speicherkontoname>.queue.core.windows.net |

<sup>1</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` kann als Alias verwendet werden. Wenn beide Formulare bereitgestellt wurden, wird das Formular `queueServiceUri` verwendet. Das Formular `serviceUri` kann nicht eingesetzt werden, wenn die gesamte Verbindungskonfiguration über Blobs, Warteschlangen und/oder Tabellen hinweg verwendet werden soll.

Zusätzliche Eigenschaften können festgelegt werden, um die Verbindung anzupassen. Weitere Informationen finden Sie unter [Allgemeine Eigenschaften für identitätsbasierte Verbindungen](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-queue-permissions](./functions-queue-permissions.md)]