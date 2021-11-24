---
title: Azure Communication Services - Anrufprotokolle Vorschau
titleSuffix: An Azure Communication Services concept document
description: Informationen zur Anrufzusammenfassung und zu Anrufdiagnoseprotokollen in Azure Monitor
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 15921a8666d6c55c90ed6d751f60a555c9c06d81
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138847"
---
# <a name="call-summary-and-call-diagnostic-logs-preview"></a>Vorschau der Anrufzusammenfassung und Anrufdiagnoseprotokolle

> [!IMPORTANT]
> Die folgenden Ausführungen beziehen sich auf Protokolle, die über [Azure Monitor](/azure/azure-monitor/overview) (siehe auch [FAQ](/azure/azure-monitor/faq)). Um diese Protokolle für Ihre Kommunikationsdienste zu aktivieren, siehe: [Protokollierung in den Diagnoseeinstellungen aktivieren](./enable-logging.md)


## <a name="data-concepts"></a>Datenkonzepte
Nachfolgend finden Sie ausführliche Beschreibungen von Datenkonzepten, die speziell für Voice und Videoanrufe in Ihren Kommunikationsdiensten gelten und wichtig sind, um die Bedeutung der in den Protokollen erfassten Daten zu verstehen.

### <a name="entities-and-ids"></a>Entitäten und IDs

Ein *Anruf*, der sich auf die in den Daten dargestellten Entitäten bezieht, ist eine Abstraktion, die durch die `correlationId` dargestellt wird. `CorrelationId`-Instanzen sind pro Anruf eindeutig und durch `callStartTime` und `callDuration` zeitgebunden. Jeder Anruf ist ein Ereignis, das Daten von zwei oder mehr *Endpunkten* enthält, die die verschiedenen menschlichen, Bot- oder Serverteilnehmer des Anrufs darstellen.

Ein *Teilnehmer* (`participantId`) ist nur vorhanden, wenn es sich um einen *Gruppenanruf* handelt, da er die Verbindung zwischen einem Endpunkt und dem Server darstellt. 

Ein *Endpunkt* ist die eindeutigste Einheit, dargestellt durch `endpointId`. `EndpointType` gibt an, ob es sich bei dem Endpunkt um einen menschlichen Benutzer (PSTN, VoIP), einen Bot oder den Server handelt, der mehrere Teilnehmer innerhalb eines Anrufs verwaltet. Wenn ein `endpointType` dem `"Server"` entspricht, wird dem Endpunkt keine eindeutige ID zugewiesen. Anhand von `endpointType` und der Anzahl der `endpointId`-Instanzen können Sie jederzeit feststellen, wie viele Benutzer und andere nicht-menschliche Teilnehmer (Bots, Server) an dem Anruf beteiligt sind. Native SDKs (wie das Android Calling SDK) verwenden dieselbe `endpointId` für einen Benutzer über mehrere Anrufe hinweg und ermöglichen so ein sitzungsübergreifendes Verständnis der Erfahrung. Dies unterscheidet sich von webbasierten Endpunkten, die bei jedem neuen Anruf immer eine neue `endpointId` generieren.

Ein *Datenstrom* ist die differenzierteste Einheit, denn es gibt einen Datenstrom pro Richtung (eingehend/ausgehend) und `mediaType` (z. B. Audio, Video).  

### <a name="p2p-vs-group-calls"></a>P2P und Gruppenanrufe

Es gibt zwei Arten von Anrufen (dargestellt durch `callType`): P2P und Gruppe. 

**P2P**-Anrufe sind eine Verbindung zwischen zwei einzelnen Endpunkten, ohne Serverendpunkt. P2P-Anrufe werden als Anruf zwischen diesen Endpunkten initiiert und werden nicht als Gruppenanrufereignis vor dem Herstellen der Verbindung erstellt.

  :::image type="content" source="media\call-logs-azure-monitor\p2p-diagram.png" alt-text="P2P-Anruf":::


