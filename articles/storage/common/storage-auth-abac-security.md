---
title: Sicherheitsüberlegungen für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)
titleSuffix: Azure Storage
description: Sicherheitsüberlegungen für Azure-Rollenzuweisungsbedingungen und die attributbasierte Zugriffssteuerung von Azure (Azure Attribute-Based Access Control, Azure ABAC).
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 46be6327052aa280b9d23bc05764550a05739c5e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627917"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Sicherheitsüberlegungen für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)

> [!IMPORTANT]
> Azure RBAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn Sie Ressourcen mithilfe der [attributbasierten Zugriffssteuerung von Azure (Attribute-Based Access Control, Azure ABAC)](storage-auth-abac.md) vollständig schützen möchten, müssen Sie auch die [Attribute](storage-auth-abac-attributes.md) schützen, die in den [Bedingungen für die Azure-Rollenzuweisung](../../role-based-access-control/conditions-format.md) verwendet werden. Basiert Ihre Bedingung beispielsweise auf einem Dateipfad, sollten Sie beachten, dass der Zugriff gefährdet sein kann, wenn der Prinzipal eine uneingeschränkte Berechtigung zum Umbenennen eines Dateipfads hat.

In diesem Artikel werden Sicherheitsüberlegungen beschrieben, die Sie in Ihren Bedingungen für Rollenzuweisungen berücksichtigen sollten.

## <a name="use-of-other-authorization-mechanisms"></a>Verwendung von anderen Autorisierungsmechanismen

Rollenzuweisungsbedingungen werden nur ausgewertet, wenn Azure RBAC zur Autorisierung verwendet wird. Diese Bedingungen können umgangen werden, wenn Sie den Zugriff mithilfe alternativer Autorisierungsmethoden zulassen:
- Autorisierung mit [gemeinsam verwendetem Schlüssel](/rest/api/storageservices/authorize-with-shared-key)
- [Konto-SAS](/rest/api/storageservices/create-account-sas) (Shared Access Signature)
- [Dienst-SAS](/rest/api/storageservices/create-service-sas).

Ebenso werden Bedingungen nicht ausgewertet, wenn der Zugriff über [Zugriffssteuerungslisten (Access Control Lists, ACLs)](../blobs/data-lake-storage-access-control.md) in Speicherkonten mit einem [hierarchischen Namespace](../blobs/data-lake-storage-namespace.md) (HNS) gewährt wird.

Sie können eine Autorisierung mit gemeinsam verwendetem Schlüssel, SAS auf Kontoebene und SAS auf Dienstebene verhindern, indem Sie bei Ihrem Speicherkonto die [Autorisierung mit gemeinsam verwendetem Schlüssel deaktivieren](shared-key-authorization-prevent.md). Da SAS für die Benutzerdelegierung von Azure RBAC abhängt, werden Bedingungen für die Rollenzuweisung ausgewertet, wenn diese Autorisierungsmethode verwendet wird.

> [!NOTE]
> Rollenzuweisungsbedingungen werden nicht ausgewertet, wenn der Zugriff über ACLs mit Data Lake Storage Gen2 gewährt wird. In diesem Fall müssen Sie den Zugriffsbereich so planen, dass er sich mit dem über ACLs gewährten Zugriffsbereich nicht überschneidet.

## <a name="securing-storage-attributes-used-in-conditions"></a>Sichern von Speicherattributen, die in Bedingungen verwendet werden

### <a name="blob-path"></a>Blobpfad

Wenn Sie „Blobpfad“ als *@Resource* -Attribut für eine Bedingung verwenden, sollten Sie auch verhindern, dass Benutzer ein Blob umbenennen und so Zugriff auf eine Datei erhalten, wenn sie Konten mit einem hierarchischen Namespace verwenden. Wenn Sie beispielsweise eine auf dem Blobpfad basierende Bedingung erstellen möchten, sollten Sie auch den Zugriff des Benutzers auf die folgenden Aktionen einschränken:

