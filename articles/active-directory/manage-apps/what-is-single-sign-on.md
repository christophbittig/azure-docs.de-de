---
title: Worum handelt es sich beim einmaligen Anmelden?
description: Erfahren Sie mehr über einmaliges Anmelden für Unternehmensanwendungen in Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q1, contperf-fy22q2
ms.openlocfilehash: 478998d69a12c7b2a708bc3fe0a2b1bd5b9a88be
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997457"
---
# <a name="what-is-single-sign-on-in-azure-active-directory"></a>Was ist einmaliges Anmelden in Azure Active Directory?

Dieser Artikel enthält Informationen zu den verfügbaren Optionen für einmaliges Anmelden (Single Sign-On, SSO) und eine Einführung in die Planung einer Bereitstellung für einmaliges Anmelden bei Verwendung von Azure Active Directory (Azure AD). Einmaliges Anmelden ist eine Authentifizierungsmethode, mit der sich Benutzer mit einem Satz von Anmeldeinformationen bei mehreren unabhängigen Softwaresystemen anmelden können. Bei Verwendung von einmaligem Anmelden (Single Sign-On, SSO) müssen sich Benutzer nicht bei jeder Anwendung anmelden, die sie verwenden. Mit SSO können Benutzer auf alle benötigten Anwendungen zugreifen, ohne sich mit unterschiedlichen Anmeldeinformation authentifizieren zu müssen. 

Viele Anwendungen sind bereits in Azure AD vorhanden, die Sie mit SSO verwenden können. Abhängig von den Anforderungen der Anwendung und ihrer Implementierung stehen Ihnen mehrere Optionen für SSO zur Verfügung. Nehmen Sie sich Zeit, Ihre SSO-Bereitstellung zu planen, bevor Sie Anwendungen in Azure AD erstellen. Die Verwaltung von Anwendungen kann mithilfe des Portals „Meine Apps“ vereinfacht werden.

## <a name="single-sign-on-options"></a>Optionen für einmaliges Anmelden

Die Auswahl einer Methode für einmaliges Anmelden hängt davon ab, wie die Anwendung für Authentifizierung konfiguriert ist. Cloudanwendungen können verbundbasierte Optionen wie OpenID Verbinden, OAuth und SAML verwenden. Die Anwendung kann auch kennwortbasiertes SSO oder verknüpftes SSO verwenden, oder SSO kann deaktiviert werden.

- **Verbund**: Wenn Sie einmaliges Anmelden für mehrere Identitätsanbieter einrichten, wird dies als Verbund bezeichnet. Eine SSO-Implementierung auf der Grundlage von Verbundprotokollen verbessert die Sicherheit, Zuverlässigkeit, Endbenutzerfreundlichkeit und Implementierung. 

    Mit Verbund-SSO nimmt Azure AD die Authentifizierung des Benutzers bei der Anwendung mithilfe seines Azure AD-Kontos vor. Diese Methode wird für SAML 2.0-, WS-Verbund- oder OpenID Connect-Anwendungen unterstützt. Verbund-SSO ist der umfangreichste SSO-Modus. Verwenden Sie Verbund-SSO anstelle von kennwortbasiertem SSO und Active Directory-Verbunddiensten (AD FS) mit Azure AD, wenn eine Anwendung dies unterstützt.

    Es gibt einige Szenarien, in denen die SSO-Option für eine Unternehmensanwendung nicht vorhanden ist. Wenn die Anwendung mithilfe von **App-Registrierungen** im Portal registriert wurde, wird die Funktion zum einmaligen Anmelden so konfiguriert, dass standardmäßig OpenID Connect und OAuth verwendet werden. In diesem Fall wird die Option zum einmaligen Anmelden in der Navigation unter Unternehmensanwendungen nicht angezeigt.

    Einmaliges Anmelden ist nicht verfügbar, wenn eine Anwendung in einem anderen Mandanten gehostet wird. Einmaliges Anmelden ist ebenfalls nicht verfügbar, wenn Ihr Konto nicht über die erforderlichen Berechtigungen verfügt (Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals). Berechtigungen können auch zu einem Szenario führen, in dem Sie die Option zum einmaligen Anmelden zwar öffnen, nicht aber speichern können.

    > [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]

- **Kennwort**: Lokale Anwendungen können für einmaliges Anmelden die Methoden „Kennwort“, „Integrierte Windows-Authentifizierung“, „Header“ oder „Verknüpft“ verwenden. Die lokalen Optionen funktionieren, wenn die Anwendungen für den Anwendungsproxy konfiguriert wurden.

    Bei kennwortbasierter einmaliger Anmeldung melden sich Benutzer mit einem Benutzernamen und einem Kennwort bei der Anwendung an, wenn sie erstmals auf sie zugreifen. Nach der ersten Anmeldung werden der Benutzername und das Kennwort von Azure AD für die Anwendung bereitgestellt. Kennwortbasiertes einmaliges Anmelden ermöglicht die sichere Speicherung des Anwendungskennworts und dessen Wiedergabe mit einer Webbrowsererweiterung oder einer mobilen App. Diese Option verwendet den von der Anwendung bereitgestellten vorhandenen Anmeldevorgang, ermöglicht es einem Administrator, die Kennwörter zu verwalten, und erfordert nicht, dass der Benutzer das Kennwort kennt.

