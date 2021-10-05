---
title: Azure Media Services-Fehlercodes für Liveereignisse
description: In diesem Artikel werden Fehlercodes für Liveereignisse aufgelistet.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: bd8a980d6f8cf7f71dbbfb5f099a12ca966be9ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661638"
---
# <a name="media-services-live-event-error-codes"></a>Azure Media Services-Fehlercodes für Liveereignisse

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In den folgenden Tabellen sind die [Liveereignis](live-event-outputs-concept.md)-Fehlercodes aufgeführt.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Wenn Sie die [Event Grid](../../event-grid/index.yml)-Ereignisse für ein Liveereignis abonnieren, wird möglicherweise einer der folgenden Fehler aus dem [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected)-Ereignis ausgegeben.
> [!div class="mx-tdCol2BreakAll"]
>| Fehler | Information |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|BESCHREIBUNG | Falsche Erfassungs-URL |
>|Vorgeschlagene Lösung| APPID ist ein GUID-Token in der RTMP-Erfassungs-URL. Stellen Sie sicher, dass sie mit der Erfassungs-URL aus der API übereinstimmt. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| BESCHREIBUNG |Die Encoder-IP-Adresse ist nicht in der konfigurierten IP-Positivliste vorhanden |
>| Vorgeschlagene Lösung| Stellen Sie sicher, dass die IP-Adresse des Encoders in der IP-Positivliste enthalten ist. Verwenden Sie ein Onlinetool wie *whoismyip* oder den *CIDR-Rechner*, um den richtigen Wert festzulegen.  Stellen Sie sicher, dass der Encoder den Server vor dem eigentlichen Liveereignis erreichen kann. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| BESCHREIBUNG|Der RTMP-Encoder hat den Befehl `setDataFrame` nicht gesendet. |
>| Vorgeschlagene Lösung|Die meisten kommerziellen Encoder senden Datenstrommetadaten. Für einen Encoder, der eine Erfassung mit Einzelbitrate pusht, ist dies möglicherweise kein Problem. Das LiveEvent ist in der Lage, die eingehende Bitrate zu berechnen, wenn die Datenstrommetadaten fehlen.  Bei der Erfassung mit Mehrfachbitrate für einen PassThru-Kanal oder ein Szenario mit doppeltem Pushvorgang können Sie versuchen, in der Erfassungs-URL „videodatarate“ und „audiodatarate“ an die Abfragezeichenfolge anzufügen. Der ungefähre Wert kann funktionieren. Die Einheit ist KBit. Beispiel: `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| BESCHREIBUNG|Der angegebene Codec wird nicht unterstützt.|
>| Vorgeschlagene Lösung| Das LiveEvent hat einen nicht unterstützten Codec empfangen. Für eine RTMP-Erfassung hat das LiveEvent z. B. einen Nicht-AVC-Videocodec empfangen.  Überprüfen Sie die Encoder-Voreinstellung. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| BESCHREIBUNG |Die Medienbeschreibungsinformationen wurden nicht empfangen, bevor die eigentlichen Mediendaten übermittelt wurden. |
>| Vorgeschlagene Lösung|Das LiveEvent erhält die Datenstrombeschreibung (Header oder FLV-Tag) nicht vom Encoder. Dies ist ein Protokollverstoß. Wenden Sie sich an den Anbieter des Encoders. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| BESCHREIBUNG| Die Anzahl von Qualitätsangaben für Audio und Video hat die maximal zulässige Anzahl überschritten. Qualitätsstufen, die den Grenzwert überschreiten, wurden vom Dienst ignoriert.|
>| Vorgeschlagene Lösung|Wenn der Modus „Liveereignis“ auf „Live Encoding“ festgelegt ist, sollte der Encoder eine Einzelbitrate für Video und Audio pushen.  Beachten Sie, dass ein redundanter Pushvorgang mit derselben Bitrate zulässig ist. Überprüfen Sie die Voreinstellung des Encoders oder die Ausgabeeinstellungen, um sicherzustellen, dass er einen Datenstrom mit einer Einzelbitrate ausgibt. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| BESCHREIBUNG|Die Gesamtbitrate (eingehend) in einem Liveereignis oder Kanaldienst hat den maximal zulässigen Wert überschritten. |
>| Vorgeschlagene Lösung|Der Encoder hat die maximale eingehende Bitrate überschritten. Dieser Grenzwert aggregiert alle eingehenden Daten des beitragenden Encoders. Prüfen Sie die Encoder-Voreinstellung oder die Ausgabeeinstellungen, um die Bitrate zu reduzieren. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| BESCHREIBUNG|Der Zeitstempel für das Video- oder Audio-FLVTag vom RTMP-Encoder ist ungültig. |
>| Vorgeschlagene Lösung|Veraltet. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| BESCHREIBUNG|Die eingehenden, vom Encoder erfassten Datenströme mit Frameraten haben den maximal zulässigen Wert von 30 fps für die Codierung von Liveereignissen/Kanälen überschritten. |
>| Vorgeschlagene Lösung|Prüfen Sie die Encoder-Voreinstellung, um die Framerate auf unter 36 fps zu senken. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| BESCHREIBUNG|Die eingehenden, vom Encoder erfassten Streams haben die folgenden zulässigen Auflösungen überschritten: 1920 x 1088 für die Codierung von Liveereignissen/Kanälen und 4096 x 2160 für Pass-Through-Liveereignisse/-Kanäle. |
>| Vorgeschlagene Lösung|Prüfen Sie die Voreinstellung des Encoders, um die Videoauflösung zu verringern, damit sie den Grenzwert nicht überschreitet. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| BESCHREIBUNG|Das Liveereignis hat eine große Menge von Audiodaten gleichzeitig oder eine große Menge von Videodaten ohne Keyframes erhalten. Wir haben den Encoder getrennt, um ihm die Möglichkeit zu geben, einen Wiederholungsversuch mit korrekten Daten auszuführen. |
>| Vorgeschlagene Lösung|Stellen Sie sicher, dass der Encoder für jedes Keyframeintervall (GOP) einen Keyframe sendet.  Aktivieren Sie Einstellungen wie „Constant bitrate (CBR)“ (Konstante Bitrate) oder „Align Key Frames“ (Keyframes ausrichten). Manchmal kann das Zurücksetzen des beitragenden Encoders helfen. Wenn dies nicht der Fall ist, wenden Sie sich an den Anbieter des Encoders. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Möglicherweise wird einer der folgenden Fehler aus dem [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected)-Ereignis ausgegeben.

> [!div class="mx-tdCol2BreakAll"]
>| Fehler | Information |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| BESCHREIBUNG|RTMP-Sitzungstimeout nach einem Leerlauf mit dem zulässigen Zeitlimit. |
>|Vorgeschlagene Lösung|Dies tritt in der Regel auf, wenn ein Encoder den Eingabefeed nicht mehr empfängt, sodass die Sitzung im Leerlauf ist, da keine Daten für die Ausgabe vorliegen. Prüfen Sie, ob der Status des Encoders oder der Status des Eingabefeeds einen fehlerfreien Zustand aufweist. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|BESCHREIBUNG| Der Zeitstempel für das Video- oder Audio-FLVTag vom RTMP-Encoder ist ungültig. |
>| Vorgeschlagene Lösung| Veraltet. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| BESCHREIBUNG|Der Encoder sendet die Daten zu schnell. |
>| Vorgeschlagene Lösung|Dies geschieht, wenn der Encoder eine große Anzahl von Fragmenten in einer kurzen Zeitspanne ausgibt.  Dies kann theoretisch vorkommen, wenn der Encoder aufgrund eines Netzwerkproblems eine Zeit lang keine Daten übertragen kann und die Daten sehr schnell ausgibt, sobald das Netzwerk verfügbar ist. Suchen Sie die Ursache im Encoder- oder Systemprotokoll. |
>|**Unbekannte Fehlercodes** |
>| BESCHREIBUNG| Diese Fehlercodes können von zu wenig Arbeitsspeicher bis zu doppelten Einträgen in der Hashzuordnung reichen. Dies kann geschehen, wenn der Encoder eine große Anzahl von Fragmenten in einer kurzen Zeitspanne sendet.  Dies kann auch vorkommen, wenn der Encoder aufgrund eines Netzwerkproblems eine Zeit lang keine Daten per Push übertragen kann und dann alle verzögerten Fragmente auf einmal sendet, sobald das Netzwerk wieder verfügbar ist. |
>|Vorgeschlagene Lösung| Überprüfen Sie die Encoder-Protokolle.|

## <a name="other-error-codes"></a>Sonstige Fehlercodes

> [!div class="mx-tdCol2BreakAll"]
>| Fehler | Information |Abgelehnt/Getrennt-Ereignis|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Ja|
>| BESCHREIBUNG|Dies ist ein allgemeiner Fehler. ||
>|Vorgeschlagene Lösung| Keine.||
>|**MPI_SYSTEM_MAINTENANCE** ||Ja|
>| BESCHREIBUNG|Die Verbindung mit dem Encoder wurde aufgrund eines Dienstupdates oder einer Systemwartung getrennt. ||
>|Vorgeschlagene Lösung|Vergewissern Sie sich, dass der Encoder „Auto Connect“ (automatische Verbindung) aktiviert hat. Dadurch kann der Encoder erneut eine Verbindung mit dem redundanten Liveereignisendpunkt herstellen, der sich nicht im Wartungsmodus befindet. ||
>|**MPE_BAD_URL_SYNTAX** ||Ja|
>| BESCHREIBUNG|Die Erfassungs-URL ist falsch formatiert. ||
>|Vorgeschlagene Lösung|Stellen Sie sicher, dass die Erfassungs-URL ordnungsgemäß formatiert ist. Für RTMP sollte sie `rtmp[s]://hostname:port/live/GUID_APPID/streamname` lauten. ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Ja|
>| BESCHREIBUNG|Der Encoder hat die Verbindung der Sitzung getrennt.  ||
>|Vorgeschlagene Lösung|Dies ist kein Fehler. Die Trennung der Verbindung wurde vom Encoder initiiert, einschließlich der „geordneten Trennung der Verbindung“. Wenn es sich um eine unerwartete Trennung der Verbindung handelt, überprüfen Sie die Encoder-Protokolle. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Nein|
>| BESCHREIBUNG|Die eingehende Datenrate stimmt nicht mit der erwarteten Bitrate überein. ||
>|Vorgeschlagene Lösung|Dies ist eine Warnung, die auftritt, wenn die eingehende Datenrate zu langsam oder zu schnell ist. Überprüfen Sie das Encoder- oder Systemprotokoll.||
>|**MPE_INGEST_DISCONTINUITY** ||Nein|
>| BESCHREIBUNG| Es gibt eine Unterbrechung bei den eingehenden Daten.||
>|Vorgeschlagene Lösung| Dies ist eine Warnung, dass der Encoder Daten aufgrund eines Netzwerkproblems oder eines Problems mit den Systemressourcen verwirft. Überprüfen Sie das Encoder- oder Systemprotokoll. Überwachen Sie auch die Systemressourcen (CPU, Arbeitsspeicher oder Netzwerk). Wenn der Wert der System-CPU zu hoch ist, versuchen Sie, die Bitrate zu senken oder die Option für den Hardware-Encoder der Systemgrafikkarte zu verwenden.||

## <a name="see-also"></a>Weitere Informationen

[Fehlercodes für den Streamingendpunkt (Ursprung)](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Livestreaming mit Media Services](stream-live-tutorial-with-api.md)
