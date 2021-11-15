---
title: 'Tutorial: Durchführen des Onboardings externer Benutzer in Azure AD über einen Genehmigungsprozess – Azure Active Directory'
description: Schritt-für-Schritt-Tutorial zum Erstellen eines Zugriffspakets für externe Benutzer, die Genehmigungen in der Azure Active Directory-Berechtigungsverwaltung benötigen.
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
ms.openlocfilehash: b97e440d318e1d08137ffa14691c5179d93d60f4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440733"
---
# <a name="tutorial---onboard-external-users-to-azure-ad-through-an-approval-process"></a>Tutorial: Onboarding externer Benutzer in Azure AD über einen Genehmigungsprozess

Sie können die Berechtigungsverwaltung verwenden, um das Onboarding externer Benutzer durchzuführen. Mit diesem Feature können externe Benutzer Zugriff auf eine Gruppe von Ressourcen anfordern und Sie Genehmigungen einrichten, bevor Benutzer Zugriff auf Ihr Verzeichnis erhalten. Für externe Benutzer, für die das Onboarding über Berechtigungen durchgeführt wurde, können Sie den Lebenszyklus über Zugriffspakete verwalten. Wenn das letzte Zugriffspaket abläuft, werden die betreffenden Benutzer aus Ihrem Verzeichnis entfernt.

In diesem Tutorial arbeiten Sie als IT-Administrator für die WoodGrove Bank. Sie wurden aufgefordert, ein Zugriffspaket für das Onboarding von Partnern aus einer externen Organisation zu erstellen, mit der Ihre Geschäftsgruppe zusammenarbeitet. Sie benötigen Zugriff auf eine Teams-Gruppe namens **External collaboration** (Externe Zusammenarbeit). Eine Genehmigung wird von einem internen Sponsor für zusammenarbeitende Organisationen benötigt. Außerdem wurden Sie darüber informiert, dass der Zugriff des Partners nach 60 Tagen ablaufen muss.
Um die Azure AD-Berechtigungsverwaltung verwenden zu können, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-Lizenz

