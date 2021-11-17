---
title: 'Schnellstart: Hinzufügen einer Unternehmensanwendung'
description: Hinzufügen einer Unternehmensanwendung in Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: f3d9cd36b821b1dca5311c266b84e259eab005c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546954"
---
# <a name="quickstart-add-an-enterprise-application"></a>Schnellstart: Hinzufügen einer Unternehmensanwendung

In diesem Schnellstart verwenden Sie Azure Active Directory Admin Center, um Ihrem Azure Active Directory-Mandanten (Azure AD) eine Unternehmensanwendung hinzuzufügen. Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Unternehmensanwendungen. Viele der von Ihrer Organisation verwendeten Anwendungen sind wahrscheinlich bereits im Katalog enthalten. Dieser Quickstart verwendet die Anwendung **Azure AD SAML Toolkit** als Beispiel, aber die Konzepte gelten für die meisten [Unternehmensanwendungen Katalog](../saas-apps/tutorial-list.md).

Es wird empfohlen, zum Testen der Schritte in diesem Schnellstart keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Ihrem Azure AD-Mandanten eine Unternehmensanwendung hinzuzufügen:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

## <a name="add-an-enterprise-application"></a>Hinzufügen einer Unternehmensanwendung

So fügen Sie Ihrem Azure AD-Mandanten eine Unternehmensanwendung hinzu:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Unternehmensanwendungen** aus. Der Bereich **Alle Anwendungen** wird geöffnet und zeigt eine Liste der Anwendungen in Ihrem Azure AD-Mandanten an.
1. Wählen Sie im Bereich **Unternehmensanwendungen** die Option **Neue Anwendung** aus.
1. Der Bereich **Azure AD-Katalog durchsuchen** wird mit Kacheln für Cloudplattformen, lokale Anwendungen und ausgewählten Anwendungen geöffnet. Für die im Abschnitt **Ausgewählte Anwendungen** aufgeführten Anwendungen wird mithilfe von Symbolen angegeben, ob sie die einmalige Verbundanmeldung und -bereitstellung unterstützen. Suchen Sie nach der Anwendung, und wählen Sie diese aus. In diesem Schnellstart wird **Azure AD SAML Toolkit** verwendet.

    :::image type="content" source="media/add-application-portal/browse-gallery.png" alt-text="Suchen Sie im Katalog der Unternehmensanwendungen nach der Anwendung, die Sie hinzufügen möchten.":::

1. Geben Sie einen Namen ein, den Sie verwenden möchten, um die Instanz der Anwendung zu bezeichnen. Beispiel: `Azure AD SAML Toolkit 1`.
1. Klicken Sie auf **Erstellen**.

Wenn Sie eine Anwendung installieren möchten, die auf OpenID Connect basierendes SSO verwendet, wird anstelle der Schaltfläche **Erstellen** eine Schaltfläche angezeigt, die Sie zur Anmelde- oder Registrierungsseite der Anwendung weiterleitet, je nachdem, ob Sie bereits über ein Konto verfügen. Weitere Informationen finden Sie unter [Hinzufügen einer auf OpenID Connect basierenden Anwendung für einmaliges Anmelden](add-application-portal-setup-oidc-sso.md). Nach der Anmeldung wird die Anwendung Ihrem Mandanten hinzugefügt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den nächsten Schnellstart durcharbeiten möchten, behalten Sie die von Ihnen erstellte Unternehmensanwendung. Andernfalls können Sie sie löschen, um Ihren Mandanten zu bereinigen. Weitere Informationen finden Sie unter [Löschen einer Anwendung](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie ein Benutzerkonto erstellen und es der Unternehmensanwendung zuweisen, die Sie hinzugefügt haben.
> [!div class="nextstepaction"]
> [Erstellen und Zuweisen eines Benutzerkontos](add-application-portal-assign-users.md)
