---
title: Einmaliges Anmelden für die Azure-Integration mit Logz.io – Azure-Partnerlösungen
description: Erfahren Sie, wie Sie das einmalige Anmelden für die Azure-Integration mit Logz.io. einrichten.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 86ef7f781e3093b2daa78a393963b9053c9f5982
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057569"
---
# <a name="set-up-logzio-single-sign-on"></a>Einrichten des einmaligen Anmeldens mit Logz.io

In diesem Artikel wird beschrieben, wie Sie einmaliges Anmelden (SSO) in Azure Active Directory einrichten. Einmaliges Anmelden für die Azure-Integration mit Logz.io ist optional.

## <a name="configure-single-sign-on"></a>Einmaliges Anmelden konfigurieren

Wenn Sie die Funktion „Einmaliges Anmelden“ (SSO) für Security Assertion Markup Language (SAML) in der Logz.io-Ressource verwenden möchten, müssen Sie eine Unternehmensanwendung einrichten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Menü des Azure-Portals die Option **Azure Active Directory** aus oder suchen Sie nach _Azure Active Directory_.
1. Navigieren Sie zu **Verwalten** > **Unternehmensanwendungen** und wählen Sie die Schaltfläche **Neue Anwendung** aus.
1. Suchen Sie nach _Logz.io_ und wählen Sie die SAML-Anwendung **Logz.io – Azure AD Integration** und dann die Option **Erstellen** aus.

   :::image type="content" source="./media/sso-setup/gallery.png" alt-text="Durchsuchen des Azure Active Directory-Katalogs":::.

1. Navigieren Sie zur **Übersicht** und kopieren Sie die **Anwendungs-ID** der SSO Anwendung.

   Notieren Sie sich die _Anwendungs-ID_ da Sie sie in einem späteren Schritt zur Konfiguration von SAML verwenden werden.

   :::image type="content" source="./media/sso-setup/app-id.png" alt-text="Kopieren Sie die Anwendungs-ID":::.

1. Wählen Sie die Kachel namens **2. Einmalige Anmelden einrichten** aus.

   :::image type="content" source="./media/sso-setup/setup.png" alt-text="Einmaliges Anmelden einrichten":::.

1. Wählen Sie als SSO-Methode die das Protokoll **SAML** aus.

   :::image type="content" source="./media/sso-setup/saml.png" alt-text="Einmaliges Anmelden mit SAML":::.

1. Wählen Sie in der SAML-Konfiguration die vorhandene Werte aus oder verwenden Sie die Textfelder, um die erforderlichen Felder **Bezeichner** und **Antwort-URL** einzugeben. Um neue Einträge zu erstellen, verwenden Sie die _Anwendungs-ID_, die Sie in einem früheren Schritt kopiert haben.

   Verwenden Sie die folgenden Muster, um neue Werte hinzuzufügen:

   - **Bezeichner**: `urn:auth0:logzio:<Application ID>`
   - **Antwort-URL**: `https://logzio.auth0.com/login/callback?connection=<Application ID>`

   :::image type="content" source="./media/sso-setup/saml-config.png" alt-text="Grundlegende SAML-Konfiguration":::.

1. Wählen Sie bei der Aufforderung **Einstellungen für das einmalige Anmelden speichern** die Option **Ja** und dann **Speichern** aus.

   Wählen Sie bei der SSO-Eingabeaufforderung **Nein, ich teste später** aus.

1. Nachdem die SAML-Konfiguration gespeichert wurde, gehen Sie zu **Verwalten** > **Eigenschaften** und setzen Sie die Option **Benutzerzuordnung erforderlich?** auf **Nein**. Wählen Sie **Speichern** aus.

   :::image type="content" source="./media/sso-setup/properties.png" alt-text="Eigenschaften für das einmalige Anmelden":::.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen um Probleme mit einmaligem Anmelden zu beheben, feinden Sie unter [Problembehandlung](troubleshoot.md).
- Informationen zum Erstellen einer Logz.io Integration finden Sie unter [Schnellstart: Erstellen einer Logz.io-Ressource in Azure-Portal](create.md).
