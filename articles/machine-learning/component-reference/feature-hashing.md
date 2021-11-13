---
title: Feature Hashing Komponentenreferenz
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Feature-Hashing-Komponente im Azure Machine Learning Designer verwenden, um Textdaten mit Merkmalen zu versehen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 172498550cf6de5d57475805fba592d7e43666e9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566678"
---
# <a name="feature-hashing-component-reference"></a>Feature Hashing Komponentenreferenz

Dieser Artikel beschreibt eine Komponente, die im Azure Machine Learning Designer enthalten ist.

Verwenden Sie die Komponente Feature Hashing, um einen englischen Textstrom in eine Reihe von Integer-Merkmalen umzuwandeln. Anschließend können Sie diese gehashte Merkmalssammlung an einen Machine Learning-Algorithmus übergeben, um ein Textanalysemodell zu trainieren.

Die in dieser Komponente bereitgestellte Feature-Hashing-Funktionalität basiert auf dem nimbusml-Framework. Weitere Informationen finden Sie unter [NgramHash-Klasse](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>Was ist Feature Hashing?

Feature Hashing funktioniert, indem eindeutige Token in Integerwerte umgewandelt werden. Diese Funktion verarbeitet die genauen Zeichenfolgen, die Sie als Eingabe bereitstellen, und führt keine linguistische Analyse oder Vorverarbeitung aus. 

Nehmen Sie z.B. eine Reihe von einfachen Sätzen wie diese an, gefolgt von einer Stimmungsbewertung. Angenommen, Sie möchten diesen Text verwenden, um ein Modell zu erstellen.

|Benutzertext|Stimmung|
|--------------|---------------|
|I loved this book (Ich mochte dieses Buch)|3|
|I hated this book (Ich habe dieses Buch gehasst)|1|
|This book was great (Dieses Buch war großartig)|3|
|I love books (Ich liebe Bücher)|2|

Intern erstellt die Komponente Feature Hashing ein Wörterbuch mit n-Grammen. Die Liste der Bigramme für dieses Dataset würde beispielsweise folgendermaßen aussehen:

|Ausdruck (Bigramme)|Häufigkeit|
|------------|---------------|
|This book (Dieses Buch)|3|
|I loved (Ich mochte)|1|
|I hated (Ich habe gehasst)|1|
|I love (Ich liebe)|1|

Sie können die Größe der N-Gramme mithilfe der Eigenschaft **N-grams** steuern. Bei der Auswahl von Bigrammen werden auch Unigramme berechnet. Das Wörterbuch würde auch einzelne Begriffe wie die folgenden enthalten:

|Begriff (Unigramme)|Häufigkeit|
|------------|---------------|
|book (Buch)|3|
|I|3|
|books (Bücher)|1|
|was (war)|1|

Nachdem das Wörterbuch erstellt wurde, wandelt die Komponente Feature Hashing die Wörterbuchbegriffe in Hash-Werte um. Anschließend wird berechnet, ob ein Merkmal in jedem Fall verwendet wurde. Für jede Zeile der Textdaten gibt die Komponente eine Reihe von Spalten aus, eine Spalte für jedes gehashte Merkmal.

Nach dem Hashing können die Merkmalsspalten beispielsweise wie folgt aussehen:

|Rating|Hashing feature 1 (Hashingmerkmal 1)|Hashing feature 2 (Hashingmerkmal 2)|Hashing feature 3 (Hashingmerkmal 3)|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Wenn der Wert in der Spalte 0 ist, hat die Zeile kein gehashtes Merkmal enthalten.
* Wenn der Wert 1 ist, hat die Zeile das Merkmal enthalten.

Mit Feature Hashing können Sie Textdokumente variabler Länge als numerische Merkmalsvektoren gleicher Länge darstellen, um die Dimensionen zu verringern. Wenn Sie versuchen würden, die Textspalte für das Training so zu verwenden, wie sie ist, würde sie als kategorische Merkmalsspalte mit vielen verschiedenen Werten behandelt.

Numerische Ausgaben ermöglichen auch die Verwendung gängiger Machine Learning-Methoden wie Klassifizierung, Clustering und Informationsabruf. Da Nachschlagevorgänge ganzzahlige Hashes anstelle von Zeichenfolgenvergleichen verwenden können, ist das Abrufen der Merkmalsgewichtungen auch wesentlich schneller.

## <a name="configure-the-feature-hashing-component"></a>Konfigurieren Sie die Komponente Feature-Hashing

1.  Fügen Sie die Komponente Feature-Hashing im Designer zu Ihrer Pipeline hinzu.

1. Verbinden Sie das Dataset, das den zu analysierenden Text enthält.

    > [!TIP]
    > Da Feature Hashing keine lexikalischen Vorgänge wie Stammformreduktion oder Trunkierung durchführt, können Sie manchmal bessere Ergebnisse erzielen, indem Sie den jeweiligen Text vorverarbeiten, bevor Sie Feature Hashing anwenden. 

1. Legen Sie **Target columns** (Zielspalten) auf die Textspalten fest, die in gehashte Merkmale konvertiert werden sollen. Bedenken Sie Folgendes:

    * Die Spalten müssen den Datentyp „string“ aufweisen.
    
    * Die Auswahl mehrerer Textspalten kann erhebliche Auswirkungen auf die Featuredimensionalität haben. So reicht beispielsweise die Anzahl der Spalten für einen 10-Bit-Hash von 1.024 für eine einzelne Spalte bis 2.048 für zwei Spalten.

1. Verwenden Sie **Hashing bitsize** (Hashing-Bitgröße), um die Anzahl von Bits anzugeben, die verwendet werden sollen, wenn Sie die Hashtabelle erstellen.
    
    Die Standardbitgröße ist 10. Dieser Wert ist für viele Probleme angemessen. Möglicherweise benötigen Sie mehr Platz, um Konflikte abhängig von der Größe des N-Gramme-Vokabulars im Trainingstext zu vermeiden.
    
1. Geben Sie für **N-grams** (N-Gramme) eine Zahl ein, die die maximale Länge der N-Gramme definiert, die dem Trainingswörterbuch hinzugefügt werden sollen. Ein N-Gramm ist eine Sequenz von *N* Wörtern, die als eindeutige Einheit behandelt wird.

    Wenn Sie z.B. 3 eingeben, werden Unigramme, Bigramme und Trigramme erstellt.

1. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss der Verarbeitung gibt die Komponente einen transformierten Datensatz aus, in dem die ursprüngliche Textspalte in mehrere Spalten umgewandelt wurde. Jede Spalte entspricht einem Merkmal im Text. Je nachdem, wie signifikant das Wörterbuch ist, kann das sich ergebende Dataset sehr groß sein:

|Column name 1 (Spaltenname 1)|Column type 2 (Spaltentyp 2)|
|-------------------|-------------------|
|USERTEXT (Benutzertext)|Ursprüngliche Datenspalte|
|SENTIMENT (Stimmung)|Ursprüngliche Datenspalte|
|USERTEXT - Hashing feature 1 (Hashingmerkmal 1)|Gehashte Merkmalsspalte|
|USERTEXT - Hashing feature 2 (Hashingmerkmal 2)|Gehashte Merkmalsspalte|
|USERTEXT - Hashing feature n (Hashingmerkmal n)|Gehashte Merkmalsspalte|
|USERTEXT - Hashing feature 1024 (Hashingmerkmal 1024)|Gehashte Merkmalsspalte|

Nachdem Sie den transformierten Datensatz erstellt haben, können Sie ihn als Eingabe für die Komponente Modell trainieren verwenden.
 
## <a name="best-practices"></a>Bewährte Methoden

Die folgenden bewährten Verfahren können Ihnen helfen, die Komponente Feature-Hashing optimal zu nutzen:

* Fügen Sie eine Komponente Preprocess Text hinzu, bevor Sie den Eingabetext mittels Feature Hashing vorverarbeiten. 

* Fügen Sie eine Select Columns Komponente nach der Feature Hashing Komponente hinzu, um die Textspalten aus dem Ausgabedatensatz zu entfernen. Sie benötigen die Textspalten nicht mehr, nachdem die Hashmerkmale generiert wurden.
    
* Erwägen Sie die Verwendung dieser Textvorverarbeitungsoptionen, um die Ergebnisse zu vereinfachen und die Genauigkeit zu verbessern:

    * Wörtertrennung
    * Stoppwortentfernung
    * Kasusnormalisierung
    * Entfernen von Interpunktions- und Sonderzeichen
    * Wortstammerkennung  

Der optimale Satz von Vorverarbeitungsmethoden, der in einer Lösung zum Einsatz kommt, hängt vom Fachgebiet, dem Vokabular und den Geschäftsanforderungen ab. Experimentieren Sie mit Ihren Daten, um zu ermitteln, welche Textverarbeitungsmethoden am effektivsten sind.

## <a name="next-steps"></a>Nächste Schritte
            
Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md)