Zu den **Gruppenanrufen** gehören alle Anrufe, die im Voraus als Besprechung/Kalenderereignis erstellt wurden, und alle Anrufe, bei denen mehr als zwei Endpunkte verbunden sind. Gruppenanrufe umfassen einen Serverendpunkt, und die Verbindung zwischen jedem Endpunkt und dem Server stellt einen Teilnehmer dar. P2P-Anrufe, bei denen während des Anrufs ein zusätzlicher Endpunkt hinzugefügt wird, sind keine P2P-Anrufe mehr, sondern werden zu Gruppenanrufen. Anhand von `participantStartTime` und `participantDuration` lässt sich der Zeitpunkt ermitteln, zu dem die einzelnen Endpunkte dem Anruf beigetreten sind.


  :::image type="content" source="media\call-logs-azure-monitor\group-call-version-a.png" alt-text="Gruppenanruf":::

## <a name="log-structure"></a>Protokollstruktur
Es werden zwei Arten von Protokollen erstellt: **Anrufzusammenfassungsprotokolle** und **Anrufdiagnoseprotokolle**. 

Anrufzusammenfassungsprotokolle enthalten grundlegende Informationen über den Anruf, einschließlich aller relevanten IDs, Zeitstempel, Endpunkt- und SDK-Informationen. Für jeden Endpunkt innerhalb eines Anrufs (den Server nicht mitgerechnet) wird ein eigenes Anrufzusammenfassungsprotokoll erstellt.

Anrufdiagnoseprotokolle enthalten Informationen über den Datenstrom sowie eine Reihe von Metriken, die Messungen der Qualität der Benutzerfreundlichkeit anzeigen. Für jeden Endpunkt innerhalb eines Anrufs (einschließlich des Servers) wird ein eigenes Anrufdiagnoseprotokoll für jeden Datenstrom (Audio, Video usw.) zwischen den Endpunkten erstellt. Bei einem P2P-Anruf enthält jedes Protokoll Daten, die sich auf jeden der ausgehenden Datenströme beziehen, die den einzelnen Endpunkten zugeordnet sind. Bei einem Gruppenanruf erstellt jeder Datenstrom, der `endpointType`= `"Server"` zugeordnet ist, ein Protokoll, das Daten für die eingehenden Datenströme enthält, und alle anderen Datenströme erstellen Protokolle, die Daten für die ausgehenden Datenströme für alle Nicht-Serverendpunkte enthalten. Bei Gruppenanrufen verwenden Sie `participantId` als Schlüssel, um die zugehörigen ein-/ausgehenden Protokolle zu einer eigenen Teilnehmerverbindung zusammenzufassen.

### <a name="example-1-p2p-call"></a>Beispiel 1: P2P-Anruf

Das folgende Diagramm zeigt zwei Endpunkte, die direkt in einem P2P-Anruf verbunden sind. In diesem Beispiel würden zwei Anrufzusammenfassungsprotokolle (einer pro `endpointId`) und vier Anrufdiagnoseprotokolle (einer pro Mediendatenstrom) erstellt werden. Jedes Protokoll enthält Daten, die sich auf den ausgehenden Datenstrom der `endpointId` beziehen.

:::image type="content" source="media\call-logs-azure-monitor\example-1-p2p-call.png" alt-text="Datenströme des P2P-Anrufs":::


### <a name="example-2-group-call"></a>Beispiel 2: Gruppenanruf

