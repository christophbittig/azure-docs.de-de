---
title: Blockblobspeicher mit Premium-Leistung
titleSuffix: Azure Storage
description: Erzielen Sie niedrigere und konsistente Latenzen für Azure Storage-Workloads, die schnelle und konsistente Antwortzeiten erfordern.
author: normesta
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8f394bb69197e9268fb986f7a8b971206aa74fc4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444836"
---
# <a name="premium-block-blob-storage-accounts"></a>Blockblobspeicher mit Premium-Leistung

Premium-Konten für Blockblobspeicher machen Daten über Hochleistungshardware verfügbar. Daten werden auf SSD-Datenträgern (Solid State Drives) gespeichert, die für niedrige Latenz optimiert sind. Der Durchsatz ist bei SSDs im Vergleich zu herkömmlichen Festplatten höher. Die Dateiübertragung ist viel schneller, da Daten auf sofort zugänglichen Speicherchips gespeichert werden. Auf alle Teile eines Laufwerks kann gleichzeitig zugegriffen werden. Im Gegensatz dazu hängt die Leistung einer Festplatte (HDD) von der Nähe der Daten zu den Lese-/Schreibköpfen ab. 

## <a name="high-performance-workloads"></a>Hochleistungsworkloads

Premium-Konten für Blockblobspeicher eignen sich ideal für Workloads, die schnelle und konsistente Antwortzeiten erfordern und/oder eine hohe Anzahl von Ein-/Ausgabevorgängen pro Sekunde (IOP) aufweisen. Beispielworkloads umfassen:

- **Interaktive Workloads**. Hochgradig interaktive Anwendungen und Echtzeitanwendungen müssen schnell Daten schreiben. Für E-Commerce- und Kartierungsanwendungen sind häufig sofortige Updates und Benutzerfeedback erforderlich. Beispielsweise werden in einer E-Commerce-Anwendung weniger häufig angezeigte Elemente wahrscheinlich nicht zwischengespeichert. Sie müssen dem Kunden jedoch sofort bei Bedarf angezeigt werden. Interaktive Bearbeitungs- oder Onlinespielanwendungen mit mehreren Playern beeindrucken durch bereitgestellte Echtzeitupdates.  

- **IoT-/Streaminganalysen** In einem IoT-Szenario können jede Sekunde viele kleinere Schreibvorgänge in die Cloud übermittelt werden. Große Datenmengen können aufgenommen, zu Analysezwecken aggregiert und dann fast unmittelbar gelöscht werden. Dank der hohen Aufnahmefähigkeiten des Premium-Blockblobspeichers kann er für diesen Workloadtyp effizient eingesetzt werden.

- **Künstliche Intelligenz/Machine Learning (KI/ML)** . Bei AI/ML geht es um den Nutzung und Verarbeitung verschiedener Datentypen wie visuelle Elemente, Sprache und Text. Dieser High Performance Compting-Workloadtyp verarbeitet große Datenmengen, die eine schnelle Reaktion und effiziente Erfassungszeiten für die Datenanalyse erfordern.

## <a name="cost-effectiveness"></a>Kosteneffizienz
  
Premium-Konten für Blockblobspeicher haben höhere Speicherkosten, aber niedrigere Transaktionskosten im Vergleich zu Standardkonten vom Typ „Universell V2“. Wenn Ihre Anwendungen und Workloads eine große Anzahl von Transaktionen ausführen, kann Premium-Blockblobspeicher kostengünstig sein, insbesondere bei Workloads mit vielen Schreibvorgängen.

In den meisten Fällen sind Workloads, die mehr als 35 bis 40 Transaktionen pro Sekunde pro Terabyte (TPS/TB) ausführen, gute Kandidaten für diesen Kontotyp. Wenn Ihre Workload beispielsweise 500 Millionen Lesevorgänge und 100 Millionen Schreibvorgänge in einem Monat ausführt, können Sie den TPS/TB wie folgt berechnen: 

- Schreibtransaktionen pro Sekunde = 100.000.000/(30 x 24 x 60 x 60) = **39** (_gerundet auf die nächste ganze Zahl_) 

- Lesetransaktionen pro Sekunde = 500.000.000/(30 x 24 x 60 x 60) = **193** (_gerundet auf die nächste ganze Zahl_)

- Transaktionen pro Sekunde gesamt = **193** + **39** = **232** 

