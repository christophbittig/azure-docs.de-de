---
title: Erstellen einer Vertrauensstellung zwischen einer App und einem externen Identitätsanbieter
titleSuffix: Microsoft identity platform
description: Richten Sie eine Vertrauensstellung zwischen einer App in Azure AD und einem externen Identitätsanbieter ein.  Dies ermöglicht einer Softwareworkload außerhalb von Azure den Zugriff auf durch Azure AD geschützte Ressourcen ohne Verwendung von Geheimnissen oder Zertifikaten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/25/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 1d2c4631b663a57a8b1b2465c8cba6d6f81d803b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095322"
---
# <a name="configure-an-app-to-trust-an-external-identity-provider-preview"></a>Konfigurieren einer Vertrauensstellung zwischen einer App und einem externen Identitätsanbieter (Vorschau)

In diesem Artikel wird beschrieben, wie Sie eine Vertrauensstellung zwischen einer Anwendung in Azure Active Directory (Azure AD) und einem externen Identitätsanbieter (IdP) erstellen.  Anschließend können Sie eine externe Softwareworkload konfigurieren, um ein Token vom externen IdP gegen ein Zugriffstoken von Microsoft Identity Platform auszutauschen. Die externe Workload kann auf durch Azure AD geschützte Ressourcen zugreifen, ohne Geheimnisse verwalten zu müssen (in unterstützten Szenarios).  Weitere Informationen zum Workflow für den Tokenaustausch finden Sie im Artikel zum [Identitätsverbund für Workloads](workload-identity-federation.md).  Sie richten die Vertrauensstellung ein, indem Sie Anmeldeinformationen für die Verbundidentität für Ihre App-Registrierung mithilfe von Microsoft Graph konfigurieren.

Jeder Benutzer mit Berechtigungen zum Erstellen einer App-Registrierung und zum Hinzufügen eines Geheimnisses oder Zertifikats kann Anmeldeinformationen für eine Verbundidentität hinzufügen.  Wenn die Option **Benutzer können Anwendungen registrieren** im Blatt [Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) auf **Nein** festgelegt ist, können Sie jedoch keine App-Registrierung erstellen und keine Anmeldeinformationen für eine Verbundidentität konfigurieren.  Wenden Sie sich dann an einen Administrator, um die Anmeldeinformationen für die Verbundidentität in Ihrem Namen konfigurieren zu lassen.  Dies kann jeder Benutzer mit einer Rolle als Anwendungsadministrator oder Anwendungsbesitzer erledigen.

Nachdem Sie Ihre App so konfiguriert haben, dass sie einem externen IdP vertraut, konfigurieren Sie Ihre Softwareworkload, um ein Zugriffstoken vom Microsoft-Identitätsanbieter abzurufen und auf von Azure AD geschützte Ressourcen zuzugreifen.

## <a name="prerequisites"></a>Voraussetzungen
[Erstellen Sie eine App-Registrierung](quickstart-register-app.md) in Azure AD.  Gewähren Sie Ihrer App Zugriff auf die Azure-Ressourcen, die von Ihrer externen Softwareworkload verwendet werden.  

Ermitteln Sie die Objekt-ID der App (nicht die Anwendungs-ID (Client)), die Sie in den folgenden Schritten benötigen.  Sie finden die Objekt-ID der App im Azure-Portal.  Navigieren Sie zur Liste der [registrierten Anwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) im Azure-Portal, und wählen Sie Ihre App-Registrierung aus.  Suchen Sie in **Übersicht**->**Essentials** die **Objekt-ID**.

Ermitteln Sie die Informationen für Ihren externen IdP und die Softwareworkload, die Sie in den folgenden Schritten benötigen.

## <a name="configure-a-federated-identity-credential-using-microsoft-graph"></a>Konfigurieren von Anmeldeinformationen für eine Verbundidentität mithilfe von Microsoft Graph

