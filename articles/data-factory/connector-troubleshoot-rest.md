---
title: Problembehandlung für den REST-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem DB2-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: dcf49b00450836aafdb5b9772744914b8e5cf0e2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390518"
---
# <a name="troubleshoot-the-rest-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den REST-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem REST-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-restsinkcallfailed"></a>Fehlercode: RestSinkCallFailed

- **Meldung**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Ursache**: Dieser Fehler tritt auf, wenn eine Data Factory- oder Synapse-Pipeline mit dem REST-Endpunkt über das HTTP-Protokoll kommuniziert und beim Anforderungsvorgang ein Fehler auftritt.

- **Empfehlung**: Überprüfen Sie den HTTP-Statuscode bzw. die HTTP-Statusnachricht in der Fehlermeldung, und beheben Sie das Problem mit dem Remoteserver.

## <a name="error-code-restsourcecallfailed"></a>Fehlercode: RestSourceCallFailed

- **Meldung**: `The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **Ursache:** Dieser Fehler tritt auf, wenn Azure Data Factory mit dem REST-Endpunkt über das HTTP-Protokoll kommuniziert und beim Anforderungsvorgang ein Fehler auftritt.

- **Empfehlung**: Überprüfen Sie den HTTP-Statuscode bzw. die Anforderungs-URL oder Antwortpayload in der Fehlermeldung, und beheben Sie das Problem mit dem Remoteserver.

## <a name="error-code-restsinkunsupportedcompressiontype"></a>Fehlercode: RestSinkUNSupportedCompressionType

- **Meldung**: `User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **Empfehlung**: Überprüfen Sie die unterstützten Komprimierungstypen für die REST-Senke.

## <a name="unexpected-network-response-from-the-rest-connector"></a>Unerwartete Netzwerkantwort vom REST-Connector

- **Symptome:** Der Endpunkt empfängt manchmal eine unerwartete Antwort (400/401/403/500) vom REST-Connector.

- **Ursache:** Für den REST-Quellenconnector werden beim Erstellen einer HTTP-Anforderung die URL und die HTTP-Methode (mit Header und Text) des verknüpften Diensts, des Datasets oder der Kopierquelle als Parameter verwendet. Der wahrscheinlichste Grund für das Problem sind einige Fehler in mindestens einem der angegebenen Parameter.

- **Lösung**: 
    - Verwenden Sie „curl“ in einem Eingabeaufforderungsfenster, um zu überprüfen, ob der Parameter die Ursache ist (die Header **Accept** und **User-Agent** sollten immer vorhanden sein):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Wenn für diesen Befehl die gleiche unerwartete Antwort zurückgegeben wird, korrigieren Sie die Parameter oben mit „curl“, bis die erwartete Antwort zurückgegeben wird. 

      Sie können auch „curl--help“ verwenden, um Informationen zu den erweiterten Optionen des Befehls anzuzeigen.

    - Falls nur vom REST-Connector eine unerwartete Antwort zurückgegeben wird, wenden Sie sich für die weitere Problembehandlung an den Microsoft-Support.
    
    - Beachten Sie, dass „curl“ ggf. nicht geeignet ist, um ein Problem bei der Überprüfung des SSL-Zertifikats zu reproduzieren. In einigen Szenarien war die Ausführung des Befehls „curl“ erfolgreich, ohne dass Probleme bei der Überprüfung des SSL-Zertifikats aufgetreten sind. Wenn dieselbe URL dann im Browser ausgeführt wird, wird für den Client aber kein SSL-Zertifikat zurückgegeben, mit dem eine Vertrauensstellung mit dem Server eingerichtet werden kann.

      Für den obigen Fall wird die Verwendung von Tools wie **Postman** und **Fiddler** empfohlen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
