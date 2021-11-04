---
title: Die Azure Speech-Befehlszeilenschnittstelle
titleSuffix: Azure Cognitive Services
description: Die Speech-Befehlszeilenschnittstelle ist ein Befehlszeilentool, mit dem der Speech-Dienst verwendet werden kann, ohne Code zu schreiben. Der Einrichtungsaufwand für die Speech-Befehlszeilenschnittstelle ist minimal, und Sie können sofort mit wichtigen Features des Speech-Diensts experimentieren, um zu ermitteln, ob die Anforderungen Ihrer Anwendungsfälle erfüllt werden können.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: eur
ms.custom: devx-track-azurecli
ms.openlocfilehash: 47aafd1ab1da55b1741c83794ad6a5e9e4c4ffef
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510510"
---
# <a name="what-is-the-speech-cli"></a>Worum handelt es sich bei der Speech-Befehlszeilenschnittstelle?

Die Speech-Befehlszeilenschnittstelle ist ein Befehlszeilentool, mit dem der Speech-Dienst verwendet werden kann, ohne Code zu schreiben. Der Einrichtungsaufwand für die Speech-Befehlszeilenschnittstelle ist minimal, und Sie können sofort mit wichtigen Features des Speech-Diensts experimentieren, um zu ermitteln, ob die Anforderungen Ihrer Anwendungsfälle erfüllt werden können. Innerhalb weniger Minuten können einfache Testworkflows wie etwa eine Batchspracherkennung auf der Grundlage eines Verzeichnisses mit Dateien oder eine Textsynthese für eine Sammlung von Zeichenfolgen aus einer Datei ausgeführt werden. Die Speech-Befehlszeilenschnittstelle kann jedoch nicht nur für einfache Workflows genutzt werden. Sie ist produktionsbereit und hochskalierbar, um umfangreichere Prozesse mit automatisierten Batches (`.bat`) oder Shellskripts auszuführen.

Über die Speech-Befehlszeilenschnittstelle stehen die meisten Features des Speech SDK zur Verfügung, und einige erweiterte Features und Anpassungen sind in der Speech-Befehlszeilenschnittstelle vereinfacht. Im Anschluss erfahren Sie, wann die Speech-Befehlszeilenschnittstelle und wann das Speech SDK verwendet werden sollte.

Verwenden Sie in folgenden Fällen die Speech-Befehlszeilenschnittstelle:
* Sie möchten mit minimalem Einrichtungs- und ohne Programmieraufwand mit Features des Speech-Diensts experimentieren.
* Sie haben relativ einfache Anforderungen an eine Produktionsanwendung, von der der Speech-Dienst genutzt wird.

Verwenden Sie in folgenden Fällen das Speech SDK:
* Sie möchten Funktionen des Speech-Diensts innerhalb einer bestimmten Sprache oder Plattform (z. B. C#, Python oder C++) integrieren.
* Sie haben komplexe Anforderungen, für die unter Umständen erweiterte Service Requests erforderlich sind oder die die Entwicklung von benutzerdefiniertem Verhalten (einschließlich Antwortstreaming) erfordern.

## <a name="core-features"></a>Wichtige Funktionen

* Spracherkennung: Konvertieren Sie Sprache aus Audiodateien oder direkt über ein Mikrofon in Text, oder erstellen Sie ein Transkript einer aufgezeichneten Unterhaltung.

* Sprachsynthese: Konvertieren Sie Text in Sprache, und verwenden Sie als Eingabe entweder Textdateien oder direkte Befehlszeileneingaben. Passen Sie Merkmale der Sprachausgabe mithilfe von [SSML-Konfigurationen](speech-synthesis-markup.md) und [Standardstimmen oder neuronalen Stimmen](speech-synthesis-markup.md#neural-and-custom-voices) an.

* Sprachübersetzung: Übersetzen Sie Audio aus einer Ausgangssprache in Text oder Audio in einer Zielsprache.

* Ausführung in Azure-Computeressourcen: Senden Sie Speech CLI-Befehle für die Ausführung in einer Azure-Remotecomputeressource unter Verwendung von `spx webjob`.

## <a name="get-started"></a>Erste Schritte

Informationen zu den ersten Schritten mit der Speech-Befehlszeilenschnittstelle finden Sie in der [Schnellstartanleitung](spx-basics.md). In diesem Artikel erfahren Sie, wie Sie einige einfache Befehle ausführen, und es werden auch etwas komplexere Befehle zum Ausführen von Batchvorgängen für Spracherkennung und -synthese gezeigt. Nach der Lektüre des Grundlagenartikels sollten Sie ausreichend mit der Syntax vertraut sein, um einige benutzerdefinierte Befehle schreiben oder einfache Speech-Dienstvorgänge automatisieren zu können.

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit der [Schnellstartanleitung zur Speech-Befehlszeilenschnittstelle](spx-basics.md)
- [Konfigurieren des Datenspeichers](./spx-data-store-configuration.md)
- Weitere Informationen zum [Ausführen von Batchvorgängen für die Speech-Befehlszeilenschnittstelle](./spx-batch-operations.md)
