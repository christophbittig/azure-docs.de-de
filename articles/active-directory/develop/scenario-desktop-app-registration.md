---
title: Registrieren von Desktop-Apps, die Web-APIs aufrufen | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Desktop-App erstellen, die Web-APIs aufruft (App-Registrierung)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ca051199e6fcbfab9a8de9e4a03d845bfa03c454
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553741"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Desktop-App, die Web-APIs aufruft: App-Registrierung

In diesem Artikel sind die Besonderheiten zur App-Registrierung bei einer Desktopanwendung aufgeführt.

## <a name="supported-account-types"></a>Unterstützte Kontotypen

Die in einer Desktopanwendung unterstützten Kontotypen sind von der gewünschten Funktion abhängig. Aufgrund dieser Beziehung sind die unterstützten Kontotypen von den Flows abhängig, die Sie verwenden möchten.

### <a name="audience-for-interactive-token-acquisition"></a>Zielgruppe für den interaktiven Tokenabruf

Wenn Ihre Desktopanwendung die interaktive Authentifizierung verwendet, können Sie Benutzer jedes [Kontotyps](quickstart-register-app.md) anmelden.

### <a name="audience-for-desktop-app-silent-flows"></a>Zielgruppe für automatische Desktop-App-Flüsse

- Ihre Anwendung muss Benutzer bei Ihrem Mandanten anmelden, um die integrierte Windows-Authentifizierung oder Benutzername und Kennwort verwenden zu können, beispielsweise wenn Sie LOB-Entwickler (Line of Business) sind. In Azure Active Directory-Organisationen muss Ihre Anwendung Benutzer bei Ihrem Mandanten anmelden, wenn es sich um ein ISV-Szenario handelt. Diese Authentifizierungsflows werden für persönliche Microsoft-Konten nicht unterstützt.
- Wenn Sie Benutzer mit Identitäten sozialer Netzwerke anmelden, die eine B2C-Autorität und -Richtlinie übergeben, können Sie ausschließlich die interaktive Authentifizierung oder die Authentifizierung über Benutzername und Kennwort verwenden.

## <a name="redirect-uris"></a>Umleitungs-URIs

Die in einer Desktopanwendung zu verwendenden Umleitungs-URIs hängen von dem Flow ab, den Sie verwenden möchten.

Geben Sie den Umleitungs-URI für Ihre App an, indem Sie im Azure-Portal unter **App-Registrierungen** die [Plattformeinstellungen konfigurieren](quickstart-register-app.md#add-a-redirect-uri).

- Für Apps, die [Webauthentifizierungs-Manager (WAM)](scenario-desktop-acquire-token-wam.md)verwenden, müssen Umleitungs-URIs nicht in MSAL, sondern in der [App-Registrierung](scenario-desktop-acquire-token-wam.md#redirect-uri) konfiguriert werden.

- Für Apps mit interaktiver Authentifizierung:

  - Apps, die eingebettete Browser verwenden: `https://login.microsoftonline.com/common/oauth2/nativeclient` (Hinweis: Wenn Ihre App ein Fenster öffnet, das in der Regel keine Adressleiste enthält, wird der „eingebettete Browser“ verwendet.)
  - Apps, die Systembrowser verwenden: `http://localhost` (Hinweis: Wenn Ihre App den Standardbrowser Ihres Systems (z. B. Edge, Chrome, Firefox usw.) zum Besuchen des Microsoft-Anmeldeportals verwenden würde, wird der „Systembrowser“ verwendet.)
  
  > [!IMPORTANT]
  > Als bewährte Sicherheitsmaßnahme wird empfohlen, als Umleitungs-URI `https://login.microsoftonline.com/common/oauth2/nativeclient` oder `http://localhost` festzulegen. Manche Authentifizierungsbibliotheken wie MSAL.NET verwenden als Standardeinstellung `urn:ietf:wg:oauth:2.0:oob` (nicht empfohlen), wenn kein anderer Umleitungs-URI angegeben wird. Diese Standardeinstellung wird als Breaking Change in der nächsten Hauptversion aktualisiert.

- Wenn Sie eine native Objective-C-oder Swift-App für macOS erstellen, sollten Sie den Umleitungs-URI basierend auf der Paket-ID Ihrer Anwendung im folgenden Format registrieren: `msauth.<your.app.bundle.id>://auth`. Ersetzen Sie `<your.app.bundle.id>` durch die Paket-ID Ihrer Anwendung.
- Wenn Sie eine Node.js Electron-App erstellen möchten, verwenden Sie anstelle eines regulären Web-Umleitungs-URIs (https://) ein benutzerdefiniertes Dateiprotokoll (z. B. `msal://redirect`), um den Umleitungsschritt im Autorisierungsablauf zu verarbeiten. Der Name des benutzerdefinierten Dateiprotokolls sollte nicht offensichtlich und somit leicht zu erraten sein. Befolgen Sie hierfür die Vorschläge in der [OAuth 2.0-Spezifikation für native Apps](https://tools.ietf.org/html/rfc8252#section-7.1).
- Wenn Ihre App nur die integrierte Windows-Authentifizierung oder Benutzername und Kennwort verwendet, müssen Sie für Ihre Anwendung keinen Umleitungs-URI registrieren. Diese Flows führen einen Roundtrip zum Microsoft Identity Platform v 2.0-Endpunkt aus. Ihre Anwendung wird nicht über einen bestimmten URI zurückgerufen.
- Konfigurieren Sie Ihre Anwendung als öffentliche Clientanwendung, um [Gerätecodeflow](scenario-desktop-acquire-token-device-code-flow.md), [Integrierte Windows-Authentifizierung](scenario-desktop-acquire-token-integrated-windows-authentication.md) sowie [Benutzername und Kennwort](scenario-desktop-acquire-token-username-password.md) von einer vertraulichen Clientanwendung mit einem Clientanmeldeinformationsflow zu unterscheiden, der in [Daemonanwendungen](scenario-daemon-overview.md) verwendet wird, die alle keinen Umleitungs-URI benötigen. Gehen Sie für diese Konfiguration wie folgt vor:

    1. Wählen Sie im <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> unter **App-Registrierungen** Ihre App aus, und wählen Sie dann **Authentifizierung** aus.
    1. Wählen Sie unter **Erweiterte Einstellungen** > **Öffentliche Clientflows zulassen** > **Folgende Flows für Mobilgerät und Desktop aktivieren:** die Option **Ja** aus.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Aktivieren der Einstellung für die Behandlung als öffentlicher Client im Bereich „Authentifizierung“ im Azure-Portal":::

## <a name="api-permissions"></a>API-Berechtigungen

Desktopanwendungen rufen APIs für den angemeldeten Benutzer auf. Sie müssen delegierte Berechtigungen anfordern. Sie können keine Anwendungsberechtigungen anfordern, die nur in [Daemon-Anwendungen](scenario-daemon-overview.md) verarbeitet werden.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Codekonfiguration](scenario-desktop-app-configuration.md).
