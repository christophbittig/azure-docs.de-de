---
title: 'Schnellstart: Löschen einer Anwendung aus Ihrem Mandanten'
titleSuffix: Azure AD
description: In dieser Schnellstartanleitung wird das Azure-Portal verwendet, um eine Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory) zu löschen.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: a29ac9aa0cecaefb3d9ada87b60d2bebb8c5269b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112419"
---
# <a name="quickstart-delete-an-application-from-your-tenant"></a>Schnellstart: Löschen einer Anwendung aus Ihrem Mandanten

In dieser Schnellstartanleitung wird das Azure-Portal verwendet, um eine Anwendung zu löschen, die Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) hinzugefügt wurde.

Weitere Informationen zum einmaligen Anmelden und Azure finden Sie unter [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Löschen einer Anwendung von Ihrem Azure AD-Mandanten benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Optional: Gehen Sie den Schnellstart [Anzeigen Ihrer Apps](view-applications-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Hinzufügen einer App](add-application-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Konfigurieren einer App](add-application-portal-configure.md) durch.
- Optional: Arbeiten Sie den Artikel [Zuweisen von Benutzern zu einer App](add-application-portal-assign-users.md) durch.
- Optional: Gehen Sie den Schnellstart [Einrichten des einmaligen Anmeldens](add-application-portal-setup-sso.md) durch.

>[!IMPORTANT]
>Verwenden Sie zum Testen der in dieser Schnellstartanleitung aufgeführten Schritte keine Produktionsumgebung.

> [!NOTE]
>Um eine Anwendung aus Azure AD löschen zu können, muss dem Benutzer eine der folgenden Rollen zugewiesen werden: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Löschen einer Anwendung von Ihrem Azure AD-Mandanten

Löschen Sie eine Anwendung wie folgt von Ihrem Azure AD-Mandanten:

1. Wählen Sie im Azure AD-Portal die Option **Unternehmensanwendungen** aus. Suchen Sie dann nach der zu löschenden Anwendung, und wählen Sie diese aus. In diesem Fall löschen Sie **360 Online**.
1. Wählen Sie im linken Bereich im Abschnitt **Verwalten** die Option **Eigenschaften** aus.
1. Wählen Sie **Löschen** aus, und wählen Sie dann **Ja** aus, um zu bestätigen, dass die App aus Ihrem Azure AD-Mandanten gelöscht werden soll.

:::image type="content" source="media/add-application-portal/delete-application.png" alt-text="Screenshot des Bildschirms „Eigenschaften“, in dem zu sehen ist, wie das Logo geändert wird":::

> [!TIP]
> Sie können die App-Verwaltung mithilfe der Graph-API automatisieren. Informationen hierzu finden Sie unter [Automatisieren einer SAML-basierten SSO-App-Konfiguration mit Microsoft Graph-API](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Schnellstartserie abgeschlossen haben, sollten Sie die App löschen, um Ihren Testmandanten zu bereinigen. Das Löschen der App wurde in dieser Schnellstartanleitung behandelt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Schnellstartserie abgeschlossen. Informieren Sie sich als Nächstes über [einmaliges Anmelden (Single Sign-On, SSO)](what-is-single-sign-on.md). Alternativ können Sie sich über die bewährten Methoden bei der App-Verwaltung informieren.
> [!div class="nextstepaction"]
> [Bewährte Methoden für die Anwendungsverwaltung](application-management-fundamentals.md)