Das folgende Diagramm zeigt ein Beispiel für einen Gruppenanruf mit drei `particpantIds`, d. h. drei `endpointIds` (`endpointIds` kann potenziell bei mehreren Teilnehmern auftreten, z. B. wenn ein Anruf von demselben Gerät wieder aufgenommen wird) und einem Serverendpunkt. Für `participantId` 1 würden zwei Anrufzusammenfassungsprotokolle erstellt: eines für die `endpointId` und ein weiteres für den Server. Es würden vier Anrufdiagnoseprotokolle in Bezug auf `participantId` 1 erstellt werden, eines für jeden Mediendatenstrom. Die drei Protokolle mit `endpointId` 1 würden Daten enthalten, die sich auf die ausgehenden Mediendatenströme beziehen, und das eine Protokoll mit `endpointId = null, endpointType = "Server"` würde Daten enthalten, die sich auf den eingehenden Datenstrom beziehen.

:::image type="content" source="media\call-logs-azure-monitor\example-2-group-call-version-a.png" alt-text="Details zum Gruppenanruf":::

## <a name="data-definitions"></a>Datendefinitionen

### <a name="call-summary-log"></a>Anrufzusammenfassungsprotokoll
Das Anrufzusammenfassungsprotokoll enthält Daten, mit denen Sie wichtige Eigenschaften aller Anrufe identifizieren können. Für jede `participantId` (`endpointId` bei P2P-Anrufen) im Anruf wird ein eigenes Anrufzusammenfassungsprotokoll erstellt.

