---
title: Microsoft Identity Platform und SAML-Bearerassertionsflow | Azure
description: Erfahren Sie, wie Sie Daten mithilfe des SAML-Bearerassertionsflows aus Microsoft Graph abrufen, ohne den Benutzer zur Eingabe von Anmeldeinformationen aufzufordern.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 1641d3390f2377065d21d8648f0c14256a0c9955
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095325"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity Platform und OAuth 2.0-SAML-Bearerassertionsflow
Mit dem OAuth 2.0-SAML-Bearerassertionsflow können Sie ein OAuth-Zugriffstoken mithilfe einer SAML-Assertion anfordern, wenn ein Client eine vorhandene Vertrauensstellung verwenden muss. Die auf die SAML-Assertion angewandte Signatur stellt die Authentifizierung der autorisierten App bereit. Eine SAML-Assertion ist ein XML-Sicherheitstoken, das von einem Identitätsanbieter ausgegeben und von einem Dienstanbieter genutzt wird. Der Dienstanbieter identifiziert basierend auf den Inhalten des Tokens den Antragsteller der Assertion für sicherheitsrelevante Zwecke.

Die SAML-Assertion wird an den Endpunkt des OAuth-Tokens gesendet.  Der Endpunkt verarbeitet die Assertion und stellt basierend auf der vorherigen Genehmigung der App ein Zugriffstoken aus. Der Client muss kein Aktualisierungstoken enthalten oder speichern. Auch der geheime Clientschlüssel muss nicht an den Tokenendpunkt übergeben werden.

Der SAML-Bearerassertionsflow ist nützlich beim Abrufen von Daten aus Microsoft Graph-APIs (die nur delegierte Berechtigungen unterstützen), ohne den Benutzer zur Eingabe von Anmeldeinformationen aufzufordern. In diesem Szenario funktioniert die Gewährung von Clientanmeldeinformationen, die bei Hintergrundprozessen bevorzugt wird, nicht.

Für Anwendungen, in denen eine interaktive browserbasierte Anmeldung durchgeführt wird, um eine SAML-Assertion abzurufen, und in denen dann der Zugriff auf eine OAuth-geschützte API (z. B. Microsoft Graph) hinzugefügt werden soll, können Sie eine OAuth-Anforderung erstellen, um einen Zugriffstoken für die API abzurufen. Wenn der Browser an Azure AD umgeleitet wird, um den Benutzer zu authentifizieren, ruft der Browser die Sitzung von der SAML-Anmeldung ab, und der Benutzer muss keine Anmeldeinformationen eingeben.

Der OAuth-SAML-Bearerassertionsflow wird auch für Benutzer unterstützt, die sich mit Identitätsanbietern authentifizieren, z. B. mit Active Directory-Verbunddiensten (ADFS) im Verbund mit Azure Active Directory.  Die von AD FS abgerufene SAML-Assertion kann in einem OAuth-Flow zur Authentifizierung des Benutzers verwendet werden.

