---
title: Übereinstimmungsbedingungen des Azure Front Door-Regelmoduls
description: Dieser Artikel enthält eine Liste der verschiedenen Übereinstimmungsbedingungen, die mit dem Azure Front Door-Regelmodul verfügbar sind.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: d645a2f19cb35c916beabad9ddd18b84c31c8f42
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438453"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Übereinstimmungsbedingungen des Azure Front Door-Regelmoduls

Im [AFD-Regelmodul](front-door-rules-engine.md) besteht eine Regel aus null oder mehr Übereinstimmungsbedingungen und einer Aktion. Dieser Artikel enthält detaillierte Beschreibungen der Übereinstimmungsbedingungen, die Sie im AFD-Regelmodul verwenden können.

Der erste Teil einer Regel besteht aus einer Übereinstimmungsbedingung oder aus mehreren Übereinstimmungsbedingungen. Eine Regel kann aus bis zu 10 Übereinstimmungsbedingungen bestehen. Eine Übereinstimmungsbedingung gibt bestimmte Typen von Anforderungen an, für die definierte Aktionen ausgeführt werden. Wenn Sie mehrere Übereinstimmungsbedingungen verwenden, werden die Übereinstimmungsbedingungen mit UND-Logik gruppiert. Für alle Übereinstimmungsbedingungen, die mehrere Werte unterstützen (als „durch Leerzeichen getrennt“ angegeben), wird der Operator „ODER“ angenommen.

Sie können eine Übereinstimmungsbedingung z.B. für Folgendes verwenden:

- Filtern von Anforderungen, die auf einer bestimmten IP-Adresse, einem bestimmten Land oder einer bestimmten Region basieren.
- Filtern von Anforderungen nach Headerinformationen
- Filtern von Anforderungen von Mobil- oder Desktopgeräten.

Die folgenden Übereinstimmungsbedingungen können auch im Azure Front Door-Regelmodul verwendet werden.  

## <a name="device-type"></a>Gerätetyp 

Identifiziert Anforderungen von einem Mobil- oder Desktopgerät.  

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | Mobil, Desktop

## <a name="post-argument"></a>POST-Argument

Identifiziert Anforderungen auf der Grundlage von Argumenten, die für die POST-Anforderungsmethode definiert sind, die in der Anforderung verwendet wird. 

#### <a name="required-fields"></a>Pflichtfelder

