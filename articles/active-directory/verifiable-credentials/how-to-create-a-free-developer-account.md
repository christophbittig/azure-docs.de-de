---
title: Erstellen eines kostenlosen Azure Active Directory-Entwicklermandanten
description: In diesem Artikel erfahren Sie, wie Sie ein Entwicklerkonto erstellen.
services: active-directory
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: fbdd59b38a9e699640b50e8f5402b60559990e8d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006848"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Erstellen eines kostenlosen Azure Active Directory-Entwicklermandanten

> [!IMPORTANT]
> Azure Active Directory-Nachweise befinden sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Während der Vorschauphase ist eine P2-Lizenz erforderlich. 

Es gibt zwei einfache Möglichkeiten, eine kostenlose Azure Active Directory-Instanz mit einer P2-Testlizenz zu erstellen, um den Dienst zum Ausstellen von Nachweisen installieren und die Erstellung und Validierung von Nachweisen testen zu können:

- [Nehmen Sie am kostenlosen Microsoft 365-Entwicklerprogramm teil](https://aka.ms/o365devprogram), und erhalten Sie eine kostenlose Sandbox sowie Tools und andere Ressourcen wie etwa eine Azure Active Directory-Instanz mit P2-Lizenzen, konfigurierte Benutzer, Gruppen, Postfächer usw.
- Erstellen Sie einen neuen [Mandanten](../develop/quickstart-create-new-tenant.md), und aktivieren Sie darin eine [kostenlose Testversion](https://azure.microsoft.com/trial/get-started-active-directory/) von Azure AD Premium P1 oder P2.

Die Registrierung für das kostenlose Microsoft 365-Entwicklerprogramm ist ganz einfach:

1. Klicken Sie auf dem Bildschirm auf die Schaltfläche **Jetzt beitreten**.

2. Melden Sie sich mit einem neuen Microsoft-Konto an, oder verwenden Sie ein bereits vorhandenes (Geschäfts-)Konto.

3. Wählen Sie auf der Registrierungsseite Ihre Region aus, geben Sie einen Firmennamen ein, und akzeptieren Sie die Bestimmungen des Programms, bevor Sie auf **Weiter** klicken.

4. Klicken Sie auf **set up subscription** (Abonnement einrichten). Geben Sie die Region an, in der Sie Ihren neuen Mandanten erstellen möchten, erstellen Sie einen Benutzernamen und eine Domäne, und geben Sie ein Kennwort ein. Daraufhin werden ein neuer Mandant und der erste Administrator des Mandanten erstellt.

5. Geben Sie die erforderlichen Sicherheitsinformationen zum Schutz des Administratorkontos Ihres neuen Mandanten ein. Dadurch wird die MFA-Authentifizierung für das Konto eingerichtet.


Sie haben nun einen Mandanten mit 25 E5-Benutzerlizenzen erstellt. Die E5-Lizenzen enthalten Azure AD P2-Lizenzen. Sie können optional Beispieldatenpakete mit Benutzern, Gruppen, E-Mails und SharePoint hinzufügen, um das Testen in der Entwicklungsumgebung zu erleichtern. Für den Dienst zum Ausstellen von Nachweisen sind sie allerdings nicht erforderlich.

Der Einfachheit halber können Sie Ihr eigenes Geschäftskonto in dem neu erstellten Mandanten als [Gast](../external-identities/b2b-quickstart-add-guest-users-portal.md) hinzufügen und dieses Konto zum Verwalten des Mandanten verwenden. Um die Verwaltung des Nachweisdiensts über das Gastkonto zu ermöglichen, muss dem entsprechenden Benutzer die Rolle „Globaler Administrator“ zugewiesen werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über ein Entwicklerkonto verfügen, können Sie das [erste Tutorial](get-started-verifiable-credentials.md) ausprobieren, um mehr über Nachweise zu erfahren.