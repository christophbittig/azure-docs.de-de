---
title: 'Tutorial: Verwalten des Lebenszyklus Ihrer gruppenbasierten Lizenzen in Azure AD'
description: Schritt-für-Schritt-Tutorial zur Erstellung eines Zugriffspakets für die Verwaltung von gruppenbasierten Lizenzen über die Azure Active Directory-Berechtigungsverwaltung.
services: active-directory
documentationCenter: ''
author: sama
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 08/18/2021
ms.author: sama
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8577da92657086edeb0d901c54693faa3cabab92
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095224"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>Tutorial: Verwalten des Lebenszyklus Ihrer gruppenbasierten Lizenzen in Azure AD
 
Mit Azure Active Directory können Sie Gruppen verwenden, um die [Lizenzen für Ihre Anwendungen](/active-directory/enterprise-users/licensing-groups-assign.md) zu verwalten. Sie können die Verwaltung dieser Gruppe noch einfacher gestalten, indem Sie die Berechtigungsverwaltung verwenden. 

*   Konfigurieren Sie regelmäßige Zugriffsüberprüfungen, um sicherzustellen, dass sich in der Gruppe nur die Benutzer befinden, von denen die Lizenzen benötigt werden. 
*   Zulassen, dass andere Benutzer die Mitgliedschaft in der Gruppe anfordern