|     Eigenschaft                  |     BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     time                      |     Der Zeitstempel (UTC), wann das Protokoll generiert wurde.                                                                                                                                                                                                                                                                                                                                                                                                                       |
|     operationName             |     Der mit der Protokollaufzeichnung verbundene Vorgang.                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     operationVersion          |     Die API-Version, die dem Vorgang zugeordnet ist, wenn für `operationName` eine API verwendet wurde. Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern.                                                                                                                                                                           |
|     category                  |     Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im `properties`-Blob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich.                                                                                                                                                                                                    |
|     correlationIdentifier     |    `correlationIdentifier` ist die eindeutige ID für einen Anruf. Der `correlationIdentifier` identifiziert korrelierte Ereignisse von allen Teilnehmern und Endpunkten, die während eines einzelnen Anrufs eine Verbindung herstellen, und kann verwendet werden, um Daten aus verschiedenen Protokollen zusammenzuführen.  Wenn Sie jemals eine Supportanfrage bei Microsoft öffnen müssen, wird die `correlationID` verwendet, um den Anruf, den Sie beheben möchten, leicht zu identifizieren.                                                                                                                                                                      |
|     Bezeichner                |     Dies ist die eindeutige ID des Benutzers, die mit der vom Kommunikationsauthentifizierungsdienst zugewiesenen Identität übereinstimmt. Sie können diese ID verwenden, um Benutzerereignisse in verschiedenen Protokollen zu korrelieren. Diese ID kann auch zur Identifizierung von Microsoft Teams "Interoperabilitäts"-Szenarien verwendet werden, die später in diesem Dokument beschrieben werden.                                                                                                                                                                                                                                                                                                                                                               |
|     callStartTime             |     Ein Zeitstempel für den Beginn des Anrufs, basierend auf dem ersten Verbindungsversuch von einem beliebigen Endpunkt.                                                                                                                                                                                                                                                                                                                                                                   |
|     callDuration              |     Die Dauer des Anrufs in Sekunden basierend auf dem ersten Verbindungsversuch und dem Ende der letzten Verbindung zwischen zwei Endpunkten.                                                                                                                                                                                                                                                                                                                         |
|     callType                  |     Enthält entweder `"P2P"` oder `"Group"`. Ein `"P2P"`-Anruf ist eine direkte 1:1-Verbindung zwischen nur zwei Endpunkten, die keine Server sind. Ein `"Group"`-Anruf ist ein Anruf mit mehr als zwei Endpunkten oder ein Anruf, der als `"Group"`-Anruf vor dem Herstellen der Verbindung erstellt wurde.                                                                                                                                                                                                                                 |
|     teamsThreadId             |     Diese ID ist nur relevant, wenn der Anruf als Microsoft Teams-Besprechung organisiert wird, was dem Anwendungsfall der Interoperabilität zwischen Microsoft Teams und Azure Communication Services entspricht. Diese ID wird in den Betriebsprotokollen angezeigt. Sie können diese ID auch über die Chat-APIs abrufen.                                                                                                                                                                                              |
|     participantID             |     Diese ID wird generiert, um die bidirektionale Verbindung zwischen einem `"Participant"`-Endpunkt (`endpointType` = `"Server"`) und dem Server darzustellen. Für `callType` = `"P2P"` besteht eine direkte Verbindung zwischen zwei Endpunkten und es wird keine `participantId` generiert.                                                                                                                                                                                                               |
|     participantStartTime      |     Zeitstempel für den Beginn des ersten Versuchs zur Herstellung einer Verbindung durch den Teilnehmer.                                                                                                                                                                                                                                                                                                                                                                                            |
|     participantDuration       |     Die Dauer jeder Teilnehmerverbindung in Sekunden, von `participantStartTime` bis zum Zeitstempel, an dem die Verbindung beendet wird.                                                                                                                                                                                                                                                                                                                                             |
|     participantEndReason      |     Enthält die vom SDK ausgegebenen Calling SDK-Fehlercodes, wenn sie für die einzelnen `participantId`-Instanzen relevant sind. Weitere Informationen finden Sie unten unter „Calling SDK-Fehlercodes“.                                                                                                                                                                                                                                                                                                                          |
|     endpointId                |     Eindeutige ID, die jeden mit dem Anruf verbundenen Endpunkt darstellt, wobei der Endpunkttyp durch `endpointType` definiert ist. Wenn der Wert `null` ist, ist die verbundene Entität der Communication Services-Server (`endpointType`= `"Server"`). Bei nativen Clients kann `EndpointId` manchmal für denselben Benutzer über mehrere Anrufe (`correlationIdentifier`) hinweg bestehen bleiben. Die Anzahl der `endpointId`-Instanzen bestimmt die Anzahl der Anrufzusammenfassungsprotokolle. Für jede `endpointId` wird ein eigenes Zusammenfassungsprotokoll erstellt.    |
|     endpointType              |     Dieser Wert beschreibt die Eigenschaften der einzelnen mit dem Anruf verbundenen Endpunkte. Kann `"Server"`, `"VOIP"`, `"PSTN"`, `"BOT"` oder `"Unknown"` enthalten.                                                                                                                                                                                                                                                                                                                               |
|     sdkVersion                |     Versionszeichenfolge für die von jedem relevanten Endpunkt verwendete Version des Communication Services Calling SDK. (Beispiel: `"1.1.00.20212500"`)                                                                                                                                                                                                                                                                                                                                                          |
|     osVersion                 |     Eine Zeichenfolge, die das Betriebssystem und die Version der einzelnen Endpunktgeräte darstellt.                                                                                                                                                                                                                                                                                                                                                                                       |


### <a name="call-diagnostic-log"></a>Anrufdiagnoseprotokoll
Anrufdiagnoseprotokolle stellen wichtige Informationen über die Endpunkte und die Medienübertragungen für die einzelnen Teilnehmer sowie Messungen bereit, die helfen, Qualitätsprobleme zu verstehen. 

