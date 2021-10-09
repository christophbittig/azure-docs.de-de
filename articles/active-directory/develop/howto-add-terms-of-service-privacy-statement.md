---
title: Nutzungsbedingungen und Datenschutzbestimmungen für Apps | Azure
description: Erfahren Sie, wie Sie die Nutzungsbedingungen und die Datenschutzerklärung für Apps konfigurieren können, die zur Verwendung von Azure AD registriert sind.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev
ms.openlocfilehash: cba6644b691c6e702ee25d0302a56ca989aa88d9
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153868"
---
# <a name="configure-terms-of-service-and-privacy-statement-for-an-app"></a>Konfigurieren von Nutzungsbedingungen und Datenschutzbestimmungen für eine App

Entwickler, die mehrinstanzenfähige Apps erstellen und verwalten, die in Azure Active Directory (Azure AD) und Microsoft-Konten integriert werden können, sollten Links zu den Nutzungsbedingungen und Datenschutzbestimmungen der Apps einfügen. Die Nutzungsbedingungen und Datenschutzbestimmungen werden auf der Oberfläche für die Benutzerzustimmung angezeigt. Auf diese Weise wissen Benutzer, dass sie Ihren Apps vertrauen können. Die Nutzungsbedingungen und Datenschutzbestimmungen sind besonders wichtig für benutzerseitige mehrinstanzenfähige Apps, d.h. Apps, die in mehreren Verzeichnissen verwendet werden oder für alle Microsoft-Konten verfügbar sind.

Sie sind verantwortlich für die Erstellung der Dokumente zu den Nutzungsbedingungen und Datenschutzbestimmungen für Ihre App sowie für die Bereitstellung der URLs zu diesen Dokumenten. Bei mehrinstanzenfähigen Apps, für die diese Links nicht angegeben sind, wird auf der Oberfläche für die Benutzerzustimmung für die jeweilige App eine Warnung angezeigt. Dies schreckt möglicherweise Benutzer davon ab, Ihrer App zuzustimmen.

> [!NOTE]
> * Die Links zu den Nutzungsbedingungen und Datenschutzbestimmungen gelten nicht für Einzelmandanten-Apps.
> * Wenn eine oder beide der zwei Links fehlen, wird in Ihrer App eine Warnung angezeigt.

## <a name="user-consent-experience"></a>Oberfläche für die Benutzerzustimmung

Im folgenden Beispiel ist die Oberfläche für die Benutzerzustimmung für eine mehrinstanzenfähige App dargestellt, wenn die Nutzungsbedingungen und Datenschutzbestimmungen konfiguriert und diese Links nicht konfiguriert sind.

![Screenshots mit und ohne bereitgestellte Datenschutzbestimmungen und Nutzungsbedingungen](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatieren von Links zu den Dokumenten zu den Nutzungsbedingungen und Datenschutzbestimmungen

Bevor Sie Links zu den Dokumenten mit den Nutzungsbedingungen und Datenschutzbestimmungen für Ihre App hinzufügen, sollten Sie sicherstellen, dass die URLs den folgenden Vorgaben entsprechen.

| Vorgabe     | BESCHREIBUNG                           |
|---------------|---------------------------------------|
| Format        | Gültige URL                             |
| Gültige Schemas | HTTP und HTTPS<br/>Empfohlen wird HTTPS |
| Max. Länge    | 2\.048 Zeichen                       |

Beispiele: `https://myapp.com/terms-of-service` und `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hinzufügen von Links zu den Nutzungsbedingungen und Datenschutzbestimmungen

Nachdem die Nutzungsbedingungen und Datenschutzbestimmungen fertig konfiguriert sind, können Sie diesen Dokumenten mit einer der folgenden Methoden Links in Ihrer App hinzufügen:

* [Über das Azure-Portal](#azure-portal)
* [Mithilfe des JSON-Codes des App-Objekts](#app-object-json)
* [Über die Microsoft Graph-API](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Verwenden des Azure-Portals
Führen Sie im Azure-Portal die folgenden Schritte aus.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an, und wählen Sie den richtigen Azure AD-Mandanten (nicht B2C) aus.
2. Navigieren Sie zum Abschnitt **App-Registrierungen**, und wählen Sie Ihre App aus.
3. Wählen Sie unter **Verwalten** die Option **Branding** aus.
4. Füllen Sie die Felder **„URL der Nutzungsbedingungen“** und **„URL der Datenschutzerklärung“** aus.
5. Wählen Sie **Speichern** aus.

    ![Eigenschaften der App mit URLs zu den Nutzungsbedingungen und zur Datenschutzerklärung](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Verwenden des JSON-Codes des App-Objekts

Wenn Sie direkt den JSON-Code des App-Objekts ändern möchten, können Sie über den Manifest-Editor im Azure-Portal oder App-Registrierungsportal Links zu den Nutzungsbedingungen und Datenschutzbestimmungen Ihrer App einfügen.

1. Navigieren Sie zum Abschnitt **App-Registrierungen**, und wählen Sie Ihre App aus.
2. Öffnen Sie den Bereich **Manifest**.
3. STRG+F, suchen Sie nach „informationalUrls“. Geben Sie die Informationen ein.
4. Speichern Sie die Änderungen.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Verwenden der Microsoft Graph-API

Zur programmgesteuerten Aktualisierung aller Ihrer Apps können Sie mithilfe der Betaversion der Microsoft Graph-API alle Ihre Apps so ändern, dass sie Links zu den Dokumenten zu den Nutzungsbedingungen und Datenschutzbestimmungen enthalten.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Achten Sie darauf, dass Sie keine bereits vorhandenen Werte überschreiben, die Sie folgenden Feldern zugewiesen haben: `supportUrl`, `marketingUrl` und `logoUrl`
> * Die Microsoft Graph-API funktioniert nur, wenn Sie sich mit einem Azure AD-Konto anmelden. Persönliche Microsoft-Konten werden nicht unterstützt.
