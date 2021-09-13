---
title: 'Gewusst wie: Abrufen einer vollständigen Liste aller Apps, die die Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL) in Ihrem Mandanten verwenden | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Anleitung erhalten Sie eine vollständige Liste aller Apps, die in Ihrem Mandanten die ADAL verwenden.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/22/2021
ms.author: shermanouko
ms.custom: aaddev, has-adal-ref
ms.reviewer: aiwang, marsma
ms.openlocfilehash: 9d9c0f9787360ed599d586295181ef359fdff293
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418668"
---
# <a name="how-to-get-a-complete-list-of-apps-using-adal-in-your-tenant"></a>Gewusst wie: Abrufen einer vollständigen Liste von Apps, die in Ihrem Mandanten die ADAL verwenden

Die Unterstützung der Active Directory-Authentifizierungsbibliothek (ADAL) endet am 30. Juni 2022. Apps, die die ADAL unter vorhandenen Betriebssystemversionen nutzen, funktionieren anschließend weiterhin, erhalten aber keinen technischen Support und keine Sicherheitsupdates mehr. Ohne laufende Sicherheitsupdates werden Apps, die die ADAL verwenden, zunehmend anfälliger für die neuesten Sicherheitsangriffsmuster. Dieser Artikel enthält Anleitungen zur Verwendung von Azure Monitor-Arbeitsmappen, um eine Liste aller Apps zu erhalten, die in Ihrem Mandanten die ADAL verwenden.

## <a name="sign-ins-workbook"></a>Arbeitsmappe „Anmeldungen“

Arbeitsmappen sind eine Reihe von Abfragen, die in Azure AD-Protokollen (Azure Active Directory) verfügbare Informationen sammeln und visualisieren. [Weitere Informationen zum Anmeldeprotokollschema finden Sie hier.](../reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) Die Arbeitsmappe „Anmeldungen“ im Azure AD-Verwaltungsportal enthält nun eine Tabelle, mit der Sie ermitteln können, welche Anwendungen die ADAL verwenden, und wie oft sie verwendet werden. Zunächst erfahren Sie, wie Sie auf die Arbeitsmappe zugreifen, bevor Sie die Visualisierung für die Anwendungsliste anzeigen.

## <a name="step-1-integrate-audit-logs-with-azure-monitor"></a>Schritt 1: Integrieren von Überwachungsprotokollen in Azure Monitor

Befolgen Sie die Schritte unter [Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), bevor Sie auf die Arbeitsmappe zugreifen. Nur nach der Azure Monitor-Integration erstellte Anmelde- und Überwachungsereignisse werden gespeichert.

## <a name="step-2-access-sign-ins-workbook-in-azure-portal"></a>Schritt 2: Zugreifen auf die Arbeitsmappe für Anmeldungen im Azure-Portal

Nachdem Sie Ihre Azure AD-Anmelde- und -Überwachungsprotokolle wie in der Azure Monitor-Integration angegeben in Azure Monitor integriert haben, greifen Sie auf die Anmeldungen-Arbeitsmappe zu:

   1. Anmelden beim Azure-Portal 
   1. Navigieren Sie zu  **Azure Active Directory** > **Überwachung** > **Arbeitsmappen**. 
   1. Öffnen Sie im Abschnitt **Nutzung** die Arbeitsmappe  **Anmeldungen**. 

   :::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/sign-in-workbook.png" alt-text="Screenshot der Azure Active Directory-Portalarbeitsmappen-Benutzeroberfläche mit Hervorhebung der Anmeldungen-Arbeitsmappe.":::

## <a name="step-3-identify-apps-that-use-adal"></a>Schritt 3: Identifizieren von Apps, die die ADAL verwenden

In der Tabelle am unteren Rand der Arbeitsmappenseite „Anmeldungen“ sind Apps aufgeführt, die kürzlich die ADAL verwendet haben. Sie können auch eine Liste der Apps exportieren. Aktualisieren Sie diese Apps, um die MSAL zu verwenden.
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/active-directory-auth-library-apps-present.png" alt-text="Screenshot der Anmeldungen-Arbeitsmappe, in der Apps angezeigt werden, die die Active Directory-Authentifizierungsbibliothek verwenden.":::
    
Wenn keine Apps die ADAL verwenden, zeigt die Arbeitsmappe eine Ansicht wie unten dargestellt an. 
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/no-active-directory-auth-library-apps.png" alt-text="Screenshot der Anmeldungen-Arbeitsmappe, wenn keine App die Active Directory-Authentifizierungsbibliothek verwendet.":::

## <a name="step-4-update-your-code"></a>Schritt 4: Aktualisieren des Codes

Nachdem Sie Ihre Apps, die die ADAL verwenden, identifiziert haben, migrieren Sie sie wie unten dargestellt je nach Anwendungstyp zur MSAL.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur MSAL, einschließlich Nutzungsinformationen und für verschiedene Programmiersprachen und Anwendungstypen verfügbarer Bibliotheken, finden Sie unter:

- [Abrufen und Zwischenspeichern von Token mit MSAL](msal-acquire-cache-tokens.md)
- [Anwendungskonfigurationsoptionen](msal-client-application-configuration.md)
- [Liste der MSAL-Authentifizierungsbibliotheken](reference-v2-libraries.md)
