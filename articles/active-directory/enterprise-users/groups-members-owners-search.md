---
title: Suchen und Filtern von Gruppenmitgliedern und -besitzern (Vorschau) – Azure Active Directory | Microsoft-Dokumentation
description: Suchen und Filtern von Gruppenmitgliedern und -besitzern im Azure-Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.author: curtand
ms.reviewer: jodah
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774bef1dd939daea4384a25bc35d22f3475f57a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049599"
---
# <a name="search-groups-and-members-in-azure-active-directory"></a>Suchen von Gruppen und Mitgliedern in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie nach Mitgliedern und Besitzern einer Gruppe suchen, und wie Sie Suchfilter im Azure Active Directory-Portal (Azure AD) verwenden. Zu den Suchfunktionen für Gruppen gehören:

- Gruppensuchfunktionen wie z. B. Suchen von Teilzeichenfolgen in Gruppennamen
- Filter- und Sortieroptionen für Mitglieds- und Besitzerlisten
- Suchfunktionen für Mitglieds- und Besitzerlisten

## <a name="group-search-and-sort"></a>Suchen und Sortieren in Gruppen

Wenn Sie auf der Seite **Alle Gruppen** eine Suchzeichenfolge eingeben, können Sie jetzt nur auf der Seite **Alle Gruppen** zwischen den Suchvorgängen „contains“ (enthält) und „starts with“ (beginnt mit) wechseln. Die Teilzeichenfolgensuche wird nur für ganze Wörter durchgeführt, und alle Sonderzeichen werden auch als mit dem UND-Operator gesucht. Wenn Sie beispielsweise nach „-Name“ suchen, wird eine Suche nach der Teilzeichenfolge „Name“ und eine Suche nach „-“ gestartet. Bei der Teilzeichenfolgesuche wird zwischen Groß- und Kleinschreibung unterschieden. Objekt-ID- oder mailNickname-Eigenschaften werden ebenfalls durchsucht.

![Neue Teilzeichenfolgesuchen auf der Seite „Alle Gruppen“](./media/groups-members-owners-search/members-list.png)

Eine Suche nach „Policy“ gibt z. B. sowohl „MDM policy – West“ als auch „Policy group“ zurück. Eine Gruppe mit dem Namen „New_policy“ würde nicht zurückgegeben. Sie können die Liste **Alle Gruppen** nach Namen in aufsteigender oder absteigender Reihenfolge sortieren.

## <a name="group-member-search-and-filter"></a>Suchen und Filtern von Gruppenmitgliedern

### <a name="search-group-member-and-owner-lists"></a>Suchen in Gruppenmitglieder- und Besitzerlisten

Sie können die Mitglieder oder Besitzer*innen einer bestimmten Gruppe nach Namen durchsuchen. Wenn Sie eine Suchzeichenfolge eingeben, wird automatisch eine `contains`-Suche durchgeführt. Beispielsweise gibt eine Suche nach „Scott“ sowohl Scott Wilkinson als auch Maya Scott zurück.

![Neue Teilzeichenfolgen-Suchvorgänge in Gruppenmitglieder- und Besitzerlisten](./media/groups-members-owners-search/groups-search-preview.png)

### <a name="filter-member-and-owner-lists"></a>Filtern von Mitglieder- und Besitzerlisten

Sie können die Gruppenmitglieder- und Besitzerlisten auch nach Benutzertyp filtern. Diese Informationen finden Sie in der Spalte **Benutzertyp** in der Liste der Mitglieder oder Besitzer. Sie können die Liste so filtern, dass nur Mitglieder oder Gäste angezeigt werden.

Die Seite **Mitglieder** enthält alle eindeutigen Mitglieder der Gruppe, einschließlich aller Mitglieder, die ihre Gruppenmitgliedschaft von einer anderen Gruppe erben.

Sie können die Listen auch einzeln durchsuchen und filtern. Das Filtern der Liste alle Mitglieder wirkt sich nicht auf die Filter aus, die auf die Liste der direkten Mitglieder angewendet werden.

## <a name="group-memberships"></a>Gruppenmitgliedschaften

Sie können Gruppenmitgliedschaften für eine Gruppe auch auf der Seite **Gruppenmitgliedschaften** anzeigen. Die Seite **Gruppenmitgliedschaften** unterstützt Such-, Sortier- und Filtervorgänge, die den anderen Gruppenseiten ähneln.

## <a name="group-member-counts"></a>Anzahl der Gruppenmitglieder

Auf der Seite **Übersicht** wird die Mitgliederanzahl für Gruppen bereitgestellt. Jetzt können Sie die Gesamtzahl der direkten Mitglieder einer Gruppe und die Gesamtzahl der Mitgliedschaften (alle eindeutigen Mitglieder der Gruppe einschließlich geerbter Mitgliedschaften) auf der Seite **Übersicht** anzeigen.

![Höhere Genauigkeit bei der Anzahl der Gruppenmitgliedschaften](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel bieten zusätzliche Informationen zur Arbeit mit Gruppen in Azure AD.

- [Anzeigen Ihrer Gruppen und Mitglieder](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Verwalten der Gruppenmitgliedschaft](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-create-rule.md)
- [Bearbeiten Ihrer Gruppeneinstellungen](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Verwalten des Zugriffs auf Ressourcen mittels Gruppen](../fundamentals/active-directory-manage-groups.md)
- [Verwalten des Zugriffs auf SaaS-Apps mittels Gruppen](groups-saasapps.md)
- [Verwalten von Gruppen mithilfe von PowerShell-Befehlen](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Hinzufügen eines Azure-Abonnements zu Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
