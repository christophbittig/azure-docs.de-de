---
title: Erstellen einer Vertrauensstellung zwischen einer App und GitHub
titleSuffix: Microsoft identity platform
description: Richten Sie eine Vertrauensstellung zwischen einer App in Azure AD und einem GitHub-Repository ein.  Dies ermöglicht einem GitHub Actions-Workflow den Zugriff auf von Azure AD geschützte Ressourcen ohne Verwendung von Geheimnissen oder Zertifikaten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/18/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 3e1e3d4857555b648b841a544e02346e6144bb2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095323"
---
# <a name="configure-an-app-to-trust-a-github-repo-preview"></a>Konfigurieren einer Vertrauensstellung zwischen einer App und einem GitHub-Repository (Vorschauversion)

In diesem Artikel wird beschrieben, wie Sie eine Vertrauensstellung zwischen einer Anwendung in Azure Active Directory (Azure AD) und einem GitHub-Repository erstellen.  Anschließend können Sie einen GitHub Actions-Workflow für den Austausch eines Tokens von GitHub durch ein Zugriffstoken von Microsoft Identity Platform konfigurieren und auf von Azure AD geschützte Ressourcen zugreifen, ohne Geheimnisse verwalten zu müssen.  Weitere Informationen zum Workflow für den Tokenaustausch finden Sie im Artikel zum [Identitätsverbund für Workloads](workload-identity-federation.md).  Sie richten die Vertrauensstellung ein, indem Sie Anmeldeinformationen für die Verbundidentität für Ihre App-Registrierung im Azure-Portal oder mithilfe von Microsoft Graph konfigurieren.

Jeder Benutzer mit Berechtigungen zum Erstellen einer App-Registrierung und zum Hinzufügen eines Geheimnisses oder Zertifikats kann Anmeldeinformationen für eine Verbundidentität hinzufügen.  Wenn die Option **Benutzer können Anwendungen registrieren** im Blatt [Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) auf **Nein** festgelegt ist, können Sie jedoch keine App-Registrierung erstellen und keine Anmeldeinformationen für eine Verbundidentität konfigurieren.  Wenden Sie sich dann an einen Administrator, um die Anmeldeinformationen für die Verbundidentität in Ihrem Namen konfigurieren zu lassen.  Dies kann jeder Benutzer mit einer Rolle als Anwendungsadministrator oder Anwendungsbesitzer erledigen.

Nachdem Sie Ihre App so konfiguriert haben, dass sie einem GitHub-Repository vertraut, [konfigurieren Sie Ihren GitHub Actions-Workflow](/azure/developer/github/connect-from-azure), um ein Zugriffstoken vom Microsoft-Identitätsanbieter abzurufen und auf von Azure AD geschützte Ressourcen zu zugreifen.

## <a name="prerequisites"></a>Voraussetzungen
[Erstellen Sie eine App-Registrierung](quickstart-register-app.md) in Azure AD.  Gewähren Sie Ihrer App Zugriff auf die Azure-Ressourcen, die von Ihrem GitHub-Workflow verwendet werden.  

Ermitteln Sie die Objekt-ID der App (nicht die Anwendungs-ID (Client)), die Sie in den folgenden Schritten benötigen.  Sie finden die Objekt-ID der App im Azure-Portal.  Navigieren Sie zur Liste der [registrierten Anwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) im Azure-Portal, und wählen Sie Ihre App-Registrierung aus.  Suchen Sie in **Übersicht**->**Essentials** die **Objekt-ID**.

Ermitteln Sie die Informationen zu Organisation, Repository und Umgebung für Ihr GitHub-Repository, die Sie in den folgenden Schritten benötigen.

## <a name="configure-a-federated-identity-credential"></a>Konfigurieren von Anmeldeinformationen für eine Verbundidentität

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.  Navigieren Sie zu **App-Registrierungen**, und öffnen Sie die App, die Sie konfigurieren möchten.

Navigieren Sie zu **Zertifikate und Geheimnisse**.  Wählen Sie auf der Registerkarte **Verbundanmeldeinformationen** die Option **Anmeldeinformationen hinzufügen** aus.  Das Blatt **Anmeldeinformationen hinzufügen** wird geöffnet.

Wählen Sie im Dropdownfeld **Szenario für Verbundanmeldeinformationen** die Option **GitHub-Aktionen, die Azure-Ressourcen bereitstellen** aus.

Geben Sie die **Organisation** und das **Repository** für den GitHub Actions-Workflow an.  

Wählen Sie für **Entitätstyp** die Option **Umgebung**, **Branch**, **Pull Request** oder **Tag** aus, und geben Sie den Wert an.

Fügen Sie einen **Namen** für die Verbundanmeldeinformationen hinzu.

Die Felder **Aussteller**, **Zielgruppen** und **Antragstellerkennung** werden basierend auf den eingegebenen Werten automatisch ausgefüllt.

Klicken Sie auf **Hinzufügen**, um die Verbundanmeldeinformationen zu konfigurieren.

