---
title: Versionen in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Konzept von Versionen in Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 10/31/2021
ms.author: jodowns
ms.openlocfilehash: a89860216563480de4803cc6e4a7e9f24853190f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471096"
---
# <a name="versions-in-azure-api-management"></a>Versionen in Azure API Management

Mit Versionen können Sie Ihren Entwicklern Gruppen verwandter APIs präsentieren. Sie können Versionen verwenden, um Breaking Changes in Ihrer API sicher zu verarbeiten. Clients können sich entschließen, Ihre neue API-Version zu verwenden, wenn sie bereit sind, während vorhandene Clients weiterhin eine ältere Version verwenden. Versionen werden durch einen Versionsbezeichner unterschieden (hierbei handelt es sich um einen von Ihnen ausgewählten Zeichenfolgenwert), und ein Versionsverwaltungsschema erlaubt es Clients, zu ermitteln, welche Version einer API sie verwenden möchten.

In den meisten Fällen kann jede API-Version als eigene unabhängige API angesehen werden. Zwei verschiedene API-Versionen können unterschiedliche Sätze von Vorgängen sowie unterschiedliche Richtlinien aufweisen.

Mit Versionen können Sie:

- Mehrere Versionen Ihrer API gleichzeitig veröffentlichen.
- Einen Pfad, eine Abfragezeichenfolge oder einen Header verwenden, um zwischen Versionen zu unterscheiden.
- Einen beliebigen Zeichenfolgenwert verwenden, um Ihre Version zu identifizieren, wobei es sich um eine Zahl, ein Datum oder einen Namen handeln kann.
- Ihre API-Versionen im Entwicklerportal zusammen gruppiert anzeigen.
- Eine vorhandene API (ohne Versionsverwaltung) nehmen und eine neue Version davon erstellen, ohne vorhandene Clients zu unterbrechen.

[Die ersten Schritte mit Versionen finden Sie in unserer exemplarischen Vorgehensweise.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Versionsverwaltungsschemas

Verschiedene API-Entwickler stellen unterschiedliche Anforderungen an die Versionsverwaltung. Azure API Management schreibt keinen einzelnen Ansatz für die Versionsverwaltung vor, sondern bietet stattdessen mehrere Optionen.

### <a name="path-based-versioning"></a>Pfadbasierte Versionsverwaltung

Wenn das Pfadschema für die Versionsverwaltung verwendet wird, muss der Versionsbezeichner im URL-Pfad für alle API-Anforderungen enthalten sein.

Beispielsweise können `https://apis.contoso.com/products/v1` und `https://apis.contoso.com/products/v2` auf dieselbe `products`-API verweisen, aber jeweils auf die Version `v1` und `v2`.

Das Format einer API-Anforderungs-URL bei Verwendung der pfadbasierten Versionsverwaltung ist `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}`.

### <a name="header-based-versioning"></a>Headerbasierte Versionsverwaltung

Wenn das Headerschema für die Versionsverwaltung verwendet wird, muss der Versionsbezeichner bei allen API-Anforderungen in einem HTTP-Anforderungsheader enthalten sein. Sie können den Namen des HTTP-Anforderungsheaders angeben.

Beispielsweise können Sie einen benutzerdefinierten Header namens `Api-Version` erstellen, und Clients können `v1` oder `v2` im Wert dieses Headers angeben.

### <a name="query-string-based-versioning"></a>Abfragezeichenfolgen-basierte Versionsverwaltung

Wenn das Abfragezeichenfolgen-Schema für die Versionsverwaltung verwendet wird, muss der Versionsbezeichner in einem Abfragezeichenfolgen-Parameter für alle API-Anforderungen enthalten sein. Der Name des Abfragezeichenfolgen-Parameters, angeben.

Das Format einer API-Anforderungs-URL bei Verwendung der abfragezeichenfolgen-basierten Versionsverwaltung ist: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}`.

Beispielsweise können `https://apis.contoso.com/products?api-version=v1` und `https://apis.contoso.com/products/api-version=v2` auf dieselbe `products`-API verweisen, aber jeweils auf die Version `v1` und `v2`.

## <a name="original-versions"></a>Original-Versionen

