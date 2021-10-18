---
title: Ressourcen zum Migrieren von Apps zu Azure Active Directory
description: Ressourcen, die Ihnen beim Migrieren des Anwendungszugriffs und der Authentifizierung zu Azure Active Directory (Azure AD) helfen.
titleSuffix: Azure AD
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
ms.openlocfilehash: b8aab437e9ffac19da56a892a4547aa234682458
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856579"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory

Ressourcen, die Ihnen beim Migrieren des Anwendungszugriffs und der Authentifizierung zu Azure Active Directory (Azure AD) helfen.

| Resource  | BESCHREIBUNG  |
|:-----------|:-------------|
|[Migrieren Ihrer Apps zu Azure AD](https://aka.ms/migrateapps/whitepaper) | Dieses Whitepaper stellt die Vorteile der Migration dar und beschreibt, wie Sie für die Migration in vier klar beschriebenen Phasen planen: Ermittlung, Klassifizierung, Migration und laufende Verwaltung. Sie werden durch den Prozess geführt und erfahren, wie Sie das Projekt in einfach umzusetzende Teilschritte aufteilen. Im gesamten Dokument finden Sie Links zu wichtigen Ressourcen, die Ihnen im Verlauf des Prozesses helfen. |
|[Developer tutorial: AD FS to Azure AD application migration playbook for developers](https://aka.ms/adfsplaybook) (Tutorial für Entwickler: Playbook für Entwickler zur Migration von Anwendungen von AD FS zu Azure AD) | Anhand dieser ASP.NET-Codebeispiele und den begleitenden Tutorials erfahren Sie, wie Sie Ihre in Active Directory-Verbunddienste (AD FS) integrierten Anwendungen problemlos und sicher zu Azure Active Directory (Azure AD) migrieren können. Dieses Tutorial richtet sich an Entwickler, die sich nicht nur mit der Konfiguration von Anwendungen in AD FS sowie in Azure AD, sondern auch mit den Änderungen vertraut machen möchten, die bei diesem Verfahren an der Codebasis erforderlich sind.|
| [Tool: Migrationsbereitschaftsskript für Active Directory-Verbunddienste (AD FS)](https://aka.ms/migrateapps/adfstools) | Dies ist ein Skript, das Sie auf Ihrem lokalen AD FS-Server (Active Directory-Verbunddienste) ausführen können, um die Bereitschaft von Apps für die Migration zu Azure AD zu bestimmen.|
| [Bereitstellungsplan: Migrieren von AD FS zu Kennworthashsynchronisierung](https://aka.ms/ADFSTOPHSDPDownload) | Mit der Kennworthashsynchronisierung werden Benutzerkennworthashes aus Ihrem lokalen Active Directory mit Azure AD synchronisiert. Dadurch kann Azure AD Benutzer authentifizieren, ohne mit dem lokalen Active Directory zu interagieren.|
| [Bereitstellungsplan: Migrieren von AD FS zu Passthrough-Authentifizierung](https://aka.ms/ADFSTOPTADPDownload)|Mit der Azure AD-Passthrough-Authentifizierung können sich Benutzer mit denselben Kennwörtern sowohl bei lokalen als auch cloudbasierten Anwendungen anmelden. Diese Funktion bietet Ihren Benutzern eine bessere Erfahrung, da sie sich ein Kennwort weniger merken müssen. Sie verringert außerdem die Kosten des IT-Helpdesks, weil die Wahrscheinlichkeit sinkt, dass Benutzer ihr Kennwort vergessen, wenn sie sich nur noch ein Kennwort merken müssen. Wenn Benutzer sich mithilfe von Azure AD anmelden, überprüft dieses Feature die Benutzerkennwörter direkt anhand Ihres lokalen Active Directory.|
| [Bereitstellungsplan: Aktivieren des einmaligen Anmeldens bei einer SaaS-App mit Azure AD](https://aka.ms/SSODPDownload) | Einmaliges Anmelden (Single Sign-On, SSO) ermöglicht Ihnen mit nur einer Anmeldung und einem einzigen Benutzerkonto den Zugriff auf sämtliche Apps und Ressourcen, die Sie für Ihre Geschäftsaktivitäten benötigen. Beispielsweise kann ein Benutzer nach der Anmeldung von Microsoft Office zu Salesforce oder zu Box wechseln, ohne sich ein zweites Mal (beispielsweise durch Eingabe eines Kennworts) zu authentifizieren.
| [Bereitstellungsplan: Erweitern von Apps auf Azure AD mit Anwendungsproxy](https://aka.ms/AppProxyDPDownload)| Für die Bereitstellung des Zugriffs von Mitarbeiter-Laptops und anderen Geräten aus auf lokale Anwendungen wurden bisher virtuelle private Netzwerke (VPNs) oder Umkreisnetzwerke (demilitarisierte Zonen, DMZs) eingesetzt. Diese Lösungen sind aber nicht nur komplex und schwer zu schützen, sondern können außerdem nur mit hohem Kostenaufwand eingerichtet und verwaltet werden. Azure AD-Anwendungsproxy erleichtert den Zugriff auf lokale Anwendungen. |
| [Bereitstellungspläne](../fundamentals/active-directory-deployment-plans.md) | Hier finden Sie weitere Bereitstellungspläne für die Bereitstellung von Features wie mehrstufige Authentifizierung, bedingten Zugriff, Benutzerbereitstellung, nahtloses SSO, Self-Service-Kennwortzurücksetzung und mehr! |
| [Migrieren von Apps von Symantec SiteMinder zu Azure AD](https://azure.microsoft.com/mediahandler/files/resourcefiles/migrating-applications-from-symantec-siteminder-to-azure-active-directory/Migrating-applications-from-Symantec-SiteMinder-to-Azure-Active-Directory.pdf) | Eine Schritt-für-Schritt-Anleitung zu Anwendungsmigrations- und Integrationsoptionen mit einem Beispiel, das Sie durch die Migration von Anwendungen von Symantec SiteMinder zu Azure AD führt. |