|     Eigenschaft              |     BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     operationName         |     Der mit der Protokollaufzeichnung verbundene Vorgang.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     operationVersion      |     Die `api-version`, die dem Vorgang zugeordnet ist, wenn für `operationName` eine API verwendet wurde. Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern.                                                                                                                                                                                                                                                                                                                                                                  |
|     category              |     Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im `properties`-Blob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich.                                                                                                                                                                                                                                                                                                                                                                                         |
|     correlationIdentifier     |     Der `correlationIdentifier` kennzeichnet korrelierte Ereignisse von allen Teilnehmern und Endpunkten, die während eines einzelnen Anrufs eine Verbindung herstellen. `correlationIdentifier` ist die eindeutige ID für einen Anruf. Wenn Sie jemals eine Supportanfrage bei Microsoft öffnen müssen, wird die `correlationID` verwendet, um den Anruf, den Sie beheben möchten, leicht zu identifizieren.                                                                                                                                                                                                                                                                                                                                                                         |
|     participantID         |     Diese ID wird generiert, um die bidirektionale Verbindung zwischen einem „Teilnehmer“-Endpunkt (`endpointType` =  `“Server”`) und dem Server darzustellen. Für `callType`   = `"P2P"` besteht eine direkte Verbindung zwischen zwei Endpunkten und es wird keine `participantId` generiert.                                                                                                                                                                                                                                                                                                                                                                                                |
|     Bezeichner            |     Diese ID stellt die Benutzeridentität dar, wie sie vom Authentifizierungsdienst definiert wurde. Verwenden Sie diese ID, um verschiedene Ereignisse über Anrufe und Dienste hinweg zu korrelieren.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     endpointId            |     Eindeutige ID, die jeden mit dem Anruf verbundenen Endpunkt darstellt, wobei der Endpunkttyp durch `endpointType` definiert ist.   Wenn der Wert `null` ist, bedeutet dies, dass die verbundene Entität der Communication Services-Server ist.  `EndpointId` kann für denselben Benutzer über mehrere Anrufe (`correlationIdentifier`) hinweg bestehen bleiben, wenn es sich um einen nativen Client handelt, ist aber für jeden Anruf eindeutig, wenn es sich bei dem Client um einen Webbrowser handelt.                                                                                                                                                                                                                                                                                  |
|     endpointType          |     Dieser Wert beschreibt die Eigenschaften der einzelnen `endpointId`. Kann `“Server”`, `“VOIP”`, `“PSTN”`, `“BOT”` oder `“Unknown”` enthalten.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     mediaType             |     Dieser Zeichenfolgenwert beschreibt die Art der Medien, die zwischen den Endpunkten innerhalb der einzelnen Datenströme übertragen werden. Mögliche Werte sind `“Audio”`, `“Video”`, `“VBSS”` (videobasierte Bildschirmfreigabe) und `“AppSharing”`.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     streamId              |     Nicht eindeutige ganze Zahl, die zusammen mit `mediaType` zur eindeutigen Identifizierung von Datenströmen mit derselben `participantId` verwendet werden kann.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     transportType         |     Ein Zeichenfolgenwert, der das Netzwerktransportprotokoll pro `participantId` beschreibt. Kann `"UDP”`, `“TCP”` oder `“Unrecognized”` enthalten. `"Unrecognized"` zeigt an, dass das System nicht feststellen konnte, ob es sich bei `transportType` um TCP oder UDP handelt.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     roundTripTimeAvg      |     Dies ist die durchschnittliche Zeit, die benötigt wird, um ein IP-Paket innerhalb einer `participantDuration`-Instanz von einem Endpunkt zu einem anderen zu übertragen. Diese Verzögerung bei der Netzwerkweitergabe ist im Wesentlichen an die physische Entfernung zwischen den beiden Punkten und die Lichtgeschwindigkeit gebunden, einschließlich des zusätzlichen Aufwands, der von den verschiedenen dazwischenliegenden Routern verursacht wird. Die Latenz (Wartezeit) wird für die unidirektionale Strecke bzw. für die Roundtripdauer (Round Trip Time, RTT) gemessen.  Der Wert wird in Millisekunden angegeben, und eine RTT von mehr als 500 ms sollte als negative Auswirkung auf die Anrufqualität betrachtet werden.                                                                                                                                                                      |
|     roundTripTimeMax      |     Die maximale RTT (ms), die pro Mediendatenstrom während einer `participantDuration` in einem Gruppenanruf oder `callDuration` in einem P2P-Anruf gemessen wird.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     jitterAvg             |     Hierbei geht es um die durchschnittliche Änderung der Verzögerung zwischen aufeinanderfolgenden Paketen. In Azure Communication Services kann ein gewisser Grad an Jitter durch eine Pufferung ausgeglichen werden. Erst wenn der Jitter die Pufferung übersteigt, was ungefähr bei `jitterAvg` >30 ms der Fall ist, ist eine negative Auswirkung auf die Qualität wahrscheinlich. Die Pakete, die mit unterschiedlicher Geschwindigkeit eintreffen, bewirken, dass die Stimme des Sprechers roboterhaft klingt. Dies wird pro Mediendatenstrom über die `participantDuration` bei einem Gruppenanruf oder `callDuration` bei einem P2P-Anruf gemessen.                                                                                                                                              |
|     jitterMax             |     Dies ist der maximale Jitterwert, der zwischen Paketen pro Mediendatenstrom gemessen wird. Bursts bei Netzwerkbedingungen können Probleme im Audio-/Videodatenverkehr verursachen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|     packetLossRateAvg     |     Dies ist der durchschnittliche Prozentsatz an Paketen, die verloren gehen. Paketverluste wirken sich direkt auf die Audioqualität aus – von kleinen, einzelnen verlorenen Paketen, die fast keine Auswirkungen haben, bis hin zu aufeinanderfolgenden Burstverlusten, die zu vollständigen Audioausfällen führen. Die Pakete, die verworfen werden und nicht an ihrem Ziel ankommen, verursachen Lücken in den Medien, was zu verpassten Silben und Wörtern sowie abgehackten Videos und Freigaben führt. Eine Paketverlustrate von mehr als 10 % (0,1) sollte als eine Rate betrachtet werden, die wahrscheinlich einen negativen Einfluss auf die Qualität hat. Dies wird pro Mediendatenstrom über die `participantDuration` bei einem Gruppenanruf oder `callDuration` bei einem P2P-Anruf gemessen.    |
|     packetLossRateMax     |     Dieser Wert stellt die maximale Paketverlustrate (%) pro Mediendatenstrom über die `participantDuration` bei einem Gruppenanruf oder `callDuration` bei einem P2P-Anruf dar. Bursts bei Netzwerkbedingungen können Probleme im Audio-/Videodatenverkehr verursachen.                                                                                                                                                                                                                                                                                                                                                                                                                  |
|                           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### <a name="error-codes"></a>Fehlercodes
`participantEndReason` enthält einen Wert aus der Reihe der Calling SDK-Fehlercodes. Sie können sich auf diese Codes beziehen, um Probleme während des Anrufs pro Endpunkt zu beheben.

