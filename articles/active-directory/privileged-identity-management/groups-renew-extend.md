---
title: Erneuern abgelaufener Gruppenbesitzer- oder Mitgliedszuweisungen in Privileged Identity Management – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Zuweisungen von Gruppen mit Rollenzuweisung in Azure AD Privileged Identity Management (PIM) verlängern oder erneuern.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.reviewer: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 791da880607de6e2e3c7ad9a9e94e41c50e5a4be
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667666"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Verlängern oder Erneuern von Zuweisungen von Gruppen mit privilegiertem Zugriff (Vorschau) in Privileged Identity Management

In Azure Active Directory (Azure AD) Privileged Identity Management (PIM) werden neue Steuerelemente zum Verwalten des Zugriffs und des Zuweisungslebenszyklus für Gruppen mit privilegiertem Zugriff bereitgestellt. Administratoren können Rollen zuweisen, indem sie Eigenschaften für die Start- und Endzeit verwenden. Wenn das Ende der Zuweisung fast erreicht ist, sendet Privileged Identity Management E-Mail-Benachrichtigungen an die betroffenen Benutzer oder Gruppen. Außerdem werden E-Mail-Benachrichtigungen an Administratoren der Ressource gesendet, um einen entsprechenden Zugriff zu gewährleisten. Zuweisungen können auch verlängert werden. Sie bleiben im abgelaufenen Zustand noch 30 Tage lang sichtbar, auch wenn der Zugriff nicht verlängert wird.

## <a name="who-can-extend-and-renew"></a>Wer kann das Verlängern und Erneuern durchführen?

Nur Administratoren der Ressource können Zuweisungen von Gruppen mit privilegiertem Zugriff verlängern oder erneuern. Der betroffene Benutzer bzw. die betroffene Gruppe kann anfordern, dass in Kürze ablaufende Zuweisungen verlängert und dass bereits abgelaufene Zuweisungen erneuert werden.

## <a name="when-notifications-are-sent"></a>Zeitpunkt des Sendens von Benachrichtigungen

Privileged Identity Management sendet Administratoren und betroffenen Benutzern E-Mail-Benachrichtigungen über ablaufende Zuweisungen von Gruppen mit privilegiertem Zugriff zu folgenden Zeitpunkten:

- Innerhalb von 14 Tagen vor Ablauf
- Einen Tag vor Ablauf
- Wenn eine Zuweisung abläuft

Administratoren erhalten Benachrichtigungen, wenn ein Benutzer oder eine Gruppe die Verlängerung bzw. Erneuerung einer ablaufenden oder abgelaufenen Zuweisung anfordert. Wenn ein Administrator die Anforderung bearbeitet, werden alle Administratoren und der anfordernde Benutzer über die Genehmigung oder Ablehnung informiert.

## <a name="extend-group-assignments"></a>Verlängern von Gruppenzuweisungen

Die folgenden Schritte beschreiben den Prozess für das Anfordern, Bearbeiten bzw. Verwalten einer Verlängerung oder Erneuerung einer Gruppenzuweisung.

### <a name="self-extend-expiring-assignments"></a>Selbständiges Verlängern von ablaufenden Zuweisungen

Benutzer, die einer Gruppe mit privilegiertem Zugriff zugewiesen sind, können ablaufende Gruppenzuweisungen direkt über die Registerkarte **Berechtigt** oder **Aktiv** auf der Seite **Zuweisungen** für die Gruppe verlängern. Benutzer oder Gruppen können die Verlängerung von berechtigten und aktiven Zuweisungen anfordern, die innerhalb der nächsten 14 Tage ablaufen.

![Seite „Meine Rollen“, auf der die berechtigten Zuweisungen mit der Spalte „Aktion“ aufgelistet werden](media/groups-renew-extend/self-extend-group-assignment.png)

Wenn das Enddatum der Zuweisung innerhalb der nächsten 14 Tagen liegt, ist der Befehl **Verlängern** verfügbar. Wählen Sie **Verlängern** aus, um das Anforderungsformular zu öffnen und eine Verlängerung einer Gruppenzuweisung anzufordern.

