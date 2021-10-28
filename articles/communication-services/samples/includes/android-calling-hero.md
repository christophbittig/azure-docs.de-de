---
title: Datei einfügen
description: include file
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 0c93d09618eca9b5f43b0b52c80607875b5c73cb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288094"
---
Das **Hero-Beispiel für Gruppenanrufe für Android** von Azure Communication Services veranschaulicht, wie das Calling Android SDK von Communication Services verwendet werden kann, um eine Benutzeroberfläche für Gruppenanrufe mit Sprach- und Videofunktion zu erstellen. In dieser Beispiel-Schnellstartanleitung wird beschrieben, wie Sie das Beispiel einrichten und ausführen. Es ist auch eine Übersicht über das Beispiel vorhanden, um Informationen zum Kontext bereitzustellen.

## <a name="download-code"></a>Code herunterladen

Suchen Sie das Projekt für dieses Beispiel auf [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero). Eine Version des Beispiels mit [Teams-Interoperabilität](../../concepts/teams-interop.md) kann in einer separaten [Verzweigung](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop) gefunden werden.

## <a name="overview"></a>Übersicht

Beim diesem Beispiel handelt es sich um eine native Android-Anwendung, für die die Android-SDKs von Azure Communication Services zum Entwickeln einer Benutzeroberfläche für Anrufe mit Sprach- und Videofunktion verwendet werden. In der Anwendung wird eine serverseitige Komponente verwendet, um Zugriffstoken bereitzustellen, die dann zum Initialisieren des Azure Communication Services-SDK genutzt werden. Informationen zum Konfigurieren dieser serverseitigen Komponente finden Sie im Tutorial [Erstellen eines vertrauenswürdigen Authentifizierungsdiensts mithilfe von Azure Functions](../../tutorials/trusted-service-tutorial.md).

Das Beispiel sieht wie folgt aus:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Screenshot: Landing Page der Beispielanwendung":::

Wenn Sie die Schaltfläche „Start new call“ (Neuen Anruf starten) auswählen, wird von der Android-Anwendung ein neuer Anruf erstellt, und Sie werden als Teilnehmer hinzugefügt. Darüber hinaus ermöglicht die Anwendung es Ihnen, einem vorhandenen Azure Communication Services-Anruf beizutreten, indem Sie die ID des vorhandenen Anrufs angeben.

Nachdem Sie einem Anruf beigetreten sind, werden Sie aufgefordert, der Anwendung die Berechtigung zum Zugreifen auf Ihre Kamera und das Mikrofon zu gewähren. Sie werden außerdem aufgefordert, einen Anzeigenamen anzugeben.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Screenshot: Bildschirm zur Vorbereitung von Anrufen in der Beispielanwendung":::

Nachdem Sie Ihren Anzeigenamen und Ihre Geräte konfiguriert haben, können Sie dem Anruf beitreten. Der Hauptbereich für Anrufe, der die zentrale Anrufbenutzeroberfläche enthält, wird angezeigt.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Screenshot: Hauptbildschirm der Beispielanwendung":::

Komponenten des Hauptbildschirms für Anrufe:

- **Medienkatalog**: Der Hauptbereich, in dem die Teilnehmer angezeigt werden. Wenn ein Teilnehmer seine Kamera aktiviert hat, wird der Videofeed hier angezeigt. Jeder Teilnehmer verfügt über eine individuelle Kachel, auf der sein Anzeigename und der Videostream (falls vorhanden) angezeigt werden. Für den Medienkatalog wird die Anzeige von mehreren Teilnehmern unterstützt, und die Anzeige wird entsprechend aktualisiert, wenn Teilnehmer für den Anruf hinzugefügt oder entfernt werden.
- **Aktionsleiste**: Diese Leiste enthält die wichtigsten Anrufsteuerelemente. Mit diesen Steuerelementen können Sie Kamera und Mikrofon ein- und ausschalten, Ihren Bildschirm freigeben und den Anruf verlassen.

Unten sind weitere Informationen zu den Voraussetzungen und Schritten zum Einrichten des Beispiels angegeben.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie unter [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) wird auf Ihrem Computer ausgeführt
- Eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Eine Azure-Funktion, die den [Authentifizierungsendpunkt](../../tutorials/trusted-service-tutorial.md) zum Abrufen von Zugriffstoken ausführt.

