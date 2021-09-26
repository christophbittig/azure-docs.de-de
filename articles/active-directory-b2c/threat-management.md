---
title: Entschärfen von Angriffen auf Anmeldeinformationen – Azure AD B2C
titleSuffix: Azure AD B2C
description: Informieren Sie sich über Erkennungs- und Entschärfungstechniken für Angriffe auf Anmeldeinformationen (Kennwortangriffe) in Azure Active Directory B2C, einschließlich intelligenter Kontosperrungsfunktionen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99363e4f9f67e6ceca0d163131a2f2ff64b09857
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546971"
---
# <a name="mitigate-credential-attacks-in-azure-ad-b2c"></a>Entschärfen von Angriffen auf Anmeldeinformationen in Azure AD B2C

Angriffe auf Anmeldeinformationen führen zu nicht autorisiertem Zugriff auf Ressourcen. Benutzerkennwörter müssen ausreichend komplex sein. Azure AD B2C verfügt über Methoden zur Entschärfung von Angriffen auf Anmeldeinformationen. Die Entschärfung umfasst die Erkennung von Brute-Force-Angriffen auf Anmeldeinformationen und von Wörterbuchangriffen. Azure Active Directory B2C (Azure AD B2C) analysiert die Integrität von Anforderungen anhand verschiedener Signale. Azure AD B2C kann auf intelligente Weise zwischen vorgesehenen Benutzern und Hackern oder Botnetzen unterscheiden.

Azure AD B2C verwendet eine ausgereifte Strategie zum Sperren von Konten. Die Konten werden basierend auf der IP-Adresse der Anforderung und den eingegebenen Kennwörtern gesperrt. Die Dauer der Sperre wird zudem gemäß der Wahrscheinlichkeit erhöht, dass es sich um einen Angriff handelt. Nachdem ein Kennwort 10 Mal (der Standardschwellenwert für fehlgeschlagene Versuche) erfolglos eingegeben wurde, kommt es zu einer Sperre von einer Minute. Bei der nächsten fehlgeschlagenen Anmeldung nach der Entsperrung des Kontos (d. h., nachdem das Konto nach Ablauf des Sperrzeitraums automatisch vom Dienst entsperrt wurde) erfolgt eine weitere Sperre von 1 Minute für jede fehlgeschlagene Anmeldung. Die wiederholte Eingabe desselben oder eines ähnlichen Kennworts wird nicht als mehrere erfolglose Anmeldungen gezählt.

> [!NOTE]
> Dieses Feature wird von [Benutzerflows, benutzerdefinierten Richtlinien](user-flow-overview.md) und [ROPC](add-ropc-policy.md)-Flows unterstützt. Das Feature ist standardmäßig aktiviert, sodass Sie es nicht in Ihren Benutzerflows oder benutzerdefinierten Richtlinien konfigurieren müssen.

## <a name="unlock-accounts"></a>Entsperren von Konten

Die ersten 10 Sperrzeiträume sind eine Minute lang. Die nächsten 10 Sperrzeiträume sind etwas länger, und die Dauer wird jeweils nach 10 Sperrzeiträumen verlängert. Der Zähler für die Kontosperrung wird nach einer erfolgreichen Anmeldung auf null zurückgesetzt, wenn das Konto nicht gesperrt ist. Sperrzeiträume können bis zu fünf Stunden dauern. Benutzer müssen warten, bis die Sperrdauer abgelaufen ist. Der Benutzer kann die Sperre jedoch mithilfe des Self-Service-[Kennwortzurücksetzungsflows](add-password-reset-policy.md) aufheben.

## <a name="manage-password-protection-settings"></a>Verwalten der Kennwortschutzeinstellungen