- **Verknüpft**: Verknüpftes Anmelden sorgt während der Migration von Anwendungen über einen längeren Zeitraum für eine einheitliche Benutzererfahrung. Wenn Sie Anwendungen zu Azure AD migrieren, können Sie mithilfe des verknüpften Anmeldens schnell Links für alle Anwendungen veröffentlichen, die Sie migrieren möchten. Benutzer können alle Links im Portal „Meine Apps“ oder im Microsoft 365-Portal finden.

    Nachdem ein Benutzer sich mit einer verknüpften Anwendung authentifiziert hat, muss ein Konto erstellt werden, bevor der Benutzer Zugriff durch einmaliges Anmelden erhält. Die Bereitstellung dieses Kontos kann automatisch oder manuell durch einen Administrator erfolgen. Sie können keine Richtlinien für bedingten Zugriff oder mehrstufige Authentifizierung auf eine verknüpfte Anwendung anwenden, da eine verknüpfte Anwendung keine Funktionen für einmaliges Anmelden über Azure AD bereitstellt. Wenn Sie eine verknüpfte Anwendung konfigurieren, fügen Sie einfach einen Link hinzu, der zum Starten der Anwendung angezeigt wird.

- **Deaktiviert**: Wenn SSO deaktiviert ist, ist diese Option für die Anwendung nicht verfügbar. Wenn einmaliges Anmelden deaktiviert ist, müssen sich Benutzer möglicherweise zweimal authentifizieren. Beim ersten Mal authentifizieren die Benutzer sich in Azure AD, danach melden sie sich bei der Anwendung an.

    Deaktivieren Sie SSO, wenn Folgendes zutrifft:

    - Sie noch nicht bereit sind, diese Anwendung in einmaliges Anmelden mit Azure AD zu integrieren
    - Sie andere Aspekte der Anwendung testen
    - Eine lokale Anwendung erfordert keine Benutzerauthentifizierung, Sie wünschen diese jedoch. Wenn SSO deaktiviert ist, muss sich der Benutzer authentifizieren.

    Wenn Sie die Anwendung für SP-initiiertes und SAML-basiertes einmaliges Anmelden konfiguriert haben und den SSO-Modus in „Deaktiviert“ ändern, wird nicht verhindert, dass sich Benutzer außerhalb des Portals „Meine Apps“ bei der Anwendung anmelden. Dazu müssen Sie die [Anmeldemöglichkeit für Benutzer deaktivieren](disable-user-sign-in-portal.md).

## <a name="plan-sso-deployment"></a>Planen der SSO-Bereitstellung

Webanwendungen werden von verschiedenen Unternehmen gehostet und als Dienst zur Verfügung gestellt. Beispiele für beliebte Webanwendungen sind Microsoft 365, GitHub und Salesforce, und es gibt noch einige Tausend mehr. Benutzer verwenden auf ihren Computern einen Webbrowser, um auf Webanwendungen zuzugreifen. Einmaliges Anmelden ermöglicht Benutzern die Navigation zwischen verschiedenen Webanwendungen, ohne dass sie sich mehrfach anmelden müssen.

Die Art der Implementierung von SSO richtet sich danach, wo die Anwendung gehostet wird. Das Hosting ist aufgrund der Art und Weise wichtig, wie Netzwerkdatenverkehr für den Zugriff auf die Anwendung weitergeleitet wird. Benutzer müssen nicht das Internet für den Zugriff auf lokale Anwendungen (gehostet in einem lokalen Netzwerk) verwenden. Wenn die Anwendung in der Cloud gehostet wird, benötigen Benutzer das Internet, um sie zu verwenden. In der Cloud gehostete Anwendungen werden auch als SaaS-Anwendungen (Software-as-a-Service) bezeichnet.

Für Cloudanwendungen werden Verbundprotokolle verwendet. Sie können einmaliges Anmelden auch für lokale Anwendungen nutzen. Sie können den Anwendungsproxy verwenden, um Zugriff für Ihre lokale Anwendung zu konfigurieren. Weitere Informationen finden Sie unter Remotezugriff auf lokale Anwendungen über den Azure AD-Anwendungsproxy.

## <a name="my-apps"></a>Meine Apps

Als Benutzer einer Anwendung interessieren Sie die SSO-Details wahrscheinlich nicht sonderlich. Sie möchten nur die Anwendungen nutzen, mit denen Sie produktiv arbeiten können, ohne häufig Ihr Kennwort eingeben zu müssen. Sie finden und verwalten Ihre Anwendungen im Portal „Meine Apps“. 

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren des einmaligen Anmeldens](add-application-portal-setup-sso.md)