| Aktion | BESCHREIBUNG |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | Bei dieser Aktion können Kunden eine Datei mithilfe der Pfaderstellungs-API umbenennen. |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | Diese Aktion ermöglicht den Zugriff auf verschiedene Dateisystem- und Pfadvorgänge. |

### <a name="blob-index-tags"></a>Blobindextags

[Blobindextags](../blobs/storage-manage-find-blobs.md) werden als Freiformattribute für Bedingungen im Speicher verwendet. Wenn Sie Zugriffsbedingungen mithilfe dieser Tags erstellen, müssen Sie auch die Tags schützen. Insbesondere ermöglicht die „DataAction“ `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` Benutzern, die Tags für ein Speicherobjekt zu ändern. Sie können diese Aktion einschränken und so verhindern, dass Benutzer einen Tagschlüssel oder -wert bearbeiten, um Zugriff auf nicht autorisierte Objekte zu erhalten.

Werden Blobindextags in Bedingungen verwendet, können Daten außerdem gefährdet sein, wenn die Daten und zugeordneten Indextags in separaten Vorgängen aktualisiert werden. Sie können `@Request`-Bedingungen für Blobschreibvorgänge verwenden, um zu verlangen, dass Indextags in demselben Aktualisierungsvorgang festgelegt werden. Dieser Ansatz kann dazu beitragen, dass Daten sofort nach dem Schreiben in den Speicher geschützt werden.

#### <a name="tags-on-copied-blobs"></a>Tags für kopierte Blobs

Standardmäßig werden Blobindextags aus einem Quellblob nicht in das Ziel kopiert, wenn Sie die API [Copy Blob](/rest/api/storageservices/Copy-Blob) (Blob kopieren) oder eine ihrer Varianten verwenden. Wenn Sie den Zugriffsbereich für Blobs beim Kopieren beibehalten möchten, sollten Sie auch die Tags kopieren.

#### <a name="tags-on-snapshots"></a>Tags für Momentaufnahmen

Tags für Blobmomentaufnahmen können nicht geändert werden. Dies bedeutet: Sie müssen die Tags für ein Blob aktualisieren, bevor Sie die Momentaufnahme erstellen. Wenn Sie die Tags für ein Basisblob ändern, behalten die Tags für dessen Momentaufnahme ihren vorherigen Wert weiterhin bei.

Wenn ein Tag für ein Basisblob geändert wird, nachdem eine Momentaufnahme erstellt wurde, kann der Zugriffsbereich für das Basisblob und die Momentaufnahme unterschiedlich sein.

#### <a name="tags-on-blob-versions"></a>Tags für Blobversionen

Blobindextags werden nicht kopiert, wenn eine Blobversion über die APIs [Put Blob](/rest/api/storageservices/put-blob) (Blob festlegen), [Put Block List](/rest/api/storageservices/put-block-list) (Blockliste festlegen) oder [Copy Blob](/rest/api/storageservices/Copy-Blob) (Blob kopieren) erstellt wird. Sie können Tags über den Header für diese APIs angeben.

Tags können einzeln für ein aktuelles Basisblob und jede Blobversion festgelegt werden. Wenn Sie Tags in einem Basisblob ändern, werden die Tags in früheren Versionen nicht aktualisiert. Wenn Sie den Zugriffsbereich für ein Blob und alle zugehörigen Versionen mithilfe von Tags ändern möchten, müssen Sie die Tags für jede Version aktualisieren.

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>Abfrage- und Filtereinschränkungen bei Versionen und Momentaufnahmen

Wenn Sie Tags zum Abfragen und Filtern von Blobs in einem Container verwenden, werden nur die Basisblobs in die Antwort einbezogen. Blobversionen oder Momentaufnahmen mit den angeforderten Schlüsseln und Werten werden nicht einbezogen.

## <a name="roles-and-permissions"></a>Rollen und Berechtigungen

Wenn Sie Rollenzuweisungsbedingungen für [integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md) verwenden, sollten Sie alle Berechtigungen, die die Rolle einem Prinzipal erteilt, sorgfältig überprüfen.

