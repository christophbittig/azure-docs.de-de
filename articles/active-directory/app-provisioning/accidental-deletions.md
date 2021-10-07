---
title: Aktivieren des Features zum Verhindern von versehentlichen Löschungen bei der Anwendungsbereitstellung in Azure Active Directory
description: Aktivieren Sie das Feature zum Verhindern versehentlicher Löschungen bei der Anwendungsbereitstellung in Azure Active Directory.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6f7cb755663b7bf454e33c5a4f785d29d862967d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155418"
---
# <a name="enable-accidental-deletions-prevention-in-the-azure-ad-provisioning-service-preview"></a>Aktivieren des Features zum Verhindern versehentlicher Löschungen im Azure AD-Bereitstellungsdienst (Vorschau)

Der Azure AD-Bereitstellungsdienst enthält ein Feature, mit dem versehentliche Löschungen vermieden werden können. Dieses Feature stellt sicher, dass Benutzer*innen in einer Anwendung nicht unerwartet deaktiviert oder gelöscht werden. 

Mit dem Feature können Sie einen Schwellenwert für die Löschung angeben. Ist der Schwellenwert überschritten, müssen Administrator*innen explizit zulassen, dass Löschungen verarbeitet werden dürfen.

> [!NOTE]
> Versehentliche Löschungen werden für unsere Workday-/SuccessFactors-Integrationen nicht unterstützt. Sie werden auch nicht für Bereichsänderungen unterstützt, z. B. Änderung eines Bereichsfilters oder der Einstellung „Alle Benutzer und Gruppen synchronisieren“ in „sync assigned users and groups“ (Zugewiesene Benutzer und Gruppen synchronisieren). Bis das Feature zum Verhindern versehentlicher Löschungen vollständig veröffentlicht ist, müssen Sie über diese URL auf die Azure-Portal zugreifen: https://aka.ms/AccidentalDeletionsPreview.


## <a name="configure-accidental-deletion-prevention"></a>Konfigurieren des Features zum Verhindern von versehentlichen Löschungen
So aktivieren Sie das Feature zum Verhindern von versehentlichen Löschungen:
1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
2.  Klicken Sie auf **Unternehmensanwendungen**, und wählen Sie Ihre Anwendung aus.
3.  Klicken Sie auf **Bereitstellung durchgeführt**, und wählen Sie auf der Bereitstellungsseite **Edit provisioning** (Bereitstellung bearbeiten) aus.
4. Aktivieren Sie unter **Einstellungen** das Kontrollkästchen **Prevent accidental deletions** (Versehentliches Löschen verhindern), und geben Sie einen Löschschwellenwert an. Stellen Sie außerdem sicher, dass die Benachrichtigungs-E-Mail-Adresse vollständig ist. Wenn der Löschschwellenwert erreicht ist, wird die E-Mail gesendet.
5. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

Wenn der Löschschwellenwert erreicht ist, wird der Auftrag unter Quarantäne gestellt, und eine Benachrichtigungs-E-Mail wird gesendet. Der unter Quarantäne gestellte Auftrag kann dann zugelassen oder abgelehnt werden. Weitere Informationen zum Quarantäneverhalten finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](application-provisioning-quarantine-status.md).

## <a name="known-limitations"></a>Bekannte Einschränkungen
Es gibt zwei wichtige Einschränkungen, die Sie beachten und an deren Behebung Sie aktiv arbeiten sollten:
- Die HR-gesteuerte Bereitstellung von Workday und SuccessFactors unterstützt das Feature zum Verhindern versehentlicher Löschungen nicht. 
- Änderungen an der Bereitstellungskonfiguration (z. B. Bereichsänderungen) werden vom Feature zum Verhindern versehentlicher Löschungen nicht unterstützt. 

## <a name="recovering-from-an-accidental-deletion"></a>Wiederherstellung nach einer versehentlichen Löschung
Wenn eine versehentliche Löschung auftritt, wird diese auf der Seite mit dem Bereitstellungsstatus  mit der Meldung **Die Bereitstellung wurde unter Quarantäne gestellt. Weitere Informationen finden Sie in den Quarantänedetails.** angezeigt.

Sie können entweder auf **Allow deletes** (Löschungen zulassen) oder **Bereitstellungsprotokolle anzeigen** klicken.

### <a name="allowing-deletions"></a>Zulassen von Löschungen

Mit der Aktion **Löschvorgänge zulassen** werden die Objekte gelöscht, die dazu geführt haben, dass der Schwellenwert für versehentliches Löschen erreicht wurde.  Verwenden Sie das folgende Verfahren, um die Löschvorgänge zu akzeptieren.  

