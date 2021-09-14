---
title: Revisionen in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Konzept von Revisionen in Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: 16a5dc58a8fddca2cef2c393aaecca61fef61a3c
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187719"
---
# <a name="revisions-in-azure-api-management"></a>Revisionen in Azure API Management

Mit Revisionen können Sie auf kontrollierte und sichere Weise Änderungen an Ihren APIs vornehmen. Wenn Sie Änderungen vornehmen möchten, erstellen Sie eine neue Revision. Anschließend können Sie die API bearbeiten und testen, ohne damit die API-Consumer zu beeinträchtigen. Wenn Sie fertig sind, machen Sie Ihre Revision zur aktuellen Version. Gleichzeitig können Sie optional einen Eintrag im Änderungsprotokoll vornehmen, um Ihre API-Consumer über die Änderungen auf dem neuesten Stand zu halten. Das Änderungsprotokoll wird im Entwicklerportal veröffentlicht.

> [!NOTE]
> Das Entwicklerportal ist nicht im Tarif „Verbrauch“ verfügbar.

Mit Revisionen können Sie Folgendes ausführen:

- Nehmen Sie Änderungen an Ihren API-Definitionen und -Richtlinien auf sichere Weise vor, ohne Ihre Produktions-API zu beeinträchtigen.
- Testen Sie Änderungen, bevor Sie sie veröffentlichen.
- Dokumentieren Sie Ihre Änderungen, damit Ihre Entwickler besser verstehen, was neu ist.
- Führen Sie bei Problemen ein Rollback aus.

[Die ersten Schritte mit Revisionen finden Sie in unserer exemplarischen Vorgehensweise.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Zugreifen auf bestimmte Revisionen

Auf jede Revision Ihrer API kann mit einer speziellen URL zugegriffen werden. Fügen Sie `;rev={revisionNumber}` am Ende Ihrer API-URL, jedoch vor der Abfragezeichenfolge an, um auf eine bestimmte Revision dieser API zuzugreifen. Beispielsweise können Sie diese URL verwenden, um auf Revision 3 der `customers`-API zuzugreifen:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Standardmäßig weist jede Revision dieselben Sicherheitseinstellungen wie die aktuelle Revision auf. Sie können die Richtlinien für eine bestimmte Revision bewusst ändern, wenn Sie für jede Revision unterschiedliche Sicherheitseinstellungen anwenden möchten. Beispielsweise können Sie eine [IP-Filterrichtlinie](./api-management-access-restriction-policies.md#RestrictCallerIPs) hinzufügen, um zu verhindern, dass externe Aufrufer auf eine Revision zugreifen, die sich noch in der Entwicklung befindet.

Eine Revision kann offline geschaltet werden, sodass sie nicht mehr für Aufrufer zugänglich ist, auch wenn diese versuchen, über ihre URL auf die Revision zuzugreifen. Sie können eine Revision im Azure-Portal als offline kennzeichnen. Wenn Sie PowerShell verwenden, können Sie das Cmdlet `Set-AzApiManagementApiRevision` verwenden und das Argument `Path` auf `$null` festlegen.

> [!NOTE]
> Es wird empfohlen, Revisionen offline zu schalten, wenn sie nicht für Tests verwendet werden.

## <a name="current-revision"></a>Aktuelle Revision

Eine einzelne Revision kann als *aktuelle* Revision festgelegt werden. Diese Revision wird für alle API-Anforderungen verwendet, bei denen in der URL keine explizite Revisionsnummer angegeben ist. Sie können ein Rollback auf eine vorherige Revision ausführen, indem Sie diese Revision als die aktuelle festlegen.

Sie können eine Revision im Azure-Portal als aktuelle Revision festlegen. Wenn Sie PowerShell verwenden, können Sie das Cmdlet `New-AzApiManagementApiRelease` verwenden.

## <a name="revision-descriptions"></a>Revisionsbeschreibungen

Wenn Sie eine Revision erstellen, können Sie eine Beschreibung für Ihre eigenen Nachverfolgungszwecke festlegen. Beschreibungen werden für Ihre API-Benutzer nicht wiedergegeben.

Wenn Sie eine Revision als aktuelle Revision festlegen, können Sie optional auch einen öffentlichen Hinweis auf ein Änderungsprotokoll angeben. Das Änderungsprotokoll kann von den API-Benutzern im Entwicklerportal angezeigt werden. Sie können den Hinweis zum Änderungsprotokoll mithilfe des PowerShell-Cmdlets `Update-AzApiManagementApiRelease` ändern.

> [!NOTE]
> Bestimmte API-Eigenschaften wie **Anzeigename** und das **API-Suffix** können nur in der aktuellen Revision aktualisiert werden.

## <a name="versions-and-revisions"></a>Versionen und Revisionen

Versionen und Revisionen sind unterschiedliche Features. Jede Version kann über mehrere Revisionen verfügen, ebenso wie eine API ohne Versionsangabe. Sie können Revisionen ohne Versionen verwenden – und umgekehrt. In der Regel werden Versionen verwendet, um API-Versionen mit Breaking Changes abzugrenzen, während Revisionen für kleinere Änderungen und Nonbreaking Changes an einer API verwendet werden können.

Wenn Sie feststellen, dass Ihre Revision Breaking Changes aufweist, oder wenn Sie Ihre Revision formal in eine Beta-/Testversion umwandeln möchten, können Sie eine Version aus einer Revision erstellen. Klicken Sie im Azure-Portal auf der Registerkarte „Revisionen“ im Kontextmenü der Revision auf die Option „Version aus dieser Revision erstellen“.
