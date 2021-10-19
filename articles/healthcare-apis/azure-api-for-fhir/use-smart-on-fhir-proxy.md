---
title: SMART on FHIR-Proxy für Azure Active Directory
description: In diesem Tutorial wird beschrieben, wie Sie Smart on FHIR-Anwendungen mit Azure API for FHIR über einen Proxy aktivieren.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: zxue
ms.date: 09/10/2019
ms.openlocfilehash: d5f690b3941e09f7e2a4e999aa59a97872aa3019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742785"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Tutorial: SMART on FHIR-Proxy für Azure Active Directory

Bei [SMART on FHIR](https://docs.smarthealthit.org/) handelt es sich um eine Reihe offener Spezifikationen für die Integration von Partneranwendungen in FHIR-Server und Systeme für elektronische Patientenakten, die über FHIR-Schnittstellen verfügen. Einer der Hauptzwecke der Spezifikationen besteht darin, zu beschreiben, wie eine Anwendung Authentifizierungsendpunkte für einen FHIR-Server ermitteln und eine Authentifizierungssequenz starten soll. 

Die Authentifizierung basiert auf OAuth2. Da SMART on FHIR jedoch Benennungskonventionen für Parameter verwendet, die nicht unmittelbar mit Azure Active Directory (Azure AD) kompatibel sind, verfügt Azure API for FHIR über einen integrierten SMART on FHIR-Proxy für Azure AD, der eine Teilmenge der SMART on FHIR-Startsequenzen ermöglicht. Insbesondere ermöglicht der Proxy die [EHR-Startsequenz](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

In diesem Tutorial wird beschrieben, wie Sie Smart on FHIR-Anwendungen mit Azure API for FHIR über den Proxy aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

- Instanz von Azure API for FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Konfigurieren von Azure AD-Registrierungen

SMART on FHIR erfordert, dass `Audience` einen Bezeichner-URI aufweist, der mit dem URI des FHIR-Diensts identisch ist. Bei der Standardkonfiguration von Azure API for FHIR wird `https://azurehealthcareapis.com` als `Audience`-Wert verwendet. Sie können aber auch einen Wert festlegen, der mit der spezifischen URL Ihres FHIR-Diensts übereinstimmt (z. B. `https://MYFHIRAPI.azurehealthcareapis.com`). Dies ist erforderlich, wenn Sie mit dem SMART on FHIR-Proxy arbeiten.

Sie benötigen außerdem eine Clientanwendungsregistrierung. Bei den meisten SMART on FHIR-Anwendungen handelt es sich um Single-Page-JavaScript-Anwendungen. Befolgen Sie daher die Anweisungen zum Konfigurieren einer [öffentlichen Clientanwendung in Azure AD](register-public-azure-ad-client-app.md).

Nachdem Sie diese Schritte ausgeführt haben, sollten Sie über Folgendes verfügen:

- Ein FHIR-Server, dessen Zielgruppe auf festgelegt `https://MYFHIRAPI.azurehealthcareapis.com` ist, wobei `MYFHIRAPI` der Name Ihrer Azure API for FHIR-Instanz ist.
- Eine Registrierung einer öffentlichen Clientanwendung. Notieren Sie sich die Anwendungs-ID für diese Clientanwendung.

### <a name="set-admin-consent-for-your-app"></a>Festlegen der Administratorgenehmigung für Ihre App

Um SMART on FHIR verwenden zu können, müssen Sie zuerst die App authentifizieren und autorisieren. Wenn Sie SMART on FHIR zum ersten Mal verwenden, müssen Sie auch die Administratorzustimmung einholen, damit die App auf Ihre FHIR-Ressourcen zugreifen kann.

Wenn Sie in der App nicht über eine Besitzrolle verfügen, wenden Sie sich an den App-Besitzer, und bitten Sie ihn, Die Administratorzustimmung für Sie in der App zu erteilen. 

Wenn Sie über Administratorrechte verfügen, führen Sie die folgenden Schritte aus, um sich selbst direkt die Administratorzustimmung zu erteilen. (Sie können sich später auch selbst die Administratorzustimmung erteilen, wenn Sie in der App dazu aufgefordert werden.) Sie können die gleichen Schritte ausführen, um andere Benutzer als Besitzer hinzuzufügen, damit diese diese App-Registrierung anzeigen und bearbeiten können.

So fügen Sie sich selbst oder einen anderen Benutzer als Besitzer einer App hinzu:

1. Navigieren Sie im Azure-Portal zu Azure Active Directory.
1. Wählen Sie im menü links die Option **App-Registrierung** aus.
1. Suchen Sie nach der app-Registrierung, die Sie erstellt haben, und wählen Sie sie aus.
1. Wählen Sie im linken Menü unter **Verwalten** die Option **Besitzer** aus.
1. Wählen **Sie Besitzer hinzufügen** aus, und fügen Sie sich selbst oder den Benutzer hinzu, dem Sie die Administratorzustimmung erteilen möchten.
1. Wählen Sie **Speichern** aus.

## <a name="enable-the-smart-on-fhir-proxy"></a>Aktivieren des SMART on FHIR-Proxys

Aktivieren Sie den SMART on FHIR-Proxy in den Einstellungen zur **Authentifizierung** für Ihre Azure API for FHIR-Instanz, indem Sie das Kontrollkästchen **SMART on FHIR-Proxy** aktivieren:

![Auswahl zum Aktivieren des SMART on FHIR-Proxys](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Aktivieren von CORS

Da es sich bei den meisten SMART on FHIR-Anwendungen um Single-Page-JavaScript-Apps handelt, müssen Sie [CORS (Cross-Origin Resource Sharing)](configure-cross-origin-resource-sharing.md) für Azure API for FHIR aktivieren:

![Auswahl zum Aktivieren von CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Konfigurieren der Antwort-URL

Der SMART on FHIR-Proxy fungiert als Vermittler zwischen der SMART on FHIR-App und Azure AD. Die Authentifizierungsantwort (der Authentifizierungscode) muss an den SMART on FHIR-Proxy und nicht an die App selbst übergeben werden. Der Proxy leitet dann die Antwort an die App weiter. 

Aufgrund dieser zweistufigen Weitergabe des Authentifizierungscodes müssen Sie die Antwort-URL (Rückruf) für Ihre Azure AD-Clientanwendung auf eine URL festlegen, die eine Kombination aus der Antwort-URL für den SMART on FHIR-Proxy und der Antwort-URL für die SMART on FHIR-App ist. Die kombinierte Antwort-URL hat folgende Form:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

In dieser Antwort ist `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` eine URL-sichere, Base64-codierte Version der Antwort-URL für die SMART on FHIR-App. Für das SMART on FHIR-App-Startprogramm lautet die Antwort-URL bei lokaler Ausführung der App `https://localhost:5001/sampleapp/index.html`. 

Sie können die kombinierte Antwort-URL mithilfe eines Skripts wie dem folgenden generieren:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Fügen Sie der öffentlichen Clientanwendung, die Sie zuvor für Azure AD erstellt haben, die Antwort-URL hinzu:

![Für den öffentlichen Client konfigurierte Antwort-URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Abrufen von Testpatientendaten

Zum Testen von Azure API for FHIR und des SMART on FHIR-Proxys muss mindestens ein Patient in der Datenbank enthalten sein. Wenn Sie noch nicht mit der API interagiert haben und keine Daten in der Datenbank vorhanden sind, finden Sie weitere Informationen unter [Zugreifen auf den FHIR-Dienst mit Postman](./../use-postman.md) zum Laden eines Patienten. Notieren Sie sich die ID eines bestimmten Patienten.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Herunterladen des SMART on FHIR-App-Startprogramms

Das [Repository für den Open-Source-FHIR-Server für Azure](https://github.com/Microsoft/fhir-server) enthält ein einfaches SMART on FHIR-App-Startprogramm und eine SMART on FHIR-Beispiel-App. In diesem Tutorial verwenden Sie dieses SMART on FHIR-Startprogramm lokal, um die Einrichtung zu testen.

Mit den folgenden Befehlen können Sie das GitHub-Repository klonen und zur Anwendung wechseln:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

Die Anwendung benötigt einige Konfigurationseinstellungen, die Sie in `appsettings.json` festlegen können:

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Es wird empfohlen, die Funktion `dotnet user-secrets` zu verwenden:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Führen Sie die Anwendung mit dem folgenden Befehl aus:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Testen des SMART on FHIR-Proxys

Nachdem Sie das SMART on FHIR-App-Startprogramm gestartet haben, können Sie in Ihrem Browser zu `https://localhost:5001`navigieren. Es sollte der folgende Bildschirm angezeigt werden:

![SMART on FHIR-App-Startprogramm](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Wenn Sie Informationen zu **Patient**, **Untersuchung** oder **Arzt** eingeben, werden Sie feststellen, dass der **Startkontext** aktualisiert wird. Wenn Sie Azure API for FHIR verwenden, ist der Startkontext einfach ein JSON-Dokument, das Informationen zum Patienten, Arzt und vielem mehr enthält. Dieser Startkontext ist Base64-codiert und wird als `launch`-Abfrageparameter an die SMART on FHIR-App übergeben. Gemäß der SMART on FHIR-Spezifikation ist diese Variable für die SMART on FHIR-App nicht transparent und wird an den Identitätsanbieter weitergeleitet. 

Der SMART on FHIR-Proxy verwendet diese Informationen, um Felder in der Tokenantwort aufzufüllen. Die SMART on FHIR-App *kann* mithilfe dieser Felder steuern, für welchen Patienten die Daten angefordert werden und wie die Anwendung auf dem Bildschirm gerendert wird. Der SMART on FHIR-Proxy unterstützt die folgenden Felder:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Diese Felder sollen Richtlinien für die App bereitstellen, vermitteln aber keine Sicherheitsinformationen. Eine SMART on FHIR-Anwendung kann diese ignorieren.

Beachten Sie, dass die Informationen für **Start-URL** am unteren Rand der Seite durch das SMART on FHIR-App-Startprogramm aktualisiert werden. Wählen Sie **Starten** aus, um die Beispiel-App zu starten. Es sollte eine ähnliche Anzeige wie im folgenden Beispiel zu sehen sein:

![SMART on FHIR-App](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Überprüfen Sie die Tokenantwort, um festzustellen, wie die Startkontextfelder an die App weitergegeben werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie den SMART on FHIR-Proxy für Azure Active Directory konfiguriert. Wenn Sie Informationen zur Verwendung von SMART on FHIR-Anwendungen mit Azure API for FHIR und dem Open-Source-FHIR-Server für Azure erhalten möchten, wechseln Sie zum Repository mit Beispielen für FHIR-Server auf GitHub:

>[!div class="nextstepaction"]
>[Beispiele für FHIR-Server](https://github.com/Microsoft/fhir-server-samples)
