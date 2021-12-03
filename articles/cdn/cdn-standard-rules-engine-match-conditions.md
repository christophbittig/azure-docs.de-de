---
title: Übereinstimmungsbedingungen in der Standardregel-Engine für Azure CDN | Microsoft-Dokumentation
description: Referenzdokumentation für Übereinstimmungsbedingungen in der Standardregel-Engine für Azure Content Delivery Network (Azure CDN).
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: duau
ms.openlocfilehash: bd0c6b0578fc301c38e96b654a281eb025c54dc5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469918"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Übereinstimmungsbedingungen in der Standardregel-Engine für Azure CDN

In der [Standardregel-Engine](cdn-standard-rules-engine.md) für Azure Content Delivery Network (Azure CDN) besteht eine Regel aus mindestens einer Übereinstimmungsbedingung und einer Aktion. In diesem Artikel werden die Übereinstimmungsbedingungen, die Sie in der Standardregel-Engine für Azure CDN verwenden können, ausführlich beschrieben.

Der erste Teil einer Regel besteht aus einer Übereinstimmungsbedingung oder aus mehreren Übereinstimmungsbedingungen. In der Standardregel-Engine für Azure CDN kann jede Regel bis zu vier Übereinstimmungsbedingungen aufweisen. Eine Übereinstimmungsbedingung gibt bestimmte Typen von Anforderungen an, für die definierte Aktionen ausgeführt werden. Wenn Sie mehrere Übereinstimmungsbedingungen verwenden, werden die Übereinstimmungsbedingungen mit UND-Logik gruppiert.

Sie können eine Übereinstimmungsbedingung z.B. für Folgendes verwenden:

- Filtern von Anforderungen, die auf einer bestimmten IP-Adresse, einem bestimmten Land oder einer bestimmten Region basieren.
- Filtern von Anforderungen nach Headerinformationen
- Filtern von Anforderungen von Mobil- oder Desktopgeräten.

## <a name="match-conditions"></a>Übereinstimmungsbedingungen

Die folgenden Übereinstimmungsbedingungen können auch in der Standardregel-Engine für Azure CDN verwendet werden. 

### <a name="device-type"></a>Gerätetyp 

Identifiziert Anforderungen von einem Mobil- oder Desktopgerät.  

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | Mobil, Desktop

### <a name="http-version"></a>HTTP-Version

Identifiziert Anforderungen auf der Grundlage der HTTP-Version der Anforderung.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | 2.0, 1.1, 1.0, 0.9, All

### <a name="request-cookies"></a>Anforderungscookies

Identifiziert Anforderungen anhand der Cookieinformationen in der eingehenden Anforderung.

#### <a name="required-fields"></a>Pflichtfelder

