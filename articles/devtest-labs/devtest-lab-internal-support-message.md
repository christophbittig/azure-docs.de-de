---
title: Hinzufügen eines internen Supporthinweises zu einem Lab
description: 'Erfahren Sie, wie Sie einem Lab in Azure DevTest Labs einen internen Supporthinweis hinzufügen. '
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 20cd7494eb0a0df340866d344b48d87e94cca490
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612147"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Hinzufügen eines internen Supporthinweises zu einem Lab in Azure DevTest Labs

Azure DevTest Labs ermöglicht Ihnen die Anpassung Ihres Labs mit einem internen Supporthinweis, in dem Sie Supportinformationen zum Lab für die Benutzer bereitstellen können. Beispielsweise können Sie Kontaktinformationen angeben, damit Benutzer wissen, wie Sie den internen Support erreichen, wenn sie Hilfe bei der Problembehandlung oder beim Zugriff auf Ressourcen im Lab benötigen. Sie können auch Links zu internen Websites oder FAQs bereitstellen, auf die Ihr Team zugreifen kann, bevor Sie den Support kontaktieren.

Ein interner Supporthinweis ist für die Bereitstellung von Labinformationen vorgesehen, die sich üblicherweise nur selten ändern. Wenn Sie Benutzer über Labinformationen benachrichtigen möchten, die sich häufiger ändern – beispielsweise über Aktualisierungen der Labrichtlinien – finden Sie weitere Informationen unter [Posten einer Ankündigung in einem Lab](devtest-lab-announcements.md).

Sie können einen Supporthinweis einfach deaktivieren oder bearbeiten, wenn er nicht mehr gültig ist.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Schritte zum Hinzufügen eines Supporthinweises zu einem vorhandenen Lab

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie ggf. **Alle Dienste** und dann in der Liste die Option **DevTest Labs**. (Ihr Lab wird im Dashboard unter Umständen bereits unter **Alle Ressourcen** angezeigt.)
1. Wählen Sie in der Liste der Labs das Lab aus, dem Sie einen Supporthinweis hinzufügen möchten.  
1. Wählen Sie im Bereich **Übersicht** des Labs die Option **Konfiguration und Richtlinien** aus.  

    ![Schaltfläche „Konfiguration und Richtlinien“](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Wählen Sie auf der linken Seite unter **EINSTELLUNGEN** die Option **Interner Support** aus.

    ![Schaltfläche „Interner Support“](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Legen Sie „Aktiviert“ auf **Ja** fest, um einen internen Supporthinweis für die Benutzer dieses Labs zu erstellen.

1. Geben Sie im Feld **Supportnachricht** den internen Supporthinweis ein, der den Benutzern Ihres Labs angezeigt werden soll. Die Supportnachricht bietet Unterstützung für Markdown. Beim Eingeben des Meldungstexts können Sie unten auf dem Bildschirm im Bereich **Vorschau** sehen, wie die Nachricht den Benutzern angezeigt wird.

    ![Bildschirm zum Erstellen eines internen Supporthinweises](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Wählen Sie **Speichern**, nachdem Ihr Supporthinweis bereit zum Posten ist.

Wenn dieser Supporthinweis den Benutzern Ihres Labs nicht mehr angezeigt werden soll, können Sie auf der Seite **Interner Support** die Option **Aktiviert** auf **Nein** festlegen.

## <a name="steps-for-users-to-view-the-support-message"></a>Benutzerschritte zur Anzeige der Supportnachricht

1. Wählen Sie im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) ein Lab aus.

1. Klicken Sie im Bereich **Übersicht** für das Lab auf **Interner Support**.  

    ![Schaltfläche „Interner Support“](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Wenn eine Supportnachricht gepostet wurde, kann der Benutzer diese im Bereich „Interner Support“ anzeigen.

    ![Bereich „Interner Support“ mit Anzeige der geposteten Nachricht](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
* Interne Supporthinweise werden üblicherweise dazu verwendet, um Informationen bereitzustellen, die sich nur selten ändern. Wenn Sie Benutzer über temporäre Änderungen oder Aktualisierungen am Lab informieren möchten, finden Sie weitere Informationen unter [Posten einer Ankündigung in einem Lab](devtest-lab-announcements.md).
* [Festlegen von Richtlinien und Zeitplänen](devtest-lab-set-lab-policy.md) enthält Informationen zum Anwenden von Einschränkungen und Konventionen für Ihr gesamtes Abonnement mithilfe von benutzerdefinierten Richtlinien.