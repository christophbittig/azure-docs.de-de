---
title: Erneuern von Zuweisungen von Azure-Ressourcenrollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Zuweisungen von Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) verlängern oder erneuern.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/19/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b8fb2eb7f6301762fcba9931dfd9da6c54b679
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253674"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Verlängern oder Erneuern von Zuweisungen von Azure-Ressourcenrollen in Privileged Identity Management

In Azure Active Directory (Azure AD) Privileged Identity Management (PIM) werden neue Steuerelemente zum Verwalten des Zugriffs und des Zuweisungslebenszyklus für Azure-Ressourcen bereitgestellt. Administratoren können Rollen zuweisen, indem sie Eigenschaften für die Start- und Endzeit verwenden. Wenn das Ende der Zuweisung fast erreicht ist, sendet Privileged Identity Management E-Mail-Benachrichtigungen an die betroffenen Benutzer oder Gruppen. Außerdem werden E-Mail-Benachrichtigungen an Administratoren der Ressource gesendet, um einen entsprechenden Zugriff zu gewährleisten. Zuweisungen können auch verlängert werden. Sie bleiben im abgelaufenen Zustand noch 30 Tage lang sichtbar, auch wenn der Zugriff nicht verlängert wird.

## <a name="who-can-extend-and-renew"></a>Wer kann das Verlängern und Erneuern durchführen?

Nur Administratoren der Ressource können Rollenzuweisungen verlängern oder erneuern. Der betroffene Benutzer bzw. die betroffene Gruppe kann anfordern, dass in Kürze ablaufende Rollen verlängert und dass bereits abgelaufene Rollen erneuert werden.

## <a name="when-are-notifications-sent"></a>Wann werden die Benachrichtigungen gesendet?

Privileged Identity Management sendet E-Mail-Benachrichtigungen an Administratoren und betroffene Benutzer und Gruppen mit Rollen, die innerhalb der nächsten 14 Tage ablaufen. Einen Tag vor dem Ablauf wird eine weitere Benachrichtigung gesendet. Wenn eine Zuweisung offiziell abgelaufen ist, wird ebenfalls eine E-Mail gesendet.

Administratoren erhalten Benachrichtigungen, wenn ein Benutzer oder eine Gruppe mit einer ablaufenden oder abgelaufenen Rolle die Verlängerung bzw. Erneuerung anfordert. Wenn ein bestimmter Administrator die Anforderung bearbeitet, werden alle anderen Administratoren über die getroffene Entscheidung (Genehmigung oder Ablehnung) informiert. Anschließend wird der anfordernde Benutzer bzw. die Gruppe ebenfalls über die Entscheidung informiert.

## <a name="extend-role-assignments"></a>Verlängern von Rollenzuweisungen

Die folgenden Schritte beschreiben den Prozess für das Anfordern, Bearbeiten bzw. Verwalten einer Verlängerung oder Erneuerung einer Rollenzuweisung.

### <a name="self-extend-expiring-assignments"></a>Selbständiges Verlängern von ablaufenden Zuweisungen

Benutzer, die einer Rolle zugewiesen sind, können die Verlängerung von ablaufenden Rollenzuweisungen direkt über die Registerkarte **Berechtigt** oder **Aktiv** auf der Seite **Meine Rollen** einer Ressource und auf der übergeordneten Seite **Meine Rollen** im Privileged Identity Management-Portal durchführen. Im Portal können Benutzer die Verlängerung von berechtigten oder aktiven (zugewiesenen) Rollen anfordern, die in den nächsten 14 Tagen ablaufen.

