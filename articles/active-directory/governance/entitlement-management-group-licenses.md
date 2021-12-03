---
title: Verwalten des Lebenszyklus gruppenbasierter Lizenzen in Azure AD
description: Dieses Schritt-für-Schritt-Tutorial zeigt die Erstellung eines Zugriffspakets für die Verwaltung gruppenbasierter Lizenzen über die Azure Active Directory-Berechtigungsverwaltung.
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
ms.openlocfilehash: 7d9b2f8ae5d679bf6642cd4cc2872e53b1234f11
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989164"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>Tutorial: Verwalten des Lebenszyklus Ihrer gruppenbasierten Lizenzen in Azure AD
 
Bei Azure Active Directory (Azure AD) können Sie die [Lizenzen für Ihre Anwendungen](../enterprise-users/licensing-groups-assign.md) anhand von Gruppen verwalten. Die Verwaltung dieser Gruppen lässt sich über die Berechtigungsverwaltung noch einfacher gestalten: 

* Konfigurieren Sie regelmäßige Zugriffsüberprüfungen, um sicherzustellen, dass sich in der Gruppe nur Mitarbeiter befinden, die die Lizenzen benötigen. 
* Erlauben Sie anderen Mitarbeitern, die Mitgliedschaft in der Gruppe anzufordern.

In diesem Tutorial übernehmen Sie die Rolle eines IT-Administrators für die Woodgrove Bank. Sie werden gebeten, ein Zugriffspaket zu erstellen, um den Mitarbeitern in Ihrer Organisation problemlosen Zugriff auf Office-Lizenzen zu ermöglichen. (Sie sollten bereits über eine Gruppe verfügen, die Ihre [Office-Lizenzen](../enterprise-users/licensing-groups-assign.md) verwaltet.) Sie möchten in der Lage sein, diese Gruppenmitglieder jedes Jahr zu überprüfen. Außerdem möchten Sie neuen Mitarbeitern gestatten, Office-Lizenzen mit Genehmigung des Vorgesetzten anzufordern.
 
