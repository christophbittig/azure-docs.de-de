---
title: Zugreifen auf die Azure Healthcare-APIs mithilfe des REST-Clients
description: In diesem Artikel wird erläutert, wie Sie mithilfe der REST-Clienterweiterung in VSCode auf die ApIs für das Gesundheitswesen zugreifen.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 615aaed8be507ed19314e7f7329d7ca2f15a5f0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042350"
---
# <a name="accessing-the-healthcare-apis-preview-using-the-rest-client-extension-in-visual-studio-code"></a>Zugreifen auf die ApIs für das Gesundheitswesen (Vorschauversion) mithilfe der REST-Clienterweiterung in Visual Studio Code

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie [mithilfe der REST-Clienterweiterung in Visual Studio Code auf die](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)ApIs für das Gesundheitswesen zugreifen.

## <a name="install-rest-client-extension"></a>Installieren der REST-Clienterweiterung

Wählen Sie im linken Bereich Ihres Visual Studio Code das Symbol Erweiterungen aus, und suchen Sie nach "REST-Client". Suchen Sie die [REST-Clienterweiterung,](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) und installieren Sie sie.

[![REST-Client-VSCode-Erweiterung ](media/rest-install.png) ](media/rest-install.png#lightbox)

## <a name="create-a-http-file-and-define-variables"></a>Erstellen einer `.http` Datei und Definieren von Variablen

Erstellen Sie eine neue Datei in Visual Studio Code. Geben Sie `GET` eine Anforderungsbefehlszeile in die Datei ein, und speichern Sie sie als `test.http` . Das `.http` Dateisuffix aktiviert automatisch die REST-Clientumgebung. Klicken Sie auf `Send Request` , um die Metadaten abzurufen. 

[![Anforderung ](media/rest-send-request.png) senden ](media/rest-send-request.png#lightbox)

## <a name="get-client-application-values"></a>Abrufen von Clientanwendungswerten

> [!Important]
> Vor dem Aufrufen der FHIR-Server-REST-API (mit Ausnahme des Abrufens der Metadaten) müssen Sie die **[Anwendungsregistrierung](register-application.md)** abschließen. Notieren Sie sich Ihre **Azure-Mandanten-ID,** **die Client-ID,** den **geheimen Clientschlüssel** und die **Dienst-URL.**

Obwohl Sie Werte wie die Client-ID direkt in Aufrufen der REST-API verwenden können, empfiehlt es sich, einige Variablen für diese Werte zu definieren und stattdessen die Variablen zu verwenden.

Fügen Sie in Ihrer `test.http` Datei die folgenden Informationen ein, die Sie beim Registrieren Ihrer Anwendung erhalten haben: 

```
### REST Client
@fhirurl =https://xxx.azurehealthcareapis.com
@clientid =xxx....
@clientsecret =xxx....
@tenantid =xxx....
```

## <a name="get-azure-ad-access-token"></a>Abrufen Azure AD Zugriffstokens

Nachdem Sie die unten angegebenen Informationen in Ihre Datei eingeschlossen `test.http` haben, klicken Sie auf `Send Request` . Es wird eine HTTP-Antwort angezeigt, die Ihr Zugriffstoken enthält.

Die Zeile, die mit `@name` beginnt, enthält eine Variable, die die HTTP-Antwort mit dem Zugriffstoken erfasst. Die Variable `@token` wird zum Speichern des Zugriffstokens verwendet.

>[!Note] 
>Der `grant_type` von `client_credentials` wird verwendet, um ein Zugriffstoken abzurufen.

```
### Get access token 
@name getAADToken 
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&resource={{fhirurl}}
&client_id={{clientid}}
&client_secret={{clientsecret}}

### Extract access token from getAADToken request
@token = {{getAADToken.response.body.access_token}}
```

[![Abrufen des Zugriffstokens ](media/rest-config.png) ](media/rest-config.png#lightbox)

## <a name="get-fhir-patient-data"></a>`GET` FHIR-Patientendaten

Sie können jetzt eine Liste der Patienten oder einen bestimmten Patienten mit der `GET` Anforderung abrufen. Die Zeile mit `Authorization` ist die Headerinformationen für die `GET` Anforderung. Sie können `PUT` FHIR-Ressourcen auch senden oder `POST` anfordern, um FHIR-Ressourcen zu erstellen/zu aktualisieren.

```
### GET Patient 
GET {{fhirurl}}/Patient/<patientid>
Authorization: Bearer {{token}}
```

[![GET Patient ](media/rest-patient.png) ](media/rest-patient.png#lightbox)

## <a name="run-powershell-or-cli"></a>Ausführen von PowerShell oder cli

Sie können PowerShell- oder CLI-Skripts in Visual Studio Code ausführen. Drücken `CTRL` Sie und die `~` TASTE, und wählen Sie PowerShell oder Bash aus. Weitere Informationen finden Sie unter [Integriertes Terminal.](https://code.visualstudio.com/docs/editor/integrated-terminal)

### <a name="powershell-in-visual-studio-code"></a>PowerShell in Visual Studio Code
[![Ausführen von PowerShell ](media/rest-powershell.png) ](media/rest-powershell.png#lightbox)

### <a name="cli-in-visual-studio-code"></a>CLI in Visual Studio Code
[![Ausführen der CLI ](media/rest-cli.png) ](media/rest-cli.png#lightbox)

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie die Metadaten nicht abrufen können, für die kein Zugriffstoken basierend auf der HL7-Spezifikation erforderlich ist, überprüfen Sie, ob Ihr FHIR-Server ordnungsgemäß ausgeführt wird.

Wenn Sie kein Zugriffstoken abrufen können, stellen Sie sicher, dass die Clientanwendung ordnungsgemäß registriert ist und Sie die richtigen Werte aus dem Anwendungsregistrierungsschritt verwenden.

Wenn Sie keine Daten vom FHIR-Server abrufen können, stellen Sie sicher, dass der Clientanwendung (oder dem Dienstprinzipal) Zugriffsberechtigungen wie "FHIR-Datenmitwirkender" für den FHIR-Server erteilt wurden.
