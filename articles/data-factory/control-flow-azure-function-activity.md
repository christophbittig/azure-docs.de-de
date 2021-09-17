---
title: Azure-Funktionsaktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Aktivität „Azure-Funktion“ nutzen, um eine Azure-Funktion in einer Azure Data Factory- oder Azure Synapse Analytics-Pipeline auszuführen.
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 4e62ea4c57e00695b2a2f969b9fd4f80f8298681
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824083"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivität „Azure Function“ in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Mit der Aktivität „Azure-Funktion“ können Sie [Azure-Funktionen](../azure-functions/functions-overview.md) in einer Azure Data Factory- oder Synapse-Pipeline ausführen. Um eine Azure-Funktion auszuführen, müssen Sie einen verknüpften Dienst erstellen sowie eine Aktivität, die die auszuführende Azure-Funktion angibt.

Das folgende Video enthält eine achtminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Verknüpfter Dienst der Azure-Funktion


Der Rückgabetyp der Azure-Funktion muss ein gültiges `JObject` sein. (Beachten Sie, dass [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)*kein*`JObject` ist.) Jeder andere Rückgabetyp als `JObject` führt zu dem Benutzerfehler *Antwortinhalt ist kein gültiges JObject*.

Der Funktionsschlüssel bietet sicheren Zugriff auf den Funktionsnamen, wobei jede Funktion über einen eigenen, eindeutigen Schlüssel oder einen Hauptschlüssel innerhalb einer Funktions-App verfügt. Verwaltete Identitäten bieten sicheren Zugriff auf die gesamte Funktions-App. Der Benutzer muss den Schlüssel für den Zugriff auf den Funktionsnamen angeben. Weitere Informationen finden Sie in der Funktionsdokumentation zum [Schlüssel für den Funktionszugriff](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#configuration).


| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| type   | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureFunction** | ja |
| Funktions-App-URL | URL für die Azure-Funktions-App. Das Format lautet `https://<accountname>.azurewebsites.net`. Diese URL ist der Wert unter dem Abschnitt **URL**, wenn Sie Ihre Funktions-App im Azure-Portal anzeigen.  | ja |
| Funktionsschlüssel | Der Zugriffsschlüssel für die Azure-Funktion. Klicken Sie in den Abschnitt **Verwalten** der jeweiligen Funktion, und kopieren Sie entweder den **Funktionsschlüssel** oder den **Hostschlüssel**. Weitere Informationen finden Sie hier: [HTTP-Trigger und -Bindungen in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivität „Azure Function“

| **Eigenschaft**  | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| --- | --- | --- | --- |
| name  | Name der Aktivität in der Pipeline  | String | ja |
| Typ  | Typ der Aktivität ist „AzureFunctionActivity“ | String | ja |
| Verknüpfter Dienst | Der mit der Azure-Funktion verknüpfte Dienst für die entsprechende Azure-Funktions-App  | Verweis auf den verknüpften Dienst | ja |
| Funktionsname  | Name der Funktion in der Azure-Funktions-App, die diese Aktivität aufruft. | String | ja |
| method  | REST-API-Methode für den Funktionsaufruf | Zeichenfolgenunterstützte Typen: GET, POST, PUT   | ja |
| header  | Header, die in der Anforderung gesendet werden. So legen Sie beispielsweise die Sprache und den Typ für eine Anforderung fest: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Zeichenfolge (oder ein Ausdruck mit resultType der Zeichenfolge) | Nein |
| body  | Text, der zusammen mit der Anforderung an die API-Methode der Funktion gesendet wird  | Zeichenfolge (oder Ausdruck mit resultType der Zeichenfolge) oder Objekt.   | Erforderlich für PUT/POST-Methoden |
|   |   |   | |

Weitere Informationen finden Sie in den Details zum Schema der Anforderungsnutzlast im Abschnitt [Schema der Anforderungsnutzlast](control-flow-web-activity.md#request-payload-schema).

## <a name="routing-and-queries"></a>Routing und Abfragen

Die Azure-Funktionsaktivität unterstützt **Routing**. Wenn Ihre Azure-Funktion z. B. den Endpunkt `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` aufweist, dann ist der in der Azure-Funktionsaktivität zu verwendende `functionName` entsprechend `<functionName>/<value>`. Sie können diese Funktion parametrisieren, um zur Laufzeit den gewünschten `functionName` bereitzustellen.

Die Azure-Funktionsaktivität unterstützt auch **Abfragen**. Eine Abfrage muss als Teil des `functionName` einbezogen werden. Wenn der Funktionsname z. B. `HttpTriggerCSharp` lautet und die einzubeziehende Abfrage `name=hello` ist, dann können Sie den `functionName` in der Azure-Funktionsaktivität als `HttpTriggerCSharp?name=hello` konstruieren. Diese Funktion kann parametrisiert werden, sodass der Wert zur Laufzeit bestimmt werden kann.

## <a name="timeout-and-long-running-functions"></a>Zeitlimit und zeitintensive Funktionen

Azure Functions erreicht nach 230 Sekunden den Timeout, unabhängig von der Einstellung `functionTimeout`, die Sie in den Einstellungen vorgenommen haben. [hier finden Sie weitere Informationen](../azure-functions/functions-versions.md#timeout) Um dieses Verhalten zu umgehen, folgen Sie einem asynchronen Muster oder verwenden Sie Durable Functions. Der Vorteil von Durable Functions besteht darin, dass es einen eigenen Mechanismus für die Zustandsnachverfolgung bereitstellt, sodass Sie keinen eigenen Mechanismus implementieren müssen.

Weitere Informationen zu Durable Functions finden Sie in [diesem Artikel](../azure-functions/durable/durable-functions-overview.md). Sie können eine Azure-Funktionsaktivität einrichten, um Durable Function aufzurufen, das eine Antwort mit einer anderen URI zurückgibt, wie z. B. [dieses Beispiel](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Da `statusQueryGetUri` den HTTP-Status 202 zurückgibt, während die Funktion ausgeführt wird, können Sie den Status der Funktion mithilfe einer Webaktivität abfragen. Richten Sie einfach eine Webaktivität ein, bei der das Feld `url` auf `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` festgelegt ist. Wenn Durable Function abgeschlossen ist, entspricht die Ausgabe der Funktion der Ausgabe der Webaktivität.


## <a name="sample"></a>Beispiel

[Hier](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction) finden Sie ein Beispiel, in dem mithilfe einer Azure-Funktion der Inhalt einer TAR-Datei extrahiert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den unterstützten Aktivitäten finden Sie in [Pipelines und Aktivitäten](concepts-pipelines-activities.md).