![Azure-Ressourcen – Seite „Meine Rollen“, auf der die berechtigten Rollen mit der Spalte „Aktion“ aufgelistet werden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Wenn der Endzeitpunkt der Zuweisung innerhalb von 14 Tagen liegt, wird die Schaltfläche **Verlängern** im Azure-Portal aktiviert. Im folgenden Beispiel wird angenommen, dass der 27. März das aktuelle Datum ist.

>[!Note]
>Für eine Gruppe, die einer Rolle zugewiesen ist, steht der Link **Verlängern** nie zur Verfügung, damit ein Benutzer mit einer geerbten Zuordnung nicht die Gruppenzuordnung verlängern kann.

![Spalte „Aktion“ mit Links zum Aktivieren oder Verlängern](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Wählen Sie **Verlängern**, um das Anforderungsformular zu öffnen und eine Verlängerung dieser Rollenzuweisung anzufordern.

![Bereich zum Verlängern einer Rollenzuweisung mit dem Feld „Grund“](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Erweitern Sie **Zuweisungsdetails**, um Informationen zur ursprünglichen Zuweisung anzuzeigen. Geben Sie einen Grund für die Verlängerungsanforderung an, und wählen Sie **Verlängern**.

>[!NOTE]
>Wir empfehlen Ihnen, die Details zum Grund der Verlängerung einzufügen und außerdem den Verlängerungszeitraum anzugeben (falls bekannt).

![Bereich zum Verlängern der Rollenzuweisung mit erweiterten Zuweisungsdetails](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Die Ressourcenadministratoren erhalten nach kurzer Zeit eine E-Mail-Benachrichtigung mit der Aufforderung, die Verlängerungsanforderung zu überprüfen. Falls bereits eine Verlängerungsanforderung übermittelt wurde, wird im Portal eine Benachrichtigung angezeigt.

![Benachrichtigung, in der erklärt wird, dass bereits eine ausstehende Anforderung zur Verlängerung einer Rollenzuweisung vorhanden ist](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Navigieren Sie zur Seite **Ausstehende Anforderungen**, um den Status der Anforderung anzuzeigen oder sie zu stornieren.

![Azure-Ressourcen – Seite „Ausstehende Anforderungen“ mit einer Auflistung ausstehender Anforderungen und einem Link zum Stornieren](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Vom Administrator genehmigte Verlängerung

Wenn ein Benutzer oder eine Gruppe eine Anforderung zur Verlängerung einer Rollenzuweisung übermittelt, erhalten die Ressourcenadministratoren eine E-Mail-Benachrichtigung mit den Details der ursprünglichen Zuweisung und dem Grund der Anforderung. Die Benachrichtigung enthält einen direkten Link zu der Anforderung, damit der Administrator diese genehmigen oder ablehnen kann.

Zusätzlich zur Verwendung des Links aus der E-Mail können Administratoren Anforderungen genehmigen oder ablehnen, indem sie zum Privileged Identity Management-Verwaltungsportal wechseln und im linken Bereich die Option **Anforderung genehmigen** auswählen.

![Azure-Ressourcen – Seite „Anforderungen genehmigen“ mit einer Auflistung von Anforderungen und Links zum Genehmigen oder Ablehnen](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Wenn ein Administrator die Option **Genehmigen** oder **Ablehnen** wählt, werden die Details der Anforderung zusammen mit einem Feld zum Angeben der geschäftlichen Begründung für die Überwachungsprotokolle angezeigt.

![Bereich zum Genehmigen der Rollenzuweisungsanforderung mit der Begründung des Anforderers, dem Zuweisungstyp, der Startzeit, der Endzeit und dem Grund](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Beim Genehmigen einer Anforderung zur Verlängerung der Rollenzuweisung können Ressourcenadministratoren einen neuen Start- und Endzeitpunkt und einen neuen Zuweisungstyp wählen. Das Ändern des Zuweisungstyps kann erforderlich sein, wenn der Administrator begrenzten Zugriff gewähren möchte (z.B. nur für einen Tag), damit eine bestimmte Aufgabe erfüllt werden kann. In diesem Beispiel kann der Administrator die Zuweisung von **Berechtigt** in **Aktiv** ändern. Das bedeutet, dass sie dem Anfordernden Zugriff gewähren können, ohne dass er aktiviert werden muss.

### <a name="admin-initiated-extension"></a>Vom Administrator initiierte Verlängerung

Wenn ein Benutzer, der einer Rolle zugewiesen ist, keine Verlängerung für die Rollenzuweisung anfordert, kann ein Administrator eine Zuweisung auch im Namen des Benutzers verlängern. Für Verlängerungen von Rollenzuweisungen durch Administratoren sind keine Genehmigungen erforderlich, nach Verlängerung der Rolle werden jedoch Benachrichtigungen an alle anderen Administratoren gesendet.

Um eine Rollenzuweisung zu verlängern, wechseln Sie zu der Ressourcenrollen- oder Zuweisungsansicht in Privileged Identity Management. Suchen Sie die Zuweisung, für die eine Verlängerung erforderlich ist. Wählen Sie dann **Verlängern** in der Aktionsspalte.

![Azure-Ressourcen – Seite „Zuweisungen“, auf der die berechtigten Rollen mit Links zum Verlängern aufgelistet werden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Erneuern von Rollenzuweisungen

Der Prozess zum Erneuern einer abgelaufenen Rollenzuweisung ist vom Konzept her zwar ähnlich dem Prozess zum Anfordern einer Verlängerung, es bestehen jedoch auch Unterschiede. Mit den folgenden Schritten können Zuweisungen und Administratoren den Zugriff auf abgelaufene Rollen bei Bedarf erneuern.

### <a name="self-renew"></a>Selbstverlängerung

Benutzer, die nicht mehr auf Ressourcen zugreifen können, können bis zu 30 Tage auf den Verlauf der abgelaufenen Zuweisungen zugreifen. Navigieren Sie dazu im linken Bereich zu **Meine Rollen** und wählen Sie die Registerkarte **Abgelaufene Rollen** im Abschnitt der Azure-Ressourcenrollen.

![Seite „Meine Rollen“ – Registerkarte „Abgelaufene Rollen“](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

In der Liste der Rollen werden die Standardeinstellungen für **Berechtigte Rollen** angezeigt. Verwenden Sie das Dropdownmenü, um zwischen berechtigten und aktiven zugewiesenen Rollen umzuschalten.

Wählen Sie die Aktion **Erneuern**, um für Rollenzuweisungen in der Liste eine Erneuerung anzufordern. Geben Sie dann einen Grund für Ihre Anforderung an. Es ist hilfreich, zusätzlich zum weiteren Kontext oder einer geschäftlichen Begründung einen Gültigkeitszeitraum anzugeben, um dem Ressourcenadministrator die Entscheidung über die Genehmigung oder Ablehnung zu erleichtern.

![Bereich zum Erneuern einer Rollenzuweisung mit dem Feld „Grund“](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Nach dem Übermitteln der Anforderung werden die Ressourcenadministratoren über eine ausstehende Anforderung zur Erneuerung einer Rollenzuweisung informiert.

### <a name="admin-approves"></a>Genehmigung durch Administrator

Ressourcenadministratoren können über den Link in der E-Mail-Benachrichtigung auf die Erneuerungsanforderung zugreifen, oder sie können über das Azure-Portal auf Privileged Identity Management zugreifen und im linken Bereich die Option **Anforderungen genehmigen** wählen.

![Azure-Ressourcen – Seite „Anforderungen genehmigen“ mit einer Auflistung von Anforderungen und Links zum Genehmigen oder Ablehnen](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Wenn ein Administrator die Option **Genehmigen** oder **Ablehnen** wählt, werden die Details der Anforderung zusammen mit einem Feld zum Angeben der geschäftlichen Begründung für die Überwachungsprotokolle angezeigt.

![Bereich zum Genehmigen der Rollenzuweisungsanforderung mit der Begründung des Anforderers, dem Zuweisungstyp, der Startzeit, der Endzeit und dem Grund](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Beim Genehmigen einer Anforderung zur Erneuerung der Rollenzuweisung müssen Ressourcenadministratoren einen neuen Start- und Endzeitpunkt und einen neuen Zuweisungstyp eingeben.

### <a name="admin-renew"></a>Erneuerung durch Administrator

Ressourcenadministratoren können abgelaufene Rollenzuweisungen im linken Navigationsmenü einer Ressource über die Registerkarte **Mitglieder** erneuern. Sie können darüber hinaus abgelaufene Rollenzuweisungen über die Registerkarte **Abgelaufene Rollen** einer Ressourcenrolle erneuern.

Um eine Liste mit allen abgelaufenen Rollenzuweisungen anzuzeigen, wählen Sie auf dem Bildschirm **Mitglieder** die Option **Abgelaufenen Rollen**.

![Azure-Ressourcen – Seite „Mitglieder“, auf der die abgelaufenen Rollen mit Links zum Erneuern aufgelistet werden](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Nächste Schritte

- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