![Bereich zum Verlängern einer Gruppenzuweisung mit dem Feld „Grund“ und Details](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Wir empfehlen Ihnen, die Details zum Grund der Verlängerung einzufügen und außerdem den Verlängerungszeitraum anzugeben (falls bekannt).

Die Administratoren erhalten nach kurzer Zeit eine E-Mail-Benachrichtigung mit der Aufforderung, die Verlängerungsanforderung zu überprüfen. Falls bereits eine Verlängerungsanforderung übermittelt wurde, wird im Portal eine Benachrichtigung angezeigt.

Um den Status der Anforderung anzuzeigen oder sie abzubrechen, öffnen Sie die Seite **Ausstehende Anforderungen** für die Gruppenzuweisung.

![Zuweisungen von Gruppen mit privilegiertem Zugriff: Seite „Ausstehende Anforderungen“ mit dem Link zum Abbrechen](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Vom Administrator genehmigte Verlängerung

Wenn ein Benutzer oder eine Gruppe eine Anforderung zur Verlängerung einer Gruppenzuweisung übermittelt, erhalten die Administratoren eine E-Mail-Benachrichtigung mit den Details der ursprünglichen Zuweisung und dem Grund der Anforderung. Die Benachrichtigung enthält einen direkten Link zu der Anforderung, damit der Administrator diese genehmigen oder ablehnen kann.

Zusätzlich zur Verwendung des Links aus der E-Mail können Administratoren Anforderungen genehmigen oder ablehnen, indem sie zum Privileged Identity Management-Verwaltungsportal wechseln und im linken Bereich die Option **Anforderung genehmigen** auswählen.

![Zuweisungen von Gruppen mit privilegiertem Zugriff: Seite „Anforderungen genehmigen“ mit einer Auflistung von Anforderungen und Links zum Genehmigen oder Ablehnen](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Wenn ein Administrator die Option **Genehmigen** oder **Ablehnen** wählt, werden die Details der Anforderung zusammen mit einem Feld zum Angeben der geschäftlichen Begründung für die Überwachungsprotokolle angezeigt.

![Bereich zum Genehmigen der Gruppenzuweisungsanforderung mit der Begründung des Anforderers, dem Zuweisungstyp, der Startzeit, der Endzeit und dem Grund](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Beim Genehmigen einer Anforderung zur Verlängerung einer Gruppenzuweisung können Ressourcenadministratoren einen neuen Start- und Endzeitpunkt und einen neuen Zuweisungstyp wählen. Das Ändern des Zuweisungstyps kann erforderlich sein, wenn der Administrator begrenzten Zugriff gewähren möchte (z.B. nur für einen Tag), damit eine bestimmte Aufgabe erfüllt werden kann. In diesem Beispiel kann der Administrator die Zuweisung von **Berechtigt** in **Aktiv** ändern. Das bedeutet, dass sie dem Anfordernden Zugriff gewähren können, ohne dass er aktiviert werden muss.

### <a name="admin-initiated-extension"></a>Vom Administrator initiierte Verlängerung

Wenn ein Benutzer, der einer Gruppe zugewiesen ist, keine Verlängerung für die Gruppenzuweisung anfordert, kann ein Administrator eine Zuweisung auch im Namen des Benutzers verlängern. Für Verlängerungen von Gruppenzuweisungen durch Administratoren sind keine Genehmigungen erforderlich, nach Verlängerung der Zuweisung werden jedoch Benachrichtigungen an alle anderen Administratoren gesendet.

Um eine Gruppenzuweisung zu verlängern, wechseln Sie zu der Zuweisungsansicht in Privileged Identity Management. Suchen Sie die Zuweisung, für die eine Verlängerung erforderlich ist. Wählen Sie dann **Verlängern** in der Aktionsspalte.

![Seite „Zuweisungen“, auf der die berechtigten Gruppenzuweisungen mit Links zum Verlängern aufgelistet werden](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Erneuern von Gruppenzuweisungen

Der Prozess zum Erneuern einer abgelaufenen Gruppenzuweisung ähnelt vom Konzept her zwar dem Prozess zum Anfordern einer Verlängerung, es bestehen jedoch auch Unterschiede. Mit den folgenden Schritten können Zuweisungen und Administratoren den Zugriff auf abgelaufene Zuweisungen bei Bedarf erneuern.

### <a name="self-renew"></a>Selbstverlängerung

Benutzer, die nicht mehr auf Ressourcen zugreifen können, können bis zu 30 Tage auf den Verlauf der abgelaufenen Zuweisungen zugreifen. Navigieren Sie dazu im linken Bereich zu **Meine Rollen**, und wählen Sie dann die Registerkarte **Abgelaufene Zuweisungen** aus.

![Seite „Meine Rollen“: Registerkarte „Abgelaufene Zuweisungen“](media/groups-renew-extend/groups-renew-from-my-roles.png)

In der Liste mit den Zuweisungen werden standardmäßig **Berechtigte Zuweisungen** angezeigt. Verwenden Sie das Dropdownmenü, um zwischen berechtigten und aktiven Zuweisungen umzuschalten.

Wählen Sie die Aktion **Erneuern** aus, um für Gruppenzuweisungen in der Liste eine Erneuerung anzufordern. Geben Sie dann einen Grund für Ihre Anforderung an. Es ist hilfreich, zusätzlich zum weiteren Kontext oder einer geschäftlichen Begründung einen Gültigkeitszeitraum anzugeben, um dem Ressourcenadministrator die Entscheidung über die Genehmigung oder Ablehnung zu erleichtern.

![Bereich zum Erneuern einer Gruppenzuweisung mit dem Feld „Grund“](media/groups-renew-extend/groups-renew-request-form.png)

Nach dem Übermitteln der Anforderung werden die Ressourcenadministratoren über eine ausstehende Anforderung zur Erneuerung einer Gruppenzuweisung informiert.

### <a name="admin-approves"></a>Genehmigung durch Administrator

Ressourcenadministratoren können über den Link in der E-Mail-Benachrichtigung auf die Erneuerungsanforderung zugreifen, oder sie können über das Azure-Portal auf Privileged Identity Management zugreifen und im linken Bereich die Option **Anforderungen genehmigen** wählen.

Wenn ein Administrator die Option **Genehmigen** oder **Ablehnen** wählt, werden die Details der Anforderung zusammen mit einem Feld zum Angeben der geschäftlichen Begründung für die Überwachungsprotokolle angezeigt.

Beim Genehmigen einer Anforderung zur Erneuerung einer Gruppenzuweisung müssen Ressourcenadministratoren einen neuen Start- und Endzeitpunkt und einen neuen Zuweisungstyp eingeben.

## <a name="next-steps"></a>Nächste Schritte

- [Genehmigen oder Ablehnen von Anforderungen für Zuweisungen von Gruppen mit privilegiertem Zugriff in Privileged Identity Management](groups-approval-workflow.md)
- [Konfigurieren von Einstellungen für Gruppen mit privilegiertem Zugriff in Privileged Identity Management](groups-role-settings.md)
