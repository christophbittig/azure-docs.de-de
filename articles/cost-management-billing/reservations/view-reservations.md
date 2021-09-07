---
title: Berechtigungen zum Anzeigen und Verwalten von Azure-Reservierungen
description: Hier erfahren Sie, wie Sie Azure-Reservierungen im Azure-Portal anzeigen und verwalten.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: d7bb27e511d799a0afe203b8147546647cc658fd
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989073"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Berechtigungen zum Anzeigen und Verwalten von Azure-Reservierungen

In diesem Artikel wird erläutert, wie Reservierungsberechtigungen funktionieren und wie Benutzer Azure-Reservierungen im Azure-Portal anzeigen und verwalten können.

## <a name="who-can-manage-a-reservation-by-default"></a>Wer kann eine Reservierung standardmäßig verwalten?

Die folgenden Benutzer können Reservierungen standardmäßig anzeigen und verwalten:

- Die Person, die eine Reservierung erwirbt, und der Kontoadministrator des Abrechnungsabonnements, mit dem die Reservierung erworben wird, werden der Reservierungsreihenfolge hinzugefügt.
- Abrechnungsadministratoren für Konzernvertrag (Enterprise Agreement, EA) und Microsoft-Kundenvereinbarung.
- Benutzer mit erhöhten Zugriffsrechten zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen

Der Reservierungslebenszyklus ist unabhängig von einem Azure-Abonnement. Daher handelt es sich bei der Reservierung nicht um eine Ressource unter dem Azure-Abonnement. Stattdessen ist es eine Ressource auf Mandantenebene mit eigener Azure RBAC-Berechtigung, die von Abonnements getrennt ist. Reservierungen erben nach dem Kauf keine Berechtigungen von Abonnements.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Anzeigen oder Verwalten von Reservierungen durch Abrechnungsadministratoren

Führen Sie als Abrechnungsadministrator die folgenden Schritte aus, um alle Reservierungen und Reservierungstransaktionen anzuzeigen und zu verwalten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Kostenverwaltung + Abrechnung**.
    - Wenn Sie EA-Administrator sind, wählen Sie im Menü auf der linken Seite die Option **Abrechnungsbereiche** und dann in der Liste der Abrechnungsbereiche einen Bereich aus.
    - Wenn Sie Besitzer eines Abrechnungsprofils für eine Microsoft-Kundenvereinbarung sind, wählen Sie im linken Menü **Abrechnungsprofile** aus. Wählen Sie in der Liste der Abrechnungsprofile ein Profil aus.
1. Wählen Sie im Menü auf der linken Seite **Produkte und Dienste** > **Reservierungen** aus.
1. Die vollständige Liste der Reservierungen für Ihr EA-Registrierungsprofil oder -Abrechnungsprofil wird angezeigt.
1. Abrechnungsadministratoren können den Besitz einer Reservierung übernehmen, indem sie sie auswählen und dann im angezeigten Fenster die Option **Zugriff gewähren** auswählen.

### <a name="how-to-add-billing-administrators"></a>Hinzufügen von Abrechnungsadministratoren

Hinzufügen eines Benutzers als Abrechnungsadministrator zu einem Konzernvertrag oder einer Microsoft-Kundenvereinbarung:

- Fügen Sie bei einem Konzernvertrag Benutzer mit der Rolle _Unternehmensadministrator_ hinzu, um alle für diesen Vertrag geltenden Reservierungsaufträge anzuzeigen und zu verwalten. Unternehmensadministratoren können Reservierungen in **Cost Management + Billing** anzeigen und verwalten.
    - Benutzer mit der Rolle _Unternehmensadministrator (schreibgeschützt)_ können die Reservierung nur in **Cost Management + Billing** anzeigen. 
    - Abteilungsadministratoren und Kontobesitzer können Reservierungen nicht anzeigen, _außer_ wenn sie ihnen mithilfe der Zugriffssteuerung (Access Control, IAM) explizit hinzugefügt werden. Weitere Informationen finden Sie unter [Verwalten von Azure Enterprise-Rollen](../manage/understand-ea-roles.md).
