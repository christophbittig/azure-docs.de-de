---
title: Aktivieren der Altersbeschränkung in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Minderjährige identifizieren, die Ihre Anwendung verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f0502f11c9a6e900ae8f25209b6897f8384c2773
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777756"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Aktivieren der Altersbeschränkung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Mit der Altersbeschränkung in Azure Active Directory B2C (Azure AD B2C) können Sie Minderjährige identifizieren, die Ihre Anwendung (mit oder ohne Zustimmung der Eltern) verwenden möchten. Sie können Minderjährige wahlweise daran hindern, sich bei der Anwendung anzumelden. Oder Sie erlauben Benutzern, die Anmeldung abzuschließen, und geben die Anwendung für Minderjährige frei. 

>[!IMPORTANT]
>Dieses Feature befindet sich in der Phase der öffentlichen Vorschau. Verwenden Sie dieses Feature nicht für Produktionsanwendungen.
>

Wenn für einen Benutzerflow die Altersbeschränkung aktiviert ist, werden Benutzer nach Geburtsdatum und Wohnsitz (Land/Region) gefragt. Wenn sich ein Benutzer anmeldet, der diese Informationen noch nicht eingegeben hat, muss dies bei der nächsten Anmeldung erfolgen. Die Regeln werden jedes Mal angewendet, wenn sich ein Benutzer anmeldet.

![Screenshot: Flow zum Sammeln von Informationen zur Altersbeschränkung](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C nutzt die vom Benutzer eingegebenen Informationen, um zu ermitteln, ob es sich um eine minderjährige Person handelt. Anschließend wird das Feld **ageGroup** im Konto aktualisiert. Der Wert kann `null`, `Undefined`, `Minor`, `Adult` oder `NotAdult` lauten.  Die Felder **ageGroup** und **consentProvidedForMinor** werden dann verwendet, um den Wert von **legalAgeGroupClassification** zu berechnen.


## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Einrichten Ihres Mandanten für die Altersbeschränkung

Um die Altersbeschränkung in einem Benutzerflow zu verwenden, müssen Sie für Ihren Mandanten zusätzliche Eigenschaften konfigurieren.

1. Über [diesen Link](https://portal.azure.com/?Microsoft_AAD_B2CAdmin_agegatingenabled=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) können Sie die Vorschauversion der Altersbeschränkung ausprobieren.
1. Stellen Sie sicher, dass Sie das Verzeichnis mit Ihrem Azure AD B2C-Mandanten verwenden, indem Sie in der Portalsymbolleiste das Symbol **Verzeichnisse + Abonnements** auswählen.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und wählen Sie diesen Eintrag aus.
1. Wählen Sie für Ihren Mandanten im Menü auf der linken Seite die Option **Eigenschaften**.
1. Wählen Sie unter **Altersbeschränkung** die Option **Konfigurieren** aus.
1. Warten Sie, bis der Vorgang abgeschlossen ist und Ihr Mandant für die Altersbeschränkung eingerichtet wird.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Aktivieren der Altersbeschränkung im Benutzerflow

Nachdem Ihr Mandant für die Nutzung der Altersbeschränkung eingerichtet wurde, können Sie dieses Feature dann in den [Benutzerflows](user-flow-versions.md) verwenden, für die es aktiviert ist. Zum Aktivieren der Altersbeschränkung führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen Benutzerflow, für den die Altersbeschränkung aktiviert ist.
1. Wählen Sie nach dem Erstellen des Benutzerflows im Menü die Option **Eigenschaften**.
1. Wählen Sie im Abschnitt **Altersbeschränkung** die Option **Aktiviert**.
1. Wählen Sie für **Registrierung oder Anmeldung** aus, wie Sie Benutzer verwalten möchten:
    - Minderjährigen den Zugriff auf Ihre Anwendung gestatten
    - Zugriff auf Ihre Anwendung nur für Minderjährige sperren, die noch nicht geschäftsfähig sind
    - Zugriff auf Ihre Anwendung für Minderjährige immer sperren
1. Wählen Sie unter **Beim Sperren** eine der folgenden Optionen aus:
    - **JSON-Funktion zurück an die Anwendung senden**: Diese Option sendet eine Antwort mit dem Hinweis zurück an die Anwendung, dass ein Minderjähriger gesperrt wurde.
    - **Fehlerseite anzeigen**: Dem Benutzer wird eine Seite angezeigt, die ihn darüber informiert, dass er nicht auf die Anwendung zugreifen kann.

## <a name="test-your-user-flow"></a>Testen des Benutzerflows

1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Melden Sie sich mit einem lokalen Konto oder einem Social Media-Konto an. Wählen Sie dann Ihren Wohnsitz (Land/Region) und ein Geburtsdatum aus, das einen Minderjährigen simuliert. 
1. Wiederholen Sie den Test, und wählen Sie ein Geburtsdatum aus, das einen Erwachsenen simuliert.  

Wenn Sie sich als Minderjähriger anmelden, sollte die folgende Fehlermeldung angezeigt werden: *Ihre Anmeldung wurde blockiert. Gesetze zu Datenschutz und Onlinesicherheit in Ihrem Land/Ihrer Region untersagen Konten von Kindern den Zugriff*.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Aktivieren der Altersbeschränkung in der benutzerdefinierten Richtlinie

1. Rufen Sie das Beispiel für eine Altersbeschränkungsrichtlinie auf [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies) ab.
1. Ersetzen Sie in jeder Datei die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten. Wenn der Name des B2C-Mandanten z.B. *contosob2c* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` zu `contosob2c.onmicrosoft.com`.
1. Laden Sie die Richtliniendateien hoch.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Verwalten des Benutzerzugriffs in Azure AD B2C](manage-user-access.md).
