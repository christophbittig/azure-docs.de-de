---
title: Azure Media Services – Signalisierung von zeitgesteuerten Metadaten beim Livestreaming
description: In dieser Spezifikation werden Methoden zur Signalisierung von zeitgesteuerten Metadaten beim Erfassen und Streamen von Daten in Azure Media Services dargelegt. Dies schließt Unterstützung für Signale für generische zeitgesteuerte Metadaten (ID3) sowie SCTE-35-Signalisierung für die Einfügung von Werbeinhalten und Signalisierung für Einblendungsbedingung ein.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: de4c836fd5108a029208ead4ab6e1152f7b3fc1b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166779"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalisieren von zeitgesteuerten Metadaten beim Livestreaming 

Letzte Aktualisierung: 2019-08-22

### <a name="conformance-notation"></a>Konformität der Schlüsselbegriffe

Die Schlüsselwörter "MUSS", "DARF NICHT", "ERFORDERLICH", "SOLL", "SOLL NICHT", "SOLLTE", "SOLLTE NICHT", "EMPFOHLEN", "KANN" und "OPTIONAL" in diesem Dokument sind wie in RFC 2119 zu verstehen.

## <a name="1-introduction"></a>1. Einführung 

Um die Einfügung von Werbeinhalten oder benutzerdefinierten Metadatenereignissen auf Clientplayern zu signalisieren, nutzen Broadcaster oftmals im Video eingebettete Metadaten mit Zeitinformationen. Um diese Szenarien zu ermöglichen, bieten Media Services Unterstützung für den Transport von zeitgesteuerten Metadaten vom Einspeisepunkt des Livestreamingkanals bis zur Clientanwendung.
In dieser Spezifikation werden mehrere Modi allgemein beschrieben, die von Media Services für die Signalisierung von zeitgesteuerten Metadaten beim Livestreaming verwendet werden.

1. [SCTE-35] Signalisierung, die die in [SCTE-35], [SCTE-214-1], [SCTE-214-3] und [RFC8216] beschriebenen Standards erfüllt.

2. [SCTE-35] Signalisierung, die die Legacy [Adobe-Primetime]-Spezifikation für die RTMP-Werbungssignalisierung erfüllt.
   
3. Ein generischer, zeitgesteuerter Metadatensignalisierungsmodus für Nachrichten, die **NICHT** [SCTE-35] sind und [ID3v2] oder andere benutzerdefinierte Schemas enthalten könnten, die vom Anwendungsentwickler definiert werden.

## <a name="11-terms-used"></a>1.1 Terminologie

| Begriff                | Definition                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Werbeunterbrechung            | Ein Ort oder ein Zeitpunkt, an dem ein oder mehrere Werbeeinblendungen zur Präsentation geplant sein können. Identisch mit der Verfügbarkeits- und Platzierungsmöglichkeit.                                                                                                                     |
| Werbungsentscheidungsdienst | Externer Dienst, der entscheidet, welche Werbung(en) und Dauer dem Benutzer angezeigt werden. Die Dienste werden in der Regel von einem Partner bereitgestellt und sprengen den Rahmen dieses Dokuments.                                                                    |
| Position (Cue)                 | Angabe von Zeit und Parametern der bevorstehenden Werbeunterbrechung. Beachten Sie, dass Cues einen ausstehenden Wechsel zu einer Werbeunterbrechung, einen ausstehenden Wechsel zur nächsten Werbung innerhalb einer Werbeunterbrechung sowie einen ausstehenden Wechsel von einer Werbeunterbrechung zurück zum Hauptinhalt anzeigen können.           |
| Packager            | Der „Streamingendpunkt“ der Azure Media Services stellt dynamische Paketerstellungsfunktionen für DASH und HLS bereit und wird in der Medienbranche als „Packager“ bezeichnet.                                                                              |
| Präsentationszeit   | Die Zeit, zu der ein Ereignis einem Benutzer präsentiert wird. Diese Zeit stellt den Moment auf der Medienzeitachse dar, in dem ein Betrachter das Ereignis zu sehen bekommt. Beispielsweise ist die Präsentationszeit für einen SCTE-35-Befehl „splice_info()“ die „splice_time()“. |
| Ankunftszeit        | Der Zeitpunkt, zu dem eine Ereignismeldung eintrifft. Diese Zeit unterscheidet sich normalerweise von der Präsentationszeit des Ereignisses, da Ereignismeldungen vor der Präsentationszeit des Ereignisses gesendet werden.                                                    |
| Platzsparende Spur        | Medienspur, die nicht fortlaufend ist und mit einer übergeordneten Spur oder Steuerungsspur zeitsynchronisiert ist.                                                                                                                                                  |
| Origin              | Der Azure Media-Streamingdienst                                                                                                                                                                                                             |
| Kanalsenke        | Der Azure Media-Livestreamingdienst                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Live Streaming-Protokoll                                                                                                                                                                                                            |
| DASH                | Dynamisches adaptives Streaming über HTTP                                                                                                                                                                                                          |
| Smooth              | Smooth Streaming-Protokoll                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG-2-Transportdatenströme                                                                                                                                                                                                                      |
| RTMP                | Real-Time Multimedia Protocol (Echtzeit-Multimediaprotokoll)                                                                                                                                                                                                                 |
| uimsbf              | Unsigned integer, most significant bit first (Ganzzahl ohne Vorzeichen, höchstwertiges Bit zuerst).                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Normative Verweise

Die folgenden Dokumente enthalten Bestimmungen, die durch Verweise innerhalb dieses Texts Bestimmungen dieses Dokuments bilden. Alle Dokumente unterliegen der Überarbeitung durch die Standardisierungsorganisationen, und Leser werden aufgefordert, eventuell verfügbare neueste Ausgaben der unten aufgeführten Dokumente zu recherchieren, um diese dann auch anzuwenden. Leser werden außerdem daran erinnert, dass neuere Ausgaben der Dokumente, auf die verwiesen wird, möglicherweise nicht mit dieser Version der Spezifikation für zeitgesteuerte Metadaten für Azure Media Services kompatibel sind.