- Angenommen, Ihr Konto hat durchschnittlich **5 TB** Daten, dann wäre TPS/TB **230/5** = **46**. 

> [!NOTE]
> Die Preise unterscheiden sich je nach Vorgang und Region. Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Preise zwischen den Leistungsstufen „Standard“ und „Premium“ zu vergleichen. 

Die folgende Tabelle zeigt die Kosteneffizienz von Premium-Konten für Blockblobspeicher. Die Zahlen in dieser Tabelle basieren auf einem Azure Data Lake Storage Gen2-fähigen Premium-Konto für Blockblobspeicher (auch als [Premium-Tarif für Azure Data Lake Storage](premium-tier-for-data-lake-storage.md) bezeichnet). Die einzelnen Spalten stellen die Anzahl der Transaktionen in einem Monat dar. Die einzelnen Zeilen stellen den Prozentsatz von Transaktionen dar, die Lesetransaktionen sind. Die einzelnen Zellen in der Tabelle zeigen den Prozentsatz der Kosteneinsparung in Verbindung mit einem Prozentsatz der Lesetransaktionen und der Anzahl ausgeführter Transaktionen.

Angenommen, Ihr Konto befindet sich in der Region USA, Osten 2, die Anzahl der Transaktionen mit Ihrem Konto übersteigt 90 Mio. und 70 % dieser Transaktionen sind Lesetransaktionen. Dann sind Premium-Konten für Blockblobspeicher wirtschaftlicher.