|     Fehlercode                 |     BESCHREIBUNG                                                                                                       |     Auszuführende Aktion                                                                                                                                                                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     0                          |     Erfolgreich                                                                                                           |     Der Anruf (P2P) oder Teilnehmer (Gruppe) wurde ordnungsgemäß beendet.                                                                                                                                                                                                                    |
|     403                        |     Unzulässig/Authentifizierungsfehler.                                                                               |     Stellen Sie sicher, dass Ihr Communication Services-Token gültig und nicht abgelaufen ist. Wenn Sie Teams-Interoperabilität verwenden, stellen Sie sicher, dass Ihr Teams-Mandant der Positivliste für den Vorschauzugriff hinzugefügt wurde. Um die Interoperabilität von Teams-Mandanten zu aktivieren/deaktivieren, füllen Sie dieses Formular aus.    |
|     404                        |     Anruf nicht gefunden.                                                                                                   |     Stellen Sie sicher, dass die Telefonnummer, die Sie anrufen (oder die Telefonkonferenz, der Sie beitreten möchten), vorhanden ist.                                                                                                                                                                                                   |
|     408                        |     Timeout des Anrufcontrollers.                                                                                        |     Timeout des Anrufcontrollers beim Warten auf Protokollmeldungen von Benutzerendpunkten. Stellen Sie sicher, dass die Clients verbunden und verfügbar sind.                                                                                                                                                 |
|     410                        |     Fehler des lokalen Medienstapels oder der Medieninfrastruktur.                                                                  |     Stellen Sie sicher, dass Sie das neueste SDK in einer unterstützten Umgebung verwenden.                                                                                                                                                                                                      |
|     430                        |     Nachricht kann nicht an die Clientanwendung übermittelt werden.                                                                  |     Stellen Sie sicher, dass die Clientanwendung ausgeführt wird und verfügbar ist.                                                                                                                                                                                                             |
|     480                        |     Remoteclientendpunkt ist nicht registriert.                                                                            |     Stellen Sie sicher, dass der Remoteendpunkt verfügbar ist.                                                                                                                                                                                                                              |
|     481                        |     Fehler beim Verarbeiten des eingehenden Anrufs.                                                                                   |     Reichen Sie eine Supportanfrage über das Azure-Portal ein.                                                                                                                                                                                                                           |
|     487                        |     Der Anruf wurde abgebrochen, lokal abgelehnt, aufgrund eines Endpunktkonfliktfehlers beendet, oder das Medienangebot konnte nicht generiert werden.    |     Erwartetes Verhalten.                                                                                                                                                                                                                                                         |
|     490, 491, 496, 487, 498    |     Netzwerkprobleme des lokalen Endpunkts.                                                                                    |     Überprüfen Sie Ihr Netzwerk.                                                                                                                                                                                                                                                        |
|     500, 503, 504              |     Communication Services-Infrastrukturfehler.                                                                      |     Reichen Sie eine Supportanfrage über das Azure-Portal ein.                                                                                                                                                                                                                           |
|     603                        |     Anruf global durch Communication Services-Remoteteilnehmer abgelehnt.                                             |     Erwartetes Verhalten.                                                                                                                                                                                                                                                         |
|     Unbekannt                    |     Nicht standardmäßiger Grund für Beendigung (nicht Teil der SIP-Standardcodes).                                                      |                                                                                                                                                                                                                                                                                |