Um die Azure AD-Berechtigungsverwaltung zu verwenden, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Weitere Informationen finden Sie unter [Lizenzanforderungen](entitlement-management-overview.md#license-requirements).
## <a name="step-1-configure-basics-for-your-access-package"></a>Schritt 1: Konfigurieren der Grundlagen für Ihr Zugriffspaket

**Erforderliche Rolle**: Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal im linken Bereich auf **Azure Active Directory**.

2. Wählen Sie unter **Verwalten** die Option **Identity Governance** aus.

3. Wählen Sie unter **Berechtigungsverwaltung** die Option **Zugriffspakete** aus. 

4. Wählen Sie **Neues Zugriffspaket** aus.

5. Geben Sie auf der Registerkarte **Grundlagen** im Feld **Name** den Namen **Office-Lizenzen** ein. Geben Sie im Feld **Beschreibung** die Beschreibung **Zugriff auf Lizenzen für Office-Anwendungen** ein.

6. Sie können den Eintrag **Allgemein** in der Liste **Katalog** übernehmen.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>Schritt 2: Konfigurieren der Ressourcen für Ihr Zugriffspaket

1. Wählen Sie **Weiter: Ressourcenrollen** aus, um zur Registerkarte **Ressourcenrollen** zu wechseln.

2. Wählen Sie auf dieser Registerkarte die Ressourcen und die Ressourcenrolle aus, die in das Zugriffspaket aufgenommen werden sollen. In diesem Szenario klicken Sie auf **Gruppen und Teams** und suchen nach Ihrer Gruppe, der [Office-Lizenzen](../enterprise-users/licensing-groups-assign.md) zugewiesen wurden.

3. Wählen Sie in der Liste **Rolle** die Option **Mitglied** aus.

## <a name="step-3-configure-requests-for-your-access-package"></a>Schritt 3: Konfigurieren von Anforderungen für Ihr Zugriffspaket

1. Wählen Sie **Weiter: Anforderungen** aus, um zur Registerkarte **Anforderungen** zu wechseln.

   Diese Registerkarte dient zum Erstellen einer Anforderungsrichtlinie. In einer *Richtlinie* sind die Regeln für den Zugriff auf ein Zugriffspaket definiert. Sie erstellen eine Richtlinie, die es den Mitarbeitern im Ressourcenverzeichnis ermöglicht, das Zugriffspaket anzufordern.

3. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für in Ihrem Verzeichnis befindliche Benutzer**, und wählen Sie dann die Option **Alle Mitglieder (keine Gäste)** aus. Diese Einstellungen gewährleisten, dass nur Mitglieder Ihres Verzeichnisses Office-Lizenzen anfordern können.

4. Stellen Sie sicher, dass **Genehmigung erforderlich** auf **Ja** festgelegt ist.

5. Behalten Sie für **Begründung des Anforderers erforderlich** die Option **Ja** bei.

6. Übernehmen Sie für **Wie viele Phasen** die Einstellung **1**.

7. Wählen Sie unter **Genehmigende Person** die Option **Vorgesetzter als genehmigende Person** aus. Bei Auswahl dieser Option kann der Vorgesetzte der anfordernden Person die Anforderung genehmigen. Sie können auch eine andere genehmigende Person als Ausweichmöglichkeit auswählen, falls der Vorgesetzte vom System nicht gefunden wird.

8. Übernehmen Sie für **Zulässige Anzahl von Tagen für Entscheidungsfindung** den Wert **14**.

9. Behalten Sie für **Begründung der genehmigenden Person erforderlich** die Option **Ja** bei.

10. Legen Sie **Neue Anforderungen und Zuweisungen aktivieren** auf **Ja** fest, damit dieses Zugriffspaket gleich nach seiner Erstellung von Mitarbeitern angefordert werden kann.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>Schritt 4: Konfigurieren der Informationen zum Anforderer für Ihr Zugriffspaket

1. Klicken Sie auf **Weiter**, um zur Registerkarte **Informationen zum Anforderer** zu wechseln.

2. Auf dieser Registerkarte können Sie Fragen stellen, um weitere Informationen vom Anforderer zu erfassen. Diese Fragen werden im Anforderungsformular angezeigt und können als erforderlich oder optional festgelegt werden. Da Sie im Rahmen dieses Szenarios nicht aufgefordert wurden, Informationen zum Anforderer für das Zugriffspaket anzugeben, können Sie diese Felder leer lassen.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>Schritt 5: Konfigurieren des Lebenszyklus für Ihr Zugriffspaket

1. Wählen Sie **Weiter: Lebenszyklus** aus, um zur Registerkarte **Lebenszyklus** zu wechseln.

2. Legen Sie im Abschnitt **Ablauf** unter **Die Zugriffspaketzuweisungen laufen ab** die Option **Anzahl Tage** fest.
    
3. Geben Sie unter **Zuweisungen laufen ab nach** die Zahl **365** ein. Dieses Feld gibt an, wann Mitglieder, die auf dieses Zugriffspaket zugreifen können, ihre Zugriffsberechtigung verlängern müssen. 

4. Sie können auch Zugriffsüberprüfungen konfigurieren, damit regelmäßig überprüft wird, ob der Mitarbeiter weiterhin Zugriff auf das Zugriffspaket benötigt. Eine Überprüfung kann eine Selbstüberprüfung sein, die vom Mitarbeiter durchgeführt wird. Sie können jedoch auch den Vorgesetzten des Mitarbeiters oder eine andere Person als Prüfer festlegen. Weitere Informationen finden Sie unter [Zugriffsüberprüfungen](entitlement-management-access-reviews-create.md). 
 
    In diesem Szenario sollen alle Mitarbeiter jährlich überprüfen, ob sie weiterhin eine Lizenz für Office benötigen.

    1. Wählen Sie unter **Zugriffsüberprüfungen erforderlich** die Option **Ja** aus.
    2. Sie können für die Option **Startet am** das aktuelle Datum übernehmen. Dieses Datum markiert den Beginn der Zugriffsüberprüfung. Nachdem Sie eine Zugriffsüberprüfung erstellt haben, können Sie das Startdatum nicht mehr aktualisieren.
    3. Legen Sie die Option **Häufigkeit der Überprüfung** auf **Jährlich** fest, da die Überprüfung einmal pro Jahr erfolgen soll. Im Feld **Häufigkeit der Überprüfung** wird festgelegt, wie häufig die Zugriffsüberprüfung durchgeführt wird.
    4. Geben Sie die **Dauer (in Tagen)** an.  Im Feld für die Dauer geben Sie an, wie viele Tage lang die Zugriffsüberprüfung jeweils durchgeführt wird.
    5. Wählen Sie unter **Prüfer** die Option **Vorgesetzte(r)** aus.

## <a name="step-6-review-and-create-your-access-package"></a>Schritt 6: Überprüfen und Erstellen Ihres Zugriffspakets

1. Wählen Sie **Weiter: Überprüfen und erstellen** aus, um zur Registerkarte **Überprüfen und erstellen** zu wechseln.

   Auf dieser Registerkarte können Sie die Konfiguration für Ihr Zugriffspaket überprüfen, bevor Sie es erstellen. Bei Problemen können Sie mithilfe der Registerkarten zu einem bestimmten Punkt im Vorgang wechseln, um Änderungen vorzunehmen.

3. Wenn Sie mit Ihrer Konfiguration zufrieden sind, klicken Sie auf **Erstellen**. Nach kurzer Zeit sollte eine Benachrichtigung mit dem Hinweis angezeigt werden, dass das Zugriffspaket erstellt wurde.

4. Nachdem das Zugriffspaket erstellt wurde, wird die Seite **Übersicht** für das Paket angezeigt. Diese Seite enthält den **Link zum Portal „Mein Zugriff“** . Kopieren Sie den Link, und geben Sie ihn an Ihr Team weiter, damit die Teammitglieder das Zugriffspaket anfordern und sich Lizenzen für Office zuweisen lassen können.

## <a name="step-7-clean-up-resources"></a>Schritt 7: Bereinigen der Ressourcen

In diesem Schritt können Sie das Zugriffspaket für Office-Lizenzen löschen. 

**Erforderliche Rolle**: Globaler Administrator, Identity Governance-Administrator oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal im linken Bereich auf **Azure Active Directory**.

2. Wählen Sie unter **Verwalten** die Option **Identity Governance** aus.

3. Wählen Sie unter **Berechtigungsverwaltung** die Option **Zugriffspakete** aus. 

4. Öffnen Sie das Zugriffspaket **Office-Lizenzen**. 

5. Wählen Sie **Ressourcenrollen** aus.

6. Wählen Sie die Gruppe aus, die Sie dem Zugriffspaket hinzugefügt haben. Wählen Sie im Detailbereich die Option **Ressourcenrolle entfernen** aus. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

7. Öffnen Sie die Liste der Zugriffspakete.

8. Wählen Sie für **Office-Lizenzen** die Schaltfläche mit den Auslassungspunkten (...) und dann **Löschen** aus. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Erstellen von Zugriffspaketen, um den Zugriff auf andere Arten von Ressourcen wie Anwendungen und Websites zu verwalten: 

[Verwalten des Zugriffs auf Ressourcen in der Azure AD-Berechtigungsverwaltung](./entitlement-management-access-package-first.md)