---
title: 'Schnellstart: Anzeigen von Unternehmensanwendungen'
description: Zeigen Sie die Unternehmensanwendungen an, die für die Verwendung Ihres Azure Active Directory-Mandanten registriert sind.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2021
ms.author: davidmu
ms.reviewer: arvinh
ms.openlocfilehash: 4da70b29fb7d5a8920db05e0d7c28f5c29a101fd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553133"
---
# <a name="quickstart-view-enterprise-applications"></a>Schnellstart: Anzeigen von Unternehmensanwendungen

In diesem Schnellstart erfahren Sie, wie Sie Azure Active Directory Admin Center verwenden, um nach den Unternehmensanwendungen zu suchen und die Anwendungen anzuzeigen, die bereits in Ihrem Azure Active Directory-Mandanten (Azure AD) konfiguriert sind.

Es wird empfohlen, zum Testen der Schritte in diesem Schnellstart keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die für Ihren Azure AD-Mandanten registrierten Anwendungen anzuzeigen:

- Ein Azure-Konto. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Ausführen der Schritte unter [Schnellstart: Hinzufügen einer Unternehmensanwendung](add-application-portal.md).

## <a name="view-a-list-of-applications"></a>Anzeigen einer Liste der Anwendungen

So zeigen Sie die Unternehmensanwendungen an, die in Ihrem Mandanten registriert sind:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Unternehmensanwendungen** aus. Der Bereich **Alle Anwendungen** wird geöffnet und zeigt eine Liste von Anwendungen in Ihrem Azure AD-Mandanten an.

    :::image type="content" source="media/view-applications-portal/view-enterprise-applications.png" alt-text="Zeigen Sie die registrierten Anwendungen in Ihrem Azure AD-Mandanten an.":::

1. Wenn Sie weitere Anwendungen anzeigen möchten, wählen Sie unten in der Liste **Mehr anzeigen** aus. Falls Ihr Mandant eine große Anzahl von Anwendungen enthält, kann es einfacher sein, direkt nach einer bestimmten Anwendung zu suchen, als durch die Liste zu scrollen.

## <a name="search-for-an-application"></a>Suchen einer Anwendung

So suchen Sie nach einer bestimmten Anwendung:

1. Wählen Sie im Menü **Anwendungstyp** die Option **Alle Anwendungen** und dann **Anwenden** aus.
1. Geben Sie den Namen der gewünschten Anwendung ein. Wenn die Anwendung zu Ihrem Azure AD-Mandanten hinzugefügt wurde, wird sie in den Suchergebnissen angezeigt. Beispielsweise können Sie nach der Anwendung **Azure AD SAML Toolkit 1** suchen, die in den vorherigen Schnellstarts verwendet wird. 
1. Geben Sie die ersten Buchstaben eines Anwendungsnamens ein.

## <a name="select-viewing-options"></a>Auswählen der Anzeigeoptionen

Wählen Sie Optionen entsprechend den von Ihnen gesuchten Inhalten aus:

1. Sie können die Anwendungen nach **Anwendungstyp**, **Anwendungsstatus** und **Anwendungssichtbarkeit** anzeigen.
1. Wählen Sie unter **Anwendungstyp** eine der folgenden Optionen aus:
    - Mit der Option **Unternehmensanwendungen** werden nicht von Microsoft stammende Anwendungen angezeigt.
    - Mit der Option **Microsoft-Anwendungen** werden Microsoft-Anwendungen angezeigt.
    - Mit der Option **Alle Anwendungen** werden nicht von Microsoft stammende Anwendungen und Microsoft-Anwendungen angezeigt.
1. Wählen Sie unter **Anwendungsstatus** die Option **Any** (Alle), **Deaktiviert** oder **Aktiviert** aus. Mit der Option **Any** (Alle) werden sowohl deaktivierte als auch aktivierte Anwendungen angezeigt.
1. Wählen Sie unter **Anwendungssichtbarkeit** die Option **Any** (Alle) oder **Ausgeblendet**. Mit der Option **Ausgeblendet** werden Anwendungen angezeigt, die sich zwar im Mandanten befinden, aber nicht für Benutzer sichtbar sind.
1. Klicken Sie nach dem Auswählen der gewünschten Optionen auf **Anwenden**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Testanwendung mit dem Namen **Azure AD SAML Toolkit 1** erstellt haben, die in den Schnellstarts verwendet wurde, können Sie erwägen, diese jetzt zu löschen, um Ihren Mandanten zu bereinigen. Weitere Informationen finden Sie unter [Löschen einer Anwendung](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie eine Unternehmensanwendung löschen.
> [!div class="nextstepaction"]
> [Löschen einer Anwendung](add-application-portal.md)
