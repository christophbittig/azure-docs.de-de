---
title: Eingeschränkte Kerberos-Delegierung mit Azure Active Directory
description: Architekturleitfaden zum Implementieren der eingeschränkten Kerberos-Delegierung mit Azure Active Directory
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
ms.openlocfilehash: 2c12ab8421b578a0c1bf60f5d901d5d9223fc685
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231271"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows-Authentifizierung – Eingeschränkte Kerberos-Delegierung mit Azure Active Directory

Die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) ermöglicht die eingeschränkte Delegierung zwischen Ressourcen und basiert auf Dienstprinzipalnamen. Domänenadministratoren müssen die Delegierungen erstellen, und die KCD ist auf eine Domäne beschränkt. Die ressourcenbasierte KCD wird häufig als Möglichkeit zum Bereitstellen der Kerberos-Authentifizierung für eine Webanwendung genutzt, die Benutzer in mehreren Domänen innerhalb einer Active Directory-Gesamtstruktur enthält.

Der Azure Active Directory-Anwendungsproxy kann einmaliges Anmelden (Single Sign-On, SSO) und Remotezugriff auf KCD-basierte Anwendungen ermöglichen, die ein Kerberos-Ticket für den Zugriff und die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) benötigen.

Sie aktivieren SSO für Ihre lokalen KCD-Anwendungen, die die integrierte Windows-Authentifizierung (IWA) verwenden, indem Sie Anwendungsproxyconnectors in Active Directory die Berechtigung erteilen, die Identität von Benutzern anzunehmen. Der Anwendungsproxyconnector verwendet diese Berechtigung, um Token im Namen des Benutzers zu senden und zu empfangen.

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Es besteht die Notwendigkeit, Remotezugriff bereitzustellen, mit Vorauthentifizierung zu schützen und SSO für lokale IWA-Anwendungen bereitzustellen.

![Diagramm der Architektur](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Greift auf die vom Anwendungsproxy bereitgestellte Legacyanwendung zu.

* **Webbrowser**: Die Komponente, mit der der Benutzer interagiert, um auf die externe URL der Anwendung zuzugreifen.

* **Azure AD**: Authentifiziert den Benutzer. 

* **Anwendungsproxydienst**: Fungiert als Reverseproxy, um Anforderungen vom Benutzer an die lokale Anwendung zu senden. Er befindet sich in Azure AD. Der Anwendungsproxy kann auch Richtlinien für bedingten Zugriff erzwingen.

* **Anwendungsproxyconnector**: Wird lokal auf Windows-Servern installiert, um die Konnektivität mit der Anwendung zu ermöglichen. Gibt die Antwort an Azure AD zurück. Führt die KCD-Aushandlung mit Active Directory aus und nimmt dabei die Identität des Benutzers an, um ein Kerberos-Token für die Anwendung abzurufen.

* **Active Directory**: Sendet das Kerberos-Token für die Anwendung an den Anwendungsproxyconnector.

* **Legacyanwendungen**: Anwendungen, die Benutzeranforderungen vom Anwendungsproxy empfangen. Die Legacyanwendungen geben die Antwort an den Anwendungsproxyconnector zurück.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementieren der Windows-Authentifizierung (KCD) mit Azure AD

* [Eingeschränkte Delegierung von Kerberos für die einmalige Anmeldung zu Ihren Apps mit dem Anwendungsproxy](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](../app-proxy/application-proxy-add-on-premises-application.md)