- Bei einer Microsoft-Kundenvereinbarung können Benutzer mit der Rolle „Besitzer des Abrechnungsprofils“ oder „Mitwirkender am Abrechnungsprofil“ alle mit dem Abrechnungsprofil getätigten Reservierungseinkäufe verwalten. Benutzer mit Leseberechtigung für das Abrechnungsprofil und Rechnungs-Manager können alle Reservierungen anzeigen, die für das Abrechnungsprofil bezahlt werden. Sie können allerdings keine Änderungen an den Reservierungen vornehmen.
    Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Anzeigen von Reservierungen mit Azure RBAC-Zugriff

Wenn Sie die Reservierung erworben haben oder einer Reservierung hinzugefügt werden, führen Sie die folgenden Schritte aus, um Reservierungen anzuzeigen und zu verwalten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Dienste** > **Reservierungen** aus, um Reservierungen aufzulisten, auf die Sie Zugriff haben.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Benutzer mit erhöhten Zugriffsrechten können alle Azure-Abonnements und Verwaltungsgruppen verwalten.

Sie können die [Zugriffsrechte eines Benutzers zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen erhöhen](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json).

Nach dem Erhöhen der Zugriffsberechtigungen:

1. Navigieren Sie zu **Alle Dienste** > **Reservierung**, um alle Reservierungen im Mandanten anzuzeigen.
1. Um Änderungen an der Reservierung vorzunehmen, fügen Sie sich selbst mithilfe der Zugriffssteuerung (IAM) als Besitzer des Reservierungsauftrags hinzu.

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Gewähren von Azure RBAC-Zugriff auf einzelne Reservierungen für Benutzer

Benutzer mit Besitzerzugriff auf die Reservierungen und Abrechnungsadministratoren können die Zugriffsverwaltung für einen einzelnen Reservierungsauftrag delegieren.

Damit andere Personen Reservierungen verwalten können, haben Sie zwei Optionen:

- Delegieren Sie die Zugriffsverwaltung für einen einzelnen Reservierungsauftrag, indem Sie einem Benutzer im Ressourcenbereich des Reservierungsauftrags die Rolle „Besitzer“ zuweisen. Wenn Sie eingeschränkten Zugriff gewähren möchten, wählen Sie eine andere Rolle aus.  
     Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

- Hinzufügen eines Benutzers als Abrechnungsadministrator zu einem Konzernvertrag oder einer Microsoft-Kundenvereinbarung:
    - Fügen Sie bei einem Konzernvertrag Benutzer mit der Rolle _Unternehmensadministrator_ hinzu, um alle für diesen Vertrag geltenden Reservierungsaufträge anzuzeigen und zu verwalten. Benutzer mit der Rolle _Unternehmensadministrator (schreibgeschützt)_ können die Reservierung nur anzeigen. Abteilungsadministratoren und Kontobesitzer können Reservierungen nicht anzeigen, _außer_ wenn sie ihnen mithilfe der Zugriffssteuerung (Access Control, IAM) explizit hinzugefügt werden. Weitere Informationen finden Sie unter [Verwalten von Azure Enterprise-Rollen](../manage/understand-ea-roles.md).

        _Unternehmensadministratoren können den Besitz einer Reservierungsreihenfolge übernehmen und andere Benutzer mithilfe der Zugriffssteuerung (Identity & Access Management, IAM) zu einer Reservierung hinzufügen._
    - Bei einer Microsoft-Kundenvereinbarung können Benutzer mit der Rolle „Besitzer des Abrechnungsprofils“ oder „Mitwirkender am Abrechnungsprofil“ alle mit dem Abrechnungsprofil getätigten Reservierungseinkäufe verwalten. Benutzer mit Leseberechtigung für das Abrechnungsprofil und Rechnungs-Manager können alle Reservierungen anzeigen, die für das Abrechnungsprofil bezahlt werden. Sie können allerdings keine Änderungen an den Reservierungen vornehmen.
    Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)