---
title: Benutzerdefinierte Berichte aus Verizon | Microsoft-Dokumentation
description: 'Mithilfe der folgenden Berichte können Sie Verwendungsmuster für Ihr CDN anzeigen: Bandbreite, Übertragene Daten, Treffer, Cachestatus, Cachetrefferquote, Übertragene IPV4/IPV6-Daten.'
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2017
ms.author: duau
ms.openlocfilehash: f461e310b44063035e2e21e4dbeb8dd340ea053b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434786"
---
# <a name="custom-reports-from-verizon"></a>Benutzerdefinierte Berichte aus Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Indem Sie benutzerdefinierte Verizon-Berichte über das Verwaltungsportal für Verizon-Profile verwenden, können Sie den Typ der zu sammelnden Daten für Edgeb CNAMEs-Berichte definieren.


## <a name="accessing-verizon-custom-reports"></a>Zugreifen auf benutzerdefinierte Verizon-Berichte
1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten** .
   
    ![Schaltfläche „Verwalten“ für CDN-Profile](./media/cdn-reports/cdn-manage-btn.png)
   
    Das CDN-Verwaltungsportal wird geöffnet.
2. Zeigen Sie auf die Registerkarte **Analysen** und dann auf das Flyout **Benutzerdefinierte Berichte**. Klicken Sie auf **Edge CNAMEs**.
   
    ![CDN-Verwaltungsportal – Menü „Benutzerdefinierte Berichte“](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Benutzerdefinierter Edge CNAMEs-Bericht
Der benutzerdefinierte Edge CNAMEs-Bericht enthält Statistiken zu Treffern und übertragenen Daten für Edge CNAMEs, für die die Protokollierung für benutzerdefinierte Berichte aktiviert wurde. Edge CNAMEs bestehen aus Azure CDN-Endpunkthostnamen und allen zugeordneten benutzerdefinierten Domänenhostnamen. 

Die Datenprotokollierung für benutzerdefinierte Berichte beginnt eine Stunde nachdem Sie die benutzerdefinierte Berichtsfunktion eines Edge CNAME aktiviert haben. Sie können Berichtsdaten anzeigen, indem Sie einen Edge CNAMEs-Bericht für eine bestimmte Plattform oder für alle Plattformen generieren. Der Umfang dieses Berichts beschränkt sich auf die Edge CNAMEs, für die im angegebenen Zeitraum benutzerdefinierte Berichtsdaten gesammelt wurden. Der Edge CNAMEs-Bericht besteht aus einem Diagramm und einer Datentabelle für die 10 wichtigsten Edge CNAMEs entsprechend der in der Option „Metriken“ definierten Metrik. 

Definieren Sie die folgenden Berichtsoptionen, um einen benutzerdefinierten Bericht zu generieren:

- Metriken: Die folgenden Optionen werden unterstützt:

   - Treffer: gibt die Gesamtanzahl der Anforderungen an, die an einen Edge CNAME weitergeleitet werden, für den die benutzerdefinierte Berichtsfunktion aktiviert ist. Diese Metrik umfasst nicht den an den Client zurückgegebenen Statuscode.

   - Übertragene Daten: gibt die Gesamtmenge der von den Edgeservern an die HTTP-Clients (z.B. Webbrowser) übertragenen Daten für Anforderungen an, die an einen Edge CNAME weitergeleitet werden, für den die benutzerdefinierte Berichtsfunktion aktiviert ist. Die Menge der übertragenen Daten wird durch Hinzufügen der HTTP-Antwortheader zum Antworttext berechnet. Daher ist die Menge der übertragenen Daten für jedes Objekt größer als seine tatsächliche Dateigröße.

- Gruppierungen: bestimmt den Typ der Statistiken, die unterhalb des Balkendiagramms angezeigt werden Die folgenden Optionen werden unterstützt:

   - HTTP-Antwortcodes: sortiert die Statistiken nach dem HTTP-Antwortcode (z.B. 200, 403 usw.), der an den Client zurückgegeben wird 

   - Cachestatus: sortiert die Statistiken nach Cachestatus.


Um den Datumsbereich für den Bericht festzulegen, können Sie in der Dropdownliste einen vordefinierten Datumsbereich auswählen, z.B. **Heute** oder **Diese Woche**, oder Sie können **Benutzerdefiniert** auswählen und durch Klicken auf die Kalendersymbole manuell einen Datumsbereich eingeben. 

Klicken Sie nach dem Auswählen des Datumsbereichs auf **Start**, um den Bericht zu generieren.

Sie können die Daten im Excel-Format exportieren, indem Sie auf das Excel-Symbol rechts neben der Schaltfläche **Start** klicken.

![CNAMEs-Bericht](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Felder des benutzerdefinierten Edge CNAMEs-Berichts

| Feld                     | BESCHREIBUNG   |
|---------------------------|---------------|
| 2xx                       | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die einen 2xx HTTP-Statuscode ergeben (z.B. 200 OK). |
| 3xx                       | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die einen 3xx HTTP-Statuscode ergeben (z.B. 302 Gefunden oder 304 Nicht geändert). |
| 4xx                       | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die einen 4xx HTTP-Statuscode ergeben (z.B. 400 Ungültige Anforderung, 403 Unzulässig oder 404 Nicht gefunden). |
| 5xx                       | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die einen 5xx HTTP-Statuscode ergeben (z.B. 500 Interner Serverfehler oder 502 Ungültiges Gateway). |
| Cachetreffer %               | Gibt den Prozentsatz der zwischenspeicherbaren Anforderungen an, die direkt aus dem Cache für die anfordernde Person bereitgestellt wurden. |
| Cachetreffer                | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die einen Cachetreffer ergeben (z.B. TCP_EXPIRED_HIT, TCP_HIT oder TCP_PARTIAL_HIT). Ein Cachetreffer wird erzielt, wenn eine zwischengespeicherte Version des angeforderten Inhalts gefunden wird. |
| Übertragene Daten (MB)     | Gibt die Gesamtmenge der übertragenen Daten (MB) von den Edgeservern an HTTP-Clients (z.B. Webbrowser) für den Edge CNAME an. Die Menge der übertragenen Daten wird durch Hinzufügen der HTTP-Antwortheader zum Antworttext berechnet. Daher ist die Menge der übertragenen Daten für jedes Objekt größer als seine tatsächliche Dateigröße. |
| BESCHREIBUNG               | Identifiziert einen Edge CNAME nach seinem Hostnamen. |
| Treffer                      | Gibt die Gesamtanzahl der Anforderungen an den Edge CNAME an. |
| Cachefehler                    | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die zu einem Cachefehler führen (z.B. TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS oder TCP_MISS). Ein Cachefehler tritt auf, wenn der angeforderte Inhalt nicht auf dem Edgeserver, der die Anforderung berücksichtigt, zwischengespeichert wurde. | 
| Kein Cache                  | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die den Cachestatuscode „CONFIG_NOCACHE“ ergeben.  |
| Sonstiges                     | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die einen HTTP-Statuscode ergeben, der außerhalb des Bereichs 2xx–5xx liegt. |
| Plattform                  | Gibt die Plattform an, die den Datenverkehr des Edge CNAME verarbeitet. |
| Nicht zugewiesen               | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, für die keine Informationen zum Cachestatuscode oder HTTP-Statuscode protokolliert wurden.  |
| Nicht zwischenspeicherbar               | Gibt die Gesamtanzahl der Anforderungen oder übertragenen Daten (MB) für den Edge CNAME an, die den Cachestatuscode „UNCACHEABLE“ ergeben.  |


## <a name="considerations"></a>Überlegungen
Berichte können nur über die letzten 18 Monate generiert werden.