In diesem Tutorial arbeiten Sie als IT-Administrator für die WoodGrove Bank. Sie wurden gebeten, für Ihre Organisation ein Zugriffspaket zu erstellen, um den problemlosen Zugriff auf Office-Lizenzen zu ermöglichen. Sie sollten bereits über eine Gruppe verfügen, über die Ihre [Office-Lizenzen](/active-directory/enterprise-users/licensing-groups-assign.md) verwaltet werden. Diese Benutzer müssen jährlich überprüft werden, und neue Benutzer können Office-Lizenzen anfordern, aber sie müssen auf die Genehmigung des Vorgesetzten warten. Um die Azure AD-Berechtigungsverwaltung verwenden zu können, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Lizenz vom Typ „Enterprise Mobility + Security (EMS) E5“. Weitere Informationen finden Sie unter [Lizenzanforderungen](entitlement-management-overview.md#license-requirements).
## <a name="step-1-configure-basics-for-your-access-package"></a>Schritt 1: Konfigurieren der Grundlagen für Ihr Zugriffspaket

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im **Azure-Portal** im linken Navigationsbereich auf **Azure Active Directory**.

2. Klicken Sie im linken Menü auf **Identity Governance**.

3. Klicken Sie im linken Menü auf **Zugriffspakete**. 

4. Klicken Sie auf **Neues Zugriffspaket**.

5. Geben Sie auf der Registerkarte **Grundlagen** den Namen **Office-Lizenzen** und die Beschreibung **Zugriff auf Lizenzen für Office-Anwendungen** ein.

6. Sie können für die Dropdownliste **Katalog** die Einstellung **Allgemein** übernehmen.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>Schritt 2: Konfigurieren der Ressourcen für Ihr Zugriffspaket

1. Klicken Sie auf **Weiter**, um die Registerkarte **Ressourcenrollen** zu öffnen.

2. Wählen Sie auf dieser Registerkarte die Ressourcen und die Ressourcenrolle aus, die in das Zugriffspaket aufgenommen werden sollen. In diesem Beispielszenario klicken Sie auf **Gruppen und Teams** und suchen nach Ihrer Gruppe, der [Office-Lizenzen](/active-directory/enterprise-users/licensing-groups-assign.md) zugewiesen wurden.

3. Wählen Sie in der Dropdownliste **Rolle** den Eintrag **Mitglied** aus.

## <a name="step-3-configure-requests-for-your-access-package"></a>Schritt 3: Konfigurieren von Anforderungen für Ihr Zugriffspaket

1. Klicken Sie auf **Weiter**, um die Registerkarte **Anforderungen** zu öffnen.

2. Diese Registerkarte dient zum Erstellen einer Anforderungsrichtlinie. In einer *Richtlinie* sind die Regeln oder Leitlinien für den Zugriff auf ein Zugriffspaket definiert. Sie erstellen eine Richtlinie, die es einem bestimmten Benutzer im Ressourcenverzeichnis ermöglicht, dieses Zugriffspaket anzufordern.

3. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für in Ihrem Verzeichnis befindliche Benutzer**, und wählen Sie die Option **Alle Mitglieder (keine Gäste)** aus. So können Sie erreichen, dass nur Mitglieder Ihres Verzeichnisses Office-Lizenzen anfordern können.

4. Stellen Sie sicher, dass **Genehmigung erforderlich** auf **Ja** festgelegt ist.

5. Behalten Sie für **Begründung des Anforderers erforderlich** die Option **Ja** bei.

6. Übernehmen Sie für **Wie viele Phasen** den Wert **1**.

7. Wählen Sie unter **Genehmigende Person** die Option **Vorgesetzter als genehmigende Person** aus. Bei Auswahl dieser Option kann der Vorgesetzte der anfordernden Person die Anforderung genehmigen. Sie können auch eine andere genehmigende Person auswählen, falls der Vorgesetzte nicht gefunden werden kann.

8. Übernehmen Sie für **Zulässige Anzahl von Tagen für Entscheidungsfindung** den Wert **14**.

9. Behalten Sie für **Begründung der genehmigenden Person erforderlich** die Option **Ja** bei.

10. Legen Sie **Neue Anforderungen und Zuweisungen aktivieren** auf **Ja** fest, damit dieses Zugriffspaket angefordert werden kann, sobald es erstellt wurde.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>Schritt 4: Konfigurieren der Informationen zum Anforderer für Ihr Zugriffspaket

1. Klicken Sie auf **Weiter**, um die Registerkarte **Informationen zum Anforderer** zu öffnen.

2. Auf diesem Bildschirm können Sie zusätzliche Fragen stellen, um weitere Informationen von Ihrem Anforderer zu erfassen. Diese Fragen werden im Anforderungsformular angezeigt und können als erforderlich oder optional festgelegt werden. Da Sie im Rahmen dieses Szenarios nicht aufgefordert wurden, Informationen zum Anforderer für dieses Zugriffspaket anzugeben, können Sie diese Felder leer lassen.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>Schritt 5: Konfigurieren des Lebenszyklus für Ihr Zugriffspaket

1. Klicken Sie auf **Weiter**, um die Registerkarte **Lebenszyklus** zu öffnen.

2. Legen Sie im Abschnitt **Ablauf** die Option **Die Zugriffspaketzuweisungen laufen ab.** auf **Anzahl Tage** fest.
    
3. Legen Sie **Zuweisungen laufen ab nach:** auf **365** Tage fest. Mit dem Wert in diesem Feld wird bestimmt, wann Mitglieder, die Zugriff auf dieses Zugriffspaket haben, ihre Zugriffsberechtigung verlängern müssen. 

4. Sie können auch **Zugriffsüberprüfungen** konfigurieren, damit regelmäßig überprüft wird, ob der Benutzer weiterhin Zugriff auf das Zugriffspaket benötigt. Eine Überprüfung kann entweder vom Benutzer selbst durchgeführt werden, oder Sie können für diese Aufgabe den Vorgesetzten oder einen anderen bestimmten Prüfer festlegen. Weitere Informationen finden Sie unter [Zugriffsüberprüfungen](entitlement-management-access-reviews-create.md). Bei diesem Szenario soll jeder Benutzer jährlich überprüfen, ob er noch eine Lizenz für Office benötigt.

    1.  Legen Sie **Zugriffsüberprüfungen erforderlich** auf **Ja** fest.
    2.  Sie können für die Option **Startet am** das aktuelle Datum übernehmen. Mit diesem Startdatum wird angegeben, wann die Zugriffsüberprüfung beginnt. Nachdem eine Zugriffsüberprüfung erstellt wurde, können Sie ihr Startdatum nicht mehr ändern.
    3.  Legen Sie die Option **Häufigkeit der Überprüfung** auf **Jährlich** fest, da die Überprüfung einmal pro Jahr erfolgen soll. Im Feld „Häufigkeit der Überprüfung“ geben Sie an, wie häufig die Zugriffsüberprüfung durchgeführt werden soll.
    4.  Geben Sie die **Dauer (in Tagen)** an.  Im Feld für die Dauer geben Sie an, wie viele Tage lang die Zugriffsüberprüfung jeweils durchgeführt wird.
    5.  Wählen Sie unter **Prüfer** die Option **Vorgesetzte(r)** aus.

## <a name="step-6-review-and-create-your-access-package"></a>Schritt 6: Überprüfen und Erstellen Ihres Zugriffspakets

1. Klicken Sie auf **Weiter**, um die Registerkarte **Überprüfen + erstellen** zu öffnen.

2. Auf diesem Bildschirm können Sie die Konfiguration für Ihr Zugriffspaket überprüfen, bevor Sie es erstellen. Bei Problemen können Sie mithilfe der Registerkarten zu einem bestimmten Punkt im Erstellungsvorgang navigieren, um Änderungen vorzunehmen.

3. Klicken Sie auf **Erstellen**, wenn Sie mit Ihrer Auswahl zufrieden sind. Nach einigen Augenblicken sollte eine Benachrichtigung angezeigt werden, dass das Zugriffspaket erfolgreich erstellt wurde.

4. Nachdem das Zugriffspaket erstellt wurde, wird die Seite **Übersicht** für Ihr Zugriffspaket angezeigt. Diese Seite enthält den **Link zum Portal „Mein Zugriff“** . Kopieren Sie den Link, und geben Sie ihn an Ihr Team weiter, damit die Mitglieder das Zugriffspaket anfordern und Lizenzen für Office erhalten können.

## <a name="step-7-clean-up-resources"></a>Schritt 7: Bereinigen der Ressourcen

In diesem Schritt können Sie das Zugriffspaket **Office-Lizenzen** löschen. 

**Erforderliche Rolle:** Unternehmensadministrator, Identity Governance-Administrator oder Zugriffspaket-Manager

1. Klicken Sie im **Azure-Portal** im linken Navigationsbereich auf **Azure Active Directory**.

2. Klicken Sie im linken Menü auf **Identity Governance**.

3. Klicken Sie im linken Menü auf **Zugriffspakete**. 

4. Öffnen Sie das Zugriffspaket **Office-Lizenzen**. 

5. Klicken Sie auf **Ressourcenrollen**.

6. Wählen Sie die Gruppe aus, die Sie diesem Zugriffspaket hinzugefügt haben, und klicken Sie im Detailbereich auf **Ressourcenrolle entfernen**. Klicken Sie in der angezeigten Meldung auf **Ja**.

7. Öffnen Sie die Liste der Zugriffspakete.

8. Klicken Sie unter **Office-Lizenzen** auf die Auslassungszeichen (...) und dann auf **Löschen**. Klicken Sie in der angezeigten Meldung auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Erstellen von Zugriffspaketen, um den Zugriff auf andere Arten von Ressourcen wie Anwendungen und Sites zu verwalten. [Tutorial: Verwalten des Zugriffs auf Ressourcen in der Azure AD-Berechtigungsverwaltung](/active-directory/governance/entitlement-management-access-package-first.md)