Wenn Sie einer API ohne Versionsverwaltung eine Version hinzufügen, wird automatisch eine `Original`-Version erstellt, die auf die Standard-URL reagiert, ohne dass ein Versionsbezeichner angegeben ist. Die `Original`-Version stellt sicher, dass alle vorhandenen Aufrufer durch das Hinzufügen einer Version nicht beeinträchtigt werden. Wenn Sie die Versionsverwaltung gleich bei der Erstellung einer neuen API aktivieren, wird keine `Original`-Version erstellt.

## <a name="how-versions-are-represented"></a>Darstellungsweise von Versionen

Azure API Management verwaltet eine Ressource namens *version set*, die einen Satz von Versionen für eine einzelne logische API darstellt. Ein Versionssatz enthält den Anzeigenamen der API mit Versionsverwaltung sowie das verwendete [Versionsverwaltungsschema](#versioning-schemes) zum Weiterleiten von Anforderungen an bestimmte Versionen.

Jede Version einer API wird als eigene API-Ressource verwaltet, die dann einem Versionssatz zugeordnet wird. Ein Versionssatz kann APIs mit unterschiedlichen Vorgängen oder Richtlinien enthalten. Möglicherweise nehmen Sie erhebliche Änderungen zwischen den Versionen in einem Satz vor.

Das Azure-Portal erstellt die Versionssätze für Sie. Sie können den Namen und die Beschreibung für einen Versionssatz im Azure-Portal ändern.

Ein Versionssatz wird automatisch gelöscht, wenn die endgültige Version gelöscht wird.

Sie können Versionssätze mit der [Azure-Befehlszeilenschnittstelle](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [Resource Manager-Vorlagen](/azure/templates/microsoft.apimanagement/service/apiversionsets) oder der [Azure Resource Manager-API](/rest/api/apimanagement/2020-12-01/api-version-set) direkt anzeigen und verwalten.

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrieren einer API ohne Versionsverwaltung zu einer API mit Versionsverwaltung

Wenn Sie das Azure-Portal verwenden, um die Versionsverwaltung für eine vorhandene API zu aktivieren, werden die folgenden Änderungen an Ihren API Management-Ressourcen vorgenommen:

 * Ein neuer Versionssatz wird erstellt.
 * Die vorhandene Version wird beibehalten und [als die `Original`-Version der API](#original-versions) konfiguriert. Die API wird mit dem Versionssatz verknüpft, aber es ist nicht erforderlich, einen Versionsbezeichner anzugeben.
 * Die neue Version wird als neue API erstellt und mit dem Versionssatz verknüpft. Auf diese neue API muss mithilfe des Versionsverwaltungsschemas und des Bezeichners zugegriffen werden.

## <a name="versions-and-revisions"></a>Versionen und Revisionen

Versionen und Revisionen sind unterschiedliche Features. Jede Version kann über mehrere Revisionen verfügen, ebenso wie eine API ohne Versionsangabe. Sie können Revisionen ohne Versionen verwenden – und umgekehrt. In der Regel werden Versionen verwendet, um API-Versionen mit Breaking Changes abzugrenzen, während Revisionen für kleinere Änderungen und Nonbreaking Changes an einer API verwendet werden können.

Wenn Sie feststellen, dass Ihre Revision Breaking Changes aufweist, oder wenn Sie Ihre Revision formal in eine Beta-/Testversion umwandeln möchten, können Sie eine Version aus einer Revision erstellen. Klicken Sie im Azure-Portal auf der Registerkarte „Revisionen“ im Kontextmenü der Revision auf die Option „Version aus dieser Revision erstellen“.

## <a name="developer-portal"></a>Entwicklerportal

Im [Entwicklerportal](./api-management-howto-developer-portal.md) wird jede Version einer API separat aufgelistet.

![API Management-Entwicklerportal mit einer Liste von APIs mit Versionsverwaltung](media/api-management-versions/portal-list.png)

In den Details einer API wird außerdem eine Liste aller Versionen dieser API angezeigt. Eine `Original`-Version wird ohne Versionsbezeichner angezeigt.

![API Management-Entwicklerportal mit den Details einer API und einer Liste der Versionen dieser API](media/api-management-versions/portal-details.png)

> [!TIP]
> API-Versionen müssen einem Produkt hinzugefügt werden, damit sie im Entwicklerportal angezeigt werden.
