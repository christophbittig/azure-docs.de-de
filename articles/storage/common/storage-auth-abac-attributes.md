---
title: Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)
titleSuffix: Azure Storage
description: Unterstützte Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen und die attributbasierte Zugriffssteuerung von Azure (Azure Attribute-Based Access Control, Azure ABAC) in Azure Storage.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 791e533cc1f0dafa8724faa8cc3a46d1f8c770d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666001"
---
# <a name="actions-and-attributes-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)

> [!IMPORTANT]
> Azure RBAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel werden die unterstützten Attributverzeichnisse beschrieben, die in Bedingungen für Azure-Rollenzuweisungen bei jeder Azure Storage-[DataAction](../../role-based-access-control/role-definitions.md#dataactions) verwendet werden können. Eine Liste der Blob-Dienstvorgänge, die von einer bestimmten Berechtigung oder „DataAction“ betroffen sind, finden Sie unter [Berechtigungen für Blob-Dienstvorgänge](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-blob-service-operations).

Informationen zum Format der Rollenzuweisungsbedingung finden Sie unter [Format und Syntax von Azure-Rollenzuweisungsbedingungen](../../role-based-access-control/conditions-format.md).

## <a name="suboperations"></a>Untervorgänge

Mehrere Speicherdienstvorgänge können einer einzelnen Berechtigung oder „DataAction“ zugeordnet werden. Allerdings unterstützt jeder dieser Vorgänge, die derselben Berechtigung zugeordnet sind, möglicherweise unterschiedliche Parameter. Mit *Untervorgängen* können Sie zwischen Dienstvorgängen unterscheiden, die dieselbe Berechtigung erfordern, aber unterschiedliche Gruppen von Attributen für Bedingungen unterstützen. Daher können Sie mithilfe eines Untervorgangs eine Bedingung für den Zugriff auf eine Teilmenge von Vorgängen angeben, die einen bestimmten Parameter unterstützen. Anschließend können Sie eine andere Zugriffsbedingung für Vorgänge mit derselben Aktion verwenden, die diesen Parameter nicht unterstützt.

Beispielsweise ist die Aktion `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` für mehr als ein Dutzend verschiedener Dienstvorgänge erforderlich. Einige dieser Vorgänge können Blobindextags als Anforderungsparameter akzeptieren, andere dagegen nicht. Bei Vorgängen, die Blobindextags als Parameter akzeptieren, können Sie Blobindextags in einer Anforderungsbedingung verwenden. Wenn eine solche Bedingung aber für die Aktion `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` definiert wird, können alle Vorgänge, die keine Tags als Anforderungsparameter akzeptieren, diese Bedingung nicht auswerten, und die Autorisierungszugriffsprüfung schlägt fehl.

In diesem Fall kann mithilfe des optionalen Untervorgangs `Blob.Write.WithTagHeaders` eine Bedingung nur auf diejenigen Vorgänge angewendet werden, die Blobindextags als Anforderungsparameter unterstützen.

Ebenso können nur Auswahlvorgänge für die Aktion `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` Blobindextags als Vorbedingung für den Zugriff unterstützen. Diese Teilmenge von Vorgängen wird durch den Untervorgang `Blob.Read.WithTagConditions` identifiziert.

> [!NOTE]
> Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden. Weitere Informationen finden Sie unter [Verwalten und Ermitteln von Daten in Azure Blob Storage mit dem Blobindex (Vorschau)](../blobs/storage-manage-find-blobs.md).

In dieser Vorschau unterstützen Speicherkonten die folgenden Untervorgänge:

> [!div class="mx-tableFixed"]
> | DataAction | Untervorgang | `Display name` | BESCHREIBUNG |
> | :--- | :--- | :--- | :--- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.Read.WithTagConditions` | Bloblesevorgänge, die Bedingungen für Tags unterstützen | Enthält die REST-Vorgänge „Get Blob“ (Blob abrufen), „Get Blob Metadata“ (Blobmetadaten abrufen), „Get Blob Properties“ (Blobeigenschaften abrufen), „Get Block List“ (Blockliste abrufen), „Get Page Ranges“ (Seitenbereiche abrufen) und „Query Blob Contents“ (Blobinhalte abrufen). |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` <br/> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `Blob.Write.WithTagHeaders` | Blob-Schreibvorgänge für Inhalte mit optionalen Tags | Enthält die REST-Vorgänge „Put Blob“ (Blob festlegen), „Copy Blob“ (Blob kopieren), „Copy Blob from URL“ (Blob aus URL kopieren) und „Put Block List“ (Blockliste festlegen). |

## <a name="actions-and-suboperations"></a>Aktionen und Untervorgänge

In der folgenden Tabelle sind die unterstützten Aktionen und Untervorgänge für Bedingungen in Azure Storage aufgeführt.

> [!div class="mx-tableFixed"]
> | `Display name` | BESCHREIBUNG | DataAction |
> | --- | --- | --- |
> | Löschen eines Blobs | „DataAction“ zum Löschen von Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` |
> | Lesen eines Blobs | „DataAction“ zum Lesen von Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | Lesen von Inhalten aus einem Blob mit Tagbedingungen  | REST-Vorgänge: „Get Blob“ (Blob abrufen), „Get Blob Metadata“ (Blobmetadaten abrufen), „Get Blob Properties“ (Blobeigenschaften abrufen), „Get Block List“ (Blockliste abrufen), „Get Page Ranges“ (Seitenbereiche abrufen) und „Query Blob Contents“ (Blobinhalte abrufen). | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**Untervorgang**<br/>`Blob.Read.WithTagConditions` |
> | Schreiben in ein Blob | „DataAction“ zum Schreiben in Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | Schreiben in ein Blob mit Blobindextags | REST-Vorgänge: „Put Blob“ (Blob festlegen), „Put Block List“ (Blockliste festlegen), „Copy Blob“ (Blob kopieren) und „Copy Blob from URL“ (Blob aus URL kopieren). |`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**Untervorgang**<br/>`Blob.Write.WithTagHeaders` |
> | Erstellen eines Blobs oder einer Momentaufnahme oder aber Anfügen von Daten | „DataAction“ zum Erstellen von Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | Schreiben von Inhalt in ein Blob mit Blobindextags | REST-Vorgänge: „Put Blob“ (Blob festlegen), „Put Block List“ (Blockliste festlegen), „Copy Blob“ (Blob kopieren) und „Copy Blob from URL“ (Blob aus URL kopieren). | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**Untervorgang**<br/>`Blob.Write.WithTagHeaders` |
> | Löschen einer Version eines Blobs | „DataAction“ zum Löschen einer Version eines Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` |
> | Ändert den Besitz eines Blobs. | „DataAction“ zum Ändern des Besitzes eines Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` |
> | Ändern von Berechtigungen eines Blobs | „DataAction“ zum Ändern von Berechtigungen eines Blobs. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` |
> | Umbenennen von Datei oder Verzeichnis | „DataAction“ zum Umbenennen von Dateien oder Verzeichnissen. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` |
> | Dauerhaftes Löschen eines Blobs, wodurch vorläufiges Löschen außer Kraft gesetzt wird | „DataAction“ zum dauerhaften Löschen eines Blobs, wodurch vorläufiges Löschen außer Kraft gesetzt wird. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` |
> | Alle Datenvorgänge für Konten mit HNS | „DataAction“ für alle Datenvorgänge in Speicherkonten mit HNS. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` |
> | Lesen von Blobindextags | „DataAction“ zum Lesen von Blobindextags. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` |
> | Schreiben von Blobindextags | „DataAction“ zum Schreiben von Blobindextags. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` |

## <a name="attributes"></a>Attribute

In der folgenden Tabelle sind die Beschreibungen der unterstützten Attribute für Bedingungen in Azure Storage aufgeführt.

> [!div class="mx-tableFixed"]
> | `Display name` | BESCHREIBUNG | attribute |
> | --- | --- | --- |
> | Containername| Name eines Speichercontainers oder Dateisystems. Verwenden Sie ihn, wenn Sie den Containernamen überprüfen möchten. | `containers:name` |
> | Blobpfad | Pfad eines virtuellen Verzeichnisses, Blobs, Ordners oder einer Dateiressource. Verwenden Sie ihn, wenn Sie den Blobnamen oder die Ordner in einem Blobpfad überprüfen möchten. | `blobs:path` |
> | Blobindextags [Schlüssel] | Indextags für eine Blobressource. Beliebige benutzerdefinierte Schlüssel-Wert-Eigenschaften, die Sie zusammen mit einer Blobressource speichern können. Verwenden Sie sie, wenn Sie den Schlüssel in Blobindextags überprüfen möchten. | `tags&$keys$&` |
> | Blobindextags [Werte im Schlüssel] | Indextags für eine Blobressource. Beliebige benutzerdefinierte Schlüssel-Wert-Eigenschaften, die Sie zusammen mit einer Blobressource speichern können. Verwenden Sie sie, wenn Sie sowohl den Schlüssel (Groß-/Kleinschreibung beachten) als auch den Wert in Blobindextags überprüfen möchten. | `tags:`*keyname*`<$key_case_sensitive$>` |

> [!NOTE]
> Bei aufgeführten Attributen und Werten muss die Groß/Kleinschreibung nicht beachtet werden, sofern nicht anders angegeben.

> [!NOTE]
> Wenn Sie Bedingungen für das Attribut `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path` angeben, dürfen die Werte weder den Containernamen noch ein vorangestelltes Zeichen „/“ enthalten. Verwenden Sie die Pfadzeichen ohne URL-Codierung.

> [!NOTE]
> Blobindextags werden bei Data Lake Storage Gen2-Speicherkonten, die einen [hierarchischen Namespace](../blobs/data-lake-storage-namespace.md) (HNS) verwenden, nicht unterstützt. Sie sollten keine Rollenzuweisungsbedingungen mithilfe von Indextags für Speicherkonten autorisieren, bei denen HNS aktiviert wurde.

## <a name="attributes-available-for-each-action"></a>Für jede Aktion verfügbare Attribute

In der folgenden Tabelle sind die Attribute aufgeführt, die Sie in Ihren Bedingungsausdrücken verwenden können – je nachdem, welche Aktion Sie als Ziel verwenden. Wenn Sie mehrere Aktionen für eine einzelne Bedingung auswählen, stehen für Ihre Bedingung möglicherweise weniger Attribute zur Auswahl, weil die Attribute für alle ausgewählten Aktionen verfügbar sein müssen.

> [!div class="mx-tableFixed"]
> | DataAction | attribute | Typ | Gilt für: |
> | --- | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**Untervorgang**<br/>`Blob.Read.WithTagConditions` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**Untervorgang**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**Untervorgang**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` | `containers:name` | Zeichenfolge | ResourceAttributeOnly |
> |  | `blobs:path` | Zeichenfolge | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |

## <a name="see-also"></a>Siehe auch

- [Beispiel für Azure-Rollenzuweisungsbedingungen (Vorschau)](storage-auth-abac-examples.md)
- [Format und Syntax von Azure-Rollenzuweisungsbedingungen (Vorschau)](../../role-based-access-control/conditions-format.md)
- [Was ist die attributbasierte Zugriffssteuerung in Azure (Azure Attribute-Based Access Control, Azure ABAC)? (Vorschau)](../../role-based-access-control/conditions-overview.md)