:::image type="content" source="media/workload-identity-federation-create-trust-github/add-credential.png" alt-text="Screenshot des Fensters „Anmeldeinformationen hinzufügen“ mit Beispielwerten." :::

> [!NOTE]
> Wenn Sie versehentlich das GitHub-Repository einer anderen Person in der Einstellung *subject* konfigurieren (z. B. durch einen Tippfehler, der dem Repository einer anderen Person entspricht), können Sie die Anmeldeinformationen für die Verbundidentität erfolgreich erstellen.  In der GitHub-Konfiguration wird jedoch ein Fehler gemeldet, da Sie nicht auf das Repository einer anderen Person zugreifen können.

> [!IMPORTANT]
> Die Werte für **Organisation**, **Repository** und **Entitätstyp** müssen exakt mit der Konfiguration in der GitHub-Workflowkonfiguration übereinstimmen. Andernfalls lehnt Microsoft Identity Platform den Austausch für ein Zugriffstoken ab, nachdem das eingehende externe Token überprüft wurde.  Es wird kein Fehler angezeigt, der Austausch schlägt ohne Fehlermeldung fehl.

# <a name="microsoft-graph"></a>[Microsoft Graph](#tab/microsoft-graph)
Starten Sie [Azure Cloud Shell](https://portal.azure.com/#cloudshell/), und melden Sie sich bei Ihrem Mandanten an.

### <a name="create-a-federated-identity-credential"></a>Erstellen von Anmeldeinformationen für eine Verbundidentität

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

*name*: Der Name Ihrer Azure-Anwendung.

*issuer*: Der Pfad zum GitHub OIDC-Anbieter: `https://token.actions.githubusercontent.com/`. Dieser Aussteller wird von Ihrer Azure-Anwendung als vertrauenswürdig eingestuft.

*subject*: Bevor Azure ein Zugriffstoken gewährt, muss die Anforderung die hier definierten Bedingungen erfüllen.
- Bei Aufträgen, die an eine Umgebung gebunden sind: `repo:< Organization/Repository >:environment:< Name >`
- Bei Aufträgen, die nicht an eine Umgebung gebunden sind, fügen Sie den Referenzpfad für den Branch/Tag auf der Grundlage des für die Auslösung des Workflows verwendeten Referenzpfads ein: `repo:< Organization/Repository >:ref:< ref path>`.  Zum Beispiel: `repo:n-username/ node_express:ref:refs/heads/my-branch` oder `repo:n-username/ node_express:ref:refs/tags/my-tag`.
- Für Workflows, die durch ein Pull Request-Ereignis ausgelöst werden: `repo:< Organization/Repository >:pull-request`.

*audiences*: Der erforderliche Wert ist `api://AzureADTokenExchange`.

> [!NOTE]
> Wenn Sie versehentlich das GitHub-Repository einer anderen Person in der Einstellung *subject* konfigurieren (z. B. durch einen Tippfehler, der dem Repository einer anderen Person entspricht), können Sie die Anmeldeinformationen für die Verbundidentität erfolgreich erstellen.  In der GitHub-Konfiguration wird jedoch ein Fehler gemeldet, da Sie nicht auf das Repository einer anderen Person zugreifen können.

> [!IMPORTANT]
> Die Werte für die Einstellung *subject* müssen exakt mit der Konfiguration in der GitHub-Workflowkonfiguration übereinstimmen.  Andernfalls lehnt Microsoft Identity Platform den Austausch für ein Zugriffstoken ab, nachdem das eingehende externe Token überprüft wurde.  Es wird kein Fehler angezeigt, der Austausch schlägt ohne Fehlermeldung fehl.

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
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/1aa3e6a7-464c-4cd2-88d3-90db98132755' 
```
---

## <a name="get-the-application-client-id-and-tenant-id-from-the-azure-portal"></a>Abrufen der Anwendungs-ID (Client) und der Mandanten-ID aus dem Azure-Portal

Rufen Sie die Werte *tenant-id* und *client-id* Ihrer App-Registrierung ab, bevor Sie Ihren GitHub Actions-Workflow konfigurieren.  Sie finden diese Werte im Azure-Portal. Navigieren Sie zur Liste der [registrierten Anwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps), und wählen Sie Ihre App-Registrierung aus.  Ermitteln Sie in **Übersicht**->**Essentials** die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** . Legen Sie diese Werte in Ihrer GitHub-Umgebung für die Verwendung in der Azure-Anmeldeaktion für Ihren Workflow fest.  

## <a name="next-steps"></a>Nächste Schritte
[Konfigurieren Sie einen GitHub Actions-Workflow](/azure/developer/github/connect-from-azure), um ein Zugriffstoken vom Microsoft-Identitätsanbieter abzurufen und auf Azure-Ressourcen zuzugreifen.

Lesen Sie die [GitHub Actions-Dokumentation](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure), um mehr über das Konfigurieren Ihres GitHub Actions-Workflows für das Abrufen eines Zugriffstokens vom Microsoft-Identitätsanbieter und den Zugriff auf Azure-Ressourcen zu erfahren.