Argumentname | Operator | Argumentwert | Umwandlung der Groß-/Kleinschreibung
--------------|----------|----------------|---------------
String | [Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

## <a name="query-string"></a>Abfragezeichenfolge

Identifiziert Anforderungen, die einen bestimmten Abfragezeichenfolgenparameter enthalten. Dieser Parameter wird auf einen Wert festgelegt, der mit einem bestimmten Muster übereinstimmt. Abfragezeichenfolgenparameter (z.B. **parameter=value**) in der Anforderungs-URL bestimmen, ob diese Bedingung erfüllt ist. Mit dieser Übereinstimmungsbedingung wird ein Abfragezeichenfolgenparameter anhand des Namens identifiziert, und für den Parameterwert werden ein oder mehrere Werte akzeptiert.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Abfragezeichenfolge | Umwandlung der Groß-/Kleinschreibung
---------|--------------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

## <a name="remote-address"></a>Remoteadresse

Identifiziert Anforderungen basierend auf dem Standort oder der IP-Adresse des Anforderers.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|-----------------
Geografische Übereinstimmung | Landesvorwahl
IP-Übereinstimmung | IP-Adresse (durch Leerzeichen getrennt)
Keine geografische Übereinstimmung | Landesvorwahl
Keine IP-Übereinstimmung | IP-Adresse (durch Leerzeichen getrennt)

#### <a name="key-information"></a>Wichtige Informationen

- Verwenden Sie die CIDR-Notation.
- Verwenden Sie zum Angeben mehrerer IP-Adressen und IP-Adressblöcke ein einzelnes Leerzeichen zwischen den Werten:
  - **IPv4-Beispiel:** *1.2.3.4 10.20.30.30.40* entspricht allen Anforderungen, die entweder von der Adresse 1.2.3.4 oder 10.20.30.40 eingehen.
  - **IPv6-Beispiel:** *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* entspricht allen Anforderungen, die von einer der Adressen 1:2:3:4:5:6:7:8 oder 10:20:30:40:50:60:70:80 eingehen.
- Die Syntax für einen IP-Adressblock besteht aus der IP-Basisadresse, gefolgt von einem Schrägstrich und der Präfixgröße. Beispiel:
  - **IPv4-Beispiel:** *5.5.5.64/26* entspricht allen Anforderungen, die von den Adressen 5.5.5.64 bis 5.5.5.127 eingehen.
  - **IPv6-Beispiel:** Die Angabe *1:2:3:/48* führt zur Übereinstimmung mit eingehenden Anforderungen von den Adressen 1:2:3:0:0:0:0:0 bis 1:2:3: ffff:ffff:ffff:ffff:ffff.
- Die Remote-Adresse stellt die ursprüngliche Client-IP dar, die entweder aus der Netzwerkverbindung oder typischerweise aus dem X-Forwarded-For Anforderungsheader stammt, wenn sich der Benutzer hinter einem Proxy befindet.

## <a name="request-body"></a>Anforderungstext

Identifiziert Anforderungen auf der Grundlage eines bestimmten Texts, der im Textkörper der Anforderung vorhanden ist.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Anforderungstext | Umwandlung der Groß-/Kleinschreibung
---------|--------------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

## <a name="request-header"></a>Anforderungsheader

Identifiziert Anforderungen, die einen bestimmten Header in der Anforderung verwenden.

#### <a name="required-fields"></a>Pflichtfelder

Headername | Operator | Headerwert | Umwandlung der Groß-/Kleinschreibung
------------|----------|--------------|---------------
String | [Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

## <a name="request-method"></a>Anforderungsmethode

Identifiziert Anforderungen, die die angegebene Anforderungsmethode verwenden.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Wichtige Informationen

- Nur die GET-Anforderungsmethode kann zwischengespeicherten Inhalt in Azure Front Door generieren. Alle anderen Anforderungsmethoden werden per Proxy durch das Netzwerk gesendet. 

## <a name="request-protocol"></a>Anforderungsprotokoll

Identifiziert Anforderungen, die das angegebene Protokoll verwenden.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Unterstützte Werte
---------|----------------
Gleich, Ungleich | HTTP, HTTPS

## <a name="request-url"></a>Anfrage-URL

Identifiziert Anforderungen, die mit der angegebenen URL übereinstimmen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Anfrage-URL | Umwandlung der Groß-/Kleinschreibung
---------|-------------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

#### <a name="key-information"></a>Wichtige Informationen

- Wenn Sie diese Regelbedingung verwenden, achten Sie darauf, dass Sie Protokollinformationen einschließen. Beispiel: `https://www.<yourdomain\>.com`.

## <a name="request-file-extension"></a>Dateierweiterung der Anforderung

Identifiziert Anforderungen, die die angegebene Dateierweiterung in den Dateinamen in der anfordernden URL einschließen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Durchwahl | Umwandlung der Groß-/Kleinschreibung
---------|-----------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

#### <a name="key-information"></a>Wichtige Informationen

- Lassen Sie bei der Erweiterung den vorangestellten Punkt weg. Verwenden Sie also *html* anstelle von *.html*.

## <a name="request-file-name"></a>Dateiname der Anforderung

Identifiziert Anforderungen, die den angegebenen Dateinamen in der anfordernden URL einschließen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Dateiname | Umwandlung der Groß-/Kleinschreibung
---------|-----------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

#### <a name="key-information"></a>Wichtige Informationen

- Um mehrere Dateinamen anzugeben, trennen Sie die einzelnen Dateinamen jeweils durch Drücken der EINGABETASTE. 

## <a name="request-path"></a>Anforderungspfad

Identifiziert Anforderungen, die den angegebenen Pfad in der anfordernden URL einschließen.

#### <a name="required-fields"></a>Pflichtfelder

Operator | Wert | Umwandlung der Groß-/Kleinschreibung
---------|-------|---------------
[Liste der Standardoperatoren](#standard-operator-list) | String, Int | Kleinbuchstaben, Großbuchstaben, Kürzung, Leerzeichen entfernen, URL-Codierung, URL-Decodierung

#### <a name="key-information"></a>Wichtige Informationen

- Wenn Sie diese Regelbedingung verwenden, beachten Sie, dass der Abgleich nach dem ersten `/` im Pfad beginnt. `https://www.<yourdomain>.com/folder/page` beginnt z. B. mit dem Abgleich von `folder/page`.

## <a name="standard-operator-list"></a>Liste der Standardoperatoren

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

Für numerische Operatoren wie *Kleiner als* oder *Größer als oder gleich* basiert der verwendete Vergleich auf der Länge. Der Wert in der Übereinstimmungsbedingung muss eine ganze Zahl sein, die der Länge entspricht, die Sie vergleichen möchten. 


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihr erstes [Regelmodul](front-door-tutorial-rules-engine.md) konfigurieren. 
- Weitere Informationen zu [Regelmodulaktionen](front-door-rules-engine-actions.md)
- Weitere Informationen zum [Azure Front Door-Regelmodul](front-door-rules-engine.md)