## <a name="running-sample-locally"></a>Lokales Ausführen des Beispiels

Das Beispiel für Gruppenanrufe kann lokal mit Android Studio ausgeführt werden. Entwickler können entweder ihr physisches Gerät oder einen Emulator verwenden, um die Anwendung zu testen.

### <a name="before-running-the-sample-for-the-first-time"></a>Vor dem erstmaligen Ausführen des Beispiels

1. Öffnen Sie Android Studio, und wählen Sie `Open an Existing Project`
2. Öffnen Sie den Ordner `AzureCalling` in der heruntergeladenen Version für das Beispiel.
3. Erweitern Sie zu aktualisierende App/Assets `appSettings.properties`. Legen Sie als Voraussetzung den Wert für den `communicationTokenFetchUrl`-Schlüssel auf die URL für Ihren Authentifizierungsendpunkt fest.

### <a name="run-sample"></a>Ausführen des Beispiels

Kompilieren Sie das Beispiel in Android Studio, und führen Sie es aus.

## <a name="optional-securing-an-authentication-endpoint"></a>(Optional) Schützen eines Authentifizierungsendpunkts

Zu Demonstrationszwecken wird in diesem Beispiel standardmäßig ein öffentlich zugänglicher Endpunkt verwendet, um ein Azure Communication Services-Token abzurufen. Für Produktionsszenarien empfehlen wir Ihnen, für die Bereitstellung eigener Token Ihren eigenen geschützten Endpunkt zu erstellen.

Wenn weitere Konfigurationsschritte ausgeführt werden, wird für dieses Beispiel auch die Verbindungsherstellung mit einem per **Azure Active Directory** (Azure AD) geschützten Endpunkt unterstützt. So kann erreicht werden, dass die Benutzeranmeldung für die App erzwungen wird, um ein Azure Communication Services-Token abzurufen. Sehen Sie sich die folgenden Schritte an:

1. Aktivieren Sie die Azure Active Directory-Authentifizierung in Ihrer App.  
   - [Registrieren Ihrer App unter Azure Active Directory (über die Einstellungen der Android-Plattform)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](../../../app-service/configure-authentication-provider-aad.md)

2. Navigieren Sie in Azure Active Directory unter „App-Registrierungen“ zur Übersichtsseite Ihrer registrierten App. Notieren Sie sich die Werte für `Package name`, `Signature hash` und `MSAL Configutaion`.

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Active Directory-Konfiguration im Azure-Portal":::

3. Bearbeiten Sie `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`, und legen Sie `isAADAuthEnabled` so fest, dass Azure Active Directory aktiviert wird
4. Bearbeiten Sie `AndroidManifest.xml`, und legen Sie `android:path` auf einen Signaturhash fest. (Optional. Der aktuelle Wert verwendet einen Hash aus dem gebündeltem debug.keystore. Wenn ein anderer Keystore verwendet wird, muss dies aktualisiert werden.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Kopieren Sie die MSAL Android-Konfiguration aus dem Azure-Portal, und fügen Sie es in `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` ein. Schließen Sie „account_mode“ : „SINGLE“ ein
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Bearbeiten Sie `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`, und legen Sie den Wert für den Schlüssel `communicationTokenFetchUrl` auf die URL für Ihren Authentifizierungsendpunkt fest.
7. Bearbeiten Sie `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` und legen Sie den des Werts für den Schlüssel `aadScopes` aus den Bereichen `Azure Active Directory` `Expose an API` fest

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Laden Sie das Beispiel von GitHub herunter](https://github.com/Azure-Samples/communication-services-android-calling-hero).

Weitere Informationen finden Sie in den folgenden Artikeln:

- Machen Sie sich mit der Verwendung des [Calling SDK](../../quickstarts/voice-video-calling/getting-started-with-calling.md) vertraut
- Informieren Sie sich über die [Funktionsweise von Anrufen](../../concepts/voice-video-calling/about-call-types.md).

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Azure Communication GitHub](https://github.com/Azure/communication): Weitere Beispiele und Informationen finden Sie auf der offiziellen GitHub-Seite
- [Beispiele:](./../overview.md) In der Übersicht über Beispiele finden Sie weitere Beispiele.
- [Azure Communication Calling-Funktionen](../../concepts/voice-video-calling/calling-sdk-features.md): Weitere Informationen zum Android Calling SDK finden Sie unter [Azure Communication Android Calling SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)