### <a name="inherited-role-assignments"></a>Geerbte Rollenzuweisungen

Rollenzuweisungen können für eine Verwaltungsgruppe, ein Abonnement, eine Ressourcengruppe, ein Speicherkonto oder einen Container konfiguriert werden, und sie werden auf jeder Ebene in der angegebenen Reihenfolge geerbt. Weil Azure RBAC ein additives Modell enthält, sind die effektiven Berechtigungen die Summe aus Rollenzuweisungen auf jeder Ebene. Wenn einem Prinzipal dieselbe Berechtigung über mehrere Rollenzuweisungen zugewiesen wurde, wird der Zugriff für einen Vorgang, der diese Berechtigung verwendet, für jede Zuweisung auf jeder Ebene separat ausgewertet.

Da Bedingungen als Bedingungen für Rollenzuweisungen implementiert werden, kann jede beliebige bedingungslose Rollenzuweisung Benutzern ermöglichen, die Bedingung zu umgehen. Angenommen, Sie weisen einem Benutzer die Rolle *Mitwirkender an Speicherblobdaten* für ein Speicherkonto und ein Abonnement zu, fügen aber nur der Zuweisung für das Speicherkonto eine Bedingung hinzu. In diesem Fall hat der Benutzer über die Rollenzuweisung auf Abonnementebene uneingeschränkten Zugriff auf das Speicherkonto.

Deshalb sollten Sie Bedingungen konsistent für alle Rollenzuweisungen in einer Ressourcenhierarchie anwenden.

## <a name="other-considerations"></a>Weitere Überlegungen

### <a name="condition-operations-that-write-blobs"></a>Bedingungsvorgänge, die Blobs schreiben

Viele Vorgänge, die Blobs schreiben, erfordern entweder die Berechtigung `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` oder die Berechtigung `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`. Integrierte Rollen, z. B. [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) und [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor), erteilen einem Sicherheitsprinzipal beide Berechtigungen.

Wenn Sie eine Rollenzuweisungsbedingung für diese Rollen definieren, sollten Sie für beide Berechtigungen identische Bedingungen verwenden, um konsistente Zugriffseinschränkungen für Schreibvorgänge sicherzustellen.

### <a name="behavior-for-copy-blob-and-copy-blob-from-url"></a>Verhalten bei „Copy Blob“(Blob kopieren) und „Copy Blob from URL“ (Blob aus URL kopieren)

Bei den Vorgängen [Copy Blob](/rest/api/storageservices/Copy-Blob) (Blob kopieren) und [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url) (Blob aus URL kopieren) werden `@Request`-Bedingungen, die den Blobpfad als Attribut für die Aktion `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` verwenden,und deren Untervorgänge nur für das Zielblob ausgewertet.

Bei Bedingungen im Quellblob werden `@Resource`-Bedingungen für die Aktion `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` ausgewertet.

### <a name="behavior-for-get-page-ranges"></a>Verhalten bei „Get Page Ranges“ (Seitenbereiche abrufen)

Beim Vorgang [Get Page Ranges](/rest/api/storageservices/get-page-ranges) (Seitenbereiche abrufen) werden `@Resource`-Bedingungen, die `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags` als Attribut für die Aktion `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` und deren Untervorgänge verwenden, nur für das Zielblob ausgewertet.

Bedingungen gelten nicht für den Zugriff auf das Blob, das durch den URI-Parameter `prevsnapshot` in der API angegeben wird.

## <a name="see-also"></a>Siehe auch

- [Autorisieren des Zugriffs auf Blobs mithilfe von Bedingungen für die Azure-Rollenzuweisung (Vorschau)](storage-auth-abac.md)
- [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md)
- [Was ist die attributbasierte Zugriffssteuerung in Azure (Azure Attribute-Based Access Control, Azure ABAC)? (Vorschau)](../../role-based-access-control/conditions-overview.md)
