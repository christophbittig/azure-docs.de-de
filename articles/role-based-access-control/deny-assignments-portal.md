---
title: Auflisten von Azure-Ablehnungszuweisungen mithilfe des Azure-Portals – Azure RBAC
description: Erfahren Sie, wie Sie mithilfe des Azure-Portals und der rollenbasierten Zugriffssteuerung (Role-based Access Control, RBAC) in Azure die Benutzer, Gruppen, Dienstprinzipale und verwalteten Identitäten auflisten können, denen der Zugriff auf bestimmte Aktionen von Azure-Ressourcen in bestimmten Bereichen untersagt wurde.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1581139a2bd941f32afbcd4f0ecbefc60c068d80
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353481"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Auflisten von Ablehnungszuweisungen über das Azure-Portal

[Azure-Ablehnungszuweisungen](deny-assignments.md) hindern Benutzer an der Ausführung bestimmter Aktionen für Azure-Ressourcen, auch wenn ihnen über eine Rollenzuweisung Zugriff erteilt wird. In diesem Artikel wird beschrieben, wie Sie mit dem Azure-Portal Ablehnungszuweisungen auflisten.

> [!NOTE]
> Sie können Ihre eigenen Ablehnungszuweisungen nicht direkt erstellen. Weitere Informationen dazu, wie Ablehnungszuweisungen erstellt werden, finden Sie unter [Azure-Ablehnungszuweisungen](deny-assignments.md).

## <a name="prerequisites"></a>Voraussetzungen

Um Informationen zu einer Ablehnungszuweisung abzurufen, müssen Sie über Folgendes verfügen:

- `Microsoft.Authorization/denyAssignments/read`-Berechtigung, die in den meisten [integrierten Azure-Rollen](built-in-roles.md) enthalten ist

## <a name="list-deny-assignments"></a>Auflisten von Ablehnungszuweisungen

Führen Sie die folgenden Schritte aus, um Ablehnungszuweisungen für ein Abonnement oder eine Verwaltungsgruppe aufzulisten.

1. Klicken Sie im Azure-Portal auf **Alle Dienste** und dann auf **Verwaltungsgruppen** oder **Abonnements**.

1. Klicken Sie auf die Verwaltungsgruppe oder das Abonnement, die bzw. das Sie auflisten möchten.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Ablehnungszuweisungen** (oder klicken Sie auf der Kachel „Ablehnungszuweisungen anzeigen“ auf **Anzeigen**).

    Wenn Ablehnungszuweisungen für diesen Bereich oder an diesen Bereich vererbte Ablehnungszuweisungen vorhanden sind, werden diese angezeigt.

    ![Zugriffssteuerung: Registerkarte „Ablehnungszuweisungen“](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Um zusätzliche Spalten anzuzeigen, klicken Sie auf **Spalten bearbeiten**.

    ![Ablehnungszuweisungen: Spalten](./media/deny-assignments-portal/deny-assignments-columns.png)

    | Column | BESCHREIBUNG  |
    | --- | --- |
    | **Name** | Der Name der Ablehnungszuweisung. |
    | **Prinzipaltyp** | Benutzer, Gruppe, vom System definierte Gruppe oder Dienstprinzipal. |
    | **Verweigert**  | Der Name des Sicherheitsprinzipals, der in der Ablehnungszuweisung enthalten ist. |
    | **Id** | Eindeutiger Bezeichner für die Ablehnungszuweisung. |
    | **Ausgeschlossene Prinzipale** | Gibt an, ob Sicherheitsprinzipale von der Ablehnungszuweisung ausgeschlossen sind. |
    | **Gilt nicht für untergeordnete Elemente** | Gibt an, ob die Ablehnungszuweisung an Unterbereiche vererbt wird. |
    | **Durch System geschützt** | Gibt an, ob die Ablehnungszuweisung durch Azure verwaltet wird. Aktuell immer „Ja“. |
    | **Umfang** | Eine Verwaltungsgruppe, ein Abonnement, eine Ressourcengruppe oder eine Ressource. |

1. Aktivieren Sie das Kontrollkästchen für eines der aktivierten Elemente, und klicken Sie auf **OK**, um die ausgewählten Spalten anzuzeigen.

## <a name="list-details-about-a-deny-assignment"></a>Auflisten von Details zu einer Ablehnungszuweisung

Führen Sie die folgenden Schritte aus, um zusätzliche Details zu einer Ablehnungszuweisung aufzulisten.

1. Öffnen Sie den Bereich **Ablehnungszuweisungen**, wie im vorherigen Abschnitt beschrieben.

1. Klicken Sie auf den Namen der Ablehnungszuweisung, um das Blatt **Benutzer** zu öffnen.

    ![Ablehnungszuweisung: Benutzer](./media/deny-assignments-portal/deny-assignment-users.png)

    Das Blatt **Benutzer** umfasst die folgenden zwei Abschnitte.

    | Ablehnungseinstellung  | BESCHREIBUNG |
    | --- | --- |
    | **Die Ablehnungszuweisung gilt für**  | Sicherheitsprinzipale, auf die die Ablehnungszuweisung angewendet wird. |
    | **Die Ablehnungszuweisung schließt Folgendes aus** | Gibt Sicherheitsprinzipale an, die von der Ablehnungszuweisung ausgeschlossen sind. |

    **Vom System definierter Prinzipal** repräsentiert alle Benutzer, Gruppen, Dienstprinzipale und verwalteten Identitäten in einem Azure AD-Verzeichnis.

1. Um eine Liste der Berechtigungen anzuzeigen, die verweigert wurden, klicken Sie auf **Verweigerte Berechtigungen**.

    ![Ablehnungszuweisung: verweigerte Berechtigungen](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Aktionstyp | BESCHREIBUNG |
    | --- | --- |
    | **Aktionen**  | Verweigerte Aktionen auf Steuerungsebene |
    | **NotActions** | Aktionen auf Steuerungsebene, die von verweigerten Aktionen der Steuerungsebene ausgeschlossen sind |
    | **DataActions**  | Verweigerte Aktionen auf Datenebene |
    | **NotDataActions** | Aktionen auf Datenebene, die von verweigerten Aktionen der Datenebene ausgeschlossen sind |

    Für das im vorstehenden Screenshot gezeigte Beispiel gelten die folgenden effektiven Berechtigungen:

    - Alle Speicheraktionen für die Datenebene bis auf Computeaktionen wurden verweigert.

1. Um die Eigenschaften für eine Ablehnungszuweisung anzuzeigen, klicken Sie auf **Eigenschaften**.

    ![Ablehnungszuweisung: Eigenschaften](./media/deny-assignments-portal/deny-assignment-properties.png)

    Auf dem Blatt **Eigenschaften** können Sie den Namen, die ID, die Beschreibung und den Bereich für eine Ablehnungszuweisung anzeigen. Die Option **Gilt nicht für untergeordnete Elemente** gibt an, ob die Ablehnungszuweisung an Unterbereiche vererbt wird. Die Option **Vom System definiert** gibt an, ob diese Ablehnungszuweisung durch Azure verwaltet wird. Aktuell lautet die Einstellung immer **Ja**.

## <a name="next-steps"></a>Nächste Schritte

* [Verstehen von Ablehnungszuweisungen für Azure-Ressourcen](deny-assignments.md)
* [Auflisten von Ablehnungszuweisungen für Azure-Ressourcen mit Azure PowerShell](deny-assignments-powershell.md)
