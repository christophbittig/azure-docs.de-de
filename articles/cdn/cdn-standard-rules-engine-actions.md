---
title: Aktionen in der Standardregel-Engine für Azure CDN | Microsoft-Dokumentation
description: Referenzdokumentation für Aktionen in der Standardregel-Engine für Azure Content Delivery Network (Azure CDN).
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: duau
ms.openlocfilehash: 32c0b0a245f864601110505af0ce81ad4e9b0b0e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461378"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Aktionen in der Standardregel-Engine für Azure CDN

In der [Standardregel-Engine](cdn-standard-rules-engine.md) für Azure Content Delivery Network (Azure CDN) besteht eine Regel aus mindestens einer Übereinstimmungsbedingung und einer Aktion. In diesem Artikel werden die Aktionen, die Sie in der Standardregel-Engine für Azure CDN verwenden können, ausführlich beschrieben.

Der zweite Teil einer Regel ist eine Aktion. Eine Aktion definiert das Verhalten, das auf den Anforderungstyp angewendet wird, der durch mindestens eine Übereinstimmungsbedingung bestimmt wurde.

## <a name="actions"></a>Aktionen

Die folgenden Aktionen können auch in der Standardregel-Engine für Azure CDN verwendet werden. 

### <a name="cache-expiration"></a>Cacheablauf

Verwenden Sie diese Aktion, um die Gültigkeitsdauer (TTL) des Endpunkts für Anforderungen zu überschreiben, die die Bedingungen für die Regelübereinstimmung angeben.

#### <a name="required-fields"></a>Pflichtfelder

Cacheverhalten |  BESCHREIBUNG              
---------------|----------------
Cache umgehen | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, wird der Inhalt nicht zwischengespeichert.
Überschreiben | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, wird der vom Ursprung zurückgegebene TTL-Wert mit dem in der Aktion angegebenen Wert überschrieben. Dieses Verhalten wird nur angewendet, wenn die Antwort zwischengespeichert werden kann. Für den Antwortheader „cache-control“ mit den Werten „no-cache“, „private“ und „no-store“ ist die Aktion nicht anwendbar.
Bei Fehlen festlegen | Wenn diese Option ausgewählt ist, die Regel übereinstimmt und vom Ursprung kein TTL-Wert zurückgegeben wurde, legt die Regel den TTL-Wert auf den in der Aktion angegebenen Wert fest. Dieses Verhalten wird nur angewendet, wenn die Antwort zwischengespeichert werden kann. Für den Antwortheader „cache-control“ mit den Werten „no-cache“, „private“ und „no-store“ ist die Aktion nicht anwendbar.

#### <a name="additional-fields"></a>Zusätzliche Felder

Tage | Stunden | Minuten | Sekunden
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Abfragezeichenfolge für Cacheschlüssel

Verwenden Sie diese Aktion, um den Cacheschlüssel basierend auf Abfragezeichenfolgen zu ändern.

#### <a name="required-fields"></a>Pflichtfelder

Verhalten | BESCHREIBUNG
---------|------------
Einschließen | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, werden bei der Generierung des Cacheschlüssels die in den Parametern angegebenen Abfragezeichenfolgen berücksichtigt. 
Zwischenspeichern jeder eindeutigen URL | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, verfügt jede eindeutige URL über einen eigenen Cacheschlüssel. 
Exclude | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, werden bei der Generierung des Cacheschlüssels die in den Parametern angegebenen Abfragezeichenfolgen ausgeschlossen.
Ignorieren von Abfragezeichenfolgen | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, werden Abfragezeichenfolgen bei der Generierung des Cacheschlüssels nicht berücksichtigt. 

### <a name="modify-request-header"></a>Anforderungsheader ändern

Verwenden Sie diese Aktion, um Header zu ändern, die in den an ihren Ursprung gesendeten Anforderungen vorhanden sind.

#### <a name="required-fields"></a>Pflichtfelder

