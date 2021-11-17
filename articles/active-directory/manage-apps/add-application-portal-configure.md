---
title: 'Schnellstart: Konfigurieren von Eigenschaften für eine Unternehmensanwendung'
titleSuffix: Azure AD
description: Konfigurieren der Eigenschaften einer Unternehmensanwendung in Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 6179330f62f1966d099cfca93fc3885895af5e40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550796"
---
# <a name="quickstart-configure-enterprise-application-properties"></a>Schnellstart: Konfigurieren von Eigenschaften für eine Unternehmensanwendung

In diesem Schnellstart verwenden Sie Azure Active Directory Admin Center, um die Eigenschaften einer Unternehmensanwendung zu konfigurieren, die Sie ihrem Azure Active Directory-Mandanten (Azure AD) zuvor hinzugefügt haben.

Sie können die folgenden allgemeinen Attribute einer Unternehmensanwendung konfigurieren:

- **Aktiviert für die Benutzeranmeldung?** – Legt fest, ob sich Benutzer, die der Anwendung zugewiesen sind, anmelden können.
- **Benutzerzuweisung erforderlich?** – Legt fest, ob sich Benutzer, die der Anwendung nicht zugewiesen sind, anmelden können.
- **Für Benutzer sichtbar?** – Legt fest, ob den einer App zugewiesenen Benutzern die Anwendung unter „Meine Apps“ und im Microsoft 365-App-Startfeld angezeigt wird. (Siehe das Waffelmenü in der oberen linken Ecke einer Microsoft 365-Website.)
- **Logo** – Bestimmt das Logo, das die Anwendung darstellt.
- **Hinweise** – Bietet einen Ort zum Hinzufügen von Hinweisen, die für die Anwendung gelten.

Es wird empfohlen, zum Testen der Schritte in diesem Schnellstart keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Eigenschaften einer Unternehmensanwendung in Azure Active Directory benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Ausführen der Schritte unter [Schnellstart: Hinzufügen einer Unternehmensanwendung](add-application-portal.md).

## <a name="configure-application-properties"></a>Konfigurieren von Anwendungseigenschaften

Anwendungseigenschaften steuern, wie die Anwendung dargestellt wird und wie auf die Anwendung zugegriffen wird.

So bearbeiten Sie die Anwendungseigenschaften:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Unternehmensanwendungen** aus. Der Bereich **Alle Anwendungen** wird geöffnet und zeigt eine Liste der Anwendungen in Ihrem Azure AD-Mandanten an. Suchen Sie nach der gewünschten Anwendung, und wählen Sie diese aus. Beispiel: **Azure AD SAML Toolkit 1**.
1. Wählen Sie im Abschnitt **Verwalten** die Option **Eigenschaften** aus, um den Bereich **Eigenschaften** zur Bearbeitung zu öffnen.
1. Wählen Sie **Ja** oder **Nein** aus, um zu entscheiden, ob die Anwendung für Benutzeranmeldung aktiviert ist.
1. Wählen **Sie Ja** oder **Nein** aus, um zu entscheiden, ob sich nur Benutzerkonten anmelden können, die der Anwendung zugewiesen wurden.
1. Wählen Sie **Ja** oder **Nein** aus, um zu entscheiden, ob Benutzer, die einer Anwendung zugewiesen sind, diese in „Meine Apps“ und Microsoft 365-Portalen anzeigen können. 

    :::image type="content" source="media/add-application-portal-configure/configure-properties.png" alt-text="Konfigurieren Sie die Eigenschaften einer Unternehmensanwendung.":::

## <a name="use-a-custom-logo"></a>Verwenden eines benutzerdefinierten Logos

Das Anwendungslogo wird im Portal „Meine Apps“ und im Microsoft 365-Portal angezeigt. Es wird auch angezeigt, wenn Administratoren diese Anwendung im Unternehmensanwendungskatalog anzeigen. Benutzerdefinierte Logos müssen genau 215x215 Pixel groß sein und im PNG-Format vorliegen. Es wird empfohlen, für Ihr Anwendungslogo einen einfarbigen Hintergrund ohne Transparenz zu verwenden, damit es für Benutzer optimal aussieht.

So verwenden Sie ein benutzerdefiniertes Logo:

1. Erstellen Sie ein Logo mit 215 x 215 Pixeln, und speichern Sie es im PNG-Format.
1. Wählen Sie das Symbol in **Datei auswählen** aus, um das Logo hochzuladen.
1. Wählen Sie abschließend **Speichern** aus.

Die Miniaturansicht für das Logo wird nicht sofort aktualisiert. Sie können den Bereich **Eigenschaften** schließen und erneut öffnen, um die aktualisierte Miniaturansicht anzuzeigen.

## <a name="add-notes"></a>Hinzufügen von Notizen

Sie können die Eigenschaft **Hinweise** verwenden, um für die Verwaltung der Anwendung in Azure AD relevante Informationen hinzuzufügen. Die Eigenschaft **Hinweise** ist ein Feld für freien Text mit einer maximalen Größe von 1024 Zeichen.

So geben Sie Hinweise für die Anwendung ein:

1. Geben Sie die Hinweise ein, die für die Anwendung gelten sollen.
1. Wählen Sie **Speichern** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den nächsten Schnellstart durcharbeiten möchten, behalten Sie die von Ihnen erstellte Unternehmensanwendung. Andernfalls können Sie sie löschen, um Ihren Mandanten zu bereinigen. Weitere Informationen finden Sie unter [Löschen einer Anwendung](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die Anwendungen in Ihrem Azure AD-Mandanten suchen und anzeigen.
> [!div class="nextstepaction"]
> [Anwendungen anzeigen](view-applications-portal.md)