> [!div class="mx-imgBorder"]
> ![Leistungstabelle](./media/storage-blob-performance-tiers/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> Wenn Sie es vorziehen, die Wirtschaftlichkeit auf Grundlage der Anzahl der Transaktionen pro Sekunde für jedes TB Daten auszuwerten, können Sie die Spaltenüberschriften unten in der Tabelle nutzen. 

## <a name="premium-scenarios"></a>Szenarios für Premium

Dieser Abschnitt enthält Beispiele aus der Praxis dafür, wie einige unserer Azure Storage-Partner Premium-Blockblobspeicher verwenden. Bei einigen von ihnen wird auch Azure Data Lake Storage Gen2 aktiviert, wodurch eine hierarchische Dateistruktur eingeführt wird, die die Transaktionsleistung in bestimmten Szenarios weiter verbessern kann. 

> [!TIP]
> Wenn Sie einen analytischen Anwendungsfall haben, empfehlen wir Ihnen dringend, Azure Data Lake Storage Gen2 zusammen mit einem Premium-Blockblob-Storage-Konto zu verwenden. 

Dieser Abschnitt enthält folgende Beispiele:

- [Schnelle Datenhydration](#fast-data-hydration)
- [Anwendungen für interaktive Bearbeitung](#interactive-editing-applications)
- [Datenvisualisierungssoftware](#data-visualization-software)
- [E-Commerce-Unternehmen](#e-commerce-businesses)
- [Interaktive Analysen](#interactive-analytics)
- [Datenverarbeitungspipelines](#data-processing-pipelines)
- [Internet der Dinge (IoT)](#internet-of-things-iot)
- [Machine Learning](#machine-learning)
- [Streaminganalysen in Echtzeit](#real-time-streaming-analytics)

### <a name="fast-data-hydration"></a>Schnelle Datenhydration

Premium-Blockblobspeicher kann Ihnen dabei helfen, für Ihre Umgebung schnell *eine Hydration auszuführen* oder sie schnell zu aktivieren. In Branchen wie dem Bankwesen erfordern bestimmte gesetzliche Anforderungen möglicherweise, dass Unternehmen ihre Umgebungen regelmäßig abbauen und dann von Grund auf neu einrichten. Die Daten, die zur Hydration ihrer Umgebung verwendet werden, müssen schnell geladen werden. 

Einige unserer Partner speichern jede Woche eine Kopie ihrer MongoDB-Instanz in einem Premium-Konto für Blockblobspeicher. Das System wird dann abgebaut. Damit das System schnell wieder online geschaltet wird, wird die neueste Kopie der MongoDB-Instanz gelesen und geladen. Zu Überwachungszwecken werden vorherige Kopien für einen bestimmten Zeitraum im Cloudspeicher aufbewahrt.

### <a name="interactive-editing-applications"></a>Anwendungen für interaktive Bearbeitung

In Anwendungen, in denen mehrere Benutzer denselben Inhalt bearbeiten, ist die Geschwindigkeit von Updates entscheidend für einen reibungslosen Ablauf. 

Einige unserer Partner entwickeln Software für die Videobearbeitung. Jedes Update, das ein Benutzer an einem Video vornimmt, ist sofort für andere Benutzer sichtbar. Benutzer können sich auf ihre Aufgaben konzentrieren, anstatt auf Updates von Inhalten zu warten. Die niedrigen Latenzzeiten, die mit Premium-Blockblob-Speicher verbunden sind, tragen zu diesem nahtlosen und kollaborativen Erlebnis bei.  

### <a name="data-visualization-software"></a>Datenvisualisierungssoftware

Benutzer können mit Datenvisualisierungssoftware viel produktiver arbeiten, wenn die Renderingzeit schnell ist. 
 
Wir haben gesehen, dass Unternehmen in der Kartierungsbranche Kartierungs-Editoren verwenden, um Probleme mit Karten zu erkennen. Diese Editoren verwenden Daten, die aus GPS-Kundendaten (Global Positioning System) generiert werden. Zum Erstellen von Kartenüberlappungen rendert die Bearbeitungssoftware kleine Abschnitte einer Karte durch schnelles Ausführen von Key-Lookups. 

In einem Fall verwendete ein Partner vor dem Einsatz von Premium-Blockblob-Speicher HBase-Cluster, die durch Standard-V2-Speicher gesichert waren. Es wurde jedoch teuer, große Cluster ständig auszuführen. Dieser Partner hat sich entschieden, von dieser Architektur abzuweichen, und stattdessen Premium-Blockblobspeicher für schnelle Key-Lookups verwendet. Um Überschneidungen zu erzeugen, nutzten sie REST-APIs, um Kacheln entsprechend den GPS-Koordinaten zu rendern. Das Premium-Konto für Blockblobspeicher bot ihm eine kostengünstige Lösung, und Latenzen waren wesentlich besser vorhersagbar.

### <a name="e-commerce-businesses"></a>E-Commerce-Unternehmen

E-Commerce-Unternehmen unterstützen nicht nur ihre kundenseitigen Stores, sondern stellen möglicherweise auch Data Warehousing- und Analyselösungen für interne Teams bereit. Wir haben gesehen, dass Partner Premium-Konten für Blockblobspeicher verwenden, um die Anforderungen dieser Data Warehousing- und Analyselösungen an geringe Latenz zu unterstützen. In einem Fall verwaltet ein Katalogteam eine Data Warehousing-Anwendung für Daten, die sich auf Angebote, Preise, Versandmethoden, Lieferanten, Inventar und Logistik beziehen. Informationen werden für mehrere Anwendungsfälle abgefragt, überprüft, extrahiert und mit Mining ausgewertet. Das Team führt Analysen für diese Daten durch, um verschiedenen Teams für die Verkaufsförderung relevante Erkenntnisse und Informationen bereitzustellen. 

### <a name="interactive-analytics"></a>Interaktive Analysen

In fast jeder Branche müssen Unternehmen ihre Daten interaktiv abfragen und analysieren. 

Data Scientists, Analysten und Entwickler können zeitkritische Erkenntnisse schneller ableiten, indem sie Abfragen für Daten ausführen, die in einem Premium-Konto für Blockblobspeicher gespeichert sind. Führungskräfte können ihre Dashboards viel schneller laden, wenn die Daten, die in diesen Dashboards angezeigt werden, aus einem Premium-Konto für Blockblobspeicher anstelle eines Standardkontos vom Typ „Universell V2“ stammen. 

In einem Szenario mussten Analysten Telemetriedaten von Millionen von Geräten schnell analysieren, um besser zu verstehen, wie ihre Produkte verwendet werden, und um Entscheidungen über Produktfreigaben zu treffen. Das Speichern von Daten in SQL-Datenbanken ist teuer. Um die Kosten zu senken und die abfragbare Fläche zu vergrößern, wurde ein Azure Data Lake Storage Gen2 aktiviertes Premium-Blockblob-Speicherkonto verwendet und Berechnungen in Presto und Spark durchgeführt, um Erkenntnisse aus Hive-Tabellen zu gewinnen. Auf diese Weise verfügen auch Daten, auf die nur selten zugegriffen wird, über die gleiche Rechenleistung wie Daten, auf die häufig zugegriffen wird.

Damit die Lücke zwischen der Subsekundenleistung von SQL und den Eingabe-/Ausgabevorgängen pro Sekunde (IOPS) von Presto für externen Speicher geschlossen wird, sind Konsistenz und Geschwindigkeit wichtig, insbesondere bei kleinen ORC-Dateien (Optimized Row Columnar). Ein mit Data Lake Storage Gen2 verwendetes Premium-Konto für Blockblobspeicher hat in diesem Szenario wiederholt eine Leistungsverbesserung um das Dreifache gegenüber einem Standardkonto vom Typ „Universell V2“ gezeigt. Abfragen werden schnell genug ausgeführt, um auf dem Computer den Eindruck einer lokalen Abfrage zu erwecken. 

In einem anderen Fall speichert und fragt ein Partner Protokolle ab, die aus seiner Sicherheitslösung generiert werden. Die Protokolle werden mithilfe von Databricks generiert und dann in einem Data Lake Storage Gen2-fähigen Premium-Konto für Blockblobspeicher gespeichert. Endbenutzer fragen diese Daten ab und durchsuchen sie mithilfe von Azure Data Explorer. Sie haben sich für diese Art von Konto entschieden, um die Stabilität zu erhöhen und die Leistung interaktiver Abfragen zu verbessern. Außerdem legen sie die Lebenszyklusverwaltungsrichtlinie `Delete Action` auf einige Tage fest, um die Kosten zu senken. Diese Richtlinie verhindert, dass sie die Daten dauerhaft aufbewahren. Stattdessen werden Daten gelöscht, sobald sie nicht mehr benötigt werden. 

### <a name="data-processing-pipelines"></a>Datenverarbeitungspipelines

In fast jeder Branche ist es erforderlich, dass Unternehmen Daten verarbeiten. Rohdaten aus verschiedenen Quellen müssen bereinigt und verarbeitet werden, damit sie für die nachgelagerte Verwendung in Tools wie Daten-Dashboards, die den Benutzern bei der Entscheidungsfindung helfen, nützlich sind. 

Die Geschwindigkeit ist bei der Verarbeitung von Daten zwar nicht immer das oberste Anliegen, in einigen Branchen ist sie jedoch erforderlich. Beispielsweise müssen Unternehmen in der Finanzdienstleistungsbranche Daten häufig zuverlässig und so schnell wie möglich verarbeiten. Zur Betrugserkennung müssen diese Unternehmen Eingaben aus verschiedenen Quellen verarbeiten, Risiken für ihre Kunden identifizieren und schnelle Maßnahmen ergreifen. 

In einigen Fällen haben wir gesehen, dass Partner mehrere Standardspeicherkonten verwenden, um Daten aus verschiedenen Quellen zu speichern. Ein Teil dieser Daten wird dann in ein Data Lake Storage-fähiges Premium-Blockblob-Speicherkonto verschoben, wo eine Datenverarbeitungsanwendung häufig neu eintreffende Daten liest. Verzeichnisauflistungsaufrufe in diesem Konto waren viel schneller und wurden wesentlich konsistenter ausgeführt als in einem Standardkonto vom Typ „Universell V2“. Die Schnelligkeit und Konsistenz des Kontos sorgte dafür, dass neue Daten den nachgelagerten Verarbeitungssystemen stets so schnell wie möglich zur Verfügung standen. Dies half ihnen, potenzielle Sicherheitsrisiken rechtzeitig zu erkennen und darauf zu reagieren.
     
### <a name="internet-of-things-iot"></a>Internet der Dinge (IoT, Internet of Things)

IoT ist zu einem wichtigen Teil unseres täglichen Lebens geworden. IoT wird verwendet, um Autobewegungen nachzuverfolgen, Beleuchtungen zu steuern und unsere Gesundheit zu überwachen. Es wird auch in der Industrie angewendet. Beispielsweise verwenden Unternehmen IoT, um ihre Smart Factory-Projekte zu ermöglichen, landwirtschaftliche Erträge zu erhöhen oder, auf Ölplattformen, für Predictive Maintenance. Premium-Konten für Blockblobspeicher haben einen erheblichen Mehrwert für diese Szenarios.
 
Wir haben Partner im Bergbau. Sie verwenden ein Data Lake Storage Gen2-fähiges Premium-Konto für Blockblobspeicher zusammen mit HDInsight (Hbase), um Zeitreihensensordaten von mehreren Bergbaugerätetypen mit einem sehr anspruchsvollem Lastprofil zu erfassen. Premium-Blockblobspeicher hat dazu beigetragen, ihre Anforderungen an die Erfassung hoher Stichprobenraten zu erfüllen. Dies ist auch kostengünstig, da Premium-Blockblobspeicher kostenoptimiert für Workloads ist, die eine große Anzahl von Schreibtransaktionen ausführen, und diese Workloads generieren eine große Anzahl kleiner Schreibtransaktionen (die pro Sekunde in die Zehntausende gehen).  

### <a name="machine-learning"></a>Machine Learning

In vielen Fällen müssen viele Daten verarbeitet werden, um ein Machine Learning-Modell zu trainieren. Zum Abschließen dieser Verarbeitung müssen Computer über einen längeren Zeitraum laufen. Im Vergleich zu den Speicherkosten machen die Rechenkosten in der Regel einen viel größeren Anteil Ihrer Rechnung aus, so dass eine Verringerung der Laufzeit Ihrer Rechenanlagen zu erheblichen Einsparungen führen kann. Die geringe Latenz, die Sie durch die Verwendung von Premium-Blockblobspeicher erhalten, kann diese Zeit und Ihre Rechnung erheblich reduzieren.

Wir haben Partner, die Datenverarbeitungspipelines in Spark-Clustern bereitstellen, in denen sie Machine Learning-Training und Rückschlüsse ausführen. Sie speichern Spark-Tabellen (Parquet-Dateien) und Prüfpunkte in einem Premium-Konto für Blockblobspeicher. Spark-Prüfpunkte können eine große Anzahl von geschachtelten Dateien und Ordnern erstellen. Ihre Verzeichnisauflistungsvorgänge sind schnell, da sie die niedrige Latenz eines Premium-Kontos für Blockblobspeicher mit der hierarchischen Datenstruktur kombiniert haben, die mit Data Lake Storage Gen2 zur Verfügung gestellt wurde. 

Wir haben auch Partner in der Halbleiterindustrie mit Anwendungsfällen, die sich mit IoT und maschinellem Lernen überschneiden. IoT-Geräte, die an Computer in der Fertigungsanlage angeschlossen sind, nehmen Bilder von Halbleiterwafern auf und senden diese an ihr Konto. Mithilfe von Deep Learning-Rückschlüssen kann das System die lokalen Computer informieren, wenn es ein Problem in der Produktion gibt und eine Aktion ausgeführt werden muss. Sie müssen Bilder schnell und zuverlässig laden und verarbeiten können. Mit einem Data Lake Storage Gen2-fähigen Premium-Konto für Blockblobspeicher wird dies ermöglicht. 

### <a name="real-time-streaming-analytics"></a>Streaminganalysen in Echtzeit

Zur Unterstützung von interaktiven Analysen nahezu in Echtzeit muss ein System große Datenmengen erfassen und verarbeiten und diese Daten dann Downstreamsystemen zur Verfügung stellen. Die Verwendung eines Data Lake Storage Gen2-fähigen Premium-Kontos für Blockblobspeicher eignet sich perfekt für diese Arten von Szenarios.

Unternehmen in der Medien- und Unterhaltungsbranche können eine große Anzahl von Protokollen und Telemetriedaten in kurzer Zeit generieren, während sie ein Ereignis übertragen. Einige unserer Partner nutzen mehrere Content Delivery Network-Partner (CDN) für das Streaming. Sie müssen nahezu in Echtzeit entscheiden, welchen CDN-Partnern Datenverkehr zugeordnet werden soll. Daher müssen Daten einige Sekunden nach der Erfassung für Abfragen verfügbar sein. Um diese schnelle Entscheidungsfindung zu erleichtern, verwenden sie Daten, die im Premium Blockblob Storage gespeichert sind, und verarbeiten diese Daten im Azure Data Explorer (ADX). Alle Telemetriedaten, die in den Speicher hochgeladen werden, werden in ADX transformiert, wo sie in einem vertrauten Format gespeichert werden können, das Operatoren und Führungskräfte schnell und zuverlässig abfragen können.

Daten werden in mehrere Blob-Storage-Konten mit Premium-Leistung hochgeladen. Jedes Konto ist mit einem Event Grid und einer Event Hub-Ressource verbunden. ADX ruft die Daten aus Blob Storage ab und führt alle erforderlichen Transformationen aus, um die Daten zu normalisieren (z. B. Dekomprimieren von ZIP-Dateien oder Konvertieren von JSON in CSV). Anschließend werden die Daten für Abfragen über ADX und Dashboards verfügbar gemacht, die in Grafana angezeigt werden. Grafana-Dashboards werden von Operatoren, Führungskräften und anderen Benutzern verwendet. Der Kunde behält seine ursprünglichen Protokolle im Premium-Leistungsspeicher bei oder kopiert sie in ein Speicherkonto vom Typ „Universell V2“, in dem sie zur langfristigen Aufbewahrung und zukünftigen Analyse auf der heißen oder kalten Zugriffsebene gespeichert werden können.

## <a name="getting-started-with-premium"></a>Erste Schritte mit Premium

Überprüfen Sie zuerst, ob Ihre bevorzugten Blob Storage-Features mit Premium-Blockblobspeicherkonten kompatibel sind, und erstellen Sie dann das Konto. 

>[!NOTE]
> Sie können ein vorhandenes Speicherkonto vom Typ „Standard, Universell V2“ nicht in ein Premium-Blockblobspeicherkonto konvertieren. Zum Migrieren zu einem Premium-Blockblobspeicherkonto müssen Sie erst ein neues Premium-Blockblobspeicherkonto erstellen und dann die Daten dorthin migrieren. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Überprüfen der Kompatibilität von Blob Storage-Features

Einige Blob Storage-Features werden in Premium-Blockblobspeicherkonten noch nicht oder nur teilweise unterstützt. Bevor Sie Premium auswählen, lesen Sie den Artikel [Unterstützung von Blob Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md), um zu ermitteln, ob die Features, die Sie nutzen möchten, in Ihrem Konto vollständig unterstützt werden. Die Funktionsunterstützung wird ständig erweitert, daher sollten Sie diesen Artikel regelmäßig auf Updates überprüfen.

### <a name="create-a-new-storage-account"></a>Neues Speicherkonto erstellen

Stellen Sie für die Erstellung eines Premium-Kontos für Blockblobspeicher sicher, dass Sie die Leistungsoption **Premium** und den Kontotyp **Blockblobs** wählen.

> [!div class="mx-imgBorder"]
> ![BlockBlobStorage-Konto erstellen](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

> [!NOTE]
> Einige Blob Storage-Features werden in Premium-Konten für Blockblobspeicher noch nicht oder nur teilweise unterstützt. Bevor Sie Premium auswählen, lesen Sie den Artikel [Unterstützung von Blob Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md), um zu ermitteln, ob die Features, die Sie nutzen möchten, in Ihrem Konto vollständig unterstützt werden. Die Funktionsunterstützung wird ständig erweitert, daher sollten Sie diesen Artikel regelmäßig auf Updates überprüfen.

Wenn Ihr Speicherkonto für Analysen verwendet werden soll, wird dringend empfohlen, Azure Data Lake Storage Gen2 zusammen mit einem Premium-Konto für Blockblobspeicher zu verwenden. Wenn Sie die Funktionen von Azure Data Lake Storage Gen2 nutzen möchten, aktivieren Sie auf der Seite **Speicherkonto erstellen** auf der Registerkarte **Erweitert** die Einstellung **Hierarchischer Namespace**. 

In der folgenden Abbildung wird diese Einstellung auf der Seite **Speicherkonto erstellen** gezeigt.

> [!div class="mx-imgBorder"]
> ![Einstellung „Hierarchischer Namespace“](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Eine ausführliche Anleitung dazu finden Sie unter [Erstellen eines Speicherkontos](../common/storage-account-create.md).



## <a name="see-also"></a>Siehe auch

- [Speicherkontoübersicht](../common/storage-account-overview.md)
- [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
- [Erstellen eines Speicherkontos für die Verwendung mit Azure Data Lake Storage Gen2](create-data-lake-storage-account.md)
- [Premium-Tarif für Azure Data Lake Storage](premium-tier-for-data-lake-storage.md)