Starten Sie [Azure Cloud Shell](https://portal.azure.com/#cloudshell/), und melden Sie sich bei Ihrem Mandanten an.

### <a name="create-a-federated-identity-credential"></a>Erstellen von Anmeldeinformationen für eine Verbundidentität

Führen Sie den folgenden Befehl aus, um neue Anmeldeinformationen für eine Verbundidentität für Ihre App zu [erstellen](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true), die über die Objekt-ID der App angegeben wird.  

*issuer* und *subject* sind die wichtigsten Informationen, die zum Einrichten der Vertrauensstellung erforderlich sind. *issuer* ist die URL des externen Identitätsanbieters und muss mit dem Anspruch `issuer` des ausgetauschten externen Tokens übereinstimmen.  *subject* ist der Bezeichner der externen Softwareworkload und muss mit dem Anspruch `sub``subject` des ausgetauschten externen Tokens übereinstimmen. *subject* hat kein festes Format, da jeder IdP sein eigenes verwendet – manchmal eine GUID, manchmal einen durch Doppelpunkt getrennten Bezeichner, manchmal beliebige Zeichenfolgen. Die Kombination von  `issuer`  und  `subject`  muss für die App eindeutig sein.  Wenn die externe Softwareworkload Microsoft Identity Platform zum Austausch des externen Tokens gegen ein Zugriffstoken auffordert, werden die Werte von *issuer* und *subject* der Anmeldeinformationen für die Verbundidentität anhand der Ansprüche `issuer` und `subject` überprüft, die im externen Token bereitgestellt werden. Wenn diese Überprüfung erfolgreich ist, stellt Microsoft Identity Platform ein Zugriffstoken für die externe Softwareworkload aus.

*audiences* listet die Zielgruppen auf, die im externen Token angezeigt werden können.  Dieses Feld ist obligatorisch und wird standardmäßig auf „api://AzureADTokenExchange“ festgelegt. Es gibt an, was Microsoft Identity Platform im Anspruch `aud` im eingehenden Token akzeptieren muss.  Dieser Wert stellt Azure AD in Ihrem externen Identitätsanbieter dar und hat keinen festen Wert für alle Identitätsanbieter. Möglicherweise müssen Sie eine neue Anwendungsregistrierung in Ihrem IdP für die Zielgruppe dieses Tokens erstellen.

*name* ist der eindeutige Bezeichner für die Anmeldeinformationen für die Verbundidentität, der eine Zeichenbegrenzung von 120 Zeichen hat und URL-freundlich sein muss. Er ist unveränderlich, sobald er erstellt wurde.

*description* ist die nicht überprüfte, vom Benutzer bereitgestellte Beschreibung der Anmeldeinformationen der Verbundidentität. 

Führen Sie den folgenden Befehl aus, um neue Anmeldeinformationen für eine Verbundidentität für Ihre App zu [erstellen](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true), die über die Objekt-ID der App angegeben wird.  *issuer* gibt GitHub als externen Tokenaussteller an.  *subject* gibt die GitHub-Organisation, das Repository und die Umgebung für Ihren GitHub Actions-Workflow an.  Wenn der GitHub Actions-Workflow Microsoft Identity Platform zum Austausch eines GitHub-Tokens gegen ein Zugriffstoken auffordert, werden die Werte in den Anmeldeinformationen der Verbundidentität anhand des bereitgestellten GitHub-Tokens überprüft.

```azurecli
az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' --body '{"name":"Testing","issuer":"https://token.actions.githubusercontent.com/","subject":"repo:octo-org/octo-repo:environment:Production","description":"Testing","audiences":["api://AzureADTokenExchange"]}' 
```

Sie erhalten daraufhin die Antwort:
```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials/$entity",
  "audiences": [
    "api://AzureADTokenExchange"
  ],
  "description": "Testing",
  "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
  "issuer": "https://token.actions.githubusercontent.com/",
  "name": "Testing",
  "subject": "repo:octo-org/octo-repo:environment:Production"
}
```

> [!IMPORTANT]
> Wenn Sie versehentlich die falschen externen Workloadinformationen in der Einstellung *subject* hinzufügen, werden die Anmeldeinformationen für die Verbundidentität ohne Fehler erfolgreich erstellt.  Der Fehler wird erst angezeigt, wenn der Tokenaustausch fehlschlägt.

### <a name="list-federated-identity-credentials-on-an-app"></a>Auflisten der Anmeldeinformationen für Verbundidentitäten für eine App

Führen Sie den folgenden Befehl aus, um die Anmeldeinformationen für Verbundidentitäten für eine App [aufzulisten](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true), die über die Objekt-ID der App angegeben wird:

```azurecli
az rest -m GET -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' 
```

Sie erhalten daraufhin eine Antwort, die in etwa wie folgt aussieht:

```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials",
  "value": [
    {
      "audiences": [
        "api://AzureADTokenExchange"
      ],
      "description": "Testing",
      "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
      "issuer": "https://token.actions.githubusercontent.com/",
      "name": "Testing",
      "subject": "repo:octo-org/octo-repo:environment:Production"
    }
  ]
}
```

### <a name="delete-a-federated-identity-credential"></a>Löschen von Anmeldeinformationen für eine Verbundidentität

Führen Sie den folgenden Befehl aus, um die Anmeldeinformationen für eine Verbundidentität für Ihre App zu [löschen](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true), die über die Objekt-ID der App angegeben wird:

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/51ecf9c3-35fc-4519-a28a-8c27c6178bca' 

```

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich, wie Azure AD die [Gewährung von OAuth 2.0-Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) und eine Clientassertion, die von einem anderen IdP ausgegeben wurde, verwendet, um ein Token abzurufen.