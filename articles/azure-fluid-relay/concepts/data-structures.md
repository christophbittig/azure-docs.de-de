---
title: Verteilte Datenstrukturen
description: Verteilte Datenstrukturen sind die Bausteine von Fluid-Anwendungen
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/data-structures/overview/
ms.openlocfilehash: c79fea26de7cda6d3097c2a8c9403420451fe665
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661797"
---
# <a name="distributed-data-structures"></a>Verteilte Datenstrukturen

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Das Fluid Framework stellt Entwicklern verteilte Datenstrukturen (Distributed Data Structures, DDSes) bereit, die automatisch sicherstellen, dass jeder verbundene Client Zugriff auf denselben Zustand hat. Die von DDSes bereitgestellten APIs sind so konzipiert, dass sie Programmierern vertraut sind, die bereits allgemeine Datenstrukturen verwendet haben.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie mit der [Einführung in verteilte Datenstrukturen](https://fluidframework.com/docs/build/dds/) auf fluidframework.com vertraut sind.

Eine verteilte Datenstruktur verhält sich wie eine lokale Datenstruktur. Ihr Code kann Daten hinzufügen, Daten entfernen, aktualisieren usw. Eine DDS ist jedoch kein lokales Objekt. Eine DDS kann auch von anderen Clients geändert werden, die denselben übergeordneten Container der DDS verfügbar machen. Da Benutzer gleichzeitig die gleiche DDS ändern können, müssen Sie berücksichtigen, welche DDS für die Modellierung Ihrer Daten verwendet werden soll.

> [!NOTE]
> **Bedeutung von „gleichzeitig“**
>
> Es wird davon gesprochen, dass zwei oder mehr Clients *gleichzeitig* eine Änderung vornehmen, wenn sie jeweils eine Änderung vornehmen, bevor sie die Änderungen der anderen vom Server erhalten haben.

Die Auswahl der richtigen Datenstruktur für Ihr Szenario kann die Leistung und Codestruktur Ihrer Anwendung verbessern.

DDSes unterscheiden sich je nach drei Merkmalen:

- **Grundlegende Datenstruktur**: z. B. Schlüssel-Wert-Paar, Sequenz oder Warteschlange.
- **Clientautonomie**: Eine *optimistische* DDS ermöglicht jedem Client, einen Wert zu ändern, und der neue Wert wird an alle anderen Clients weitergeleitet. Eine *Konsens*-DDS lässt jedoch nur dann eine Änderung zu, wenn sie von anderen Clients durch einen Konsensprozess akzeptiert wird.
- **Mergerichtlinie**: Die Richtlinie, die bestimmt, wie in Konflikt stehende Änderungen von Clients aufgelöst werden.

Im Folgenden haben wir die Datenstrukturen aufgelistet und beschrieben, wann sie am nützlichsten sind.

## <a name="key-value-data"></a>Schlüssel-Wert-Daten

Diese DDSes werden zum Speichern von Schlüssel-Wert-Daten verwendet. Sie sind optimistisch und verwenden eine Mergerichtlinie, bei der der letzte Schreiber gewinnt. Obwohl der Wert eines Paars ein komplexes Objekt sein kann, kann der Wert eines bestimmten Paars nicht direkt bearbeitet werden. der gesamte Wert muss durch einen neuen Wert ersetzt werden, der die gewünschten Bearbeitungen enthält (whole-for-whole).

- **SharedMap**: eine grundlegende Schlüssel-Wert-Datenstruktur.

### <a name="key-value-scenarios"></a>Schlüssel-Wert-Szenarien

Schlüssel-Wert-Datenstrukturen sind die gängigste Wahl für viele Szenarien.

- Daten zur Benutzerpräferenz.
- Aktueller Status einer Umfrage.
- Die Konfiguration einer Ansicht.

### <a name="common-issues-and-best-practices-for-key-value-ddses"></a>Häufige Probleme und bewährte Methoden für Schlüssel-Wert-DDSes

- Das Speichern eines Zählers in einer SharedMap hat ein unerwartetes Verhalten. Nutzen Sie stattdessen den SharedCounter.
- Das Speichern von Arrays, Listen oder Protokollen in einem Schlüssel-Wert-Eintrag kann zu unerwartetem Verhalten führen, da Benutzer Teile eines Eintrags nicht gemeinsam ändern können. Versuchen Sie, das Array oder die Listendaten in einer SharedSequence oder SharedInk zu speichern.
- Das Speichern vieler Daten in einem Schlüssel-Wert-Eintrag kann zu Leistungs- oder Mergeproblemen führen. Jedes Update aktualisiert den gesamten Wert, anstatt zwei Updates zusammenzuführen. Versuchen Sie, die Daten auf mehrere Schlüssel aufzuteilen.

## <a name="sequences"></a>Sequenzen

Diese DDSes werden zum Speichern sequenzieller Daten verwendet. Sie sind optimistisch. Sequenzdatenstrukturen sind nützlich, wenn Sie Daten an einem angegebenen Index in einer Liste oder einem Array hinzufügen oder entfernen müssen. Im Gegensatz zu den Schlüssel-Wert-Datenstrukturen weisen Sequenzen eine sequenzielle Reihenfolge auf und können gleichzeitige Einfügungsvorgänge von mehreren Benutzern verarbeiten.

- **SharedNumberSequence**: eine Sequenz von Zahlen.
- **SharedObjectSequence**: eine Sequenz einfacher Objekte.

### <a name="sequence-scenarios"></a>Sequenzszenarien

- Listen
- Zeitpläne

### <a name="common-issues-and-best-practices-for-sequence-ddses"></a>Häufige Probleme und bewährte Methoden für Sequenz-DDSes

- Speichern Sue nur unveränderliche Daten als Element in einer Sequenz. Die einzige Möglichkeit, den Wert eines Elements zu ändern, besteht darin, es zuerst aus der Sequenz zu entfernen und dann einen neuen Wert an der Position einzufügen, an der sich der alte Wert befand. Da andere Clients jedoch einfügen und entfernen können, gibt es keine zuverlässige Möglichkeit, den neuen Wert an die gewünschte Position zu bringen.

## <a name="strings"></a>Zeichenfolgen

SharedString DDS wird für unstrukturierte Textdaten verwendet, die gemeinsam bearbeitet werden können. Sie ist optimistisch.

- `SharedString` – eine Datenstruktur für die Verarbeitung von Zusammenarbeitstext.

### <a name="string-scenarios"></a>Stringszenarien

- Text-Editoren

## <a name="see-also"></a>Siehe auch

Weitere Informationen zu DDSes und deren Verwendung finden Sie in den folgenden Abschnitten von fluidframework.com:

- [Einführung in verteilte Datenstrukturen](https://fluidframework.com/docs/build/dds/).
- [Typen verteilter Datenstrukturen](https://fluidframework.com/docs/data-structures/overview/).
