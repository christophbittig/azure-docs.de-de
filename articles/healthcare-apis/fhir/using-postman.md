---
title: Zugreifen auf den FHIR-Dienst für Azure Healthcare-APIs mithilfe von Postman
description: In diesem Artikel wird beschrieben, wie Sie mit Postman auf den FHIR-Dienst für Azure Healthcare-APIs zugreifen.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: 908e9a9c17f93725b3cfb7a464224539379fddd1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777597"
---
# <a name="access-the-fhir-service-using-postman"></a>Zugreifen auf den FHIR-Dienst mithilfe von Postman

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel werden die Schritte für den Zugriff auf den FHIR-Dienst für Gesundheits-APIs (auch als FHIR-Dienst bezeichnet) mit [Postman beschrieben.](https://www.getpostman.com/)

## <a name="prerequisites"></a>Voraussetzungen

* Der in Azure bereitgestellte FHIR-Dienst. Informationen zum Bereitstellen des FHIR-Diensts finden Sie unter [Bereitstellen eines FHIR-Diensts.](fhir-portal-quickstart.md)
* Eine registrierte Clientanwendung für den Zugriff auf den FHIR-Dienst. Informationen zum Registrieren einer Clientanwendung finden Sie unter [Registrieren einer Dienstclientanwendung in Azure Active Directory.](../register-application.md) 
* Berechtigungen, die der Clientanwendung und Ihrem Benutzerkonto gewährt werden, z. B. "FHIR-Daten-Mitwirkender", für den Zugriff auf den FHIR-Dienst. Weitere Informationen finden Sie unter [Bereitstellen eines FHIR-Diensts in Azure Healthcare-APIs.](fhir-portal-quickstart.md)
* Postman lokal installiert. Weitere Informationen zu Postman finden Sie unter Erste Schritte [mit Postman](https://www.getpostman.com/).

## <a name="using-postman-create-workspace-collection-and-environment"></a>Verwenden von Postman: Erstellen eines Arbeitsbereichs, einer Sammlung und einer Umgebung

Wenn Sie noch nicht mit Postman arbeiten, führen Sie die folgenden Schritte aus. Andernfalls können Sie diesen Schritt überspringen.
 
Postman führt das Arbeitsbereichskonzept ein, damit Sie und Ihr Team APIs, Sammlungen, Umgebungen und andere Komponenten freigeben können. Sie können die Standardeinstellung "Mein Arbeitsbereich" oder "Teamarbeitsbereich" verwenden oder einen neuen Arbeitsbereich für Sie oder Ihr Team erstellen.
 
[![Erstellen Sie einen neuen Arbeitsbereich in Postman. ](media/postman/postman-create-new-workspace.png) ](media/postman/postman-create-new-workspace.png#lightbox)

Erstellen Sie als Nächstes eine neue Sammlung, in der Sie alle zugehörigen REST-API-Anforderungen gruppiert werden können. Wählen Sie im Arbeitsbereich **Sammlungen erstellen aus.** Sie können den Standardnamen Neue **Sammlung behalten oder** umbenennen. Die Änderung wird automatisch gespeichert.

[![Erstellen Sie eine neue Sammlung. ](media/postman/postman-create-a-new-collection.png) ](media/postman/postman-create-a-new-collection.png#lightbox)

Sie können auch Postman-Sammlungen importieren und exportieren. Weitere Informationen finden Sie in [der Postman-Dokumentation.](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/)

[![Importieren von Daten. ](media/postman/postman-import-data.png) ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>Erstellen oder Aktualisieren von Umgebungsvariablen

Sie können zwar die vollständige URL in der Anforderung verwenden, es wird jedoch empfohlen, die URL und andere Daten in Variablen zu speichern und zu verwenden.

Für den Zugriff auf den FHIR-Dienst müssen wir die folgenden Variablen erstellen oder aktualisieren.

* **tenantid:** Azure-Mandant, in dem der FHIR-Dienst bereitgestellt wird. Sie befindet sich über die **Menüoption Anwendungsregistrierung –** Übersicht.
* **subid:** Azure-Abonnement, in dem der FHIR-Dienst bereitgestellt wird. Sie befindet sich über die **Menüoption Übersicht des FHIR-Diensts.**
* **clientid:** Anwendungsclient-Registrierungs-ID.
* **clientsecret:** Geheimer Clientregistrierungsgeheimnis der Anwendung.
* **fhirurl:** Die vollständige URL des FHIR-Diensts. Beispiel: `https://xxx.azurehealthcareapis.com`. Sie befindet sich über die **Menüoption Übersicht des FHIR-Diensts.**
* **bearerToken:** Die Variable zum Speichern des Azure Active Directory Zugriffstokens (Azure AD) im Skript. Lassen Sie dieses Feld leer.

> [!NOTE]
> Stellen Sie sicher, dass Sie die Umleitungs-URL [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) in der Clientanwendungsregistrierung konfiguriert haben.

[![Umgebungsvariable. ](media/postman/postman-environments-variable.png) ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>Verbinden zum FHIR-Server

Öffnen Sie Postman, und wählen Sie **den Arbeitsbereich,** **die Sammlung** **und** die Umgebung aus, die Sie verwenden möchten. Wählen Sie das `+` Symbol aus, um eine neue Anforderung zu erstellen. 

[![Erstellen Sie eine neue Anforderung. ](media/postman/postman-create-new-request.png) ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>Get capability statement

Geben `{{fhirurl}}/metadata` Sie in die Anforderung `GET` ein, und treffen Sie auf `Send` . Es sollte die Capability-Anweisung des FHIR-Diensts zu sehen sein.

[![Funktions-Anweisungsparameter. ](media/postman/postman-capability-statement.png) ](media/postman/postman-capability-statement.png#lightbox)

[![Speichern Sie die Anforderung. ](media/postman/postman-save-request.png) ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>Abrufen Azure AD Zugriffstokens

Der FHIR-Dienst wird durch Azure AD. Die Standardauthentifizierung kann nicht deaktiviert werden. Um auf den FHIR-Dienst zugreifen zu können, müssen Sie Azure AD Zugriffstoken abrufen. Weitere Informationen finden Sie unter [Microsoft Identity Platform Zugriffstoken.](../../active-directory/develop/access-tokens.md)

Erstellen Sie eine neue `POST` Anforderung:

1. Geben Sie in den Anforderungsheader ein: `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. Wählen Sie die **Registerkarte Text** und **dann x-www-form-urlencoded aus.** Geben Sie im Abschnitt Schlüssel und Wert die folgenden Werte ein:
    - **grant_type**: `Client_Credentials`
    - **client_id**: `{{clientid}}`
    - **client_secret**: `{{clientsecret}}`
    - **resource**: `{{fhirurl}}`
    
3. Wählen Sie die **Registerkarte Test** aus, und geben Sie im Textabschnitt ein: `pm.environment.set("bearerToken", pm.response.json().access_token);`
4. Klicken Sie auf **Speichern**, um die Einstellungen zu speichern.
5. Treffe **auf Senden.** Es sollte eine Antwort mit dem zugriffsbasierten Azure AD werden, das automatisch in der Variablen `accessToken` gespeichert wird. Sie können sie dann in allen API-Anforderungen des FHIR-Diensts verwenden.

  [![Schaltfläche "Senden". ](media/postman/postman-send-button.png) ](media/postman/postman-send-button.png#lightbox)

Sie können das Zugriffstoken mithilfe von Onlinetools wie [https://jwt.ms](https://jwt.ms) untersuchen. Wählen Sie die **Registerkarte** Ansprüche aus, um ausführliche Beschreibungen für jeden Anspruch im Token zu sehen.

[![Zugriffstokenansprüche. ](media/postman/postman-access-token-claims.png) ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>FHIR-Ressource erhalten

Nachdem Sie ein Zugriffstoken Azure AD haben, können Sie auf die FHIR-Daten zugreifen. Geben Sie in `GET` einer neuen Anforderung `{{fhirurl}}/Patient` ein.

Wählen **Sie Bearertoken** als Autorisierungstyp aus.  Geben `{{bearerToken}}` Sie im Abschnitt **Token** ein. Wählen Sie **Send** (Senden) aus. Als Antwort sollte eine Liste der Patienten in Ihrer FHIR-Ressource angezeigt werden.

[![Wählen Sie Bearertoken aus. ](media/postman/postman-select-bearer-token.png) ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>Erstellen oder Aktualisieren Ihrer FHIR-Ressource

Nachdem Sie ein Zugriffstoken Azure AD haben, können Sie die FHIR-Daten erstellen oder aktualisieren. Beispielsweise können Sie einen neuen Patienten erstellen oder einen vorhandenen Patienten aktualisieren.
 
Erstellen Sie eine neue Anforderung, ändern Sie die Methode in "Post", und geben Sie den Wert im Anforderungsabschnitt ein.

`{{fhirurl}}/Patient`

Wählen **Sie Bearertoken** als Autorisierungstyp aus.  Geben `{{bearerToken}}` Sie im Abschnitt **Token** ein. Wählen Sie die **Registerkarte Text** aus. Wählen Sie die **Rohdatenoption** und **JSON** als Texttextformat aus. Kopieren Sie den Text, und fügen Sie ihn in den Textabschnitt ein. 


```
{
    "resourceType": "Patient",
    "active": true,
    "name": [
        {
            "use": "official",
            "family": "Kirk",
            "given": [
                "James",
                "Tiberious"
            ]
        },
        {
            "use": "usual",
            "given": [
                "Jim"
            ]
        }
    ],
    "gender": "male",
    "birthDate": "1960-12-25"
}
```
Wählen Sie **Send** (Senden) aus. In der JSON-Antwort sollte ein neuer Patient angezeigt werden.

[![Schaltfläche "Senden", um einen neuen Patienten zu erstellen. ](media/postman/postman-send-create-new-patient.png) ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>Exportieren von FHIR-Daten

Nachdem Sie ein Zugriffstoken Azure AD haben, können Sie FHIR-Daten in ein Azure-Speicherkonto exportieren.

Erstellen Sie eine neue `GET` Anforderung: `{{fhirurl}}/$export?_container=export`

Wählen **Sie Bearertoken** als Autorisierungstyp aus.  Geben `{{bearerToken}}` Sie im Abschnitt **Token** ein. Wählen **Sie Header aus,** um zwei neue Header hinzuzufügen:

- **Akzeptieren Sie**: `application/fhir+json`
- **Bevorzugen Sie**:  `respond-async`

Treffe **auf Senden.** Sie sollten eine Antwort `202 Accepted` bemerken. Wählen Sie **die Registerkarte Header der** Antwort aus, und notieren Sie sich den Wert in **Content-Location**. Sie können den Wert verwenden, um den Status des Exportauftrags abfragt.

[![Veröffentlichen Sie , um eine neue akzeptierte Antwort für Patient 202 zu erstellen. ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mit Postman auf den FHIR-Dienst in Azure Healthcare-APIs zugreifen. Informationen zum FHIR-Dienst in Azure Healthcare-APIs finden Sie unter

>[!div class="nextstepaction"]
>[Was ist der FHIR-Dienst?](overview.md)