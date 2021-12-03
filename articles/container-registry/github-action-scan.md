---
title: Überprüfen von Containerimages mit GitHub Actions
description: Hier erfahren Sie, wie Sie die Containerimages mithilfe der Aktion zur Containerüberprüfung überprüfen.
author: v-abiss
ms.author: v-abiss
ms.topic: quickstart
ms.reviewer: jukullam
ms.service: azure
ms.date: 05/20/2021
ms.custom: github-actions-azure
ms.openlocfilehash: a16c858008ce2233d0382e8a8f2a8e6224bbb6b8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315803"
---
# <a name="scan-container-images-using-github-actions"></a>Überprüfen von Containerimages mit GitHub Actions

Beginnen Sie mit [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions), indem Sie einen Workflow zum Erstellen und Überprüfen eines Containerimages erstellen.

Mit GitHub Actions können Sie Ihren CI/CD-Prozess beschleunigen, indem Sie Images aus Ihren Workflows erstellen, überprüfen und per Push in eine öffentliche oder private [Container Registry](https://azure.microsoft.com/services/container-registry/)-Instanz übertragen.

In diesem Artikel wird die [Überprüfung von Containerimages](https://github.com/marketplace/actions/container-image-scan) aus dem [GitHub Marketplace](https://github.com/marketplace) verwendet.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine Azure Container Registry-Instanz zum Speichern aller Containerimages, die erstellt und per Push übertragen werden. Sie können [eine Azure Container Registry-Instanz über das Azure-Portal erstellen](../container-registry/container-registry-get-started-portal.md).
- Ein GitHub-Konto mit einem aktiven Repository. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren. 
    - In diesem Beispiel wird die [Java-Beispielanwendung „Spring PetClinic“](https://github.com/spring-projects/spring-petclinic) verwendet.

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus drei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Erstellen einer Container Registry-Instanz in Azure <br /> 2. Erstellen eines GitHub-Geheimnisses. <br /> 3. Anmelden bei der Registrierung mit GH-Aktionen |
|**Build** | 1. Einrichten der Umgebung <br /> 2. Erstellen der App |
|**Erstellen, Überprüfen und Übertragen des Images per Push in die Containerregistrierung** | 1. Erstellen des Images <br /> 2. Überprüfen des Images <br /> 3. Übertragen des Images per Push|

## <a name="create-github-secrets"></a>Erstellen von GitHub-Geheimnissen

Wenn Sie die [Aktion zur Anmeldung bei Azure Container Registry](https://github.com/marketplace/actions/azure-container-registry-login) verwenden möchten, müssen Sie zunächst Ihre Container Registry-Details als ein Geheimnis zu Ihrem GitHub-Repository hinzufügen.

In diesem Beispiel erstellen Sie drei Geheimnisse, die Sie für die Authentifizierung bei Azure verwenden können.  

1. Öffnen Sie Ihr GitHub-Repository, und navigieren Sie zu **Einstellungen**.

    :::image type="content" source="media/github-action-scan/github-repo-settings.png" alt-text="Auswählen von „Einstellungen“ im Navigationsbereich":::

1. Wählen Sie **Geheimnisse** und dann **Neues Geheimnis** aus.

    :::image type="content" source="media/github-action-scan/azure-secret-add.png" alt-text="Auswählen der Option zum Hinzufügen eines Geheimnisses":::

1. Fügen Sie die folgenden Werte für jedes Geheimnis ein, das mit den folgenden Werten aus dem Azure-Portal erstellt wurde. Dazu navigieren Sie in Container Registry zu den **Zugriffsschlüsseln**. 

    | Name des GitHub-Geheimnisses | Azure Container Registry-Werte |
    |---------|---------|
    |`ACR_LOGIN_SERVER` | **Anmeldeserver** |
    |`REGISTRY_USERNAME` | **Benutzername** |
    |`REGISTRY_PASSWORD` | **password** |
    
1. Speichern Sie die Änderungen, indem Sie die Option **Add secret** (Geheimnis hinzufügen) auswählen.

## <a name="add-a-dockerfile"></a>Hinzufügen eines Dockerfile

Verwenden Sie den folgenden Codeausschnitt, um ein `Dockerfile` zu erstellen und ein Commit in das Repository auszuführen.

```
FROM openjdk:11-jre-stretch
ADD target/spring-petclinic-2.4.2.jar spring-petclinic-2.4.2.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "spring-petclinic-2.4.2.jar" ]
```


## <a name="use-the-docker-login-action"></a>Verwenden der Docker-Anmeldeaktion

Verwenden Sie Ihr Geheimnis mit der [Aktion zur Anmeldung bei Azure Container Registry](https://github.com/marketplace/actions/azure-container-registry-login), um sich bei Azure zu authentifizieren.

In diesem Workflow authentifizieren Sie sich mithilfe der Azure Container Registry-Anmeldung über die Details zu **Anmeldeserver**, **Benutzername und **Kennwort** der Registrierung, die in `secrets.ACR_LOGIN_SERVER`, `secrets.REGISTRY_USERNAME` bzw. `secrets.REGISTRY_PASSWORD` in der [Docker-Anmeldeaktion](https://github.com/Azure/docker-login) gespeichert sind. Weitere Informationen zum Verweisen auf GitHub-Geheimnisse in einer Workflowdatei finden Sie in den GitHub-Dokumentationen unter [Verwenden verschlüsselter Geheimnisse in einem Workflow](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow).


```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
```

## <a name="configure-java"></a>Konfigurieren von Java

Richten Sie mithilfe der [Aktion zum Einrichten des Java JDK](https://github.com/marketplace/actions/setup-java-jdk) die Java-Umgebung ein. In diesem Beispiel richten Sie die Umgebung ein und nehmen die Erstellung mit Maven vor. Dann erstellen und überprüfen Sie das Image und übertragen es per Push in die Containerregistrierung.

[GitHub-Artefakte](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts) ermöglichen die Weitergabe von Dateien in einem Workflow zwischen Aufträgen. 

```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest    
    steps:
    - name: Checkout
      uses: actions/checkout@v2    

    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        java-version: '1.8.x'
        
    - name: Build with Maven
      run: mvn package --file pom.xml
```

## <a name="build-your-image"></a>Erstellen Ihres Images 

Verwenden Sie den folgenden Codeausschnitt im Workflow, um das Image zu erstellen und zu markieren. Der folgende Codeausschnitt verwendet die Docker CLI zum Erstellen und Markieren des Images in einem Shell-Terminal. 

```yaml
    - name: Build and Tag image
      run: |
        docker build -f ./Dockerfile -t ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }} .
        
```

## <a name="scan-the-image"></a>Überprüfen des Images

Bevor Sie das erstellte Image per Push in die Containerregistrierung übertragen, sollten Sie sicherstellen, dass es auf Sicherheitsrisiken überprüft wird. Dazu verwenden Sie die [Aktion zum Überprüfen von Containerimages](https://github.com/marketplace/actions/container-image-scan).

Fügen Sie dem Workflow den folgenden Codeausschnitt hinzu, der das Image auf ***kritische Sicherheitsrisiken*** überprüft, damit das per Push übertragene Image sicher ist und den Standards entspricht.

Sie können mit dieser Aktion auch andere Eingaben hinzufügen oder Ihrem Repository eine Datei `allowedlist.yaml` hinzufügen, um Sicherheitsrisiken und Überprüfungen bewährter Methoden zu ignorieren. 

```yaml
    - name: Scan image
      uses: Azure/container-scan@v0
      with:
        image-name: ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        severity-threshold: CRITICAL
        run-quality-checks: true        
```

Beispiel: `allowedlist.yaml`.

```yaml
general:
  vulnerabilities:
    - CVE-2003-1307
    - CVE-2011-3374
  bestPracticeViolations:
    - CIS-DI-0005
```
## <a name="push-the-image-to-a-private-registry"></a>Übertragen des Images per Push in eine private Registrierung

Sobald das Image überprüft wurde und keine Sicherheitsrisiken gefunden wurden, kann das erstellte Image per Push in eine private Registrierung übertragen werden. Der folgende Codeausschnitt verwendet die Docker CLI in einem Shell-Terminal, um das Image per Push zu übertragen.

```yaml
    - name: Push image
      run: |
        docker push ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        
```
## <a name="next-steps"></a>Nächste Schritte
- Hier erfahren Sie, wie Sie eine [Bereitstellung in Azure Container Instances aus Azure Container Registry](../container-instances/container-instances-using-azure-container-registry.md) vornehmen.
