---
title: 'Schnellstart: Löschen einer Unternehmensanwendung'
description: Löschen einer Unternehmensanwendung in Azure Active Directory.
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
ms.openlocfilehash: 16bc055ed2b47ee5c212fa26387599a8d4ada7a8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058196"
---
# <a name="quickstart-delete-an-enterprise-application-in-azure-active-directory"></a>Schnellstart: Löschen einer Unternehmensanwendung in Azure Active Directory

In diesem Schnellstart verwenden Sie Azure Active Directory Admin Center, um eine Unternehmensanwendung zu löschen, die Ihrem Azure Active Directory-Mandanten (Azure AD) hinzugefügt wurde.

Es wird empfohlen, zum Testen der Schritte in diesem Schnellstart keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um eine Unternehmensanwendung zu löschen:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Ausführen der Schritte unter [Schnellstart: Hinzufügen einer Unternehmensanwendung](add-application-portal.md).

## <a name="delete-an-enterprise-application"></a>Löschen einer Unternehmensanwendung

So löschen Sie eine Unternehmensanwendung:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Unternehmensanwendungen** aus. Der Bereich **Alle Anwendungen** wird geöffnet und zeigt eine Liste von Anwendungen in Ihrem Azure AD-Mandanten an. Suchen Sie nach der Anwendung, die Sie löschen möchten, und wählen Sie diese aus. Beispiel: **Azure AD SAML Toolkit 1**.
1. Wählen Sie im Menü auf der linken Seite im Abschnitt **Verwalten** die Option **Eigenschaften** aus.
1. Wählen Sie oben im Bereich **Eigenschaften** die Option **Löschen** aus, und wählen Sie dann **Ja** aus, um zu bestätigen, dass die Anwendung aus Ihrem Azure AD-Mandanten gelöscht werden soll.

    :::image type="content" source="media/delete-application-portal/delete-application.png" alt-text="Löschen einer Unternehmensanwendung.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Schnellstartserie abgeschlossen haben, sollten Sie die Anwendung löschen, um Ihren Testmandanten zu bereinigen. Das Löschen der Anwendung wurde in diesem Schnellstart behandelt.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Planung einer Bereitstellung für einmaliges Anmelden.
> [!div class="nextstepaction"]
> [Planen der Bereitstellung des einmaligen Anmeldens](plan-sso-deployment.md)
