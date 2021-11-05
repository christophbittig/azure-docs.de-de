---
title: Headerbasierte Authentifizierung mit Azure Active Directory
description: Architekturleitfaden zum Erzielen der headerbasierten Authentifizierung mit Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99cf28c88b3c94fad5e9abe7eaee1e11bcfacbe6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052368"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Headerbasierte Authentifizierung mit Azure Active Directory

Legacyanwendungen verwenden in der Regel eine headerbasierte Authentifizierung. In diesem Szenario authentifiziert sich ein Benutzer bzw. eine Benutzerin (oder ein Nachrichtenabsender) bei einer Vermittlerlösung für Identitäten. Die Vermittlerlösung authentifiziert den Benutzer und gibt die erforderlichen HTTP-Header (Hypertext Transfer Protocol) an den Zielwebdienst weiter. Azure Active Directory (AD) unterstützt dieses Muster über den Anwendungsproxydienst sowie Integrationen in andere Netzwerkcontrollerlösungen.

In unserer Lösung bietet der Anwendungsproxy Remotezugriff auf die Anwendung, authentifiziert den Benutzer und übergibt die von der Anwendung benötigten Header. 

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Remotebenutzer müssen sich per SSO sicher bei lokalen Anwendungen anmelden, die eine headerbasierte Authentifizierung erfordern.

![Abbildung der Architektur für die headerbasierte Authentifizierung](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Der Benutzer greift auf die vom Anwendungsproxy bereitgestellte Legacyanwendung zu.

* **Webbrowser**: Die Komponente, mit der der Benutzer interagiert, um auf die externe URL der Anwendung zuzugreifen.

* **Azure AD**: Authentifiziert den Benutzer. 

* **Anwendungsproxydienst**: Dieser Dienst fungiert als Reverseproxy, um Anforderungen vom Benutzer an die lokale Anwendung zu senden. Der Dienst befindet sich in Azure AD und kann auch Richtlinien für bedingten Zugriff erzwingen.

* **Anwendungsproxyconnector**: Der Connector wird lokal auf Windows-Servern installiert, um Konnektivität mit den Anwendungen zu ermöglichen. Er verwendet nur ausgehende Verbindungen und gibt die Antwort an Azure AD zurück.

* **Legacyanwendungen**: Anwendungen, die Benutzeranforderungen vom Anwendungsproxy empfangen. Die Legacyanwendung empfängt die erforderlichen HTTP-Header zum Einrichten einer Sitzung und Zurückgeben einer Antwort. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementieren der headerbasierten Authentifizierung mit Azure AD

* [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure AD](../app-proxy/application-proxy-add-on-premises-application.md)  

* [Headerbasierte Authentifizierung für einmaliges Anmelden mit Anwendungsproxy und PingAccess](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md) 

* [Schützen von Legacy-Apps mit App-Bereitstellungscontrollern und -netzwerken](../manage-apps/secure-hybrid-access.md)
