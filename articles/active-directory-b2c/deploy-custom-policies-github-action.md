---
title: Bereitstellen benutzerdefinierter Richtlinien mit GitHub Actions
titleSuffix: Azure AD B2C
description: Hier lernen Sie, wie Sie Azure AD B2C benutzerdefinierte Richtlinien in einer CI/CD-Pipeline unter Verwendung von GitHub Actions bereitstellen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ce060e9e251313b93930200a73dde9b747c2d75
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037366"
---
# <a name="deploy-custom-policies-with-github-actions"></a>Bereitstellen benutzerdefinierter Richtlinien mit GitHub Actions

Mit [GitHub Actions](https://docs.github.com/actions/quickstart) können Sie benutzerdefinierte Workflows für Continuous Integration (Cl) und Continuous Deployment (CD) direkt in Ihrem GitHub-Repository erstellen. Dieser Artikel beschreibt, wie Sie die Bereitstellung von Azure Active Directory B2C (Azure AD B2C) [benutzerdefinierten Richtlinien](user-flow-overview.md) mithilfe von GitHub Actions automatisieren können.

Um den Prozess der Bereitstellung von benutzerdefinierten Richtlinien zu automatisieren, verwenden Sie die [GitHub-Aktion für die Bereitstellung von benutzerdefinierten Azure AD B2C-Richtlinien](https://github.com/marketplace/actions/deploy-azure-ad-b2c-custom-policy). Diese GitHub-Aktion wurde von der [Azure AD B2C-Community](https://github.com/azure-ad-b2c/deploy-trustframework-policy) entwickelt. 

Diese Aktion stellt benutzerdefinierte Azure AD B2C-Richtlinien in Ihrem Azure AD B2C-Mandanten unter Verwendung der [Microsoft Graph API](/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true) bereit. Wenn die Richtlinie noch nicht in Ihrem Mandaten vorhanden ist, wird sie erstellt. Andernfalls wird sie ersetzt.

> [!IMPORTANT]
> Bei der Verwaltung von benutzerdefinierten Richtlinien in Azure AD B2C mit Azure Pipelines werden derzeit **Vorschau**-Vorgänge verwendet, die im `/beta`-Endpunkt der Microsoft Graph-API zur Verfügung stehen. Die Verwendung dieser APIs in Produktionsanwendungen wird nicht unterstützt. Weitere Informationen finden Sie in der [Microsoft Graph-REST-API-Beta-Endpunkt-Referenz](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true).

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md) beschriebenen Schritte aus.
* Wenn Sie kein GitHub-Repositorium erstellt haben, [erstellen Sie eines](https://docs.github.com/en/get-started/quickstart/create-a-repo).  

## <a name="select-a-custom-policies-folder"></a>Wählen Sie einen Ordner für benutzerdefinierte Richtlinien

Ihr GitHub-Repository kann alle Ihre Azure AD B2C-Richtliniendateien und andere Objekte enthalten. Erstellen Sie im Stammordner Ihres Repositorys einen Ordner oder wählen Sie einen vorhandenen Ordner aus, der Ihre benutzerdefinierten Richtlinien enthält. 

Wählen Sie zum Beispiel einen Ordner namens *Richtlinien*. Fügen Sie Ihre benutzerdefinierten Azure AD B2C-Richtliniendateien in den Ordner  *Richtlinien* ein. Committen Sie dann die Änderungen mit **Commit**.

Die Änderungen nicht **pushen**. Sie werden dies später tun, nachdem Sie den Bereitstellungsworkflow eingerichtet haben.

## <a name="register-a-microsoft-graph-application"></a>Registrieren einer Microsoft Graph-Anwendung

Damit die GitHub Action mit der [Microsoft Graph API](microsoft-graph-operations.md) interagieren kann, erstellen Sie eine Anwendungsregistrierung in Ihrem Azure AD B2C-Mandanten. Wenn dies noch nicht erfolgt ist, [registrieren Sie eine Microsoft Graph-Anwendung](microsoft-graph-get-started.md).

Damit die GitHub Action auf Daten in Microsoft Graph zugreifen kann, erteilen Sie der registrierten Anwendung die entsprechenden [Anwendungsberechtigungen](/graph/permissions-reference). Erteilen Sie der **Microsoft Graph** > **Richtlinie** >  die Berechtigung **Policy.ReadWrite.TrustFramework** innerhalb der **API-Berechtigungen** der App-Registrierung.

## <a name="create-a-github-encrypted-secret"></a>Ein verschlüsseltes GitHub-Geheimnis erstellen

GitHub-Geheimnisse sind verschlüsselte Umgebungsvariablen, die Sie in einer Organisation, einem Repository oder einer Repository-Umgebung erstellen. In diesem Schritt speichern Sie das Anwendungsgeheimnis für die Anwendung, die Sie zuvor im Schritt [Registrieren einer MS Graph-Anwendung](#register-a-microsoft-graph-application) registriert haben.

Die GitHub-Aktion für die Bereitstellung von benutzerdefinierten Azure AD B2C-Richtlinien verwendet das Geheimnis, um ein Zugriffstoken zu erhalten, das für die Interaktion mit der Microsoft Graph-API verwendet wird. Weitere Informationen finden Sie unter [Erstellen verschlüsselter Geheimnisse für ein Repository](https://docs.github.com/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository).

Gehen Sie folgendermaßen vor, um ein GitHub-Geheimnis zu erstellen:

1. Navigieren Sie in GitHub zur Hauptseite des Repositorys.
1. Wählen Sie unter dem Namen Ihres Repositorys **settings**.
1. Wählen Sie in der linken Seitenleiste **Secrets**.
1. Wählen Sie **New repository secret** (Neues Repositorygeheimnis) aus.
1. Geben Sie für **Name** **Clientsecret** ein.
1. Geben Sie für **Wert** das zuvor erstellte Anwendungsgeheimnis ein.
1. Klicken Sie auf **Add secret** (Geheimnis hinzufügen).

## <a name="create-a-github-workflow"></a>Erstellen eines GitHub-Workflows

Der GitHub-Workflow ist eine automatisierte Prozedur, die Sie zu Ihrem Repository hinzufügen. Workflows bestehen aus einem oder mehreren Jobs und können geplant oder durch ein Ereignis ausgelöst werden. In diesem Schritt erstellen Sie einen Workflow, der Ihre benutzerdefinierte Richtlinie bereitstellt.

Gehen Sie zum Erstellen eines Workflows wie folgt vor:

1. Wechseln Sie in GitHub zur Hauptseite Ihres Repositorys.
1. Wählen Sie unter dem Namen Ihres Repositorys **Aktionen**.

   ![Screenshot mit der Registerkarte „GitHub Actions“](media/deploy-custom-policies-github-action/github-actions-tab.png)

1. Wenn Sie noch keinen Workflow konfiguriert haben, wählen Sie **Einen Workflow selbst einrichten**. Wählen Sie andernfalls **Neuer Workflow**.

   ![Screenshot, der das Erstellen eines neuen Workflows zeigt](media/deploy-custom-policies-github-action/set-up-a-workflow.png)

1. GitHub bietet Ihnen an, eine Workflow-Datei mit dem Namen `main.yml` im Ordner `.github/workflows` zu erstellen. Diese Datei enthält Informationen über den Workflow, einschließlich Ihrer Azure AD B2C-Umgebung und der bereitzustellenden benutzerdefinierten Richtlinien. Fügen Sie im GitHub Webeditor den folgenden YAML-Code ein:

    ```yml
    on: push

    env:
      clientId: 00000000-0000-0000-0000-000000000000
      tenant: your-tenant.onmicrosoft.com
    
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
    
        - name: 'Upload TrustFrameworkBase Policy'
          uses: azure-ad-b2c/deploy-trustframework-policy@v3
          with:
            folder: "./Policies"
            files: "TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
            tenant: ${{ env.tenant }}
            clientId: ${{ env.clientId }}
            clientSecret: ${{ secrets.clientSecret }}
    ```

1.  Aktualisieren Sie die folgenden Eigenschaften der YAML-Datei:

    | `Section`| Name | Wert |
    | ---- | ----- |----- |
    | `env` | `clientId` | **Anwendungs-(Client-)ID** der Anwendung, die Sie im Schritt [ Registrieren einer MS Graph-Anwendung](#register-a-microsoft-graph-application) registriert haben. |
    |`env`| `tenant` | Ihr Azure AD B2C [Mandantenname](tenant-management.md#get-your-tenant-name) (z. B. contoso.onmicrosoft.com). |
    | `with`| `folder`| Ein Ordner, in dem die benutzerdefinierten Richtliniendateien gespeichert sind, z. B.`./Policies`|
    | `with`| `files` | Durch Kommas getrennte Liste der bereitzustellenden Richtliniendateien, z. B.`TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`|
    
    > [!IMPORTANT]
    > Achten Sie beim Ausführen der Agenten und Hochladen der Richtliniendateien darauf, dass sie in der richtigen Reihenfolge hochgeladen werden:
    >
    > 1. *TrustFrameworkBase.xml*
    > 1. *TrustFrameworkLocalization.xml*
    > 1. *TrustFrameworkExtensions.xml*
    > 1. *SignUpOrSignin.xml*
    > 1. *ProfileEdit.xml*
    > 1. *PasswordReset.xml*

1. Klicken Sie auf **Start commit** (Commit starten).
1. Geben Sie unterhalb der Felder für Commit-Nachrichten an, ob Ihr Commit der aktuellen Verzweigung oder einer neuen Verzweigung hinzugefügt werden soll. Wählen Sie **Neue Datei committen** oder **Neue Datei vorschlagen**, um eine Pull-Anforderung zu erstellen.

## <a name="test-your-workflow"></a>Testen Ihres Workflows

Um den von Ihnen erstellten Workflow auszuprobieren **Pushen** Sie die Änderungen Ihrer benutzerdefinierten Richtlinie. Sobald Ihr Einzelvorgang läuft, können Sie den Fortschritt des Verlaufs in einem Diagramm visualisieren und die Aktivitäten der einzelnen Schritte auf GitHub einsehen.

1. Wechseln Sie auf GitHub zur Hauptseite Ihres Repositorys.
1. Wählen Sie unter dem Namen Ihres Repositorys **Aktionen** aus.
1. Wählen Sie in der linken Seitenleiste den von Ihnen erstellten Workflow aus.
1. Unter **Workflowausführungen** wählen Sie den Namen des Laufs aus, den Sie sehen möchten.

   ![Screenshot mit der Auswahl der Workflow-Aktivität](media/deploy-custom-policies-github-action/workflow-report.png)

1. Wählen Sie unter **Jobs** oder im Visualisierungsdiagramm den Job aus, den Sie sehen möchten.
1. Zeigen Sie die Ergebnisse der einzelnen Schritte an. Der folgende Screenshot zeigt das Schrittprotokoll **Benutzerdefinierte Richtlinie hochladen**.
 
   ![Das Schrittprotokoll für das Hochladen benutzerdefinierter Richtlinien](media/deploy-custom-policies-github-action/job-activity.png)


## <a name="optional-schedule-your-workflow"></a>Optional: Planen Sie Ihren Workflow

Der von Ihnen erstellte Workflow wird durch das Ereignis [pushen](https://docs.github.com/actions/reference/events-that-trigger-workflows#push) ausgelöst. Wenn Sie möchten, können Sie auch ein anderes Ereignis als Auslöser für den Workflow wählen, z. B. eine[ Pull-Anforderung](https://docs.github.com/actions/reference/events-that-trigger-workflows#pull_request).

Sie können einen Workflow auch so planen, dass er mittles [POSIX cron-Syntax](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07) zu bestimmten UTC-Zeiten läuft. Mit dem Zeitplanereignis können Sie einen Workflow zu einem geplanten Zeitpunkt auslösen. Weitere Informationen finden Sie unter [Geplante Ereignisse](https://docs.github.com/actions/reference/events-that-trigger-workflows#scheduled-events).

Im folgenden Beispiel wird der Workflow jeden Tag um 5:30 und 17:30 UTC ausgelöst:

```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

So bearbeiten Sie Ihren Workflow:

1. Wechseln Sie in GitHub zur Hauptseite Ihres Repositorys.
1. Wählen Sie unter dem Namen Ihres Repositorys **Aktionen** aus.
1. Wählen Sie in der linken Seitenleiste den von Ihnen erstellten Workflow aus.
1. Unter **Workflowausführungen** wählen Sie den Namen des Laufs aus, den Sie sehen möchten.
1. Wählen Sie im Menü die drei Punkte **...** und dann **Workflow-Datei anzeigen**.

   ![Screenshot mit der Anzeige der Workflow-Datei](media/deploy-custom-policies-github-action/edit-workflow.png)
   
1. Wählen Sie im GitHub Webeditor **Bearbeitung**.
1. Ändern Sie `on: push` wie im obigen Beispiel.
1. **Committen** Sie Ihre Änderungen.

## <a name="next-steps"></a>Nächste Schritte

- Lernen Sie, wie Sie [Ereignisse, die Workflows auslösen](https://docs.github.com/actions/reference/events-that-trigger-workflows) konfigurieren


