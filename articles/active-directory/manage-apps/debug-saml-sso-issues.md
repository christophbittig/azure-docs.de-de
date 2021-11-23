---
title: Debuggen des SAML-basierten einmaligen Anmeldens
titleSuffix: Azure AD
description: Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory.
services: active-directory
ms.author: davidmu
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: ergreenl
ms.openlocfilehash: 588c5d7149a88ef56692cc59db9c14f244c7b6d2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553152"
---
# <a name="debug-saml-based-single-sign-on-to-applications"></a>Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen

Hier erfahren Sie, wie Sie Probleme beim [einmaligen Anmelden](what-is-single-sign-on.md) bei Anwendungen in Azure Active Directory (Azure AD), die das SAML-basierte einmalige Anmelden nutzen, finden und beheben können.

## <a name="before-you-begin"></a>Voraussetzungen

Sie sollten die [Erweiterung zur sicheren Anmeldung bei „Meine Apps“](https://support.microsoft.com/account-billing/troubleshoot-problems-with-the-my-apps-portal-d228da80-fcb7-479c-b960-a1e2535cbdff#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) installieren. Diese Browsererweiterung erleichtert das Erfassen der SAML-Anforderung sowie die SAML-Antwortinformationen, die Sie für die Behebung von Problemen mit einmaligem Anmelden benötigen. Für den Fall, dass Sie die Erweiterung nicht installieren können, zeigt dieser Artikel Ihnen die Behebung von Problemen mit und ohne Erweiterung.

Verwenden Sie zum Herunterladen und Installieren der Erweiterung zur sicheren Anmeldung bei „Meine Apps“ einen der folgenden Links.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://microsoftedge.microsoft.com/addons/detail/my-apps-secure-signin-ex/gaaceiggkkiffbfdpmfapegoiohkiipl)

## <a name="test-saml-based-single-sign-on"></a>Testen des einmaligen SAML-basierten Anmeldens

So testen Sie das einmalige SAML-basierte Anmelden zwischen Azure AD und einer Zielanwendung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler oder sonstiger Administrator an, der autorisiert ist, Anwendungen zu verwalten.
1. Wählen Sie auf dem Blatt auf der linken Seite die Option **Azure Active Directory** und dann **Unternehmensanwendungen**.
1. Wählen Sie in der Liste mit den Unternehmensanwendungen die Anwendung aus, für die Sie einmaliges Anmelden testen möchten, und wählen Sie dann in den Optionen links die Option **Einmaliges Anmelden**.
1. Navigieren Sie zu **Einmaliges Anmelden testen** (Schritt 5), um die Testumgebung für SAML-basiertes einmaliges Anmelden zu öffnen. Wenn die Schaltfläche **Test** ausgegraut ist, müssen Sie die erforderlichen Attribute zuerst im Abschnitt **Grundlegende SAML-Konfiguration** einfügen und speichern.
1. Melden Sie sich auf dem Blatt **Einmaliges Anmelden testen** mit Ihren Unternehmensanmeldeinformationen bei der Zielanwendung an. Sie können sich als der aktuelle Benutzer oder anderer Benutzer anmelden. Wenn Sie sich als anderer Benutzer anmelden, werden Sie in einer Eingabeaufforderung aufgefordert, sich zu authentifizieren.

    ![Screenshot der Seite für das Testen des einmaligen SAML-basierten Anmeldens](./media/debug-saml-sso-issues/test-single-sign-on.png)

Wenn Sie sich erfolgreich angemeldet haben, haben Sie den Test bestanden. Azure AD hat in diesem Fall ein SAML-Antworttoken für die Anwendung ausgestellt. Die Anwendung verwendete das SAML-Token, um Sie erfolgreich anzumelden.

Wenn die Unternehmensanmeldeseite oder die Anwendungsseite einen Fehler aufweist, lösen Sie den Fehler in einem der nächsten Abschnitte auf.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Auflösen eines Anmeldefehlers auf Ihrer Unternehmensanmeldeseite

Wenn Sie versuchen, sich anzumelden, wird auf der Anmeldeseite Ihres Unternehmens ggf. ein Fehler angezeigt, der dem folgenden Beispiel ähnelt.

![Beispiel für einen Fehler auf der Unternehmensanmeldeseite](./media/debug-saml-sso-issues/error.png)

Um diesen Fehler zu debuggen, benötigen Sie die Fehlermeldung und die SAML-Anforderung. Die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ erfasst automatisch diese Informationen und zeigt eine Auflösungsanleitung in Azure AD an.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Beheben des Anmeldefehlers, wenn die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ installiert ist

1. Wenn ein Fehler auftritt, leitet die Erweiterung Sie zurück zum Azure AD-Blatt **Einmaliges Anmelden testen**.
1. Wählen Sie auf dem Blatt **Einmaliges Anmelden testen** die Option **SAML-Anforderung herunterladen**.
1. Sie sollten eine spezifische Auflösungsanleitung auf Basis der Fehler und Werte in der SAML-Anforderung sehen.
1. Die Schaltfläche **Korrigieren** wird angezeigt, mit der die Konfiguration in Azure AD automatisch aktualisiert wird, um das Problem zu beheben. Wenn diese Schaltfläche nicht angezeigt wird, ist der Grund für das Anmeldungsproblem nicht eine Fehlkonfiguration in Azure AD.

Wenn für den Anmeldefehler keine Lösung angegeben ist, schlagen wir vor, das Textfeld für Feedback zu verwenden, um uns zu informieren.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Beheben des Fehlers, ohne die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ zu installieren

1. Kopieren Sie die Fehlermeldung rechts unten auf der Seite. Die Fehlermeldung enthält:
    - Eine Korrelations-ID und einen Zeitstempel. Diese Werte werden bei der Erstellung einer Supportanfrage an Microsoft benötigt, um Ihr Problem zu identifizieren und einen Lösungsvorschlag zu unterbreiten.
    - Eine Anweisung, die die Ursache des Problems identifiziert.
1. Wechseln Sie zurück zu Azure AD, und suchen Sie das Blatt **Einmaliges Anmelden testen**.
1. Fügen Sie die Fehlermeldung oben in das Textfeld **Leitfaden zur Problemlösung abrufen** ein.
1. Klicken Sie auf **Leitfaden zur Problemlösung abrufen**, um Schritte zur Lösung des Problems anzuzeigen. Der Leitfaden könnte Informationen aus der SAML-Anforderung bzw. -Antwort benötigen. Wenn Sie die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ nicht verwenden, könnten Sie mit einem Tool wie [Fiddler](https://www.telerik.com/fiddler) die SAML-Anforderung und -Antwort abrufen.
1. Vergewissern Sie sich, dass das Ziel in der SAML-Anforderung der URL des SAML-Diensts für einmaliges Anmelden aus Azure AD entspricht.
1. Vergewissern Sie sich, dass der Aussteller in der SAML-Anforderung dem Bezeichner entspricht, den Sie in Azure AD für die Anwendung konfiguriert haben. Azure AD sucht anhand des Ausstellers in Ihrem Verzeichnis nach einer Anwendung.
1. Vergewissern Sie sich, dass sich AssertionConsumerServiceURL an der Stelle befindet, an der die Anwendung das SAML-Token aus Azure AD erwartet. Sie können diesen Wert in Azure AD konfigurieren, aber er ist nicht zwingend erforderlich, wenn er Teil der SAML-Anforderung ist.

## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Auflösen eines Anmeldefehlers auf der Anwendungsseite

Sie können sich vielleicht erfolgreich anmelden und sehen dann die Anzeige einer Fehlermeldung auf der Anwendungsseite. Dies geschieht, wenn Azure AD ein Token für die Anwendung ausgestellt hat, aber die Anwendung die Antwort nicht akzeptiert.

Um den Fehler zu beheben, führen Sie die folgenden Schritte aus, oder sehen Sie sich dieses [kurzes Video über die Verwendung von Azure AD zur Problembehandlung von SAML-SSO](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8) an:

1. Wenn sich die Anwendung im Azure AD-Katalog befindet, sollten Sie sich vergewissern, dass Sie alle Schritte zur Integration der Anwendung in Azure AD ausgeführt haben. Die Integrationsanweisungen für Ihre Anwendung finden Sie in der [Liste der Tutorials zur Integration von SaaS-Anwendungen](../saas-apps/tutorial-list.md).
1. Rufen Sie die SAML-Antwort ab.
    - Wenn die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ installiert ist, klicken Sie im Blatt **Einmaliges Anmelden testen** auf **SAML-Antwort herunterladen**.
    - Wenn die Erweiterung nicht installiert ist, verwenden Sie ein Tool wie [Fiddler](https://www.telerik.com/fiddler) zum Abrufen der SAML-Antwort.
1. Beachten Sie diese Elemente im SAML-Antworttoken:
   - Für den Benutzer eindeutiger Bezeichner von Wert und Format der NameID
   - Im Token ausgestellte Ansprüche
   - Zum Signieren des Tokens verwendetes Zertifikat.

     Weitere Informationen zur SAML-Antwort finden Sie unter [SAML-Protokoll für einmaliges Anmelden](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Da Sie nun die SAML-Antwort überprüft haben, können Sie unter [Fehler auf der Seite einer Anwendung nach dem Anmelden](application-sign-in-problem-application-error.md) die Anleitung zum Beheben des Problems verwenden.
1. Wenn Sie sich immer noch nicht erfolgreich anmelden können, sollten Sie beim Anwendungsanbieter erfragen, was in der SAML-Antwort fehlt.

## <a name="next-steps"></a>Nächste Schritte

Da nun einmaliges Anmelden für Ihre Anwendung funktioniert, können Sie mit dem [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md) fortfahren oder [die ersten Schritte mit bedingtem Zugriff ausführen](../conditional-access/app-based-conditional-access.md).