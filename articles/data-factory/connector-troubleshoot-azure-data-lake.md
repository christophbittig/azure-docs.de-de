---
title: Problembehandlung für Azure Data Lake Storage-Connectors
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit den Connectors für Azure Data Lake Storage Gen1 und Gen2 in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 1c36fa5295acafb96e57484cf34429091dd634e9
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390524"
---
# <a name="troubleshoot-the-azure-data-lake-storage-connectors-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für Azure Data Lake Storage-Connectors in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit den Connectors für Azure Data Lake Storage Gen1 und Gen2 in Azure Data Factory und Azure Synapse.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Fehlermeldung: Die zugrunde liegende Verbindung wurde geschlossen: Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden.

- **Symptome:** Bei der Kopieraktivität tritt der folgende Fehler auf: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Ursache:** Bei der Zertifikatüberprüfung ist während des TLS-Handshakes ein Fehler aufgetreten.

- **Lösung:** Um dieses Problem zu umgehen, können Sie die Überprüfung von TLS (Transport Layer Security) für Azure Data Lake Storage Gen1 mithilfe der gestagten Kopie überspringen. Sie müssen dieses Problem reproduzieren und die Netzwerküberwachungs-Ablaufverfolgung (netmon) erfassen. Anschließend prüfen Sie zusammen mit dem Netzwerkteam die Konfiguration des lokalen Netzwerks.

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="Abbildung: Azure Data Lake Storage Gen1-Verbindungen zum Beheben von Problemen.":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fehlermeldung: The remote server returned an error: (403) Unzulässig

- **Symptome:** Für die Kopieraktivität tritt der folgende Fehler auf: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Ursache:** Eine mögliche Ursache ist, dass der von Ihnen verwendete Dienstprinzipal oder die verwaltete Identität nicht über die Berechtigung zum Zugreifen auf bestimmte Ordner oder Dateien verfügt.

- **Lösung:** Gewähren Sie die entsprechenden Berechtigungen für alle Ordner und Unterordner, die Sie kopieren müssen. Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fehlermeldung: Fehler beim Abrufen des Zugriffstokens mit dem Dienstprinzipal. ADAL-Fehler: service_unavailable

- **Symptome:** Bei der Kopieraktivität tritt der folgende Fehler auf:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Ursache:** Wenn der Tokenserver des Diensts von Azure Active Directory nicht verfügbar ist (also die Auslastung für die Verarbeitung von Anforderungen zu hoch ist), wird der HTTP-Fehler 503 zurückgegeben. 

- **Lösung:** Führen Sie die Kopieraktivität nach einigen Minuten erneut aus.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Fehlercode: ADLSGen2OperationFailed

- **Meldung**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

  | Ursachenanalyse                                               | Empfehlung                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Wenn Azure Data Lake Storage Gen2 diesen Fehler auslöst, ist für einen Vorgang ein Fehler aufgetreten.| Überprüfen Sie die von Azure Data Lake Storage Gen2 ausgegebene detaillierte Fehlermeldung. Wenn der Fehler ein vorübergehender Fehler ist, wiederholen Sie den Vorgang. Um weitere Hilfe zu erhalten, wenden Sie sich an den Azure Storage-Support, und geben Sie die Anforderungs-ID in der Fehlermeldung an. |
  | Wenn die Fehlermeldung die Zeichenfolge „Forbidden“ (Verboten) enthält, verfügt der von Ihnen verwendete Dienstprinzipal oder die verwaltete Identität möglicherweise nicht über die erforderliche Berechtigung, um auf Azure Data Lake Storage Gen2 zuzugreifen. | Informationen zum Behandeln dieses Fehlers finden Sie unter [Kopieren und Transformieren von Daten in Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Wenn die Fehlermeldung die Zeichenfolge „InternalServerError“ enthält, wird der Fehler von Azure Data Lake Storage Gen2 zurückgegeben. | Dieser Fehler wird möglicherweise durch einen vorübergehenden Fehler verursacht. Wenn dies so ist, wiederholen Sie den Vorgang. Wenden Sie sich an den Azure Storage-Support, und geben Sie die Anforderungs-ID aus der Fehlermeldung an, wenn das Problem weiterhin besteht. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Timeoutfehler bei der Anforderung des Azure Data Lake Storage Gen2-Kontos

- **Nachricht**: 
  * Fehlercode = `UserErrorFailedBlobFSOperation`
  * Fehlermeldung = `BlobFS operation failed for: A task was canceled.`

- **Ursache:** Das Problem wird durch den Timeoutfehler der Azure Data Lake Storage Gen2-Senke verursacht, der in der Regel auf dem selbstgehosteten IR-Computer (Integration Runtime) auftritt.

- **Empfehlung**: 

    - Platzieren Sie den Computer mit der selbstgehosteten IR und das Azure Data Lake Storage Gen2-Zielkonto nach Möglichkeit in derselben Region. Dadurch vermeiden Sie einen zufälligen Timeoutfehler und erzielen eine bessere Leistung.

    - Überprüfen Sie, ob spezielle Netzwerkeinstellungen wie ExpressRoute vorliegen, und stellen Sie sicher, dass das Netzwerk über genügend Bandbreite verfügt. Es wird empfohlen, die Einstellung für gleichzeitige Aufträge der selbstgehosteten IR zu verringern, wenn die Gesamtbandbreite gering ist. Dies kann dazu beitragen, den Wettbewerb von Netzwerkressourcen über mehrere gleichzeitige Aufträge hinweg zu vermeiden.

    - Wenn die Datei mittelgroß oder klein ist, verwenden Sie für nicht binäre Kopiervorgänge eine kleinere Blockgröße, um einen derartigen Timeoutfehler zu vermeiden. Weitere Informationen finden Sie unter [Blob Storage: Put-Block](/rest/api/storageservices/put-block).

       Um die benutzerdefinierte Blockgröße anzugeben, bearbeiten Sie die Eigenschaft im JSON-Datei-Editor wie hier gezeigt:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