![OAuth-Flow](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Aufrufen von Graph mithilfe einer SAML-Bearerassertion
Im Folgenden wird erläutert, wie die SAML-Assertion programmgesteuert abgerufen werden kann. Der programmgesteuerte Ansatz wird mit ADFS getestet. Der Ansatz funktioniert mit jedem Identitätsanbieter, der die Rückgabe der SAML-Assertionen programmgesteuert unterstützt. Der allgemeine Vorgang besteht darin, eine SAML-Assertion abzurufen, ein Zugriffstoken abzurufen und auf Microsoft Graph zuzugreifen.

### <a name="prerequisites"></a>Voraussetzungen

Richten Sie eine Vertrauensstellung zwischen dem Autorisierungsserver bzw. der Umgebung (Microsoft 365) und dem Identitätsanbieter oder dem Aussteller der SAML 2.0-Bearerassertion ein. Informationen zum Konfigurieren von ADFS für einmaliges Anmelden und als Identitätsanbieter finden Sie unter [Einrichten von AD FS und Aktivieren von single Sign-On für Office 365](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Registrieren Sie die Anwendung im [Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Melden Sie sich auf der [Seite „App-Registrierungen“ des Portals](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) an. (Beachten Sie, dass die v2.0-Endpunkte für die Graph-API verwendet werden und die Anwendung daher in diesem Portal registriert werden muss. Andernfalls können die Registrierungen im Azure Active Directory verwendet werden). 
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein: 
    1. **Name**: Geben Sie einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird.
    1. **Unterstützte Kontotypen**: Wählen Sie aus, welche Konten von Ihrer Anwendung unterstützt werden sollen.
    1. **Umleitungs-URI (optional):** Wählen Sie die Art der App aus, die Sie erstellen: „Web“ oder „Öffentlicher Client“ (Mobilgerät und Desktop), und geben Sie dann den Umleitungs-URI (oder die Antwort-URL) für die Anwendung ein.
    1. Wenn Sie so weit sind, klicken Sie auf **Registrieren**.
1. Notieren Sie sich die Anwendungs-ID (Client-ID).
1. Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** aus. Klicken Sie im Abschnitt **Geheime Clientschlüssel** auf **Neuer geheimer Clientschlüssel**. Kopieren Sie den neuen geheimen Clientschlüssel. Nach dem Schließen der Seite können Sie ihn nicht mehr abrufen.
1. Wählen Sie im linken Bereich die Option **API-Berechtigungen** und dann **Berechtigung hinzufügen** aus. Wählen Sie **Microsoft Graph**, anschließend **Delegierte Berechtigungen** und dann **Tasks.read** aus, da die Outlook-Graph-API verwendet werden soll. 

Installieren Sie [Postman](https://www.getpostman.com/), ein Tool, das zum Testen der Beispielanforderungen erforderlich ist.  Später können Sie die Anforderungen in Code konvertieren.

### <a name="get-the-saml-assertion-from-adfs"></a>Abrufen der SAML-Assertion aus AD FS
Erstellen Sie eine POST-Anforderung an den AD FS-Endpunkt mithilfe eines SOAP-Umschlags zum Abrufen der SAML-Assertion:

![Abrufen der SAML-Assertion](./media/v2-saml-bearer-assertion/2.png)

Headerwerte:

![Headerwerte](./media/v2-saml-bearer-assertion/3.png)

AD FS-Anforderungstext:

![AD FS-Anforderungstext](./media/v2-saml-bearer-assertion/4.png)

Nachdem diese Anforderung erfolgreich gesendet wurde, sollten Sie eine SAML-Assertion von ADFS erhalten. Nur die Daten des Tags **SAML:Assertion** sind erforderlich. Konvertieren Sie sie zur Verwendung in weiteren Anforderungen in die base64-Codierung.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Abrufen des OAuth2-Tokens mithilfe der SAML-Assertion 

In diesem Schritt rufen Sie über die Antwort der ADFS-Assertion einen OAuth2-Token ab.

1. Erstellen Sie wie unten gezeigt eine POST-Anforderung mit den Headerwerten:

    ![POST-Anforderung](./media/v2-saml-bearer-assertion/5.png)
1. Ersetzen Sie im Text der Anforderung **client_id**, **client_secret** und **assertion** (die base64-codierte im vorherigen Schritt abgerufene SAML-Assertion):

    ![Anforderungstext](./media/v2-saml-bearer-assertion/6.png)
1. Nach erfolgreicher Anforderung erhalten Sie einen Zugriffstoken vom Azure Active Directory.

### <a name="get-the-data-with-the-oauth2-token"></a>Abrufen der Daten mit dem OAuth2 Token

Rufen Sie nach dem Empfang des Zugriffstokens die Graph-APIs (Outlook-Aufgaben in diesem Beispiel) auf. 

1. Erstellen Sie eine GET-Anforderung mit dem im vorherigen Schritt erstellten Zugriffstoken:

    ![GET-Anforderung](./media/v2-saml-bearer-assertion/7.png)

1. Nach erfolgreicher Anforderung erhalten Sie eine JSON-Antwort.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur App-Registrierung und zum Authentifizierungsablauf finden Sie unter:

- [Registrieren einer Anwendung bei der Microsoft Identity Platform](quickstart-register-app.md)
- [Authentifizierungsflows und Anwendungsszenarien](authentication-flows-app-scenarios.md)