So verwalten Sie die Kennwortschutzeinstellungen, einschließlich des Sperrschwellenwerts:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie unter **Sicherheit** die Option **Authentifizierungsmethoden (Vorschau)** und dann **Kennwortschutz** aus.
1. Geben Sie unter **Benutzerdefinierte intelligente Sperre** Ihre gewünschten Kennwortschutzeinstellungen ein:

   - **Sperrschwellenwert**: Die Anzahl von fehlgeschlagenen Anmeldeversuchen, die zulässig sind, bevor das Konto zum ersten Mal gesperrt wird. Wenn die erste Anmeldung nach einer Sperre ebenfalls erfolglos ist, wird das Konto erneut gesperrt.
   - **Sperrdauer in Sekunden**: Die Mindestdauer jeder Sperre in Sekunden. Wenn ein Konto wiederholt gesperrt wird, verlängert sich diese Dauer.

       ![Seite „Kennwortschutz“ in den Azure AD-Einstellungen des Azure-Portals](./media/threat-management/portal-02-password-protection.png)
    <br />*Festlegen des Sperrschwellenwerts auf 5 in den Einstellungen für **Kennwortschutz***.

1. Wählen Sie **Speichern** aus.

## <a name="testing-the-password-protection-settings"></a>Testen der Kennwortschutzeinstellungen

Die intelligente Sperre verwendet viele Faktoren, um zu bestimmen, wann ein Konto gesperrt werden soll, aber der primäre Faktor ist das Kennwortmuster. Die intelligente Sperre betrachtet geringfügige Variationen eines Kennworts als einen Satz, und sie werden als einzelner Versuch gezählt. Beispiel:

- Kennwörter wie „12456!“ und „1234567!“ (oder „newAccount1234“ und „newaccount1234“) ähneln sich so sehr, dass der Algorithmus sie als menschliche Fehler interpretiert und als einzelnen Versuch zählt.
- Größere Variationen im Muster (wie z. B. „12456!“ und „ABCD2!“) werden als separate Versuche gezählt.

Verwenden Sie beim Testen der intelligenten Sperre ein differenziertes Muster für jedes eingegebene Kennwort. Erwägen Sie die Verwendung von Web-Apps zur Kennwortgenerierung, wie z. B. [https://passwordsgenerator.net/](https://passwordsgenerator.net/).

Wenn der Schwellenwert der intelligenten Sperre erreicht wird, erhalten Sie die folgende Meldung, während das Konto gesperrt ist: **Ihr Konto ist vorübergehend gesperrt, um eine nicht autorisierte Verwendung zu verhindern. Versuchen Sie es später noch mal**. Die Fehlermeldungen können [lokalisiert](localization-string-ids.md#sign-up-or-sign-in-error-messages) werden.

> [!NOTE]
> Wenn Sie Smart Lockout testen, werden Ihre Anmeldeanforderungen möglicherweise von unterschiedlichen Rechenzentren verarbeitet, da der Azure AD-Authentifizierungsdienst geografisch verteilt ist und über einen Lastenausgleich verfügt. Da in diesem Szenario jedes Azure AD-Rechenzentrum die Sperre unabhängig nachverfolgt, kann es mehr Versuche erfordern, als durch den Sperrschwellenwert festgelegt sind, bis eine Sperre ausgelöst wird. Ein Benutzer hat eine maximale Anzahl (threshold_limit * datacenter_count) fehlerhafter Versuche, bevor er vollständig gesperrt wird.

## <a name="viewing-locked-out-accounts"></a>Anzeigen gesperrter Konten

Informationen zu gesperrten Konten finden Sie im [Anmeldeaktivitätsbericht](../active-directory/reports-monitoring/concept-sign-ins.md) von Active Directory. Klicken Sie unter **Status** auf **Fehler**. Fehlgeschlagene Anmeldungen mit dem **Anmeldefehlercode**`50053` geben ein gesperrtes Konto an:

![Abschnitt des Azure AD-Anmeldeberichts, in dem ein gesperrtes Konto angezeigt wird](./media/threat-management/portal-01-locked-account.png)

Informationen zum Anzeigen des Anmeldeaktivitätsberichts in Azure Active Directory finden Sie unter [Fehlercodes des Berichts mit den Anmeldeaktivitäten](../active-directory/reports-monitoring/concept-sign-ins.md).

