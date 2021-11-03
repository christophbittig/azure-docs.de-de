---
title: Hinzufügen der Rolle "Mitwirkender" auf der Media Services
description: In diesem Thema wird erläutert, wie die Rolle "Mitwirkender" auf der Media Services hinzugefügt wird.
ms.author: itnorman
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 294587e9f502a7f927bb905fbd354ab588955a85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095677"
---
# <a name="add-contributor-role-on-the-media-services"></a>Hinzufügen der Rolle "Mitwirkender" auf der Media Services

In diesem Thema wird erläutert, wie die Rolle "Mitwirkender" auf der Media Services hinzugefügt wird.

> [!NOTE]
> Wenn Sie Ihre Azure Video Analyzer für Medien über die Azure-Portal Benutzeroberfläche erstellen, wird der ausgewählten verwalteten Identität automatisch eine Mitwirkenderberechtigung für das ausgewählte Media Service-Konto zugewiesen.

## <a name="prerequisites"></a>Voraussetzungen

1. Azure Media Services (AMS)
2. Benutzerseitig zugewiesene verwaltete Identität
> [!NOTE]
> Sie benötigen ein Azure-Abonnement, in dem Sie Zugriff auf die Rolle [Mitwirkender][docs-role-contributor] und die Rolle [Benutzerzugriffsadministrator][docs-role-administrator] für die Ressourcengruppe haben, unter der Sie neue Ressourcen erstellen (vom Benutzer zugewiesene verwaltete Identität, Speicherkonto, Video Analyzer-Konto). Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen diese Berechtigungen erteilt. Die zugeordneten Azure Media Services müssen sich in derselben Region wie das Video Analyzer für Media-Konto befinden.


## <a name="add-contributor-role-on-the-media-services"></a>Hinzufügen der Rolle "Mitwirkender" auf der Media Services
### <a name="azure-portal"></a>[Azure-Portal](#tab/portal/)

### <a name="add-contributor-role-on-the-media-services-in-the-azure-portal"></a>Hinzufügen der Rolle "Mitwirkender" auf dem Media Services im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
    * Geben Sie in der Suchleiste im oberen Bereich **Media-Analyzer** ein.
    * Suchen Sie Nach Ihrer Media Service-Ressource, und wählen Sie sie aus.
1. Klicken Sie im linken Menü auf **Zugriffssteuerung (IAM)** .
    * Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen**. Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „**Rollenzuweisung hinzufügen**“ deaktiviert.
1. Wählen Sie in der Liste „Rolle“ die Rolle [Mitwirkender][docs-role-contributor] aus, und klicken Sie auf **Weiter.**
1. Wählen Sie im **Zugriff zuweisen auf** das Optionsfeld *Verwaltete Identität* aus.
    * Klicken Sie auf die Schaltfläche **+Mitglieder auswählen,** und der Bereich **Verwaltete Identitäten auswählen** sollte angezeigt werden.
1. Folgendes **Auswählen**:
    * Im **Abonnement** das Abonnement, in dem sich die verwaltete Identität befindet.
    * Wählen Sie unter **Verwaltete Identität** *Vom Benutzer zugewiesene verwaltete Identität* aus.
    * Suchen Sie im Abschnitt **Auswählen** nach der verwalteten Identität, der Sie Berechtigungen für Mitwirkende für die Media Services-Ressource gewähren möchten.    
1. Nachdem Sie den Dienstprinzipal gefunden haben, wählen Sie ihn durch Mausklick aus.
1. Klicken Sie auf **Überprüfen und zuweisen**, um die Rolle zuzuweisen.

<!-- links -->
[docs-role-contributor]: ../../role-based-access-control/built-in-roles.md#contributor
[docs-role-administrator]: ../../role-based-access-control/built-in-roles.md#user-access-administrator
