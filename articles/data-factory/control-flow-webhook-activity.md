---
title: Webhook-Aktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Die Webhook-Aktivität für Azure Data Factory und Synapse Analytics steuert die Ausführung von Pipelines über benutzerdefinierten Code.
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 8b0443cd44dffeec1ea9a70e460ca0f72e05b24d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798824"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-Aktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Eine Webhook-Aktivität kann die Ausführung von Pipelines über benutzerdefinierten Code steuern. Mithilfe der Webhook-Aktivität kann Code einen Endpunkt aufrufen und eine Rückruf-URL an ihn übergeben. Die Pipelineausführung wartet, bis der Rückruf aufgerufen wurde, bevor sie mit der nächsten Aktivität fortfährt.

> [!IMPORTANT]
> Mit der Webhook-Aktivität können Sie nun Fehlerstatus- und benutzerdefinierte Meldungen an die Aktivität und die Pipeline zurückgeben. Legen Sie _reportStatusOnCallBack_ auf „true“ fest, und geben Sie _StatusCode_ und _Error_ in der Rückrufnutzlast an. Weitere Informationen finden Sie unter [Weitere Hinweise](#additional-notes).

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
**name** | Der Name der Webhook-Aktivität. | String | Ja |
**type** | Muss auf „WebHook“ festgelegt werden. | String | Ja |
**method** | Die REST-API-Methode für den Zielendpunkt. | Eine Zeichenfolge. Der unterstützte Typ ist „POST“. | Ja |
**url** | Der Zielendpunkt und Pfad. | Eine Zeichenfolge oder ein Ausdruck mit dem **resultType**-Wert einer Zeichenfolge. | Ja |
**headers** | Header, die in der Anforderung gesendet werden. Hier sehen Sie ein Beispiel für das Festlegen von Sprache und Typ für eine Anforderung: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Eine Zeichenfolge oder ein Ausdruck mit dem **resultType**-Wert einer Zeichenfolge. | Ja. Ein `Content-Type`-Header wie `"headers":{ "Content-Type":"application/json"}` ist erforderlich. |
**body** | Stellt die an den Endpunkt gesendete Nutzlast dar. | Gültiges JSON-Format oder ein Ausdruck mit dem **resultType**-JSON-Wert. Weitere Informationen zum Schema der Anforderungsnutzlast finden Sie unter [Schema der Anforderungsnutzlast](./control-flow-web-activity.md#request-payload-schema). | Ja |
**Authentifizierung** | Die zum Aufrufen des Endpunkts verwendete Authentifizierungsmethode. Unterstützte Typen sind „Basic“ und „ClientCertificate“. Weitere Informationen finden Sie unter [Authentifizierung](./control-flow-web-activity.md#authentication). Wenn keine Authentifizierung erforderlich ist, schließen Sie diese Eigenschaft aus. | Eine Zeichenfolge oder ein Ausdruck mit dem **resultType**-Wert einer Zeichenfolge. | Nein |
**timeout** | Legt fest, wie lange die Aktivität darauf warten soll, dass der mittels **callBackUri** festgelegte Rückruf aufgerufen wird. Der Standardwert ist 10 Minuten („00:10:00“). Werte haben das TimeSpan-Format *t*.*hh*:*mm*:*ss*. | String | Nein |
**Report status on callback** | Ermöglicht einem Benutzer, den Fehlerstatus einer Webhook-Aktivität zu melden. | Boolesch | Nein |

## <a name="authentication"></a>Authentifizierung

Eine Webhook-Aktivität unterstützt die folgenden Authentifizierungstypen.

### <a name="none"></a>Keine

Wenn keine Authentifizierung erforderlich ist, schließen Sie die Eigenschaft **authentication** nicht ein.

### <a name="basic"></a>Basic

Geben Sie Benutzername und Kennwort für die Standardauthentifizierung an.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Clientzertifikat

Geben Sie die Base64-codierten Inhalte einer PFX-Datei und ein Kennwort an.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Verwaltete Identität

Verwenden Sie die verwaltete Identität für den Data Factory- oder Synapse-Arbeitsbereich, um den Ressourcen-URI anzugeben, für den das Zugriffstoken angefordert wird. Verwenden Sie zum Aufrufen der Azure-Ressourcenverwaltungs-API `https://management.azure.com/`. Weitere Informationen zur Funktion verwalteter Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Wenn der Dienst mit einem Git-Repository konfiguriert ist, müssen Sie Ihre Anmeldeinformationen in Azure Key Vault speichern, um die Standardauthentifizierung oder die Clientzertifikatauthentifizierung zu verwenden. Der Dienst speichert keine Kennwörter in Git.

## <a name="additional-notes"></a>Zusätzliche Hinweise

Der Dienst übergibt die zusätzliche Eigenschaft **callBackUri** in dem an den URL-Endpunkt gesendeten Text. Der Dienst erwartet, dass dieser URI vor dem angegebenen Timeoutwert aufgerufen wird. Wenn der URI nicht aufgerufen wird, tritt bei der Aktivität mit dem Status „TimedOut“ ein Fehler auf.

Bei der Webhook-Aktivität tritt ein Fehler auf, wenn beim Aufruf des benutzerdefinierten Endpunkts ein Fehler auftritt. Im Textkörper des Rückrufs kann jede beliebige Fehlermeldung hinzugefügt und in einer späteren Aktivität verwendet werden.

Bei jedem REST-API-Befehl kommt es zu einem Timeout des Clients, wenn der Endpunkt nicht innerhalb einer Minute antwortet. Dieses Verhalten ist die bewährte HTTP-Standardmethode. Um dieses Problem zu beheben, implementieren Sie ein 202-Muster. Im aktuellen Fall gibt der Endpunkt 202 (Akzeptiert) und die Clientabrufe zurück.

Das 1-Minute-Timeout für die Anforderung hat nichts mit dem Aktivitätstimeout zu tun. Letzteres wird verwendet, um auf den durch **callbackUri** angegeben Rückruf zu warten.

Der an den Rückruf-URI zurückgegebene Textkörper muss im gültigen JSON-Format vorliegen. Legen Sie den Header `Content-Type` auf `application/json` fest.

Wenn Sie die Eigenschaft **Status bei Rückruf melden** verwenden, müssen Sie dem Textkörper bei einem Rückruf den folgenden Code hinzufügen:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden unterstützten Ablaufsteuerungsaktivitäten:

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)