| Standard          | Definition                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital Program Insertion Signaling Specification 1.2 (Spezifikation für digitale Programmeinfügungssignalisierung zur Primetime)](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [FLASH ActionScript Language Reference (FLASH ActionScript-Sprachreferenz)](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | [„Action Message Format AMF0“ (Aktionsnachrichtenformat AMF0)](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Industry Forum Interop Guidance v 4.2 (DASH-Industrieforums-Interop-Anleitung v 4.2) [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Timed Metadata for HTTP Live Streaming (Zeitgesteuerte Metadaten für HTTP-Livestreaming) – [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Timed Metadata in the Common Media Application Format (CMAF) (Zeitgesteuerte Metadaten im allgemeinen Medienanwendungsformat)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag Version 2.4.0 [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Part 12 ISO base media file format, Fourth Edition 2012-07-15 (Teil 12 ISO-Mediendatei-Basisformat, 4. Auflage, 15. Juli 2012)                                                                                                                                 |
| [MPEGDASH]        | Information Technology – Dynamic adaptive streaming over HTTP (Informationstechnologie – Dynamisches adaptives Streaming über HTTP) (DASH) – Part 1 (Teil 1): Media Presentation description und segment formats (Medienpräsentationsbeschreibung und -segmentformate). Mai 2014. Veröffentlicht. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Information Technology – Multimedia application format (MPEG-A) (Informationstechnologie – Multimediaanwendungsformat) – Part 19 (Teil 19): Common media application format (CMAF) for segmented media (Allgemeines Medienanwendungsformat (CMAF) für segmentierte Medien). Januar 2018. Veröffentlicht. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Information Technology – MPEG systems technologies (Informationstechnologie – MPEG-Systemtechnologien) – Part 7 (Teil 7): Common encryption in ISO base media file format files (Allgemeine Verschlüsselung in ISO-Basismediendateiformat-Dateien) Februar 2016. Veröffentlicht. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | [„Microsoft Smooth Streaming Protocol“, 15. Mai 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (Microsoft Smooth Streaming-Protokoll)                                                     |
| [MS-SSTR-Ingest]  | [Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP Live Streaming. August 2017. Zur Information. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | The Base16, Base32, and Base64 Data Encodings (Die Datencodierungen Base16, Base32 und Base64) – [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | [„Adobe’s Real-Time Messaging Protocol“, 21. Dezember 2012 (Echtzeit-Nachrichtenprotokoll von Adobe)](https://rtmp.veriskope.com/docs/spec/)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 – Digital Program Insertion Cueing Message for Cable (Positionsnachricht für die Einfügung von digitalen Programmen im Kabel) – https://scte-cms-resource-storage.s3.amazonaws.com/ANSI_SCTE-35-2019a-1582645390859.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH for IP-Based Cable Services (MPEG DASH für IP-basierte Kabeldienste) Part 1 (Teil 1): MPD Constraints and Extensions (MPD-Einschränkungen und -Erweiterungen)                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 – MPEG DASH for IP-Based Cable Services (MPEG DASH für IP-basierte Kabeldienste) Part 3 (Teil 3): DASH/FF Profile                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Event Scheduling and Notification Interface (Schnittstelle für Ereignisplanung und -benachrichtigung)                                                                                                                                                  |
| [SCTE-250]        | Event and Signaling Management API (ESAM) (Ereignis- und Signalisierungsverwaltungs-API)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Erfassung von zeitgesteuerten Metadaten

Azure Media Services unterstützt In-Band-Metadaten in Echtzeit für die [RTMP]- und [MS-SSTR-Ingest]-Protokolle (Smooth Streaming). Echtzeit-Metadaten können verwendet werden, um benutzerdefinierte Ereignisse mit eigenen benutzerdefinierten Schemas (JSON, Binary, XML) sowie branchendefinierten Formaten wie ID3 oder SCTE-35 für die Werbesignalisierung in einem Broadcaststream zu definieren. 

Dieser Artikel enthält die Details zum Senden von benutzerdefinierten Metadatensignalen mithilfe der unterstützten Erfassungsprotokolle von Azure Media Services. In diesem Artikel wird auch erläutert, wie die Manifeste für HLS, DASH und Smooth Streaming mit den zeitgesteuerten Metadatensignalen ausgestattet sind. Zudem ist eine Erläuterung enthalten, wie sie bandintern übertragen werden, wenn der Inhalt über CMAF- (MP4-Fragmente) oder TS-Segmente (Transportdatenstrom) für HLS bereitgestellt wird. 

Allgemeine Anwendungsszenarien für zeitgesteuerte Metadaten sind:

 - SCTE-35-Werbesignale zum Auslösen von Werbeunterbrechungen in einem Liveereignis oder bei einer linearen Übertragung
 - Benutzerdefinierte ID3-Metadaten, mit denen Ereignisse bei einer Clientanwendung (Browser, iOS oder Android) ausgelöst werden können
 - Benutzerdefinierte JSON-, Binär- oder XML-Metadaten zum Auslösung von Ereignissen in einer Clientanwendung
 - Telemetrie von einem Liveencoder, einer IP-Kamera oder Drohne
 - Ereignisse von einer IP-Kamera wie Bewegungsabläufe, Gesichtserkennung usw.
 - Geografische Positionsinformationen von einer Actionkamera, einer Drohne oder einem sich bewegenden Gerät
 - Liedtexte
 - Programmgrenzen bei einem linearen Livefeed
 - Bilder oder ergänzende Metadaten, die in einem Livefeed angezeigt werden sollen
 - Sportergebnisse oder Zeitinformationen zu Spielen
 - Interaktive Werbepakete, die neben dem Video im Browser angezeigt werden sollen
 - Quiz oder Umfragen
  
Azure Media Services Live Events und Packager sind in der Lage, diese zeitgesteuerten Metadatensignale zu empfangen und in einen Metadatenstrom umzuwandeln, der Clientanwendungen über standardbasierte Protokolle wie HLS und DASH erreichen kann.


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP – Zeitgesteuerte Metadaten

Das [RTMP]-Protokoll ermöglicht das Senden von zeitgesteuerten Metadatensignalen für verschiedene Szenarien, einschließlich benutzerdefinierter Metadaten und SCTE-35-Werbesignalen. 

Werbesignale (Positionsnachrichten) werden als [AMF0]-Positionsnachrichten gesendet, die in den [RTMP]-Stream eingebettet sind. Das Senden der Positionsnachrichten kann einige Zeit vor dem erforderlichen Eintreten des eigentlichen Ereignisses oder [SCTE35] dem Werbeeinblendungssignal erfolgen. Zur Unterstützung dieses Szenarios wird der tatsächliche Präsentationszeitstempel des Ereignisses innerhalb der Positionsnachricht gesendet. Weitere Informationen finden Sie unter [AMF0].

Die folgenden [AMF0]-Befehle werden von Azure Media Services für die RTMP-Erfassung unterstützt:

- **onUserDataEvent** – Wird für benutzerdefinierte Metadaten oder zeitgesteuerte [ID3v2]-Metadaten verwendet.
- **onAdCue** – Wird hauptsächlich zur Signalisierung einer Werbeplatzierungsmöglichkeit im Livestream verwendet. Es werden zwei Formen der Position unterstützt, ein einfacher und ein SCTE-35-Modus. 
- **onCuePoint** – Wird von bestimmten lokalen Hardwareencodern, wie dem Elemental Liveencoder, unterstützt, um [SCTE35]-Nachrichten zu signalisieren. 
  

In der folgenden Tabelle ist das Format der AMF-Nachrichtennutzlast beschrieben, die von Media Services sowohl für den „einfachen“ als auch für den [SCTE35]-Nachrichtenmodus erfasst wird.

Der Name der [AMF0]-Nachricht kann verwendet werden, um mehrere Ereignisdatenströme vom gleichen Typ zu unterscheiden.  Sowohl für [SCTE-35]-Nachrichten als auch für Nachrichten im „einfachen“ Modus MUSS der Name der AMF-Nachricht „onAdCue“ sein, wie in der [Adobe-Primetime]-Spezifikation gefordert.  Alle unten nicht aufgeführten Felder MÜSSEN von Azure Media Services bei der Erfassung ignoriert werden.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP mit benutzerdefinierten Metadaten mithilfe von „onUserDataEvent“

Wenn Sie benutzerdefinierte Metadatenfeeds von Ihrem vorgeschalteten Encoder, Ihrer IP-Kamera, Ihrer Drohne oder Ihrem Gerät über das RTMP-Protokoll bereitstellen möchten, verwenden Sie den [AMF0]-Befehlstyp „onUserDataEvent“ für Datennachrichten.

Der Datennachrichtenbefehl **„onUserDataEvent“** MUSS eine Nachrichtennutzlast mit der folgenden Definition enthalten, die von Media Services erfasst und in das In-Band-Dateiformat sowie die Manifeste für HLS, DASH und Smooth Streaming verpackt werden muss.
Es wird empfohlen, zeitgesteuerte Metadatennachrichten nicht häufiger als einmal alle 0,5 Sekunden (500 ms) zu senden, da sonst Stabilitätsprobleme beim Livestream auftreten können. Jede Nachricht könnte Metadaten aus mehreren Frames aggregieren, wenn Sie Metadaten auf Frameebene bereitstellen müssen. Wenn Sie Datenströme mit Mehrfachbitrate senden, wird empfohlen, die Metadaten auch nur für eine Einzelbitrate bereitzustellen, um die Bandbreite zu reduzieren und Störungen bei der Video-/Audioverarbeitung zu vermeiden. 

Die Nutzlast für **„onUserDataEvent“** sollte eine Nachricht im [MPEGDASH]-EventStream-XML-Format sein. Dadurch ist es einfach, benutzerdefinierte Schemas per In-Band-Übermittlung in „emsg“-Nutzlasten für CMAF [MPEGCMAF]-Inhalte zu übergeben, die über HLS- oder DASH-Protokolle bereitgestellt werden. Jede DASH Event Stream-Nachricht enthält ein „schemeIdUri“, das als URN-Nachrichtenschemabezeichner fungiert und die Nutzlast der Nachricht definiert. Einige Schemas wie „https://aomedia.org/emsg/ID3“ für [ID3v2] oder **urn:scte:scte35:2013:scte35:2013:bin** für [SCTE-35] werden von Industriekonsortien zwecks Interoperabilität standardisiert. Jeder Anwendungsanbieter kann sein eigenes benutzerdefiniertes Schema unter Verwendung einer von ihm kontrollierten URL (eigene Domäne) definieren und kann bei Bedarf eine Spezifikation unter dieser URL bereitstellen. Wenn ein Player über einen Handler für das definierte Schema verfügt, dann ist dies die einzige Komponente, die die Nutzlast und das Protokoll verstehen muss.

Das Schema für die [MPEG-DASH] EventStream-XML-Nutzlast ist definiert als (Auszug aus DASH ISO-IEC-23009-1-3rd Edition). Beachten Sie, dass derzeit nur ein „EventType“ pro „EventStream“ unterstützt wird. Nur das erste **Event**-Element wird verarbeitet, wenn mehrere Ereignisse im **EventStream** bereitgestellt werden.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>XML Event Stream-Beispiel mit ID3-Schema-ID und base64-codierter Datennutzlast.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Event Stream-Beispiel mit benutzerdefinierter Schema-ID und base64-codierten Binärdaten.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Event Stream-Beispiel mit benutzerdefinierter Schema-ID und benutzerdefiniertem JSON.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Integrierte unterstützte Schema-ID-URIs
| Schema-ID-URI                 | BESCHREIBUNG                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Beschreibt, wie [ID3v2]-Metadaten als zeitgesteuerte Metadaten in CMAF-kompatiblem [MPEGCMAF]-fragmentierten MP4 übertragen werden können. Weitere Informationen finden Sie unter [Timed Metadata in the Common Media Application Format (CMAF) (Zeitgesteuerte Metadaten im allgemeinen Medienanwendungsformat)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Ereignisverarbeitung und Manifestsignalisierung

Nach Erhalt eines gültigen **„onUserDataEvent“** -Ereignisses sucht Azure Media Services nach einer gültigen XML-Nutzlast, die mit dem „EventStreamType“ (definiert in [MPEGDASH]) übereinstimmt, analysiert die XML-Nutzlast und konvertiert sie in ein [MPEGCMAF]-MP4-Fragmentfeld „emsg“ Version 1 zur Speicherung im Livearchiv und Übertragung an den Media Services Packager.   Der Packager erkennt das „emsg“-Feld im Livestream und führ folgende Aktionen aus:

- (a) „Dynamisches Verpacken“ in TS-Segmenten für die Bereitstellung für HLS-Clients gemäß der HLS-Spezifikationen für zeitgesteuerte Metadaten [HLS-TMD] oder
- (b) Weiterleiten zur Bereitstellung in CMAF-Fragmenten über HLS oder DASH oder 
- (c) Konvertieren in ein platzsparenden Spursignal für die Übermittlung über Smooth Streaming [MS-SSTR]

Zusätzlich zu den In-Band-CMAF- oder TS-PES-Paketen im emsg-Format für HLS enthalten die Manifeste für DASH (MPD) und Smooth Streaming einen Verweis auf die In-Band-Ereignisdatenströme (im Smooth Streaming auch als „platzsparende Datenstromspur“ bezeichnet).

Einzelne Ereignisse oder deren Datennutzlasten werden NICHT direkt in den HLS-, DASH- oder Smooth-Manifesten ausgegeben. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Zusätzliche informative Einschränkungen und Voreinstellungen für onUserDataEvent-Ereignisse

- Wenn die Zeitskala im EventStream-Element nicht festgelegt ist, wird standardmäßig die Zeitskala „RTMP 1 kHz“ verwendet.
- Die Übermittlung einer onUserDataEvent-Nachricht ist auf maximal einmal alle 500 ms begrenzt. Wenn Sie Ereignisse häufiger senden, kann dies Auswirkungen auf die Bandbreite und die Stabilität des Livefeeds haben.

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP – Werbepositionssignalisierung mit „onAdCue“

Azure Media Services können auf mehrere [AMF0]-Nachrichtentypen lauschen und auf diese reagieren, die zum Signalisieren verschiedener in Echtzeit synchronisierter Metadaten im Livestream verwendet werden können.  Die [Adobe-Primetime]-Spezifikation definiert zwei Positionstypen (Cues), bezeichnet als „einfacher“ (simple) und „SCTE-35“-Modus. Im „einfachen“ Modus unterstützt Media Services eine einzelne AMF-Positionsnachricht namens „onAdCue“, indem eine Nutzlast verwendet wird, die der nachfolgenden Tabelle entspricht, die für das „Simple Mode“-Signal definiert ist.  

Im folgenden Abschnitt wird die RTMP-Nutzlast des „einfachen“ Modus gezeigt, die verwendet werden kann, um ein einfaches „spliceOut“-Werbungssignal zu signalisieren, das in das Clientmanifest für HLS, Dash und Microsoft Smooth Streaming übertragen wird. Dies ist sehr nützlich für Szenarien, in denen der Kunde über keine komplexe SCTE-35-basierte Signalisierung für Werbungsbereitstellung oder Werbungseinfügung verfügt und einen einfachen lokalen Encoder verwendet, um die Positionsnachricht über eine API hineinzusenden. In der Regel unterstützt der lokale Encoder eine REST-basierte API, um dieses Signal auszulösen, wodurch auch der Videostream „splice-konditioniert“ wird, indem ein IDR-Frame in das Video einfügt und eine neue GOP gestartet wird.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP – Werbepositionssignalisierung mit „onAdCue“ – Einfacher Modus

| Feldname | Feldtyp | Erforderlich? | Beschreibungen                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | Erforderlich  | Die Ereignismeldung.  Ist „SpliceOut“, um eine einfache Einfügung anzugeben.                                                                                                                                                                                                         |
| id         | String     | Erforderlich  | Ein eindeutiger Bezeichner, der die Einfügung oder das Segment beschreibt. Identifiziert diese Instanz der Nachricht                                                                                                                                                                                       |
| duration   | Number     | Erforderlich  | Die Dauer der Einfügung. Die Einheit sind Sekundenbruchteile.                                                                                                                                                                                                                           |
| elapsed    | Number     | Optional  | Wenn das Signal zur Unterstützung der Abstimmung wiederholt wird, gibt dieses Feld die Menge der Präsentationszeit an, die seit dem Anfang der Einfügung verstrichen ist. Die Einheit sind Sekundenbruchteile. Bei der Verwendung des einfachen Modus sollte dieser Wert die ursprüngliche Dauer der Einfügung nicht überschreiten. |
| time       | Number     | Erforderlich  | Die Zeit der Einfügung in der Präsentationszeit. Die Einheit sind Sekundenbruchteile.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Beispiel für die Ausgabe eines MPEG DASH-Manifests bei Verwendung des einfachen RTMP-Modus von Adobe

Siehe Beispiel [3.3.2.1 MPEG DASH .mpd EventStream unter Verwendung des einfachen Modus von Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Weitere Informationen finden Sie unter dem Beispiel [3.3.3.1 DASH-Manifest mit einzelnem Zeitraum und einfachem Modus von Adobe](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Beispiel für die Ausgabe eines HLS-Manifests bei Verwendung des einfachen RTMP-Modus von Adobe

Weitere Informationen finden Sie unter dem Beispiel [3.2.2 HLS-Manifest unter Verwendung des einfachen Modus von Adobe und des EXT-X-CUE-Tags](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP – Werbepositionssignalisierung mit „onAdCue“ – SCTE-35-Modus

Wenn Sie mit einem komplexeren Broadcastproduktions-Workflow arbeiten, für den die vollständige SCTE-35-Nutzlastnachricht in das HLS- oder DASH-Manifest übertragen werden muss, empfiehlt es sich, den „SCTE-35-Modus“ der [Adobe-Primetime]-Spezifikation zu verwenden.  Dieser Modus unterstützt In-Band-SCTE-35-Signale, die direkt in einen lokalen Liveencoder gesendet werden, der dann die Signale als Ausgabe in den RTMP-Stream codiert, indem er den „SCTE-35“-Modus verwendet, der in der [Adobe-Primetime]-Spezifikation angegeben ist. 

Normalerweise können SCTE-35-Nachrichten nur in MPEG-2-Transportdatenstromeingaben (TS) auf einem lokalen Encoder vorkommen. Informieren Sie sich beim Hersteller Ihres Encoders hinsichtlich der Details, wie Sie eine Transportdatenstromerfassung konfigurieren müssen, die SCTE-35 enthält, und wie Sie diese für das Pass-Through an RTMP im Adobe SCTE-35-Modus aktivieren.

In diesem Szenario MUSS die folgende Nutzlast vom lokalen Encoder mithilfe des [AMF0]-Nachrichtentyps **„onAdCue“** gesendet werden.

| Feldname | Feldtyp | Erforderlich? | Beschreibungen                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cue        | String     | Erforderlich  | Die Ereignismeldung.  Für [SCTE-35]-Nachrichten MUSS dies die base64-codierte [RFC4648] binäre „splice_info_section()“ sein, damit Nachrichten an HLS-, Smooth- und Dash-Clients gesendet werden können.                                                                                                                                                                                                                               |
| type       | String     | Erforderlich  | Ein URN oder eine URL, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten **SOLLTE** dies **„scte35“** sein, damit Nachrichten [Adobe-Primetime]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können. Optional kann auch der URN „urn:scte:scte35:2013:bin“ verwendet werden, um eine [SCTE-35]-Nachricht zu signalisieren.                                                                                                        |
| id         | String     | Erforderlich  | Ein eindeutiger Bezeichner, der die Einfügung oder das Segment beschreibt. Gibt die Instanz der Nachricht an.  Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf.                                                                                                                                                                                                                                                       |
| duration   | Number     | Erforderlich  | Die Dauer des Ereignisses oder der Werbeeinblendung, sofern bekannt. Wenn sie unbekannt ist, **SOLLTE** der Wert 0 sein.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Number     | Optional  | Wenn das [SCTE-35]-Werbesignal zur Abstimmung wiederholt wird, gibt dieses Feld die Menge der Präsentationszeit an, die seit dem Anfang der Einfügung verstrichen ist. Die Einheit sind Sekundenbruchteile. Im [SCTE-35]-Modus darf dieser Wert die ursprünglich angegebene Dauer der Einfügung oder des Segments überschreiten.                                                                                                                   |
| time       | Number     | Erforderlich  | Die Präsentationszeit des Ereignisses oder der Werbeeinblendung.  Die Präsentationszeit und -dauer **SOLLTEN** an den Datenstrom-Zugriffspunkten (Stream Access Points, SAPs) vom Typ 1 oder 2 ausgerichtet sein, wie in [ISO-14496-12], Anhang I, beschrieben. Für HLS-Ausgang **SOLLTEN** Zeit und Dauer an den Segmentgrenzen ausgerichtet sein. Die Präsentationszeit und die Dauer verschiedener Ereignisnachrichten innerhalb des gleichen Ereignisdatenstroms DÜRFEN sich NICHT überschneiden. Die Einheit sind Sekundenbruchteile. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Beispiel: HLS-Manifest .m3u8 mit SCTE-35-Modussignal
Siehe [Abschnitt 3.2.1.1 Beispiel: HLS-Manifest mit SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP – Werbesignalisierung mit „onCuePoint“ für Elemental Live

Der lokale Encoder von Elemental Live unterstützt Werbungsmarkierungen im RTMP-Signal. Azure Media Services unterstützt zurzeit nur den Werbungsmarkierungstyp „onCuePoint“ für RTMP.  Dies kann in den Adobe RTMP-Gruppeneinstellungen in den Einstellungen oder der API des Elemental Media-Liveencoders aktiviert werden, indem die „**ad_markers**“ auf „onCuePoint“ festgelegt werden.  Weitere Informationen finden Sie in der Dokumentation zu Elemental Live. Durch die Aktivierung dieser Funktion in der RTMP-Gruppe werden SCTE-35-Signale an die Adobe RTMP-Ausgaben übergeben, um sie von Azure Media Services verarbeiten zu lassen.

Der Nachrichtentyp „onCuePoint“ ist in [Adobe-Flash-AS] definiert und hat folgende Nutzlaststruktur, wenn er von der Elemental Live-RTMP-Ausgabe gesendet wird.


| Eigenschaft   | BESCHREIBUNG                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Der Name SOLLTE von Elemental Live **scte35** lauten.                                                                                                                                                                              |
| time       | Die Zeit in Sekunden, zu der der Positionspunkt während der Zeitachse in der Videodatei aufgetreten ist.                                                                                                                                           |
| type       | Der Typ des Positionspunkts SOLLTE auf „**event**“ festgelegt werden.                                                                                                                                                                             |
| parameters | Ein assoziatives Array aus Name/Wert-Paarzeichenfolgen, das die Informationen aus der SCTE-35-Nachricht enthält, einschließlich ID und Dauer. Diese Werte werden von Azure Media Services analysiert und in das Manifestdekorations-Tag eingeschlossen. |


Wenn dieser Modus für Werbungsmarkierungen verwendet wird, ähnelt die HLS-Manifestausgabe dem „einfachen“ Adobe-Modus.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Beispiel: MPEG Dash MPD, einzelner Zeitraum, Signale des einfachen Modus von Adobe

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Beispiel: HLS-Wiedergabeliste, Signale des einfachen Modus von Adobe mit EXT-X-CUE-Tag (aus Gründen der Übersichtlichkeit wird „...“ abgeschnitten)

Das folgende Beispiel zeigt die Ausgabe des dynamischen Packagers von Media Services für einen RTMP-Erfassungsdatenstrom unter Verwendung von Signalen im „einfachen“ Modus von Adobe und dem EXT-X-CUE-Tag der Vorgängerversion [Adobe-Primetime].  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>2.1.6 Stornierung und Aktualisierungen

Nachrichten können durch das Senden mehrerer Nachrichten mit der gleichen Präsentationszeit und ID storniert oder aktualisiert werden. Ein Ereignis wird durch die Präsentationszeit und die ID eindeutig identifiziert, und die letzte für eine bestimmte Präsentationszeit empfangene Nachricht, die den Vorlaufbedingungen entspricht, ist die Nachricht, für die die Aktion ausgeführt wird. Das aktualisierte Ereignis ersetzt alle zuvor empfangenen Nachrichten. Die Vorlaufbedingung beträgt vier Sekunden. Aktionen werden für Nachrichten ausgeführt, die mindestens vier Sekunden vor der Präsentationszeit empfangen wurden.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Fragmentierte MP4-Erfassung (Smooth Streaming)

Informationen zu den Anforderungen für die Livestreamerfassung finden Sie unter [MS-SSTR-Ingest]. Die folgenden Abschnitte enthalten Detailinformationen zur Erfassung von zeitgesteuerten Präsentationsmetadaten.  Zeitgesteuerte Präsentationsmetadaten werden als Sparsespur erfasst, die sowohl in der Live Server Manifest Box (siehe dazu MS-SSTR) als auch in der Movie Box („moov“) definiert ist.  

Jedes Sparsefragment besteht aus einer Movie Fragment Box („moof“) und einer Media Data Box („mdat“), wobei es sich bei der mdat-Box um die binäre Nachricht handelt.

Um eine Frame-genaue Einfügung von Werbungen zu erzielen, MUSS der Encoder das Fragment bei der Präsentationszeit aufteilen, wo der Cue eingefügt werden muss.  Es MUSS ein neues Fragment erstellt werden, das mit einem neu erstellten IDR-Frame beginnt, oder Streamzugriffspunkte (SAP) vom Typ 1 oder 2, wie in [ISO-14496-12] Anhang I definiert.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box

Die Sparsespur **MUSS** in der Live Server Manifest Box mit dem Eintrag **\<textstream\>** deklariert werden, und dafür **MÜSSEN** die folgenden Attribute festgelegt sein:

| **Attributname** | **Feldtyp** | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Number         | Erforderlich      | **MUSS** „0“ sein, was eine Spur mit einer unbekannten, variablen Bitrate angibt.                                                                                                                                                          |
| parentTrackName    | String         | Erforderlich      | **MUSS** der Name der übergeordneten Spur sein, an deren Zeitachse die Zeitcodes der Sparsespur ausgerichtet sind. Die übergeordnete Spur darf ihrerseits keine Sparsespur sein.                                                                             |
| manifestOutput     | Boolean        | Erforderlich      | **MUSS** „true“ sein, um anzugeben, dass die Sparsespur im Smooth-Clientmanifest eingebettet ist.                                                                                                                        |
| Subtype            | String         | Erforderlich      | **MUSS** der aus vier Buchstaben bestehende Code „DATA“ sein.                                                                                                                                                                                  |
| Schema             | String         | Erforderlich      | **MUSS** ein URN oder eine URL sein, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten **MUSS** dies „urn:scte:scte35:2013:bin“ sein, damit Nachrichten [SCTE-35]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können. |
| trackName          | String         | Erforderlich      | **MUSS** der Name der Sparsespur sein. Der Spurname kann verwendet werden, um mehrere Ereignisstreams mit dem gleichen Schema zu unterscheiden. Jeder eindeutige Ereignisdatenstrom **MUSS** einen eindeutigen Spurnamen aufweisen.                                |
| timescale          | Number         | Optional      | **MUSS** die Zeitskala der übergeordneten Spur sein.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Movie Box

Die Movie Box („moov“) folgt auf die Live Server Manifest Box als Teil des Datenstromheaders für eine Sparsespur.

Die moov-Box **SOLLTE** eine **TrackHeaderBox („tkhd“)** gemäß der Definition in [ISO-14496-12] mit den folgenden Einschränkungen enthalten:

| **Feldname** | **Feldtyp**          | **Erforderlich?** | **Beschreibung**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | **SOLLTE** 0 sein, da die Spurbox 0 (null) Stichproben enthält und die Dauer der Stichproben in der Spurbox 0 ist. |

---

Die moov-Box **SOLLTE** eine **HandlerBox („hdlr“)** gemäß der Definition in [ISO-14496-12] mit den folgenden Einschränkungen enthalten:

| **Feldname** | **Feldtyp**          | **Erforderlich?** | **Beschreibung**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | **SOLLTE** „meta“ sein. |

---

Die stsd-Box **SOLLTE** eine MetaDataSampleEntry-Box mit einem gemäß [ISO-14496-12] definierten Codierungsnamen enthalten.  Beispielsweise **SOLLTE** der Codierungsname für SCTE-35-Nachrichten ‚scte‘ sein.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box und Media Data Box

Fragmente von Sparsespuren bestehen aus einer Movie Fragment Box („moof“) und einer Media Data Box („mdat“).

> [!NOTE]
> Um eine Frame-genaue Einfügung von Werbungen zu erzielen, MUSS der Encoder das Fragment bei der Präsentationszeit aufteilen, wo der Cue eingefügt werden muss.  Es MUSS ein neues Fragment erstellt werden, das mit einem neu erstellten IDR-Frame beginnt, oder Streamzugriffspunkte (SAP) vom Typ 1 oder 2, wie in [ISO-14496-12] Anhang I definiert.
> 

Die MovieFragmentBox („moof“) **MUSS** eine **TrackFragmentExtendedHeaderBox („uuid“)** gemäß Definition in [MS-SSTR] mit den folgenden Feldern enthalten:

| **Feldname**         | **Feldtyp**          | **Erforderlich?** | **Beschreibung**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | **MUSS** die Ankunftszeit des Ereignisses sein. Dieser Wert richtet die Nachricht an der übergeordneten Spur aus.           |
| fragment_duration      | 64-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | **MUSS** die Dauer des Ereignisses sein. Die Dauer kann 0 (null) sein, um anzugeben, dass die Dauer unbekannt ist. |

---


Die MediaDataBox („mdat“) **MUSS** das folgende Format aufweisen:

| **Feldname**          | **Feldtyp**                   | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32-Bit-Ganzzahl ohne Vorzeichen (uimsbf) | Erforderlich      | Bestimmt das Format des Inhalts der mdat-Box. Nicht erkannte Versionen werden ignoriert. Aktuell wird nur der Wert 1 unterstützt.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-Bit-Ganzzahl ohne Vorzeichen (uimsbf) | Erforderlich      | Gibt die Instanz der Nachricht an. Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf; d.h., die Verarbeitung einer beliebigen von mehreren Ereignisnachrichtenboxen mit gleicher ID ist ausreichend.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-Bit-Ganzzahl ohne Vorzeichen (uimsbf) | Erforderlich      | Die Summe von „fragment_absolute_time“, die in „TrackFragmentExtendedHeaderBox“ angegeben ist, und das „presentation_time_delta“ **MUSS** die Präsentationszeit des Ereignisses bilden. Die Präsentationszeit und -dauer **SOLLTEN** an den Datenstrom-Zugriffspunkten (Stream Access Points, SAPs) vom Typ 1 oder 2 ausgerichtet sein, wie in [ISO-14496-12], Anhang I, beschrieben. Für HLS-Ausgang **SOLLTEN** Zeit und Dauer an den Segmentgrenzen ausgerichtet sein. Die Präsentationszeit und die Dauer verschiedener Ereignisnachrichten innerhalb des gleichen Ereignisdatenstroms **DÜRFEN sich NICHT** überschneiden. |
| message                 | Bytearray                       | Erforderlich      | Die Ereignismeldung. Für [SCTE-35]-Nachrichten ist die Nachricht die binäre „splice_info_section()“. Für [SCTE-35]-Nachrichten **MUSS** dies „splice_info_section()“ sein, damit Nachrichten [SCTE-35]-konform an HLS-, Smooth- und Dash-Clients gesendet werden können. Für [SCTE-35]-Nachrichten ist die binäre Angabe für „splice_info_section()“ die Nutzlast der mdat-Box, die **NICHT** base64-codiert ist.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Stornierung und Updates

Nachrichten können durch das Senden mehrerer Nachrichten mit der gleichen Präsentationszeit und ID storniert oder aktualisiert werden.  Das Ereignis ist durch die Präsentationszeit und die ID eindeutig identifiziert. Die letzte für eine bestimmte Präsentationszeit empfangene Nachricht, die den Vorlaufbedingungen entspricht, ist die Nachricht, für die die Aktion ausgeführt wird. Die aktualisierte Nachricht ersetzt alle zuvor empfangenen Nachrichten.  Die Vorlaufbedingung beträgt vier Sekunden. Aktionen werden für Nachrichten ausgeführt, die mindestens vier Sekunden vor der Präsentationszeit empfangen wurden. 


## <a name="3-timed-metadata-delivery"></a>3 Zustellung von zeitgesteuerten Metadaten

Ereignisdatenstrom-Daten sind für Media Services nicht transparent. Media Services übergibt einfach drei Informationsstücke zwischen dem Erfassungsendpunkt und dem Clientendpunkt. Die folgenden Eigenschaften werden gemäß [SCTE-35] und/oder dem Streamingprotokoll des Clients an den Client zugestellt:

1.  Scheme: ein URN oder eine URL, der bzw. die das Schema der Nachricht angibt.
2.  Presentation Time: die Präsentationszeit des Ereignisses auf der Medienzeitachse.
3.  Duration: die Dauer des Ereignisses.
4.  ID: ein optionaler eindeutiger Bezeichner für das Ereignis.
5.  Message: die Ereignisdaten.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming-Manifest  

Ausführliche Informationen zum Formatieren einer Sparsenachrichtenspur finden Sie unter „Behandlung von Sparsespuren [MS-SSTR]“. Für [SCTE35]-Nachrichten gibt Smooth Streaming die base64-codierten splice_info_section() in ein Sparsefragment aus.
Der StreamIndex **MUSS** den Untertyp „DATA“ aufweisen, und die CustomAttributes **MÜSSEN** ein Attribut mit Name="Schema" und Value="urn:scte:scte35:2013:bin" enthalten.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Beispiel für ein Smooth-Clientmanifest, das base64-codiertes [SCTE35] splice_info_section() zeigt
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS-Manifestdekoration

Azure Media Services unterstützt die folgenden HLS-Manifest-Tags, um Informationen zur Werbungsverfügbarkeit während eines Live- oder On-Demand-Ereignisses zu signalisieren. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE wie in [Adobe-Primetime] definiert
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

Die Datenausgabe an jedes einzelne Tag variiert je nach verwendetem Erfassungssignalmodus. Beispielsweise enthält die RTMP-Erfassung mit dem Adobe Simple-Modus nicht die vollständige, base64-codierte SCTE-35-Nutzlast.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 Beispiel: HLS-Manifest .m3u8, das die EXT-X-CUE-Signalisierung von SCTE-35 anzeigt

Das folgende Beispiel einer HLS-Manifestausgabe aus dem dynamischen Packager von Media Services zeigt das EXT-X-CUE-Tag für [Adobe-Primetime] im SCTE35-Modus. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS mit Adobe Primetime EXT-X-CUE

Media Services (API der Version 2 und 3) unterstützt die Ausgabe des EXT-X-CUE-Tags gemäß der Definition im „SCTE-35-Modus“ von [Adobe-Primetime]. In diesem Modus bettet Azure Media Services base64-codierte [SCTE-35] splice_info_section() in das EXT-X-CUE-Tag ein.  

Das „Legacy“-Tag EXT-X-CUE ist wie folgt definiert und kann auch normativ in der [Adobe-Primetime]-Spezifikation referenziert werden. Dies sollte nur für die Legacy-SCTE35-Signalisierung bei Bedarf verwendet werden. Andernfalls ist das empfohlene Tag in [RFC8216] als EXT-X-DATERANGE definiert. 

| **Attributname** | **Typ**                      | **Erforderlich?**                             | **Beschreibung**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | Zeichenfolge in Anführungszeichen                 | Erforderlich                                  | Die als base64-Zeichenfolge codierte Nachricht, wie in [RFC4648] beschrieben. Für [SCTE-35]-Nachrichten ist dies die base64-codierte „splice_info_section()“.                                                                                                                                      |
| TYPE               | Zeichenfolge in Anführungszeichen                 | Erforderlich                                  | Ein URN oder eine URL, der bzw. die das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten nimmt der Typ den Sonderwert „scte35“ an.                                                                                                                                                                          |
| id                 | Zeichenfolge in Anführungszeichen                 | Erforderlich                                  | Eindeutiger Bezeichner für das Ereignis. Wenn die ID bei der Erfassung der Nachricht nicht angegeben wird, erstellt Azure Media Services eine eindeutige ID.                                                                                                                                              |
| DURATION           | Dezimale Gleitkommazahl | Erforderlich                                  | Die Dauer des Ereignisses. Wenn sie unbekannt ist, **SOLLTE** der Wert 0 sein. Die Einheit sind Sekundenbruchteile.                                                                                                                                                                                           |
| ELAPSED            | Dezimale Gleitkommazahl | Optional, für gleitendes Fenster jedoch erforderlich | Wenn das Signal wiederholt wird, um ein gleitendes Präsentationsfenster zu unterstützen, **MUSS** dieses Feld gleich dem Betrag der seit dem Beginn des Ereignisses verstrichenen Präsentationszeit sein. Die Einheit sind Sekundenbruchteile. Dieser Wert darf die ursprünglich angegebene Dauer der Einfügung oder des Segments überschreiten. |
| TIME               | Dezimale Gleitkommazahl | Erforderlich                                  | Die Präsentationszeit des Ereignisses. Die Einheit sind Sekundenbruchteile.                                                                                                                                                                                                                        |

Die Anwendungsschicht des HLS-Players verwendet TYPE, um das Format der Nachricht zu bestimmen, die Nachricht zu decodieren, die erforderlichen Zeitkonvertierungen anzuwenden und das Ereignis zu verarbeiten.  Die Ereignisse sind in der Segmentwiedergabeliste der übergeordneten Spur entsprechend dem Ereigniszeitstempel zeitsynchronisiert.  Sie werden vor dem nächstgelegenen Segment (#EXTINF-Tag) eingefügt.


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 Beispiel für HLS .m3u8-Manifest unter Verwendung von Adobe Primetime EXT-X-CUE

Das folgende Beispiel zeigt die HLS-Manifestdekoration mithilfe des Adobe Primetime-Tags EXT-X-CUE.  Der „CUE“-Parameter enthält nur die Eigenschaften „TYPE“ und „Duration“, was bedeutet, dass es sich um eine RTMP-Quelle mit Signalisierung im „einfachen“ Modus von Adobe handelt.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 HLS-Nachrichtenbehandlung für Adobe Primetime EXT-X-CUE

Ereignisse werden in der Segmentwiedergabeliste jeder Video- und Audiospur signalisiert. Die Position des EXT-X-CUE-Tags **MUSS** immer entweder unmittelbar vor dem ersten HLS-Segment (für splice_out oder Segmentstart) oder unmittelbar nach dem letzten HLS-Segment (für splice_in oder Segmentende) sein, auf das sich die TIME- und DURATION-Attribute beziehen, wie für [Adobe-Primetime] erforderlich.

Wenn ein gleitendes Präsentationsfenster aktiviert ist, **MUSS** das EXT-X-CUE-Tag ausreichend oft wiederholt werden, dass die Einfügung oder das Segment jederzeit vollständig in der Segmentwiedergabeliste beschrieben ist, und das ELAPSED-Attribut **MUSS** verwendet werden, um die Aktivitätsdauer der Einfügung oder des Segments anzugeben, wie für [Adobe-Primetime] erforderlich.

Wenn ein gleitendes Präsentationsfenster aktiviert ist, werden die EXT-X-CUE-Tags aus der Segmentwiedergabeliste entfernt, wenn sich die Medienzeit, auf die sie sich beziehen, aus dem gleitenden Präsentationsfenster herausbewegt hat.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH-Manifestdekoration (MPD)

[MPEGDASH] stellt drei Möglichkeiten zur Signalisierung von Ereignissen bereit:

1.  Im MPD EventStream signalisierte Ereignisse
2.  In-Band-signalisierte Ereignisse unter Verwendung der Ereignisnachrichten-Box („emsg“)
3.  Eine Kombination aus 1 und 2

Im MPD EventStream signalisierte Ereignisse sind für das VOD-Streaming nützlich, da Clients beim Download der MPD sofort Zugriff auf alle Ereignisse haben. Die Methode ist auch nützlich für die SSAI-Signalisierung, bei der der SSAI-Downstreamanbieter die Signale aus dem MPD-Manifest analysieren und Werbungsinhalt dynamisch einfügen muss.  Die In-Band-Lösung („emsg“) eignet sich für Livestreaming, bei dem Clients das MPD nicht erneut herunterladen müssen, oder wo zwischen dem Client und dem Ursprung keine Manipulation von SSAI-Manifesten stattfindet. 

Das Standardverhalten von Azure Media Services für DASH besteht darin, sowohl im MPD-EventStream als auch In-Band mithilfe der Ereignisnachrichten-Box („emsg“) zu signalisieren.

Über [RTMP] oder [MS-SSTR-Ingest] erfasste Positionsnachrichten werden DASH-Ereignissen zugeordnet, wobei die In-Band-Boxen „emsg“ und/oder In-MPD-EventStreams verwendet werden. 

Die SCTE-35-In-Band-Signalisierung für DASH folgt der Definition und den Anforderungen, die in [SCTE-214-3] sowie im [DASH-IF-IOP]-Abschnitt 13.12.2 („SCTE35 Events“) definiert sind. 

Für die [SCTE-35]-In-Band-Übertragung verwendet die Ereignisnachrichten-Box („emsg“) die schemeId = „urn:scte:scte35:2013:bin“. Bei der MPD-Manifestdekoration verwendet die EventStream schemeId „urn:scte:scte35:2014:xml+bin“.  Dieses Format ist eine XML-Darstellung des Ereignisses, die eine binäre base64-codierte Ausgabe der vollständigen SCTE-35-Nachricht enthält, die bei der Erfassung eingetroffen ist. 

Normative Verweisdefinitionen der Übertragung von [SCTE-35]-Positionsnachrichten in DASH finden Sie in [SCTE-214-1], Abschnitt 6.7.4 (MPD), und [SCTE-214-3], Abschnitt 7.3.2 (Übertragung von SCTE 35-Positionsnachrichten).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG Dash (MPD) EventStream-Signalisierung

Manifestdekoration (MPD) von Ereignissen wird im MPD mithilfe des EventStream-Elements signalisiert, das innerhalb des Period-Elements vorkommt. Die verwendete schemeid ist „urn:scte:scte35:2014:xml+bin“.


> [!NOTE]
> Aus Gründen der Kürze ermöglicht [SCTE-35] die Verwendung des base64-codierten Abschnitts im „Signal.Binary“-Element (anstatt im „Signal.Signal.SpliceInfoSection“-Elements) als Alternative zur Übertragung einer vollständig analysierten Positionsnachricht.
> Azure Media Services verwendet diesen „xml+bin“-Ansatz zur Signalisierung im MPD-Manifest.
> Dies ist auch die empfohlene Methode, die im [DASH-IF-IOP] verwendet wird. Siehe im Abschnitt [„Werbeeinfügungs-Ereignisdatenströme“ der DASH IF IOP-Richtlinie](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams).
> 


Das EventStream-Element weist die folgenden Attribute auf:

| **Attributname** | **Typ**                | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | Zeichenfolge                  | Erforderlich      | Gibt das Schema der Nachricht an. Das Schema ist auf den Wert des Scheme-Attributs in der Live Server Manifest-Box festgelegt. Der Wert **SOLLTE** ein URN oder eine URL sein, der/die das Nachrichtenschema identifiziert. Die unterstützte Ausgabe schemeId sollte „urn:scte:scte35:2014:xml+bin“ gemäß [SCTE-214-1], Abschnitt 6.7.4 (MPD), lauten, da der Dienst zurzeit aus Gründen der Kürze nur „xml+bin“ in MPD unterstützt. |
| value              | Zeichenfolge                  | Optional      | Ein zusätzlicher Zeichenfolgenwert, der von den Besitzern des Schemas zum Anpassen der Semantik der Nachricht verwendet wird. Um mehrere Ereignisdatenströme mit demselben Schema unterscheiden zu können, **MUSS** der Wert auf den Namen des Ereignisdatenstroms festgelegt werden (‚trackName‘ für [MS-SSTR-Ingest]-Erfassung oder AMF-Nachrichtenname für RTMP-Erfassung).                                                                         |
| Timescale          | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Die Zeitspanne in Sekundenbruchteilen.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Beispiel: Ereignisdatenströme für MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Beispiel: MPEG DASH .mpd-Manifestsignalisierung des RTMP-Streamings mit dem einfachen Modus von Adobe

Das folgende Beispiel zeigt einen Auszug von EventStream aus dem dynamischen Packager von Media Services für einen RTMP-Datenstrom mit der Signalisierung im „einfachen“ Modus von Adobe.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Beispiel: MPEG DASH .mpd-Manifestsignalisierung eines RTMP-Datenstroms mit dem SCTE-35-Modus von Adobe

Das folgende Beispiel zeigt einen Auszug von EventStream aus dem dynamischen Packager von Media Services für einen RTMP-Datenstrom mit der Signalisierung im SCTE-35-Modus von Adobe.

EventStream-Beispielelement mit xml+bin-Signalisierung nach [SCTE-214-1]

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> Beachten Sie, dass „presentationTime“ die Präsentationszeit des [SCTE-35]-Ereignisses ist, relativ zur Startzeit des Zeitraums übersetzt, nicht die Ankunftszeit der Nachricht.
> [MPEGDASH] definiert die Event@presentationTime als „Gibt die Präsentationszeit des Ereignisses relativ zum Start des Zeitraums an“.
> Der Wert der Präsentationszeit in Sekunden entspricht der Division des Werts dieses Attributs und dem Wert des Attributs EventStream@timescale.
> Wenn dieser Wert nicht vorhanden ist, ist der Wert der Präsentationszeit gleich 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Beispiel: MPEG DASH-Manifest (MPD) mit einzelnem Zeitraum, EventStream, mit Signalen des einfachen Modus von Adobe

Das folgende Beispiel zeigt die Ausgabe des dynamischen Packagers von Media Services für einen RTMP-Quelldatenstrom unter Verwendung der Werbesignalmethode im „einfachen“ Modus von Adobe. Die Ausgabe ist ein Manifest mit einem einzelnen Zeitraum, das ein EventStream mit dem auf „urn:com:adobe:dpi:simple:2015“ festgelegten „schemeId Uri“ und mit der auf „simplesignal“ festgelegten Werteigenschaft verwendet.
Jedes einfache Signal wird in einem Ereigniselement mit den Eigenschaften @presentationTime, @duration und @id bereitgestellt, das auf der Grundlage der eingehenden einfachen Signale gefüllt wird.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH In-Band-Event Message Box-Signalisierung

Für einen In-Band-Ereignisdatenstrom muss das MPD ein InbandEventStream-Element auf der Adaptation Set-Ebene aufweisen.  Dieses Element weist ein obligatorisches schemeIdUri-Attribut und ein optionales timescale-Attribut auf, die ebenfalls in der Event Message Box („emsg“) vorkommen.  Es **SOLLTEN** keine Ereignisnachrichten-Boxen mit Schemabezeichnern vorhanden sein, die nicht im MPD definiert sind.

Für die [SCTE-35]-In-Band-Übertragung **MÜSSEN** Signale die schemeId = „urn:scte:scte35:2013:bin“ verwenden.
Normative Definitionen der Übertragung von [SCTE-35]-In-Band-Nachrichten finden Sie in [SCTE-214-3], Abschnitt 7.3.2 (Übertragung von SCTE 35-Positionsnachrichten).

Die folgenden Details beschreiben die spezifischen Werte, die der Client in der „emsg“ in Übereinstimmung mit [SCTE-214-3] erwarten sollte:

| **Feldname**          | **Feldtyp**          | **Erforderlich?** | **Beschreibung**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | Zeichenfolge                  | Erforderlich      | Gibt das Schema der Nachricht an. Das Schema ist auf den Wert des Scheme-Attributs in der Live Server Manifest-Box festgelegt. Der Wert **MUSS** ein URN sein, der das Nachrichtenschema angibt. Für [SCTE-35]-Nachrichten **MUSS** dies „urn:scte:scte35:2013:bin“ in Übereinstimmung mit [SCTE-214-3] sein.          |
| Wert                   | Zeichenfolge                  | Erforderlich      | Ein zusätzlicher Zeichenfolgenwert, der von den Besitzern des Schemas zum Anpassen der Semantik der Nachricht verwendet wird. Um mehrere Ereignisdatenströme mit gleichem Schema zu unterscheiden, wird der Wert auf den Namen des Ereignisdatenstroms festgelegt (‚trackName‘ für Smooth-Erfassung oder AMF-Nachrichtenname für RTMP-Erfassung). |
| Timescale               | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Die Zeitskala in Sekundenbruchteilen der Zeit- und Dauerfelder innerhalb der emsg-Box.                                                                                                                                                                                                            |
| Presentation_time_delta | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Das Delta der Medienpräsentationszeit aus der Präsentationszeit des Ereignisses und der frühesten Präsentationszeit in diesem Segment. Die Präsentationszeit und die Dauer **SOLLTEN** an den Datenstrom-Zugriffspunkten, (Stream Access Points, SAPs) von Typ 1 oder Typ 2 ausgerichtet sein, wie in [ISO-14496-12] Anhang I definiert.                                  |
| event_duration          | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Die Dauer des Ereignisses, oder 0xFFFFFFFF, um eine unbekannte Dauer anzugeben.                                                                                                                                                                                                                              |
| Id                      | 32-Bit-Ganzzahl ohne Vorzeichen | Erforderlich      | Gibt die Instanz der Nachricht an. Nachrichten mit gleichbedeutender Semantik weisen den gleichen Wert auf. Wenn die ID bei der Erfassung der Nachricht nicht angegeben wird, erstellt Azure Media Services eine eindeutige ID.                                                                                        |
| Message_data            | Bytearray              | Erforderlich      | Die Ereignismeldung. Für [SCTE-35]-Nachrichten sind die Nachrichtendaten die binäre „splice_info_section()“ in Übereinstimmung mit [SCTE-214-3].                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Beispiel: InBandEvenStream-Entität im einfachen Modus von Adobe
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>3.3.5 DASH-Nachrichtenbehandlung

Ereignisse werden sowohl für Video- als auch für Audiospuren innerhalb der emsg-Box In-Band signalisiert.  Die Signalisierung erfolgt für alle Segmentanforderungen, deren „presentation_time_delta“ 15 Sekunden oder weniger beträgt. 

Wenn ein gleitendes Präsentationsfenster aktiviert ist, werden Ereignisnachrichten aus dem MPD entfernt, wenn die Summe von Zeit und Dauer der Ereignisnachricht kleiner als die Zeit der Mediendaten im Manifest ist.  Anders ausgedrückt: Die Ereignisnachrichten werden aus dem Manifest entfernt, wenn die Medienzeit, auf die sie sich beziehen, sich aus dem gleitenden Präsentationsfenster heraus bewegt hat.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Implementierungsleitfaden für die SCTE-35-Erfassung für Encoder-Anbieter

Die folgenden Richtlinien zeigen häufige Probleme, die die Implementierung dieser Spezifikation durch den Encoder-Anbieter beeinträchtigen können.  Die nachstehenden Richtlinien wurden basierend auf dem echten Feedback von Partnern gesammelt, um anderen die Implementierung dieser Spezifikation zu erleichtern. 

[SCTE-35]-Nachrichten werden im binären Format gemäß dem Schema **„urn:scte:scte35:2013:bin“** bei [MS-SSTR-Ingest]-Erfassung und gemäß dem Typ **scte35** bei RTMP-Erfassung erfasst. Um die Konvertierung der [SCTE-35]-Zeitgebung, die auf MPEG-2-Transportdatenstrom-Präsentationszeitstempeln (PTS) basiert, zu erleichtern, wird in Form der Ereignispräsentationszeit (dem Feld „fragment_absolute_time“ bei Smooth-Erfassung und dem Feld „time“ bei RTMP-Erfassung) eine Zuordnung zwischen PTS (pts_time + pts_adjustment der splice_time()) und der Medienzeitachse bereitgestellt. Die Zuordnung ist erforderlich, da ein Rollover des 33-Bit PTS-Werts ungefähr alle 26,5 Stunden erfolgt.

Die Smooth Streaming-Erfassung [MS-SSTR-Ingest] erfordert, dass die Media Data Box („mdat“) die in [SCTE-35] definierte Angabe für **splice_info_section()** enthalten **MUSS**. 

Bei der RTMP-Erfassung wird das cue-Attribut der AMF-Nachricht auf die base64-codierte **splice_info_section()** festgelegt, die in [SCTE-35] definiert ist.  


Wenn die Nachrichten das oben beschriebene Format aufweisen, werden sie, wie weiter oben definiert, an HLS-, Smooth- und DASH-Clients gesendet.  

Wenn Sie Ihre Implementierung mit der Azure Media Services-Plattform testen, beginnen Sie das Testen zunächst mit einem „Pass-Through“-Liveereignis, bevor Sie zu Tests mit einem Codierungs-Liveereignis wechseln.

---

## <a name="change-history"></a>Änderungsverlauf

| Date     | Änderungen                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Unterstützung der überarbeiteten RTMP-Erfassung, RTMP „onCuePoint“ für Elemental Live wurde hinzugefügt                                            |
| 08/22/19 | Aktualisiert, um OnUserDataEvent zu RTMP für benutzerdefinierte Metadaten hinzuzufügen                                                          |
| 1/08/20  | Fehler im „einfachen“ RTMP-Modus und im RTMP SCTE35-Modus behoben. „onCuePoint“ wurde in „onAdCue“ geändert. Tabelle für den einfachen Modus wurde aktualisiert. |
| 08/4/20  | Die Unterstützung für das DATERANGE-Tag wurde entfernt, um der Implementierung im Produktionsdienst zu entsprechen.    |
