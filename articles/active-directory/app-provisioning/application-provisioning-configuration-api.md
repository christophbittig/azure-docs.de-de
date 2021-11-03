---
title: Konfigurieren der Bereitstellung mithilfe von Microsoft Graph-APIs
description: Erfahren Sie, wie Sie Zeit sparen können, indem Sie die Konfiguration der automatischen Bereitstellung mithilfe von Microsoft Graph-APIs automatisieren.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/03/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: b9d337af752d7b463ffc4922b903e4b6064c6881
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028080"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Konfigurieren der Bereitstellung mithilfe von Microsoft Graph-APIs

Das Azure-Portal bietet eine bequeme Möglichkeit, die Bereitstellung für einzelne Apps einzeln zu konfigurieren. Wenn Sie jedoch mehrere – oder sogar Hunderte – Instanzen einer Anwendung erstellen, kann es einfacher sein, die App-Erstellung und -Konfiguration mithilfe der Microsoft Graph-APIs zu automatisieren. In diesem Artikel wird beschrieben, wie Sie die Bereitstellungskonfiguration über APIs automatisieren. Dieses Verfahren wird häufig für Anwendungen wie [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso) verwendet.

**Übersicht über die Schritte zum Automatisieren der Bereitstellungskonfiguration mithilfe von Microsoft Graph-APIs**


