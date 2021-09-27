---
title: Ressourcen zum Migrieren von Apps zu Azure Active Directory | Microsoft-Dokumentation
description: Ressourcen, die Ihnen beim Migrieren des Anwendungszugriffs und der Authentifizierung zu Azure Active Directory (Azure AD) helfen.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 05c7b2f668565fe4ab37ed01ad65bef7cb6d95d6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437920"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory

Ressourcen, die Ihnen beim Migrieren des Anwendungszugriffs und der Authentifizierung zu Azure Active Directory (Azure AD) helfen.

| Resource  | BESCHREIBUNG  |
|:-----------|:-------------|
|[Migrieren Ihrer Apps zu Azure AD](https://aka.ms/migrateapps/whitepaper) | Dieses Whitepaper stellt die Vorteile der Migration dar und beschreibt, wie Sie für die Migration in vier klar beschriebenen Phasen planen: Ermittlung, Klassifizierung, Migration und laufende Verwaltung. Sie werden durch den Prozess geführt und erfahren, wie Sie das Projekt in einfach umzusetzende Teilschritte aufteilen. Im gesamten Dokument finden Sie Links zu wichtigen Ressourcen, die Ihnen im Verlauf des Prozesses helfen. |
|[Lösungsanleitung: Migrieren von Apps aus Active Directory-Verbunddienste (AD FS) zu Azure AD](./migrate-adfs-apps-to-azure.md) | Diese Lösungsanleitung führt Sie durch dieselben vier Phasen der Planung und Ausführung eines Anwendungsmigrationsprojekts, die im Whitepaper zur Migration auf einer höheren Ebene beschrieben sind. In dieser Anleitung erfahren Sie, wie Sie diese Phasen auf das spezifisches Ziel des Verschiebens einer Anwendung aus Azure Directory-Verbunddienste (AD FS) in Azure AD anwenden.|
|[Developer tutorial: AD FS to Azure AD application migration playbook for developers](https://aka.ms/adfsplaybook) (Tutorial für Entwickler: Playbook für Entwickler zur Migration von Anwendungen von AD FS zu Azure AD) | Anhand dieser ASP.NET-Codebeispiele und den begleitenden Tutorials erfahren Sie, wie Sie Ihre in Active Directory-Verbunddienste (AD FS) integrierten Anwendungen problemlos und sicher zu Azure Active Directory (Azure AD) migrieren können. Dieses Tutorial richtet sich an Entwickler, die sich nicht nur mit der Konfiguration von Anwendungen in AD FS sowie in Azure AD, sondern auch mit den Änderungen vertraut machen möchten, die bei diesem Verfahren an der Codebasis erforderlich sind.|
| [Tool: Migrationsbereitschaftsskript für Active Directory-Verbunddienste (AD FS)](https://aka.ms/migrateapps/adfstools) | Dies ist ein Skript, das Sie auf Ihrem lokalen AD FS-Server (Active Directory-Verbunddienste) ausführen können, um die Bereitschaft von Apps für die Migration zu Azure AD zu bestimmen.|
| [Bereitstellungsplan: Migrieren von AD FS zu Kennworthashsynchronisierung](https://aka.ms/ADFSTOPHSDPDownload) | Mit der Kennworthashsynchronisierung werden Benutzerkennworthashes aus Ihrem lokalen Active Directory mit Azure AD synchronisiert. Dadurch kann Azure AD Benutzer authentifizieren, ohne mit dem lokalen Active Directory zu interagieren.|
| [Bereitstellungsplan: Migrieren von AD FS zu Passthrough-Authentifizierung](https://aka.ms/ADFSTOPTADPDownload)|Mit der Azure AD-Passthrough-Authentifizierung können sich Benutzer mit denselben Kennwörtern sowohl bei lokalen als auch cloudbasierten Anwendungen anmelden. Diese Funktion bietet Ihren Benutzern eine bessere Erfahrung, da sie sich ein Kennwort weniger merken müssen. Sie verringert außerdem die Kosten des IT-Helpdesks, weil die Wahrscheinlichkeit sinkt, dass Benutzer ihr Kennwort vergessen, wenn sie sich nur noch ein Kennwort merken müssen. Wenn Benutzer sich mithilfe von Azure AD anmelden, überprüft dieses Feature die Benutzerkennwörter direkt anhand Ihres lokalen Active Directory.|
| [Bereitstellungsplan: Aktivieren des einmaligen Anmeldens bei einer SaaS-App mit Azure AD](https://aka.ms/SSODPDownload) | Einmaliges Anmelden (Single Sign-On, SSO) ermöglicht Ihnen mit nur einer Anmeldung und einem einzigen Benutzerkonto den Zugriff auf sämtliche Apps und Ressourcen, die Sie für Ihre Geschäftsaktivitäten benötigen. Beispielsweise kann ein Benutzer nach der Anmeldung von Microsoft Office zu Salesforce oder zu Box wechseln, ohne sich ein zweites Mal (beispielsweise durch Eingabe eines Kennworts) zu authentifizieren.
| [Bereitstellungsplan: Erweitern von Apps auf Azure AD mit Anwendungsproxy](https://aka.ms/AppProxyDPDownload)| Für die Bereitstellung des Zugriffs von Mitarbeiter-Laptops und anderen Geräten aus auf lokale Anwendungen wurden bisher virtuelle private Netzwerke (VPNs) oder Umkreisnetzwerke (demilitarisierte Zonen, DMZs) eingesetzt. Diese Lösungen sind aber nicht nur komplex und schwer zu schützen, sondern können außerdem nur mit hohem Kostenaufwand eingerichtet und verwaltet werden. Azure AD-Anwendungsproxy erleichtert den Zugriff auf lokale Anwendungen. |
| [Bereitstellungspläne](../fundamentals/active-directory-deployment-plans.md) | Hier finden Sie weitere Bereitstellungspläne für die Bereitstellung von Features wie mehrstufige Authentifizierung, bedingten Zugriff, Benutzerbereitstellung, nahtloses SSO, Self-Service-Kennwortzurücksetzung und mehr! |
| [Migrieren von Apps von Symantec SiteMinder zu Azure AD](https://azure.microsoft.com/mediahandler/files/resourcefiles/migrating-applications-from-symantec-siteminder-to-azure-active-directory/Migrating-applications-from-Symantec-SiteMinder-to-Azure-Active-Directory.pdf) | Eine Schritt-für-Schritt-Anleitung zu Anwendungsmigrations- und Integrationsoptionen mit einem Beispiel, das Sie durch die Migration von Anwendungen von Symantec SiteMinder zu Azure AD führt. |
| [Migrieren von Apps von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md) | Hier finden Sie eine Schritt-für-Schritt-Anleitung zur Anwendungsmigration von Okta zu Azure AD. |
| [Migrieren des Okta-Verbunds zur verwalteten Azure AD-Authentifizierung](migrate-okta-federation-to-azure-active-directory.md) | Erfahren Sie, wie Sie Ihre vorhandenen Office 365-Mandanten mit Okta für Funktionen für einmaliges Anmelden verbinden. |
| [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md) | Schritt-für-Schritt-Anleitungen für Organisationen, die derzeit die Benutzerbereitstellung von Okta zu Azure AD verwenden und entweder die Benutzersynchronisierung oder die universelle Synchronisierung zu Azure AD Connect migrieren. |
| [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md) | Hier finden Sie Schritt-für-Schritt-Anleitungen zum Migrieren von globalen Anmelderichtlinien oder Anmelderichtlinien auf Anwendungsebene in Okta zu Richtlinien für bedingten Azure AD-Zugriff, um den Benutzerzugriff in Azure AD und verbundenen Anwendungen zu schützen. |

