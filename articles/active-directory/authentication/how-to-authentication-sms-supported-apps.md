---
title: App-Unterstützung für die SMS-basierte Authentifizierung in Azure Active Directory
description: Erfahren Sie, welche Apps von Benutzern für die Anmeldung bei Azure Active Directory per SMS unterstützt werden.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: aanjusingh
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a35994441ad2b0cd2ecdf39675ef51840dc0de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027947"
---
# <a name="app-support-for-sms-based-authentication"></a>App-Unterstützung für die SMS-basierte Authentifizierung

Die SMS-basierte Authentifizierung ist für Microsoft-Apps verfügbar, die in Microsoft Identity Platform (Azure AD) integriert sind. In der Tabelle sind einige der Web-Apps und mobilen Apps aufgeführt, die die SMS-basierte Authentifizierung unterstützen. [Nehmen Sie Kontakt mit uns aus](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789), wenn Sie eine App hinzufügen oder überprüfen möchten. 

| App | Web-/Browser-App | Native mobile App |
| --- |:---:|:---:|
| Office 365 – Microsoft Online Services* | ● |   |
| Microsoft One Note | ● |   |
| Microsoft Teams | ● | ● |
| Microsoft Intune-Portal/Unternehmensportal | ● | ● |
| Portal „Meine Apps“ | ● |Nicht verfügbar|
| Microsoft Forms | ● |Nicht verfügbar|
| Microsoft Edge | ● |   |
| Microsoft Power BI | ● |   |
| Microsoft Stream | ● |   |
| Microsoft Power Apps | ● |   |
| Microsoft Azure | ● | ● |
| Microsoft Authenticator |   | ● |
| Azure Virtual Desktop | ● |  | 

*_Die SMS-Anmeldung ist nicht für Office-Anwendungen, z. B. Word, Excel usw., verfügbar, wenn direkt im Web auf sie zugegriffen wird. Sie ist jedoch verfügbar, wenn der Zugriff über die [Office 365-Web-App](https://www.office.com)_ erfolgt.

Die oben erwähnten Microsoft-Apps unterstützen die SMS-Anmeldung, da sie die Microsoft Identity-Anmeldung (`https://login.microsoftonline.com/`) verwenden, die Benutzern die Eingabe einer Telefonnummer und eines SMS-Codes ermöglicht.

## <a name="unsupported-microsoft-apps"></a>Nicht unterstützte Microsoft-Apps

Microsoft 365 Desktop-Apps (Windows oder Mac) und Microsoft 365-Web-Apps (mit Ausnahme von Microsoft OneNote), auf die direkt im Web zugegriffen wird, unterstützen keine SMS-Anmeldung. Diese Apps verwenden die Microsoft Office-Anmeldung (`https://office.live.com/start/*`), die ein Kennwort für die Anmeldung erfordert.
Aus dem gleichen Grund wird die SMS-Anmeldung von mobilen Microsoft Office-Apps nicht unterstützt (mit Ausnahme von Microsoft Teams, Intune-Unternehmensportal und Microsoft Azure).

| Nicht unterstützte Microsoft-Apps| Beispiele |
| --- | --- |
| Native Microsoft-Desktop-Apps | Microsoft Teams, O365-Apps, Word, Excel usw.|
| Native mobile Microsoft-Apps (mit Ausnahme von Microsoft Teams, Intune-Unternehmensportal und Microsoft Azure) | Outlook, Edge, Power BI, Stream, SharePoint, Power Apps, Word usw.|
| Microsoft 365-Web-Apps (zugriff direkt im Web) | [Outlook](https://outlook.live.com/owa/), [Word](https://office.live.com/start/Word.aspx), [Excel](https://office.live.com/start/Excel.aspx), [PowerPoint](https://office.live.com/start/PowerPoint.aspx), [OneDrive](https://onedrive.live.com/about/signin)|  

## <a name="support-for-non-microsoft-apps"></a>Unterstützung für Nicht-Microsoft-Apps 

So machen Sie Nicht-Microsoft-Apps mit dem SMS-Anmeldefeature kompatibel: 
- Integrieren Sie Nicht-Microsoft-Web-Apps in Azure AD, und verwenden Sie die Azure AD-Authentifizierung. Verwenden Sie [SAML](../manage-apps/add-application-portal-setup-sso.md) (Security Assertion Markup Language) oder [OIDC](../manage-apps/add-application-portal-setup-oidc-sso.md) (Open ID Connect) für die Integration in Azure AD SSO. 
- Integrieren Sie lokale Nicht-Microsoft-Apps mithilfe des [Azure AD-Anwendungsproxys](../app-proxy/application-proxy-add-on-premises-application.md) in Azure AD.
- Integrieren Sie Nicht-Microsoft-Client-Apps in [Microsoft Identity Platform](../develop/v2-overview.md) für die Authentifizierung. 
    - [Beispiel-App für iOS](../develop/tutorial-v2-ios.md)
    - [Beispiel-App für Android](../develop/tutorial-v2-android.md)

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren der SMS-basierten Anmeldung für Benutzer](howto-authentication-sms-signin.md)
- Informationen zum Aktivieren der SMS-Anmeldung für native mobile Apps mithilfe von MSAL-Bibliotheken finden Sie unter den folgenden Links: 
  - [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)
  - [Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Integration von SAAS-Anwendungen in Azure Active Directory](../saas-apps/tutorial-list.md)

## <a name="recommended-content"></a>Empfohlener Inhalt

- [Hinzufügen einer Anwendung zu Ihrem Azure Active Directory-Mandanten](../manage-apps/add-application-portal.md)
- [Übersicht über die Microsoft-Authentifizierungsbibliothek (MSAL) zum Abrufen von Token von Microsoft Identity Platform für die Authentifizierung von Benutzern](../develop/msal-overview.md)
- [Konfigurieren der Managed Home Screen-App von Microsoft mit Azure AD](/mem/intune/apps/app-configuration-managed-home-screen-app)