|Schritt  |Details  |
|---------|---------|
|[Schritt 1: Erstellen der Kataloganwendung](#step-1-create-the-gallery-application)     |Anmelden beim API-Client <br> Abrufen der Kataloganwendungsvorlage <br> Erstellen der Kataloganwendung         |
|[Schritt 2: Erstellen eines Bereitstellungsauftrags basierend auf der Vorlage](#step-2-create-the-provisioning-job-based-on-the-template)     |Abrufen der Vorlage für den Bereitstellungsconnector <br> Erstellen des Bereitstellungsauftrags         |
|[Schritt 3: Autorisieren des Zugriffs](#step-3-authorize-access)     |Testen der Verbindung mit der Anwendung <br> Speichern der Anmeldeinformationen         |
|[Schritt 4: Starten des Bereitstellungsauftrags](#step-4-start-the-provisioning-job)     |Starten des Auftrags         |
|[Schritt 5: Überwachen der Bereitstellung](#step-5-monitor-provisioning)     |Überprüfen des Status des Bereitstellungsauftrags <br> Abrufen der Bereitstellungsprotokolle         |

## <a name="step-1-create-the-gallery-application"></a>Schritt 1: Erstellen der Kataloganwendung

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Melden Sie sich bei Microsoft Graph-Tester (empfohlen), Postman oder einem beliebigen anderen API-Client an, den Sie verwenden.

1. Öffnen Sie den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer).
1. Wählen Sie die Schaltfläche „Mit Microsoft anmelden“ aus, und melden Sie sich mit den globalen Azure AD-Anmeldeinformationen oder mit den Anmeldeinformationen für den App-Administrator an.
1. Nach der erfolgreichen Anmeldung werden im linken Bereich die Details des Benutzerkontos angezeigt.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Abrufen des Bezeichners für die Kataloganwendungsvorlage
Anwendungen im Azure AD Anwendungskatalog verfügen jeweils über eine [Anwendungsvorlage](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true), in der die Metadaten für die Anwendung beschrieben werden. Mithilfe dieser Vorlage können Sie im Mandanten für die Verwaltung eine Instanz der Anwendung und des Dienstprinzipals erstellen. Rufen Sie den Bezeichner der Anwendungsvorlage für **AWS Single-Account Access** ab, und notieren Sie sich den Wert der Eigenschaft **id** aus der Antwort zur späteren Verwendung in diesem Tutorial.

#### <a name="request"></a>Anforderung

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates?$filter=displayName eq 'AWS Single-Account Access'
```
#### <a name="response"></a>Antwort

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Single-Account Access",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": "Federate to a single AWS account and use SAML claims to authorize access to AWS IAM roles. If you have many AWS accounts, consider using the AWS Single Sign-On gallery application instead."    

}
```

### <a name="create-the-gallery-application"></a>Erstellen der Kataloganwendung

Verwenden Sie die Vorlagen-ID, die Sie im letzten Schritt für die Anwendung abgerufen haben, um im Mandanten [eine Instanz](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta&preserve-view=true) der Anwendung und des Dienstprinzipals zu erstellen.

#### <a name="request"></a>Anforderung


```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Antwort

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Schritt 2: Erstellen des Bereitstellungsauftrags basierend auf der Vorlage

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Abrufen der Vorlage für den Bereitstellungsconnector

Anwendungen im Katalog, die für die Bereitstellung aktiviert sind, verfügen über Vorlagen zum Optimieren der Konfiguration. Verwenden Sie die nachstehende Anforderung, um [die Vorlage für die Bereitstellungskonfiguration abzurufen](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true). Beachten Sie, dass Sie die ID angeben müssen. Die ID bezieht sich auf die vorherige Ressource, in diesem Fall die servicePrincipal-Ressource. 

#### <a name="request"></a>Anforderung

```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```

#### <a name="response"></a>Antwort
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Erstellen des Bereitstellungsauftrags
Um die Bereitstellung zu ermöglichen, müssen Sie zunächst [einen Auftrag erstellen](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true). Verwenden Sie die nachstehende Anforderung, um einen Bereitstellungsauftrag zu erstellen. Verwenden Sie die „templateId“ aus dem vorherigen Schritt, wenn Sie die Vorlage angeben, die für den Auftrag verwendet werden soll.

#### <a name="request"></a>Anforderung

```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>Antwort
```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Schritt 3: Autorisieren des Zugriffs

### <a name="test-the-connection-to-the-application"></a>Testen der Verbindung mit der Anwendung

Testen Sie die Verbindung mit der Drittanbieteranwendung. Das folgende Beispiel gilt für eine Anwendung, die einen geheimen Clientschlüssel und ein geheimes Token erfordert. Jede Anwendung verfügt über eigene Anforderungen. Anwendungen verwenden häufig eine Basisadresse anstelle eines geheimen Clientschlüssels. Um festzustellen, welche Anmeldeinformationen für Ihre App erforderlich sind, navigieren Sie zur Seite der Bereitstellungskonfiguration für Ihre Anwendung, und klicken Sie im Entwicklermodus auf **Verbindung testen**. Unter „Netzwerkdatenverkehr“ werden die Parameter gezeigt, die als Anmeldeinformationen verwendet werden. Eine vollständige Liste der Anmeldeinformationen finden Sie unter [synchronizationJob: validateCredentials](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true). Die meisten Anwendungen, z. B. Azure Databricks, verwenden eine BaseAddress und ein SecretToken. Die BaseAddress wird im Azure-Portal als Mandanten-URL bezeichnet. 

#### <a name="request"></a>Anforderung
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials

{ 
    "credentials": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx" 
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```
#### <a name="response"></a>Antwort
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Speichern Ihrer Anmeldeinformationen

Das Konfigurieren der Bereitstellung erfordert das Einrichten einer Vertrauensstellung zwischen Azure AD und der Anwendung. Autorisieren Sie den Zugriff auf die Drittanbieteranwendung. Das folgende Beispiel gilt für eine Anwendung, die einen geheimen Clientschlüssel und ein geheimes Token erfordert. Jede Anwendung verfügt über eigene Anforderungen. Die verfügbaren Optionen finden Sie in der [API-Dokumentation](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true). 

#### <a name="request"></a>Anforderung
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 

{ 
    "value": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx"
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```

#### <a name="response"></a>Antwort
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Schritt 4: Starten des Bereitstellungsauftrags
Nachdem der Bereitstellungsauftrag konfiguriert wurde, verwenden Sie den folgenden Befehl, um [den Auftrag zu starten](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true). 


#### <a name="request"></a>Anforderung

```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```


#### <a name="response"></a>Antwort
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Schritt 5: Überwachen der Bereitstellung

### <a name="monitor-the-provisioning-job-status"></a>Überwachen des Status des Bereitstellungsauftrags

Da der Bereitstellungsauftrag jetzt ausgeführt wird, verwenden Sie den folgenden Befehl, um den Fortschritt des aktuellen Bereitstellungszyklus sowie die bisherigen Statistiken, z. B. die Anzahl der Benutzer und Gruppen, die im Zielsystem erstellt wurden, nachzuverfolgen. 

#### <a name="request"></a>Anforderung
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>Antwort
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Überwachen von Bereitstellungsereignissen mithilfe der Bereitstellungsprotokolle
Zusätzlich zur Überwachung des Status des Bereitstellungsauftrags können Sie über die [Bereitstellungsprotokolle](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta&preserve-view=true) alle auftretenden Ereignisse abfragen. Sie können beispielsweise einen bestimmten Benutzer abfragen, um zu ermitteln, ob dieser erfolgreich bereitgestellt wurde.

#### <a name="request"></a>Anforderung
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>Antwort
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "AWS Contoso",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "AWS Contoso"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            }
            ]
       }
    ]
}
```
## <a name="see-also"></a>Siehe auch

- [Lesen Sie die Dokumentation zur Synchronisierung für Microsoft Graph](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
- [Integrieren einer benutzerdefinierten SCIM-App in Azure AD](./use-scim-to-provision-users-and-groups.md)
