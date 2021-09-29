---
title: Verwenden des Self-Service-Anwendungszugriffs in Azure AD
description: Aktivieren von Self-Service, damit Benutzer Apps in Azure AD finden können
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: lenalepa
ms.openlocfilehash: 017f43152ed041d0b3b9a24bab151bb06acb9d3f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748478"
---
# <a name="how-to-use-self-service-application-access"></a>Verwenden des Self-Service-Anwendungszugriffs

Bevor Ihre Benutzer Anwendungen über die Seite „Meine Apps“ selbst ermitteln können, müssen Sie den **Self-Service-Anwendungszugriff** auf alle Anwendungen aktivieren, die Benutzer selbst ermitteln und für die sie den Zugriff anfordern können sollen.

Diese Funktion ermöglicht Ihnen als IT-Abteilung, Zeit und Geld zu sparen, und empfiehlt sich unbedingt als Bestandteil einer modernen Anwendungsbereitstellung mit Azure Active Directory.

Informationen zur Verwendung von „Meine Apps“ aus der Sicht eines Endbenutzers finden Sie unter [Hilfe zum Portal „Meine Apps“](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

Mit dieser Funktion können Sie die folgenden Schritte ausführen:

- Es Benutzern ermöglichen, Anwendungen über [„Meine Apps“](https://myapps.microsoft.com/) ohne Zutun der IT-Abteilung selbst zu ermitteln.
- Diese Benutzer einer vorkonfigurierten Gruppe hinzufügen, sodass Sie sehen können, welcher Benutzer den Zugriff angefordert hat, sowie den Zugriff aufheben und die den Benutzern zugewiesenen Rollen verwalten können
- Optional einer Person das Genehmigen von App-Zugriffsanforderungen ermöglichen, damit die IT-Abteilung dies nicht erledigen muss.
- Optional bis zu 10 Personen konfigurieren, die den Zugriff auf eine Anwendung genehmigen können
- Optional einer Person das Festlegen von Kennwörtern ermöglichen, mit denen sich diese Benutzer bei der Anwendung anmelden können.
- Optional Benutzer mit Self-Service-Zuweisung automatisch direkt einer Anwendungsrolle zuweisen

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivieren des Self-Service-Anwendungszugriffs, um Benutzern die Suche ihrer eigenen Anwendungen zu ermöglichen

Der Self-Service-Anwendungszugriff bietet die Möglichkeit, dass Benutzer Anwendungen selbst ermitteln können und die entsprechende Geschäftseinheit den Zugriff auf diese Anwendungen optional genehmigen kann. Sie können der Geschäftseinheit das Verwalten der Anmeldeinformationen ermöglichen, die diesen Benutzern zugewiesen wurden, damit sie direkt über ihre Seite „Meine Apps“ auf „Anwendungen mit einmaligem Anmelden per Kennwort zugreifen“ können.

Führen Sie die folgenden Schritte aus, um den Self-Service-Anwendungszugriff auf eine Anwendung zu aktivieren:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.
5. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.
   - Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.
6. Wählen Sie in der Liste die Anwendung aus, für die Sie den Self-Service-Zugriff aktivieren möchten.
7. Nachdem die Anwendung geladen wurde, wählen Sie im linken Navigationsmenü der Anwendung **Self-Service** aus.
8. Um den Self-Service-Anwendungszugriff auf die Anwendung zu aktivieren, legen Sie **Benutzern das Anfordern des Zugriffs auf diese Anwendung erlauben?** auf **Ja** fest.
9. Zum Auswählen der Gruppe, der Benutzer, die Zugriff auf diese Anwendung anfordern, hinzugefügt werden sollen, wählen Sie als Nächstes den Selektor neben **Welcher Gruppe sollen zugewiesene Benutzer hinzugefügt werden?** und dann die gewünschte Gruppe aus.
10. **Optional:** Wenn eine Genehmigung des Unternehmens erforderlich sein soll, damit Benutzer Zugriff erhalten, legen Sie **Genehmigung anfordern, bevor Zugriff auf diese Anwendung gewährt wird?** auf **Ja** fest.
11. **Optional: nur für Anwendungen mit einmaligem Anmelden per Kennwort:** Wenn Sie möchten, dass die genehmigenden Personen des Unternehmens die für genehmigte Benutzer an die Anwendung gesendeten Kennwörter angeben können, legen Sie **Genehmigenden Personen das Festlegen von Benutzerkennwörtern für diese Anwendung gestatten?** auf **Ja** fest.
12. **Optional:** Geben Sie die genehmigenden Personen des Unternehmens an, denen der Zugriff auf diese App gestattet werden darf. Wählen Sie **Wer darf den Zugriff auf diese Anwendung genehmigen?** aus. Wählen Sie dann bis zu 10 genehmigende Personen des Unternehmens aus.
    - Gruppen werden nicht unterstützt.
13. **Optional:** Wenn Sie **bei Anwendungen, die Rollen verfügbar machen**, den für den Self-Service genehmigten Benutzern eine Rolle zuweisen möchten, wählen Sie den Selektor neben **Welcher Rolle sollen Benutzer in dieser Anwendung zugewiesen werden?** und dann die Rolle aus, denen diese Benutzer zugewiesen werden sollen.
14. Wählen Sie oben die Schaltfläche **Speichern** aus, um den Vorgang zu beenden.

Nachdem Sie die Self-Service-Anwendungskonfiguration abgeschlossen haben, können Benutzer zu [Meine Apps](https://myapps.microsoft.com/) navigieren und die Schaltfläche **+Hinzufügen** auswählen, um die Apps zu suchen, für die Sie den Self-Service-Zugriff aktiviert haben. Außerdem wird genehmigenden Personen des Unternehmens auf der Seite [„Meine Apps“](https://myapps.microsoft.com/) eine Benachrichtigung angezeigt. Sie können festlegen, dass sie in einer E-Mail darüber benachrichtigt werden, dass ein Benutzer den Zugriff auf eine Anwendung angefordert hat, der zu genehmigen ist.

Diese Genehmigungen unterstützen nur Workflows mit einzelnen Genehmigungen. Das bedeutet, dass bei der Angabe mehrerer genehmigender Personen jede einzelne genehmigende Person den Zugriff auf die Anwendung genehmigen kann.

## <a name="things-to-check-if-self-service-isnt-working"></a>Zu überprüfende Aspekte, wenn der Self-Service nicht funktioniert

- Stellen Sie sicher, dass der Benutzer oder die Gruppe für die Anforderung des Self-Service-Anwendungszugriffs aktiviert wurde.
- Stellen Sie sicher, dass die Benutzer die richtige Stelle für den Self-Service-Anwendungszugriff verwenden. Benutzer können zu ihrer Seite [„Meine Apps“](https://myapps.microsoft.com/) navigieren und die Schaltfläche **+Hinzufügen** auswählen, um die Apps zu suchen, für die Sie den Self-Service-Zugriff aktiviert haben.
- Wenn der Self-Service-Anwendungszugriff kürzlich konfiguriert wurde, probieren Sie Folgendes aus: Melden Sie sich von „Meine Apps“ des Benutzers ab und nach einigen Minuten dort wieder an, um zu sehen, ob die Änderungen am Self-Service-Zugriff angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

[Einrichten von Azure Active Directory zur Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md)