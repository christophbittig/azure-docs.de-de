---
title: Berechtigungen in Microsoft Defender für Cloud | Microsoft Docs
description: In diesem Artikel wird erläutert, wie Microsoft Defender für Cloud die rollenbasierte Zugriffssteuerung verwendet, um Benutzern Berechtigungen zuzuweisen und die zulässigen Aktionen für jede Rolle zu identifizieren.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d274c06774377c7b70f3ba81fb1bacbaa2c837ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100970"
---
# <a name="permissions-in-microsoft-defender-for-cloud"></a>Berechtigungen in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender für Cloud verwendet [Azure rollenbasierte Zugriffskontrolle (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), die [eingebaute Rollen](../role-based-access-control/built-in-roles.md) bietet, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können.

Defender für Cloud bewertet die Konfiguration Ihrer Ressourcen, um Sicherheitsprobleme und Schwachstellen zu identifizieren. In Defender für Cloud sehen Sie nur dann Informationen zu einer Ressource, wenn Ihnen die Rolle des Eigentümers, Mitwirkenden oder Lesers für das Abonnement oder die Ressourcengruppe der Ressource zugewiesen ist.

Zusätzlich zu den integrierten Rollen gibt es zwei spezielle Rollen für Defender für Cloud:

* **Sicherheitsleser**: Ein Benutzer, der dieser Rolle angehört, hat Ansichtsrechte für Defender für Cloud. Der Benutzer kann Empfehlungen, Warnungen, Sicherheitsrichtlinien und Sicherheitszustände anzeigen, jedoch keine Änderungen vornehmen.
* **Sicherheitsadministrator**: Ein Benutzer, der dieser Rolle angehört, hat die gleichen Rechte wie der Sicherheitsleseberechtigte und kann darüber hinaus die Sicherheitsrichtlinie aktualisieren sowie Warnungen und Empfehlungen verwerfen.

> [!NOTE]
> Die Sicherheitsrollen Security Reader und Security Admin haben nur in Defender für Cloud Zugriff. Die Sicherheitsrollen haben keinen Zugriff auf andere Azure Dienste wie Speicher, Web + Mobil oder Internet der Dinge.

## <a name="roles-and-allowed-actions"></a>Rollen und zulässige Aktionen

Die folgende Tabelle zeigt die Rollen und zulässigen Aktionen in Defender für Cloud.

| **Aktion**                                                                                                                      | [Sicherheitsleseberechtigter](../role-based-access-control/built-in-roles.md#security-reader) / <br> [Leser](../role-based-access-control/built-in-roles.md#reader) | [Sicherheitsadministrator](../role-based-access-control/built-in-roles.md#security-admin) | [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) / [Besitzer](../role-based-access-control/built-in-roles.md#owner)| [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)| [Besitzer](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(Ressourcengruppenebene)**|**(Abonnementebene)**|**(Abonnementebene)**|
| Initiativen hinzufügen/zuweisen (einschließlich Standards für die Einhaltung gesetzlicher Bestimmungen)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| Sicherheitsrichtlinie bearbeiten                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Aktivieren/Deaktivieren von Microsoft Defender-Plänen                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| Automatische Bereitstellung aktivieren/deaktivieren                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Warnungen verwerfen                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Sicherheitsempfehlungen für eine Ressource anwenden</br> (und [Fix](implement-security-recommendations.md#fix-button) verwenden) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Warnungen und Empfehlungen anzeigen                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> Es empfiehlt sich, den Benutzern eine Rolle zuzuweisen, die jeweils nur so viele Berechtigungen umfasst wie für die Erfüllung ihrer Aufgaben erforderlich sind. Weisen Sie die Rolle „Leser“ etwa Benutzern zu, die nur Informationen zur Sicherheitsintegrität einer Ressource anzeigen, aber keine Aktionen durchführen müssen (also beispielsweise keine Empfehlungen umsetzen oder Richtlinien bearbeiten).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde erläutert, wie Defender für Cloud Azure RBAC verwendet, um Benutzern Berechtigungen zuzuweisen und die zulässigen Aktionen für jede Rolle zu identifizieren. Informieren Sie sich als Nächstes über Folgendes, nachdem Sie sich mit den Rollenzuweisungen zum Überwachen des Sicherheitszustands Ihres Abonnements, Bearbeiten von Sicherheitsrichtlinien und Anwenden von Empfehlungen vertraut gemacht haben:

- [Sicherheitsrichtlinien in Defender für Cloud festlegen](tutorial-security-policy.md)
- [Verwaltung von Sicherheitsempfehlungen in Defender für Cloud](review-security-recommendations.md)
- [Verwaltung und Reaktion auf Sicherheitswarnungen in Defender für Cloud](managing-and-responding-alerts.md)
- [Überwachen von Partnerlösungen mit Azure Security Center](./partner-integration.md)
