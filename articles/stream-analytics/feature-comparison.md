---
title: Vergleich der Features von Azure Stream Analytics
description: Dieser Artikel vergleicht die für die Azure Stream Analytics-Cloud und IoT Edge-Aufträge in Azure-Portal, Visual Studio und Visual Studio Code unterstützten Features.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 26ef280950d14f7bb3a833edd466912540ff1059
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263190"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Vergleich der Features von Azure Stream Analytics

Mit Azure Stream Analytics können Sie mit [Azure-Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md) und [Visual Studio Code](quick-create-visual-studio-code.md) in der Cloud und in IoT Edge Streaminglösungen erstellen. Die Tabellen in diesem Artikel zeigen, welche Features von jeder Plattform für beide Auftragstypen unterstützt werden.

> [!NOTE]
> Visual Studio und Visual Studio Code Tools unterstützen keine Aufträge in den Regionen „China, Osten“, „China, Norden“, „Deutschland, Mitte“ oder „Deutschland, Nordosten“.

## <a name="cloud-job-features"></a>Cloudauftragsfeatures


|Funktion  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plattformübergreifend     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Erstellen von Skripts     |Ja         |Ja         |Ja         |
|IntelliSense-Skript     |Syntaxhervorhebung         |Syntaxhervorhebung</br>Codevervollständigung</br>Fehlermarkierung         |Syntaxhervorhebung</br>Codevervollständigung</br>Fehlermarkierung         |
|Definieren aller Typen von Eingaben, Ausgaben und Auftragskonfigurationen     |Ja         |Ja         |Ja         |
|Quellcodeverwaltung     |Nein         |Ja         |Ja         |
|CI/CD-Unterstützung     |Teilweise         |Ja         |Ja         |
|Mehrere Abfragen übergreifende Freigabe von Eingaben und Ausgaben     |Nein         |Ja         |Ja         |
|Abfragetests mit einer Beispieldatei     |Ja         |Ja        |Ja         |
|Lokales Testen mit Livedaten     |Nein         |Ja       |Ja      |
|Auflisten von Aufträgen und Anzeigen von Auftragsentitäten     |Ja         |Ja        |Ja         |
|Exportieren eines Auftrags in ein lokales Projekt     |Nein         |Ja         |Ja         |
|Senden, Starten und Beenden von Aufträgen     |Ja         |Ja         |Ja         |
|Anzeigen von Auftragsmetriken und Diagramm     |Ja         |Ja         |Ja         |
|Anzeigen von Auftragslaufzeitfehlern     |Ja         |Ja         |Ja         |
|Ressourcenprotokolle     |Ja         |Nein         |Ja         |
|Benutzerdefinierte Nachrichteneigenschaften     |Ja         |Ja         |Ja       |
|Benutzerdefinierte C#-Codefunktion und Deserialisierer|Schreibgeschützter Modus|Ja|Ja|
|JavaScript-UDF und -UDA     |Ja         |Ja         |Nur Windows         |
|Azure Machine Learning      |Ja        |Ja         |Ja         |
|Kompatibilitätsgrad     |1.0</br>1.1</br>1.2 (Standard)         |1.0</br>1.1</br>1.2 (Standard)           |1.0</br>1.1</br>1.2 (Standard)           |
|Integrierte Anomalieerkennungsfunktionen auf ML-Basis     |Ja         |Ja         |Ja         |
|Integrierte Geofunktionen     |Ja         |Ja         |Ja         |



## <a name="iot-edge-job-features"></a>IoT Edge-Auftragsfeatures

|Funktion  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Auftragserstellung     |Ja         |Ja         |Nein         |
|Quellcodeverwaltung     |Nein         |Ja         |Nein         |
|Exportieren eines Auftrags in ein lokales Projekt     |Nein         |Ja         |Nein         |
|Abfragetests mit einer Beispieldatei     |Ja         |Ja         |Nein         |
|Mehrere Abfragen übergreifende Freigabe von Eingaben und Ausgaben     |Nein         |Ja         |Nein         |
|C#-UDF     |Nein         |Ja         |Nein         |
|Übermitteln von Aufträgen     |Ja         |Ja         |Nein         |
|Auflisten von Aufträgen und Anzeigen von Auftragsentitäten     |Ja         |Ja         |Nein         |
|Anzeigen von Auftragsmetriken und Diagramm     |Ja         |Teilweise         |Nein         |
|Anzeigen von Auftragslaufzeitfehlern     |Ja         |Teilweise         |Nein         |
|CI/CD-Unterstützung     |Nein         |Nein         |Nein         |


## <a name="next-steps"></a>Nächste Schritte

* [Azure Stream Analytics auf IoT Edge](stream-analytics-edge.md)
* [Tutorial: Schreiben einer benutzerdefinierten C#-Funktion für einen Azure Stream Analytics IoT Edge-Auftrag (Vorschauversion)](stream-analytics-edge-csharp-udf.md)
* [Entwickeln von Stream Analytics IoT Edge-Aufträgen mit Visual Studio-Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Anzeigen von Azure Stream Analytics-Aufträgen mit Visual Studio](stream-analytics-vs-tools.md)
* [Erkunden von Azure Stream Analytics mit Visual Studio Code (Vorschauversion)](visual-studio-code-explore-jobs.md)