Aktion | HTTP-Headername | Wert
-------|------------------|------
Anfügen | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, wird der im **Headernamen** angegebene Header mit dem angegebenen Wert der Anforderung hinzugefügt. Wenn der Header bereits vorhanden ist, wird der Wert an den vorhandenen Wert angefügt. | String
Overwrite | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, wird der im **Headernamen** angegebene Header mit dem angegebenen Wert der Anforderung hinzugefügt. Wenn der Header bereits vorhanden ist, wird der vorhandene Wert von dem angegebenen Wert überschrieben. | String
Löschen | Wenn diese Option ausgewählt ist, die Regel übereinstimmt und der in der Regel angegebene Header vorhanden ist, wird der Header aus der Anforderung gelöscht. | String

### <a name="modify-response-header"></a>Antwortheader ändern

Verwenden Sie diese Aktion, um Header zu ändern, die in den an Ihre Clients zurückgegebenen Antworten vorhanden sind.

#### <a name="required-fields"></a>Pflichtfelder

Aktion | HTTP-Headername | Wert
-------|------------------|------
Anfügen | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, wird der im **Headernamen** angegebene Header mit dem angegebenen **Wert** der Antwort hinzugefügt. Wenn der Header bereits vorhanden ist, wird der **Wert** an den vorhandenen Wert angefügt. | String
Overwrite | Wenn diese Option ausgewählt ist und die Regel übereinstimmt, wird der im **Headernamen** angegebene Header mit dem angegebenen **Wert** der Antwort hinzugefügt. Wenn der Header bereits vorhanden ist, überschreibt der **Wert** den vorhandenen Wert. | String
Löschen | Wenn diese Option ausgewählt ist, die Regel übereinstimmt und der in der Regel angegebene Header vorhanden ist, wird der Header aus der Antwort gelöscht. | String

### <a name="url-redirect"></a>Umleitungs-URL

Leiten Sie mit dieser Aktion Clients an eine neue URL um. 

#### <a name="required-fields"></a>Pflichtfelder

Feld | BESCHREIBUNG 
------|------------
type | Wählen Sie den Antworttyp aus, der an den Anforderer zurückgegeben werden soll: „Gefunden“ (302), „Verschoben“ (301), „Temporäre Umleitung“ (307) und „Permanente Umleitung“ (308).
Protocol | Übereinstimmungsanforderung, HTTP, HTTPS.
Hostname | Wählen Sie den Hostnamen aus, an den die Anforderung umgeleitet werden soll. Lassen Sie das Feld leer, um den eingehenden Host beizubehalten.
Pfad | Definieren Sie den bei der Umleitung zu verwendenden Pfad. Lassen Sie das Feld leer, um den eingehenden Pfad beizubehalten.  
Abfragezeichenfolge | Definieren Sie die bei der Umleitung zu verwendende Abfragezeichenfolge. Lassen Sie das Feld leer, um die eingehende Abfragezeichenfolge beizubehalten. 
Fragment | Definieren Sie das bei der Umleitung zu verwendende Fragment. Lassen Sie das Feld leer, um das eingehende Fragment beizubehalten. 

Es wird dringend empfohlen, eine absolute URL zu verwenden. Bei Verwendung einer relativen URL werden Azure CDN-URLs möglicherweise an einen ungültigen Pfad umgeleitet. 

### <a name="url-rewrite"></a>URL Rewrite

Verwenden Sie diese Aktion, um den Pfad einer Anforderung auf dem Weg zu Ihrem Ursprung erneut zu generieren.

#### <a name="required-fields"></a>Pflichtfelder

Feld | BESCHREIBUNG 
------|------------
Quellmuster | Definieren Sie das Quellmuster im URL-Pfad, das ersetzt werden soll. Derzeit verwendet das Quellmuster eine auf Präfixen basierende Übereinstimmung. Verwenden Sie einen Schrägstrich ( **/** ) als Quellmusterwert, um alle URL-Pfade abzugleichen.
Destination | Definieren Sie den Zielpfad, der für das erneute Generieren verwendet werden soll. Der Zielpfad überschreibt das Quellmuster.
Nicht übereinstimmenden Pfad beibehalten | Wenn **Ja** ausgewählt ist, wird der verbleibende Pfad nach dem Quellmuster an den neuen Zielpfad angefügt. 

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
- [Referenz zur Standardregel-Engine](cdn-standard-rules-engine-reference.md)
- [Übereinstimmungsbedingungen in der Standardregel-Engine](cdn-standard-rules-engine-match-conditions.md)
- [Erzwingen von HTTPS mithilfe der Standardregel-Engine](cdn-standard-rules-engine.md)