## <a name="call-examples-and-sample-data"></a>Anrufbeispiele und Beispieldaten

### <a name="p2p-call"></a>P2P-Anruf

:::image type="content" source="media\call-logs-azure-monitor\call-examples-and-sample-data-p2p-call.png" alt-text="Beispiel für P2P-Beispielprotokoll":::

Gemeinsame Felder für alle Protokolle des Anrufs:

```json
"time":                     "2021-07-19T18:46:50.188Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "8d1a8374-344d-4502-b54b-ba2d6daaf0ae",
```

#### <a name="call-summary-logs"></a>Anrufzusammenfassungsprotokolle
Die Anrufzusammenfassungsprotokolle enthalten gemeinsame Informationen zu Vorgang und Kategorie:

```json
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",

```
Anrufzusammenfassung für VoIP-Benutzer 1
```json
"properties": {
    "identifier":               "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "callStartTime":            "2021-07-19T17:54:05.113Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",    
    "participantStartTime":     "2021-07-19T17:54:06.758Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.1.0",
    "osVersion":                "Windows 10.0.17763 Arch: x64"
}
```

Anrufzusammenfassung für VoIP-Benutzer 2
```json
"properties": {
    "identifier":               "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "callStartTime":            "2021-07-19T17:54:05.335Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",
    "participantStartTime":     "2021-07-19T17:54:06.335Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.1.0.0",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>Anrufdiagnoseprotokolle
Anrufdiagnoseprotokolle geben Vorgangsinformationen frei:
```json
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
Diagnoseprotokoll für den Audiodatenstrom von VoIP-Endpunkt 1 zu VoIP-Endpunkt 2:
```json
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "82",
    "roundTripTimeMax":     "88",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Diagnoseprotokoll für den Audiodatenstrom von VoIP-Endpunkt 2 zu VoIP-Endpunkt 1:
```json
"properties": {
    "identifier":           "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "participantId":        "null",
    "endpointId":           "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1363841599",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "78",
    "roundTripTimeMax":     "84",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Diagnoseprotokoll für den Videodatenstrom von VoIP-Endpunkt 1 zu VoIP-Endpunkt 2:
```json
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Video",
    "streamId":             "2804",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "103",
    "roundTripTimeMax":     "143",
    "jitterAvg":            "0",
    "jitterMax":            "4",
    "packetLossRateAvg":    "3.146336E-05",
    "packetLossRateMax":    "0.001769911"
}
```
### <a name="group-call"></a>Gruppenanruf
Im folgenden Beispiel befinden sich drei Benutzer in einem Gruppenanruf, zwei sind über VoIP und einer über PSTN (Telefonfestnetz) verbunden. Alle verwenden nur Audio. 

:::image type="content" source="media\call-logs-azure-monitor\call-examples-and-sample-data-group-call.png" alt-text="Beispiel für ein Gruppenanrufbeispielprotokoll":::

Die Daten würden in drei Anrufzusammenfassungsprotokollen und sechs Anrufdiagnoseprotokollen generiert werden.

Gemeinsame Felder für alle Protokolle des Anrufs:
```json
"time":                     "2021-07-05T06:30:06.402Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "341acde7-8aa5-445b-a3da-2ddadca47d22",
```

#### <a name="call-summary-logs"></a>Anrufzusammenfassungsprotokolle
Die Anrufzusammenfassungsprotokolle enthalten gemeinsame Informationen zu Vorgang und Kategorie:
```json
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",
```

Anrufzusammenfassung für VoIP-Endpunkt 1:
```json
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "participantStartTime":     "2021-07-05T06:16:44.235Z",
    "participantDuration":      "82",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Darwin Kernel Version 18.7.0: Mon Nov 9 15:07:15 PST 2020; root:xnu-4903.272.3~3/RELEASE_ARM64_S5L8960X"
}
```
Anrufzusammenfassung für VoIP-Endpunkt 3:
```json
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "participantStartTime":     "2021-07-05T06:16:40.240Z",
    "participantDuration":      "87",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Android 11.0; Manufacturer: Google; Product: redfin; Model: Pixel 5; Hardware: redfin"
}
```
Anrufzusammenfassung für PSTN-Endpunkt 2:
```json
"properties": {
    "identifier":               "null",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "515650f7-8204-4079-ac9d-d8f4bf07b04c",
    "participantStartTime":     "2021-07-05T06:17:10.447Z",
    "participantDuration":      "52",
    "participantEndReason":     "0",
    "endpointId":               "46387150-692a-47be-8c9d-1237efe6c48b",
    "endpointType":             "PSTN",
    "sdkVersion":               "null",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>Anrufdiagnoseprotokolle
Anrufdiagnoseprotokolle geben Vorgangsinformationen frei:
```json
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
Diagnoseprotokoll für den Audiodatenstrom von VoIP-Endpunkt 1 zum Serverendpunkt:
```json
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "14884",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "46",
    "roundTripTimeMax":     "48",
    "jitterAvg":            "0",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Diagnoseprotokoll für den Audiodatenstrom vom Serverendpunkt zu VoIP-Endpunkt 1:
```json
"properties": {
    "identifier":           null,
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           null,
    "endpointType":         "Server",
    "mediaType":            "Audio",
    "streamId":             "2001",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "42",
    "roundTripTimeMax":     "44",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Diagnoseprotokoll für den Audiodatenstrom von VoIP-Endpunkt 3 zum Serverendpunkt:
```json
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "13783",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "2",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Diagnoseprotokoll für den Audiodatenstrom vom Serverendpunkt zu VoIP-Endpunkt 3:
```json
"properties": {
    "identifier":           "null",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           null,
    "endpointType":         "Server"    
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "4",
    "packetLossRateAvg":    "0",
```