Cookiename | Operator | Cookiewert | Umwandlung der Groß-/Kleinschreibung
------------|----------|--------------|---------------
String | [Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

#### <a name="key-information"></a>Wichtige Informationen

- Sie können keine Platzhalterwerte (einschließlich Sternchen (\*)) verwenden, wenn Sie einen Cookienamen angeben. Sie müssen den genauen Cookienamen verwenden.
- Für jede Instanz dieser Übereinstimmungsbedingung kann nur ein einziger Cookiename angegeben werden.
- Beim Vergleichen von Cookienamen wird die Groß-/Kleinschreibung nicht beachtet.
- Verwenden Sie zum Angeben mehrerer Cookiewerte ein einzelnes Leerzeichen zwischen den jeweiligen Cookiewerten. 
- Für Cookiewerte können Platzhalterwerte verwendet werden.
- Wenn kein Platzhalterwert angegeben wurde, wird diese Übereinstimmungsbedingung nur bei einer genauen Übereinstimmung erfüllt. Beispielsweise entspricht „Value“ dem Wert „Value“, nicht aber dem Wert „Value1“. 

### <a name="post-argument"></a>POST-Argument

Identifiziert Anforderungen auf der Grundlage von Argumenten, die für die POST-Anforderungsmethode definiert sind, die in der Anforderung verwendet wird. 

#### <a name="required-fields"></a>Pflichtfelder

Argumentname | Operator | Argumentwert | Umwandlung der Groß-/Kleinschreibung
--------------|----------|----------------|---------------
String | [Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

### <a name="query-string"></a>Abfragezeichenfolge

Identifiziert Anforderungen, die einen bestimmten Abfragezeichenfolgenparameter enthalten. Dieser Parameter wird auf einen Wert festgelegt, der mit einem bestimmten Muster übereinstimmt. Abfragezeichenfolgenparameter (z.B. **parameter=value**) in der Anforderungs-URL bestimmen, ob diese Bedingung erfüllt ist. Mit dieser Übereinstimmungsbedingung wird ein Abfragezeichenfolgenparameter anhand des Namens identifiziert, und für den Parameterwert werden ein oder mehrere Werte akzeptiert.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Abfragezeichenfolge | Umwandlung der Groß-/Kleinschreibung
---------|--------------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

### <a name="remote-address"></a>Remoteadresse

Identifiziert Anforderungen basierend auf dem Standort oder der IP-Adresse des Anforderers.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|-----------------
Any | –
Geografische Übereinstimmung | Landesvorwahl
IP-Übereinstimmung | IP-Adresse (durch Leerzeichen getrennt)
Keine | –
Keine geografische Übereinstimmung | Landesvorwahl
Keine IP-Übereinstimmung | IP-Adresse (durch Leerzeichen getrennt)

#### <a name="key-information"></a>Wichtige Informationen

- Verwenden Sie die CIDR-Notation.
- Verwenden Sie zum Angeben mehrerer IP-Adressen und IP-Adressblöcke ein einzelnes Leerzeichen zwischen den Werten:
  - **IPv4-Beispiel:** *1.2.3.4 10.20.30.30.40* entspricht allen Anforderungen, die entweder von der Adresse 1.2.3.4 oder 10.20.30.40 eingehen.
  - **IPv6-Beispiel:** *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* entspricht allen Anforderungen, die von einer der Adressen 1:2:3:4:5:6:7:8 oder 10:20:30:40:50:60:70:80 eingehen.
- Die Syntax für einen IP-Adressblock besteht aus der IP-Basisadresse, gefolgt von einem Schrägstrich und der Präfixgröße. Beispiel:
  - **IPv4-Beispiel:** *5.5.5.64/26* entspricht allen Anforderungen, die von den Adressen 5.5.5.64 bis 5.5.5.127 eingehen.
  - **IPv6-Beispiel:** Die Angabe *1:2:3:/48* führt zur Übereinstimmung mit eingehenden Anforderungen von den Adressen 1:2:3:0:0:0:0:0 bis 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Die Remoteadresse stellt die ursprüngliche Client-IP dar, die entweder von der Netzwerkverbindung oder in der Regel vom X-Forwarded-For-Anforderungs-Header kommt, wenn sich der Benutzer hinter einem Proxy befindet.

### <a name="request-body"></a>Anforderungstext

Identifiziert Anforderungen auf der Grundlage eines bestimmten Texts, der im Textkörper der Anforderung vorhanden ist.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Anforderungstext | Umwandlung der Groß-/Kleinschreibung
---------|--------------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

### <a name="request-header"></a>Anforderungsheader

Identifiziert Anforderungen, die einen bestimmten Header in der Anforderung verwenden.

#### <a name="required-fields"></a>Pflichtfelder

Headername | Operator | Headerwert | Umwandlung der Groß-/Kleinschreibung
------------|----------|--------------|---------------
String | [Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

### <a name="request-method"></a>Anforderungsmethode

Identifiziert Anforderungen, die die angegebene Anforderungsmethode verwenden.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Wichtige Informationen

- Nur die GET-Anforderungsmethode zwischengespeicherten Inhalt in Azure CDN generieren. Alle anderen Anforderungsmethoden werden per Proxy durch das Netzwerk gesendet. 

### <a name="request-protocol"></a>Anforderungsprotokoll

Identifiziert Anforderungen, die das angegebene Protokoll verwenden.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | HTTP, HTTPS

### <a name="request-url"></a>Anfrage-URL

Identifiziert Anforderungen, die mit der angegebenen URL übereinstimmen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Anfrage-URL | Umwandlung der Groß-/Kleinschreibung
---------|-------------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

#### <a name="key-information"></a>Wichtige Informationen

- Wenn Sie diese Regelbedingung verwenden, achten Sie darauf, dass Sie Protokollinformationen einschließen. Beispiel: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>URL-Dateierweiterung

Identifiziert Anforderungen, die die angegebene Dateierweiterung in den Dateinamen in der anfordernden URL einschließen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Durchwahl | Umwandlung der Groß-/Kleinschreibung
---------|-----------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

#### <a name="key-information"></a>Wichtige Informationen

- Lassen Sie bei der Erweiterung den vorangestellten Punkt weg. Verwenden Sie also *html* anstelle von *.html*.

### <a name="url-file-name"></a>URL-Dateiname

Identifiziert Anforderungen, die den angegebenen Dateinamen in der anfordernden URL einschließen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Dateiname | Umwandlung der Groß-/Kleinschreibung
---------|-----------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

#### <a name="key-information"></a>Wichtige Informationen

- Um mehrere Dateinamen anzugeben, trennen Sie die einzelnen Dateinamen jeweils durch ein einzelnes Leerzeichen. 

### <a name="url-path"></a>URL-Pfad

Identifiziert Anforderungen, die den angegebenen Pfad in der anfordernden URL einschließen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Wert | Umwandlung der Groß-/Kleinschreibung
---------|-------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Keine Umwandlung, in Großbuchstaben, in Kleinbuchstaben

#### <a name="key-information"></a>Wichtige Informationen

- Für Dateinamen können Platzhalterwerte verwendet werden. Beispielsweise kann jedes Dateinamensmuster ein oder mehrere Sternchen (*) enthalten, wobei jedes Sternchen für ein Zeichen oder eine Folge von Zeichen steht.

## <a name="reference-for-rules-engine-match-conditions"></a>Referenz für Übereinstimmungsbedingungen der Regel-Engine

### <a name="standard-operator-list"></a>Liste der Standardoperatoren

Für Regeln, die Werte aus der Liste der Standardoperatoren akzeptieren, sind die folgenden Operatoren gültig:

- Any
- Equals 
- Enthält 
- Beginnt mit 
- Endet mit 
- Kleiner als
- Kleiner als oder gleich
- Größer als
- Größer als oder gleich
- Keine
- Enthält nicht
- Beginnt nicht mit 
- Endet nicht mit 
- Nicht kleiner als
- Nicht kleiner als oder gleich
- Nicht größer als
- Nicht größer als oder gleich

Für numerische Operatoren wie *Kleiner als* oder *Größer als oder gleich* basiert der verwendete Vergleich auf der Länge. In diesem Fall muss der Wert in der Übereinstimmungsbedingung eine ganze Zahl sein, die der Länge entspricht, die Sie vergleichen möchten. 

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
- [Referenz zur Standardregel-Engine](cdn-standard-rules-engine-reference.md)
- [Aktionen in der Standardregel-Engine](cdn-standard-rules-engine-actions.md)
- [Erzwingen von HTTPS mithilfe der Standardregel-Engine](cdn-standard-rules-engine.md)
