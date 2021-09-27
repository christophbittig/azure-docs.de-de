---
title: Bereinigen Sie die Ressourcen und löschen Sie einen Mandanten - Azure Active Directory B2C
description: Schritte, die beschreiben, wie sie einen Azure AD B2C Mandanten löschen. Erfahren Sie, wie Sie alle Mandanten-Ressourcen löschen und dann den Mandanten löschen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 243943b518c422e63261034e860fc174dc4f1fcd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588901"
---
# <a name="clean-up-resources-and-delete-the-tenant"></a>Bereinigen Sie die Ressourcen und löschen Sie den Mandanten

Wenn Sie die Azure AD B2C-Tutorials abgeschlossen haben, können Sie den Mandanten löschen, den Sie zum Testen oder Trainieren verwendet haben. Um den Mandanten zu löschen, müssen Sie zunächst alle Mandanten-Ressourcen löschen. In diesem Artikel führen Sie Folgendes durch:

> [!div class="checklist"]
> * Verwenden Sie die Option **Mandanten löschen** zum Identifizieren von Bereinigungsaufgaben
> * Löschen Sie Mandanten-Ressourcen (Benutzerabläufe, Identitätsanbieter, Anwendungen, Benutzer)
> * Löschen Sie den Mandanten

## <a name="identify-cleanup-tasks"></a>Identifizieren Sie Bereinigungsaufgaben

1. Melden Sie sich am [Azure Portal](https://portal.azure.com/) mit einer globalen Administrator- oder Abonnement-Administratorenrolle an. Verwenden Sie dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wähle den Dienst **Azure Active Directory** aus.
1. Wählen Sie unter **Verwalten** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** die Option **Ja** und dann **Speichern** aus.
1. Melden Sie sich vom Azure-Portal ab und dann wieder an, um Ihren Zugriff zu aktualisieren. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wähle den Dienst **Azure Active Directory** aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Mandant löschen** aus. Die Spalte **Erforderliche Maßnahme** gibt die Ressourcen an, die Sie entfernen müssen, bevor Sie den Mandanten löschen können.

   ![Löschen Sie Mandanten-Aufgaben](media/tutorial-delete-tenant/delete-tenant-tasks.png)

## <a name="delete-tenant-resources"></a>Löschen Sie Mandanten-Ressourcen

Wenn Sie die Bestätigungsseite aus dem vorherigen Abschnitt geöffnet haben, können Sie die Links in der Spalte **Erforderliche Maßnahmen** verwenden, um die Azure-Portalseiten zu öffnen, auf denen Sie diese Ressourcen entfernen können. Oder, Sie können Mandanten-Ressourcen aus dem Azure AD B2C-Dienst heraus entfernen, indem Sie die folgenden Schritte ausführen.

1. Melden Sie sich am [Azure Portal](https://portal.azure.com/) mit einer globalen Administrator- oder Abonnement-Administratorenrolle an. Verwenden Sie dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie **den Azure AD B2C** Dienst aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Löschen Sie alle Benutzer *mit Ausnahme* des Administratorkontos, mit dem Sie derzeit angemeldet sind: Wählen Sie unter **Verwalten** die Option **Benutzer** aus. Aktivieren Sie auf der Seite **Alle Benutzer** das Kontrollkästchen neben jedem Benutzer (mit Ausnahme des Administratorkontos, mit dem Sie derzeit angemeldet sind). Wählen Sie **Löschen** und wählen Sie dann **Ja**, wenn Sie dazu aufgefordert werden.

   ![Löschen von Benutzern](media/tutorial-delete-tenant/delete-users.png)

1. Löschen Sie die App-Registrierungen und die *b2c-Extensions-App*: Wählen Sie unter **Verwalten** die **App-Registrierungen**. Wählen Sie die **Alle Anwendungen**-Registerkarte. Wählen Sie eine Anwendung aus und wählen Sie dann **Löschen**. Wiederholen Sie dies für alle Anwendungen, einschließlich der Anwendung **b2c-Extensions-App.**

   ![Löschen der Anwendung](media/tutorial-delete-tenant/delete-applications.png)

1. Löschen Sie alle von Ihnen konfigurierten Identitätsanbieter: Wählen Sie unter **Verwalten** die Option **Identitätsanbieter** aus. Wählen Sie einen von Ihnen konfigurierten Identitätsanbieter und dann **Löschen** aus.

   ![Löschen Sie den Identitätsanbieter](media/tutorial-delete-tenant/identity-providers.png)

1. Löschen Sie die Benutzerflows: Wählen Sie unter **Richtlinien** die Option **Benutzerflows aus**. Wählen Sie neben jedem Benutzerfluss die Ellipsen (...) und wählen Sie dann **Löschen** aus.

   ![Löschen Sie die Benutzerflows](media/tutorial-delete-tenant/user-flow.png)

1. Löschen Sie die Richtlinienschlüssel: Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** und anschließend die Option **Richtlinienschlüssel** aus. Wählen Sie neben jedem Richtlinienschlüssel Ellipsen (...) und dann **Löschen** aus.

1. Löschen Sie die benutzerdefinierte Richtlinien : Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus, wählen Sie **Benutzerdefinierte Richtlinien**, und löschen Sie dann alle Richtlinien.

## <a name="delete-the-tenant"></a>Löschen Sie den Mandanten

1. Melden Sie sich am [Azure Portal](https://portal.azure.com/) mit einer globalen Administrator- oder Abonnement-Administratorenrolle an. Verwenden Sie dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wähle den Dienst **Azure Active Directory** aus.
1. Wenn Sie sich selbst noch keine Zugriffsverwaltungsberechtigungen erteilt haben, gehen Sie wie folgt vor:

   * Wählen Sie unter **Verwalten** die Option **Eigenschaften** aus.
   * Wählen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** die Option **Ja** und dann **Speichern** aus.
   * Melden Sie sich vom Azure-Portal ab und dann wieder an, um Ihren Zugriff zu aktualisieren und wählen Sie den **Azure Active Directory** Service.

1. Wählen Sie auf der Seite **Übersicht** die Option **Mandant löschen** aus.

   ![Löschen Sie den Mandanten](media/tutorial-delete-tenant/delete-tenant.png)

1. Folgen Sie den Anweisungen auf dem Bildschirm, um den Prozess abzuschließen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Löschen Sie Ihre Mandanten-Ressourcen
> * Löschen Sie den Mandanten

Als nächstes erfahren Sie mehr über die ersten Schritte mit Azure AD B2C- [Benutzerabläufen und benutzerdefinierten Richtlinien](user-flow-overview.md).
