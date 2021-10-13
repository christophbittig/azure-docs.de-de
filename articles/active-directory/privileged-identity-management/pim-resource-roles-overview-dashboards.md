---
title: Ressourcendashboards für Zugriffsüberprüfungen in PIM – Azure AD | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie ein Ressourcendashboard verwenden, um eine Zugriffsüberprüfung in Azure AD Privileged Identity Management (PIM) durchzuführen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87ff7dc27dea5b25995dcfbd7f969a6d01fec56
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669262"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Verwenden eines Ressourcendashboards zum Durchführen einer Zugriffsüberprüfung in Privileged Identity Management

Sie können ein Ressourcendashboard verwenden, um eine Zugriffsüberprüfung in Privileged Identity Management (PIM) durchzuführen. In Azure Active Directory (Azure AD) umfasst das Dashboard für die Administratoransicht drei Hauptkomponenten:

- Eine grafische Darstellung der Ressourcenrollenaktivierungen
- Diagramme, in denen die Verteilung von Rollenzuweisungen nach Zuweisungstyp dargestellt ist
- Einen Datenbereich, der Informationen zu neuen Rollenzuweisungen enthält

![Screenshot der Dashboardadministratoransicht mit Diagrammen](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Screenshot der Dashboardadministratoransicht mit Datenlisten](media/pim-resource-roles-overview-dashboards/role-settings.png)

Die grafische Darstellung der Ressourcenrollenaktivierungen deckt die letzten sieben Tage ab. Diese Daten beziehen sich auf die ausgewählte Ressource und geben Aufschluss über Aktivierungen für die gängigsten Rollen (Besitzer, Mitwirkender, Benutzerzugriffsadministrator) sowie für alle Rollen zusammen.

Auf einer Seite des Aktivierungsdiagramms befinden sich zwei Diagramme, in denen die Verteilung von Rollenzuweisungen nach Zuweisungstyp für Benutzer und Gruppen dargestellt ist. Wenn Sie auf ein Segment des Diagramms klicken, ändert sich der Wert in einen Prozentsatz (oder umgekehrt).

Unterhalb der Diagramme sind die Anzahl von Benutzern und Gruppen mit neuen Rollenzuweisungen in den letzten 30 Tagen sowie die nach Gesamtzuweisungen in absteigender Reihenfolge sortierten Rollen aufgeführt.

## <a name="next-steps"></a>Nächste Schritte

- [Starten einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management](./pim-create-azure-ad-roles-and-resource-roles-review.md)