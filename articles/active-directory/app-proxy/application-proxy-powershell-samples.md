---
title: PowerShell-Beispiele für den Azure Active Directory-Anwendungsproxy
description: Verwenden Sie diese PowerShell-Beispiele für den Azure Active Directory-Anwendungsproxy, um Informationen zu Anwendungsproxy-Apps und Connectors in Ihrem Verzeichnis abzurufen, Apps Benutzer und Gruppen zuzuweisen und Zertifikatinformationen abzurufen.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: cd37d281ce3c6506bb09a650d215240d5208dacc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988941"
---
# <a name="azure-active-directory-application-proxy-powershell-examples"></a>PowerShell-Beispiele für den Azure Active Directory-Anwendungsproxy

Die folgende Tabelle enthält Links zu PowerShell-Skriptbeispielen für Azure AD-Anwendungsproxy. Für diese Beispiele ist entweder das [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) oder das [Azure AD PowerShell V2-Modul in der Vorschauversion für Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) erforderlich, sofern nicht anders angegeben.


Weitere Informationen zu den in diesen Beispielen verwendeten Cmdlets finden Sie unter [Verwaltung von Anwendungsproxyanwendungen](/powershell/module/azuread/#application_proxy_application_management) und [Anwendungsproxyconnector-Verwaltung](/powershell/module/azuread/#application_proxy_connector_management).

| Link | BESCHREIBUNG |
|---|---|
|**Anwendungsproxy-Apps**||
| [Auflisten grundlegender Informationen für alle Anwendungsproxy-Apps](scripts/powershell-get-all-app-proxy-apps-basic.md) | Listet grundlegende Informationen („AppId“, „DisplayName“, „ObjId“) zu allen Anwendungsproxy-Apps in Ihrem Verzeichnis auf. |
| [Auflisten erweiterter Informationen für alle Anwendungsproxy-Apps](scripts/powershell-get-all-app-proxy-apps-extended.md) | Listet erweiterte Informationen („AppId“, „DisplayName“, „ExternalUrl“, „InternalUrl“, „ExternalAuthenticationType“) zu allen Anwendungsproxy-Apps in Ihrem Verzeichnis auf.  |
| [Auflisten aller Anwendungsproxy-Apps nach Connectorgruppe](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Listet Informationen zu allen Anwendungsproxy-Apps in Ihrem Verzeichnis auf und gibt an, welchen Connectorgruppen die Apps zugewiesen sind. |
| [Abrufen aller Anwendungsproxy-Apps mit einer Richtlinie für die Tokengültigkeitsdauer](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Listet alle in Ihrem Verzeichnis enthaltenen Anwendungsproxy-Apps mit einer Richtlinie für die Tokengültigkeitsdauer sowie deren Details auf. Für dieses Beispiel ist das [Azure AD PowerShell V2-Modul in der Vorschauversion für Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) erforderlich. |
|**Connectorgruppen**||
| [Abrufen aller Connectorgruppen und Connectors im Verzeichnis](scripts/powershell-get-all-connectors.md) | Listet alle Connectorgruppen und Connectors in Ihrem Verzeichnis auf. |
| [Verschieben aller einer Connectorgruppe zugewiesenen Apps in eine andere Connectorgruppe](scripts/powershell-move-all-apps-to-connector-group.md) | Verschiebt alle Anwendungen, die derzeit einer Connectorgruppe zugewiesen sind, in eine andere Connectorgruppe. |
|**Benutzer- und Gruppenzuweisungen**||
| [Anzeigen der einer Anwendungsproxyanwendung zugewiesenen Benutzer und Gruppen](scripts/powershell-display-users-group-of-app.md) | Listet die einer bestimmten Anwendungsproxyanwendung zugewiesenen Benutzer und Gruppen auf. |
| [Zuweisen eines Benutzers zu einer Anwendung](scripts/powershell-assign-user-to-app.md) | Weist einer Anwendung einen bestimmten Benutzer zu. |
| [Zuweisen einer Gruppe zu einer Anwendung](scripts/powershell-assign-group-to-app.md) | Weist einer Anwendung eine bestimmte Gruppe zu. |
|**Externe URL-Konfiguration**||
| [Abrufen aller Anwendungsproxy-Apps, die Standarddomänen („.msappproxy.net“) verwenden](scripts/powershell-get-all-default-domain-apps.md)  | Listet alle Anwendungsproxy-Anwendungen auf, die Standarddomänen („.msappproxy.net“) verwenden. |
| [Abrufen aller Anwendungsproxy-Apps mit Platzhalterveröffentlichung](scripts/powershell-get-all-wildcard-apps.md) | Listet alle Anwendungsproxy-Apps mit Platzhalterveröffentlichung auf. |
|**Konfiguration benutzerdefinierter Domänen**||
| [Abrufen aller Anwendungsproxy-Apps, die benutzerdefinierte Domänen verwenden, und der Zertifikatinformationen](scripts/powershell-get-all-custom-domains-and-certs.md) | Listet alle Anwendungsproxy-Apps auf, die benutzerdefinierte Domänen verwenden, und listet die den benutzerdefinierten Domänen zugeordneten Zertifikatinformationen auf. |
| [Abrufen aller Azure AD-Anwendungsproxy-Apps, die ohne ein hochgeladenes Zertifikat veröffentlicht wurden](scripts/powershell-get-all-custom-domain-no-cert.md) | Listet alle Anwendungsproxy-Apps auf, die benutzerdefinierte Domänen verwenden, aber für die kein gültiges TLS/SSL-Zertifikat hochgeladen wurde. |
| [Abrufen aller mit einem identischen Zertifikat veröffentlichten Azure AD-Anwendungsproxy-Apps](scripts/powershell-get-custom-domain-identical-cert.md) | Listet alle Azure AD-Anwendungsproxy-Apps auf, die mit einem identischen Zertifikat veröffentlicht wurden. |
| [Abrufen aller mit einem identischen Zertifikat veröffentlichten Azure AD-Anwendungsproxy-Apps und Ersetzen des Zertifikats](scripts/powershell-get-custom-domain-replace-cert.md) | Damit können Sie das Zertifikat für alle mit einem identischen Zertifikat veröffentlichten Azure AD-Anwendungsproxy-Apps in einem Massenvorgang ersetzen. |