1. Klicken Sie auf **Löschungen zulassen**.
2. Klicken Sie in der Bestätigungsmeldung auf **Ja**, um die Löschvorgänge zuzulassen.
3. Es wird eine Bestätigung angezeigt, dass die Löschvorgänge akzeptiert wurden. Für den nächsten Zyklus wird wieder „Fehlerfrei“ als Status angezeigt.

### <a name="rejecting-deletions"></a>Ablehnen von Löschvorgängen

Gehen Sie wie folgt vor, falls Sie die Löschvorgänge nicht zulassen möchten:
- Informieren Sie sich über die Quelle der Löschvorgänge. Ausführliche Details finden Sie in den Bereitstellungsprotokollen.
- Verhindern Sie Löschungen, indem Sie der App die Benutzer*innen/die Gruppe erneut zuweisen, die Benutzer*innen/die Gruppe wiederherstellen oder Ihre Bereitstellungskonfiguration aktualisieren.
- Nachdem Sie die erforderlichen Änderungen vorgenommen haben, um zu verhindern, dass die Benutzer*innen oder die Gruppe gelöscht werden, starten Sie die Bereitstellung neu. Starten Sie die Bereitstellung erst neu, wenn Sie die erforderlichen Änderungen vorgenommen haben, um zu verhindern, dass die Benutzer*innen oder Gruppen gelöscht werden. 


### <a name="test-deletion-prevention"></a>Testen der Verhinderung von Löschungen
Sie können das Feature testen, indem Sie Deaktivierungs-/Löschungsereignisse auslösen. Legen Sie den Schwellenwert dazu auf eine niedrige Zahl fest, z. B. 3, und ändern Sie dann Bereichsfilter, heben Sie die Zuweisung von Benutzer*innen auf, und löschen Sie Benutzer*innen aus dem Verzeichnis (siehe häufige Szenarios im nächsten Abschnitt). 

Lassen Sie den Bereitstellungsauftrag zu Ende laufen (20–40 Minuten), und navigieren Sie zurück zur Bereitstellungsseite. Sie sehen den unter Quarantäne gestellten Bereitstellungsauftrag und können die Löschungen zulassen oder die Bereitstellungsprotokolle überprüfen, um zu verstehen, warum die Löschungen aufgetreten sind.

## <a name="common-de-provisioning-scenarios-to-test"></a>Häufige Szenarios für die Bereitstellungsaufhebung, die getestet werden sollten
- Löschen Sie Benutzer*innen, oder legen Sie sie in den Papierkorb.
- Blockieren Sie die Anmeldung für Benutzer*innen.
- Heben Sie die Zuweisung von Benutzer*innen oder einer Gruppe für die Anwendung auf.
- Entfernen Sie Benutzer*innen aus einer Gruppe, die ihnen Zugriff auf die App ermöglicht.

Weitere Informationen zu Bereitstellungsaufhebungsszenarios finden Sie unter [Funktionsweise der Anwendungsbereitstellung](how-provisioning-works.md#de-provisioning).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-scenarios-count-toward-the-deletion-threshold"></a>Welche Szenarios werden auf den Löschschwellenwert angerechnet?
Wenn die Entfernung von Benutzer*innen aus der Zielanwendung geplant ist, werden diese Benutzer*innen bereits auf den Löschschwellenwert angerechnet. Folgende Szenarios können dazu führen, dass Benutzer*innen aus der Zielanwendung entfernt werden: Aufhebung der Zuweisung von Benutzer*innen aus der Anwendung sowie vorläufiges/endgültiges Löschen von Benutzer*innen im Verzeichnis. Gruppen, für die eine Löschung ausgewertet wird, werden auf den Löschschwellenwert angerechnet. Neben Löschungen funktioniert diese Funktionalität auch für Deaktivierungen.

### <a name="what-is-the-interval-that-the-deletion-threshold-is-evaluated-on"></a>In welchem Intervall wird der Löschschwellenwert ausgewertet?
Er wird in jedem Zyklus ausgewertet. Wenn die Anzahl der Löschungen den Schwellenwert während eines einzelnen Zyklus nicht überschreitet, wird der „Trennschalter“ nicht ausgelöst. Wenn mehrere Zyklen für einen stabilen Zustand erforderlich sind, wird der Löschschwellenwert pro Zyklus ausgewertet.

### <a name="how-are-these-deletion-events-logged"></a>Wie werden diese Löschereignisse protokolliert?
Sie können Benutzer*innen suchen, für die eine Deaktivierung/Löschung hätte durchgeführt werden sollen, die jedoch aufgrund des Löschschwellenwerts nicht erfolgt ist. Navigieren Sie zu **Bereitstellungsprotokolle**, und filtern Sie **Aktion** nach *StagedAction* oder *StagedDelete*.


## <a name="next-steps"></a>Nächste Schritte 

- [Funktionsweise der Anwendungsbereitstellung](how-provisioning-works.md)
- [Planen einer Anwendungsbereitstellung](plan-auto-user-provisioning.md)