Weitere Informationen finden Sie unter [Lizenzanforderungen](entitlement-management-overview.md#license-requirements).

## <a name="step-1-configure-basics"></a>Schritt 1: Konfigurieren der Grundlagen

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal im linken Navigationsbereich auf **Azure Active Directory**.

2. Klicken Sie im linken Menü auf **Identity Governance**.

3. Klicken Sie im linken Menü auf **Zugriffspakete**. Sollte Zugriff verweigert angezeigt werden, prüfen Sie, vergewissern Sie sich, dass in Ihrem Verzeichnis eine Azure AD Premium P2-Lizenz vorhanden ist.

4. Klicken Sie auf **Neues Zugriffspaket**.

5. Geben Sie auf der Registerkarte **Grundlagen** den Namen **External user package** (Externes Benutzerpaket) und die Beschreibung **Access for external users pending approval** (Zugriff für externe Benutzer mit ausstehender Genehmigung) ein.

6. Sie können für die Dropdownliste **Katalog** die Einstellung **Allgemein** übernehmen.

## <a name="step-2-configure-resources"></a>Schritt 2: Konfigurieren der Ressourcen

1. Klicken Sie auf **Weiter**, um die Registerkarte **Ressourcenrollen** zu öffnen.
 
   Wählen Sie auf dieser Registerkarte die Ressourcen und die Ressourcenrolle aus, die in das Zugriffspaket aufgenommen werden sollen.

2. Klicken Sie auf **Gruppen und Teams**, und suchen Sie nach Ihrer Gruppe **External collaboration** (Externe Zusammenarbeit).

## <a name="step-3-configure-requests"></a>Schritt 3: Konfigurieren von Anforderungen

1. Klicken Sie auf **Weiter**, um die Registerkarte **Anforderungen** zu öffnen.

   Diese Registerkarte dient zum Erstellen einer Anforderungsrichtlinie. In einer *Richtlinie* sind die Regeln oder Leitlinien für den Zugriff auf ein Zugriffspaket definiert. Sie erstellen eine Richtlinie, die es einem bestimmten Benutzer im Ressourcenverzeichnis ermöglicht, dieses Zugriffspaket anzufordern.

2. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden**, und klicken Sie dann auf **Alle Benutzer (Alle verbundenen Organisationen und alle neuen externen Benutzer)** .

3. Stellen Sie sicher, dass **Genehmigung erforderlich** auf **Ja** festgelegt ist.

4. Mit den folgenden Einstellungen können Sie konfigurieren, wie Ihre Genehmigungen für Ihre externen Benutzer funktionieren:

5. Belassen Sie **Begründung des Anforderers erforderlich** als **Ja**.

6. Übernehmen Sie für **Wie viele Phasen** den Wert **1**.

7. Wählen Sie für das Szenario **Genehmigende Person** die Option **Interner Sponsor** aus. Diese Option stammt von einer konfigurierten [verbundenen Organisation](entitlement-management-organization.md), in der Sie für bestimmte Organisationen, mit denen Sie zusammenarbeiten, als Sponsor fungieren können. Auf diese Weise können Sie eine in der verbundenen Organisation angegebene Person aus Ihrer Organisation als genehmigende Person festlegen. 

8. Übernehmen Sie für **Zulässige Anzahl von Tagen für Entscheidungsfindung** den Wert **14**.

9. Belassen Sie **Begründung der genehmigenden Person erforderlich** als **Ja**.

10. Legen Sie **Neue Anforderungen und Zuweisungen aktivieren** auf **Ja** fest, damit dieses Zugriffspaket angefordert werden kann, sobald es erstellt wurde.

## <a name="step-4-configure-requestor-information"></a>Schritt 4: Konfigurieren von Anfordererinformationen

1. Klicken Sie auf **Weiter**, um die Registerkarte **Anfordererinformationen** zu öffnen.

2. Auf diesem Bildschirm können Sie zusätzliche Fragen stellen, um weitere Informationen von Ihrem Anforderer zu erfassen. Diese Fragen werden im Anforderungsformular angezeigt und können als erforderlich oder optional festgelegt werden. Vorerst können Sie diese Angaben leer lassen.

## <a name="step-5-configure-lifecycle"></a>Schritt 5: Konfigurieren des Lebenszyklus

1. Klicken Sie auf **Weiter**, um die Registerkarte **Lebenszyklus** zu öffnen.

2. Legen Sie im Abschnitt **Ablauf** die Option **Zugriffspaketzuweisung läuft ab** auf **Anzahl Tage** fest.

3. Legen Sie **Zuweisung läuft ab nach** auf **60** Tage fest. Dieses Feld bestimmt, wann Ihre Gastbenutzer ihren Zugriff erneuern müssen.

4. Sie können auch **Zugriffsüberprüfungen** konfigurieren, die regelmäßige Überprüfungen ermöglichen, ob der Gast weiterhin Zugriff auf das Zugriffspaket benötigt. Eine Überprüfung kann eine Selbstüberprüfung sein, oder Sie können bestimmte Überprüfungen für diese Aufgabe festlegen. Weitere Informationen finden Sie unter [Zugriffsüberprüfungen](entitlement-management-access-reviews-create.md).

## <a name="step-6-review-and-create-your-access-package"></a>Schritt 6: Überprüfen und Erstellen Ihres Zugriffspakets

1. Klicken Sie auf **Weiter**, um die Registerkarte **Überprüfen + erstellen** zu öffnen.

2. Auf diesem Bildschirm können Sie die Konfiguration für Ihr Zugriffspaket überprüfen, bevor Sie es erstellen. Bei Problemen können Sie mithilfe der Registerkarten zu einem bestimmten Punkt im Erstellungsvorgang navigieren, um Änderungen vorzunehmen.

3. Wenn Sie mit Ihrer Auswahl zufrieden sind, klicken Sie auf **Erstellen**. Nach einigen Augenblicken sollte eine Benachrichtigung angezeigt werden, dass das Zugriffspaket erfolgreich erstellt wurde.

4. Nach der Erstellung gelangen Sie auf die **Übersichtsseite** für Ihr Zugriffspaket. Hier finden Sie den **Link zum Portal „Mein Zugriff“** und können ihn kopieren. Geben Sie diesen Link für Ihre externen Benutzer frei, die dieses Paket dann anfordern können, um mit der Zusammenarbeit zu beginnen.

## <a name="step-7-clean-up-resources"></a>Schritt 7: Bereinigen der Ressourcen

In diesem Schritt können Sie das Zugriffspaket für **Externen Benutzerpaketzugriff** löschen. 

**Erforderliche Rolle:** Unternehmensadministrator, Identity Governance-Administrator oder Zugriffspaket-Manager

1. Klicken Sie im **Azure-Portal** im linken Navigationsbereich auf **Azure Active Directory**.

2. Klicken Sie im linken Menü auf **Identity Governance**.

3. Klicken Sie im linken Menü auf **Zugriffspakete**. 

4. Öffnen Sie das Zugriffspaket für **Externen Benutzerzugriff**. 

5. Klicken Sie auf **Ressourcenrollen**.

6. Wählen Sie die Gruppe **Externe Zusammenarbeit** aus, die Sie diesem Zugriffspaket hinzugefügt haben, und wählen Sie im Bereich **Details** die Option **Ressourcenrolle entfernen** aus. Wählen Sie in der angezeigten Meldung **Ja** aus.

7. Öffnen Sie die Liste der Zugriffspakete.

8. Wählen Sie für **Externes Benutzerpaket** die Auslassungspunkte (...) und anschließend **Löschen** aus. Wählen Sie in der angezeigten Meldung **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Erstellen von Zugriffspaketen, um den Zugriff auf andere Arten von Ressourcen wie Anwendungen und Sites zu verwalten. [Tutorial: Verwalten des Zugriffs auf Ressourcen in der Azure AD-Berechtigungsverwaltung](./entitlement-management-access-package-first.md)
