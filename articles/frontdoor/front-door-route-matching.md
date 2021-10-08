---
title: Azure Front Door – Überwachung des Abgleichs mit Routingregeln | Microsoft-Dokumentation
description: Dieser Artikel bietet grundlegende Informationen dazu, wie Azure Front Door entscheidet, welche Routingregel für eine eingehende Anforderung verwendet werden soll.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 1aaabce5dc13098d183ee595d27b5b45a3fd0caa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586286"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Abgleichen von Anforderungen mit einer Routingregel

Wenn eine Anforderung nach dem Herstellen einer Verbindung und dem Ausführen eines TLS-Handshakes in einer Front Door-Umgebung eingeht, besteht eine der ersten Aktionen von Front Door in der Ermittlung, mit welcher Routingregel die Anforderung abgeglichen werden soll. Danach wird die definierte Aktion in der Konfiguration ausgeführt. Der folgende Artikel erläutert, wie Front Door bestimmt, welche Routenkonfiguration beim Verarbeiten einer HTTP-Anforderung verwendet werden soll.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktur einer Front Door-Routenkonfiguration
Die Konfiguration einer Front Door-Routingregel besteht aus zwei Hauptteilen: einer „linken Seite“ und einer „rechten Seite“. Die eingehende Anforderung wird mit der linken Seite der Route abgeglichen, während die rechte Seite definiert, wie die Anforderung verarbeitet wird.

### <a name="incoming-match-left-hand-side"></a>Abgleich der eingehenden Anforderung (linke Seite)
Die folgenden Eigenschaften bestimmen, ob die eingehende Anforderung mit der Routingregel (also der linken Seite) übereinstimmt:

* **HTTP-Protokolle** (HTTP/HTTPS)
* **Hosts** (z.B. www\.foo.com, \*.bar.com)
* **Pfade** (z.B.: /\*, /users/\*, /file.gif)

Diese Eigenschaften werden intern erweitert, sodass jede Kombination aus Protokoll/Host/Pfad ein potenzieller Treffer ist.

### <a name="route-data-right-hand-side"></a>Weiterleiten der Daten (rechte Seite)
Die Entscheidung darüber, wie die Anforderung verarbeitet werden soll, richtet sich danach, ob für die angegebene Route das Caching aktiviert ist oder nicht. Wenn keine zwischengespeicherte Antwort für die Anforderung vorhanden ist, wird die Anforderung an das geeignete Back-End im konfigurierten Back-End-Pool weitergeleitet.

## <a name="route-matching"></a>Routenabgleich
In diesem Abschnitt wird erläutert, wie der Abgleich mit einer bestimmten Front Door-Routingregel funktioniert. Das grundlegende Konzept besteht darin, immer den **ersten genauesten Treffer** zu verwenden, wobei nur die „linke Seite“ in Betracht gezogen wird.  Der Abgleich erfolgt zunächst basierend auf dem HTTP-Protokoll, dann basierend auf dem Front-End-Host, dann basierend auf dem Pfad.

### <a name="frontend-host-matching"></a>Abgleich mit Front-End-Hosts
Beim Abgleich mit Front-End-Hosts wird die unten definierte Logik verwendet:

1. Es wird nach allen Routingregeln mit einem exakten Treffer für den Host gesucht.
2. Wenn kein exakter Treffer für einen Front-End-Host gefunden wird, wird die Anforderung abgelehnt und der Fehler „400 – Ungültige Anforderung“ gesendet.

Um diesen Prozess genauer zu erklären, betrachten wir eine Beispielkonfiguration von Front Door-Routen (nur linke Seite):

| Routingregel | Front-End-Hosts | `Path` |
|-------|--------------------|-------|
| Ein | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Wenn die folgenden eingehenden Anforderungen an Front Door gesendet würden, würden sie mit den folgenden Routingregeln von oben abgeglichen:

| Front-End-Host für eingehende Anforderungen | Abgeglichene Routingregel(n) |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Fehler 400: Ungültige Anforderung |
| foo.adventure-works.com | C |
| contoso.com | Fehler 400: Ungültige Anforderung |
| www\.adventure-works.com | Fehler 400: Ungültige Anforderung |
| www\.northwindtraders.com | Fehler 400: Ungültige Anforderung |

### <a name="path-matching"></a>Pfadabgleich
Nach dem Ermitteln des Front-End-Hosts und dem Filtern möglicher Routingregeln, um sicherzustellen, dass nur die Routen mit diesem Front-End-Host verwendet werden, filtert Front Door die Routingregeln basierend auf dem Anforderungspfad. Hierbei wird eine ähnliche Logik wie bei den Front-End-Hosts verwendet:

1. Es wird nach allen Routingregeln mit einem exakten Treffer für den Pfad gesucht.
2. Wenn kein exakter Pfad vorhanden ist, wird nach Routingregeln mit einem übereinstimmenden Platzhalterpfad gesucht.
3. Wenn keine Routingregeln mit einem übereinstimmenden Pfad gefunden werden, wird die Anforderung abgelehnt und eine HTTP-Antwort „400: Ungültige Anforderung“ gesendet.

>[!NOTE]
> Alle Pfade ohne Platzhalter gelten als exakte Treffer für den Pfad. Selbst wenn ein Pfad mit einem Schrägstrich endet, wird er dennoch als exakter Treffer betrachtet.

Sehen wir uns weitere Beispiele an, um dies genauer zu erläutern:

| Routingregel | Front-End-Host    | `Path`     |
|-------|---------|----------|
| Ein     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

In dieser Konfiguration würde sich folgende Treffertabelle ergeben:

| Eingehende Anforderung    | Übereinstimmende Route |
|---------------------|---------------|
| www\.contoso.com/            | Ein             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Wenn keine Routingregeln für einen exakt übereinstimmenden Front-End-Host mit einem Routingpfad zur Abdeckung aller Fälle (`/*`) vorhanden sind, gibt es für keine Routingregel einen Treffer.
>
> Beispielkonfiguration:
>
> | Route | Host             | `Path`    |
> |-------|------------------|---------|
> | Ein     | profile.contoso.com | /api/\* |
>
> Treffertabelle:
>
> | Eingehende Anforderung       | Übereinstimmende Route |
> |------------------------|---------------|
> | profile.domain.com/other | Keine. Fehler 400: Ungültige Anforderung |

### <a name="routing-decision"></a>Routingentscheidung

Nachdem Sie eine Zuordnung zu einer einzelnen Front Door-Routingregel vorgenommen haben, wählen Sie aus, wie die Anforderung verarbeitet werden soll. Wenn Front Door über eine zwischengespeicherte Antwort für die übereinstimmende Routingregel verfügt, wird die zwischengespeicherte Antwort an den Client zurückgesendet. Wenn Front Door keine zwischengespeicherte Antwort für die übereinstimmende Routingregel aufweist, wird als nächstes ausgewertet, ob Sie [URL-Rewrite (einen benutzerdefinierten Weiterleitungspfad)](front-door-url-rewrite.md) für die übereinstimmende Routingregel konfiguriert haben. Wenn kein benutzerdefinierter Weiterleitungspfad definiert wurde, wird die Anforderung unverändert an das entsprechende Back-End im konfigurierten Back-End-Pool weitergeleitet. Wenn ein benutzerdefinierter Weiterleitungspfad definiert wurde, wird der Anforderungspfad gemäß dem definierten [benutzerdefinierten Weiterleitungspfad](front-door-url-rewrite.md) aktualisiert und dann an das Back-End weitergeleitet.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
