---
title: Entitätskategorien, die von der Textanalyse für Gesundheit erkannt werden
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über Entitätskategorien, die von der Textanalyse für Gesundheit erkannt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: e61589eaf90da3cb7e8310724a28bf993e8efb21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021545"
---
# <a name="supported-text-analytics-for-health-entity-categories"></a>Unterstützte Textanalyse für Gesundheitsentitätskategorien

Text Analytics for Health verarbeitet und extrahiert Informationen aus unstrukturierten medizinischen Daten. Der Dienst erkennt medizinische Begriffe, zeigt diese an, weist ihnen Assertionen zu, leitet semantische Beziehungen zwischen ihnen ab und verknüpft sie mit häufigen medizinischen Ontologien.

Gewusst wie: Verwenden von Text Analytics for Health (Vorschauversion) erkennt medizinische Begriffe in den folgenden Kategorien.

## <a name="anatomy"></a>Aufbau

### <a name="entities"></a>Entitäten

**BODY_STRUCTURE**: Körpersysteme, anatomische Bereiche und Körperregionen. Beispielsweise „Arm“, „Knie“, „Bauch“, „Nase“, „Leber“, „Kopf“, „Atemsystem“, „Lymphozyten“.

:::image type="content" source="../media/entities/anatomy-entities-body-structure.png" alt-text="Ein Beispiel für die Körperstrukturentität." lightbox="../media/entities/anatomy-entities-body-structure.png":::

## <a name="demographics"></a>Demografische Informationen

### <a name="entities"></a>Entitäten

**AGE**: Alle Bezeichnungen und Ausdrücke in Bezug auf das Alter von Patienten, Familienmitgliedern und anderen. Beispielsweise „40-jährig“, „51 Jahre“, „3 Monate alt“, „erwachsen“, „älterer“, „jung“, „minderjährig“, „mittleren Alters“.

**GENDER**: Benennungen, die das Geschlecht der Person angeben. Beispielsweise „männlich“, „weiblich“, „Frau“, „Herr“, „Dame“.

:::image type="content" source="../media/entities/age-entity.png" alt-text="Ein Beispiel für eine Altersentität." lightbox="../media/entities/age-entity.png":::

## <a name="examinations"></a>Untersuchungen

### <a name="entities"></a>Entitäten

**EXAMINATION_NAME**: Diagnoseverfahren und Tests einschließlich Vitalzeichen und Körpermaße. Beispielsweise „MRT“, „EKG“, „HIV-Test“, „Hämoglobin“, „Blutplättchenanzahl“, „Skalensysteme“ wie *Bristol-Stuhlformen-Skala*.

:::image type="content" source="../media/entities/exam-name-entities.png" alt-text="Ein Beispiel für eine Untersuchungsentität." lightbox="../media/entities/exam-name-entities.png":::

## <a name="external-influence"></a>Externer Einfluss

### <a name="entities"></a>Entitäten

**ALLERGEN**: Ein Antigen, das eine allergische Reaktion auslöst. Beispiele: Katzen, Erdnüsse.

:::image type="content" source="../media/entities/external-influence-allergen.png" alt-text="Ein Beispiel für eine Entität mit einem externen Einfluss." lightbox="../media/entities/external-influence-allergen.png":::


## <a name="general-attributes"></a>Allgemeine Attribute

### <a name="entities"></a>Entitäten

**COURSE:** : Beschreibung einer Änderung in einer anderen Entität im Laufe der Zeit, z. B. der Zustandsverlauf (z. B. Verbesserung, Verschlechterung, Auflösung, Remission), ein Behandlungs- oder Medikationsverlauf (z. B. höhere Dosierung). 

:::image type="content" source="../media/entities/course-entity.png" alt-text="Ein Beispiel für eine Verlaufsentität." lightbox="../media/entities/course-entity.png":::

**DATE**: Vollständiges Datum einer Erkrankung, Untersuchung, Behandlung, Arzneiverordnung oder eines Verwaltungsereignisses.

:::image type="content" source="../media/entities/date-entity.png" alt-text="Ein Beispiel für eine Datumsentität." lightbox="../media/entities/date-entity.png":::

**DIRECTION**: Begriffe zur lokalen Bestimmung im Zusammenhang mit dem Körperbau, einer Erkrankung, Untersuchung oder Behandlung. Beispiel: links, seitlich, oberhalb von, hinter.

:::image type="content" source="../media/entities/direction-entity.png" alt-text="Ein Beispiel für eine Richtungsentität." lightbox="../media/entities/direction-entity.png":::

**FREQUENCY**: Beschreibt, wie häufig eine Erkrankung, Untersuchung, Behandlung oder Arzneiverordnung stattfand, stattfindet oder stattfinden sollte.

:::image type="content" source="../media/entities/medication-form.png" alt-text="Ein Beispiel für ein Attribut zur Häufigkeit der Medikamenteneinnahme." lightbox="../media/entities/medication-form.png":::


**TIME**: Zeitliche Begriffe zur Angabe des Beginns und/oder der Dauer einer Erkrankung, Untersuchung, Behandlung, Arzneiverordnung oder eines Verwaltungsereignisses. 

**MEASUREMENT_UNIT**: Einheit des Messwerts im Zusammenhang mit einer Untersuchung oder einer Erkrankung.

**MEASUREMENT_VALUE**: Messwert im Zusammenhang mit einer Untersuchung oder einer Erkrankung.

:::image type="content" source="../media/entities/measurement-value-entity.png" alt-text="Ein Beispiel für eine Messwertentität." lightbox="../media/entities/measurement-value-entity.png":::

**RELATIONAL_OPERATOR**: Ausdrücke für die quantitative Beziehung zwischen einer Entität und zusätzlichen Informationen.

:::image type="content" source="../media/entities/measurement-unit.png" alt-text="Ein Beispiel für eine Maßeinheitsentität." lightbox="../media/entities/measurement-unit.png"::: 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entitäten

**VARIANT**: Alle Erwähnungen von Genvarianten und -mutationen. Beispiel: `c.524C>T`, `(MTRR):r.1462_1557del96`.
  
**GENE_OR_PROTEIN**: Alle Erwähnungen von Namen und Symbolen menschlicher Gene sowie von Chromosomen und Chromosomen-/Proteinteilen. Beispiel: „MTRR“, „F2“.

**MUTATION_TYPE**: Beschreibung der Mutation, einschließlich Typ, Wirkung und Ort. Beispiel: Trisomie, Keimbahnmutation, Funktionsverlust.

:::image type="content" source="../media/entities/genomics-entities.png" alt-text="Ein Beispiel für eine Genentität." lightbox="../media/entities/genomics-entities.png":::

**EXPRESSION** : Genexpressionsebene. Beispielsweise „positive for-“, „negative for-“, „overexpressed“, „detected in high/low levels“, „elevated“.

:::image type="content" source="../media/entities/expression.png" alt-text="Ein Beispiel für eine Genexpressionsentität." lightbox="../media/entities/expression.png":::


## <a name="healthcare"></a>Integritätsressourcen

### <a name="entities"></a>Entitäten
  
**ADMINISTRATIVE_EVENT**: Ereignisse in Bezug auf das Gesundheitswesen, aber (teilweise) administrativ. Beispielsweise „Registrierung“, „Zulassung“, „Testversion“, „Studieneintrag“, „Überweisung“, „Entlassung“, „Krankenhauseinweisung“, „Krankenhausaufenthalt“. 

**CARE_ENVIRONMENT**: Eine Umgebung oder ein Ort, wo Patienten versorgt werden. Beispielsweise „Notaufnahme“, „Arztpraxis“, „kardiologische Station“, „Hospiz“, „Krankenhaus“.

:::image type="content" source="../media/entities/healthcare-event-entity.png" alt-text="Ein Beispiel für eine Ereignisentität im Gesundheitswesen." lightbox="../media/entities/healthcare-event-entity.png" :::

**HEALTHCARE_PROFESSION**: Praktizierender im Gesundheitswesen. Beispielsweise „Zahnarzt“, „Pathologe“, „Neurologe“, „Radiologe“, „Apotheker“, „Ernährungsberater“, „Physiotherapeut“, „Chiropraktiker“.

:::image type="content" source="../media/entities/healthcare-profession-entity-2.png" alt-text="Ein weiteres Beispiel für eine Umgebungsentität im Gesundheitswesen." lightbox="../media/entities/healthcare-profession-entity-2.png":::

## <a name="medical-condition"></a>Medizinischer Zustand

### <a name="entities"></a>Entitäten

**DIAGNOSIS**: Krankheit, Syndrom, Vergiftung. Beispielsweise „Brustkrebs“, „Alzheimer“, „Bluthochdruck“, „Herzinsuffizienz“, „Wirbelsäulenverletzung“.

**SYMPTOM_OR_SIGN**: Subjektiver oder objektiver Nachweis von Krankheiten oder sonstige Diagnosen. Beispielsweise „Brustschmerzen“, „Kopfschmerzen“, „Schwindel“, „Effloreszenz“, „Dyspnoe“, „gute Darmgeräusche“, „gut genährt“.

:::image type="content" source="../media/entities/medical-condition-entity.png" alt-text="Ein Beispiel für eine Entität für den medizinischen Zustand." lightbox="../media/entities/medical-condition-entity.png":::

**CONDITION_QUALIFIER**: Qualitative Angaben zur Beschreibung eines medizinischen Zustands oder einer Erkrankung. Alle folgenden Unterkategorien gelten als Qualifizierer:

* Zeitbezogene Ausdrücke: Dies sind Benennungen, die die Zeitdimension qualitativ beschreiben, z. B. „plötzlich“, „akut“, „chronisch“, „langjährig“. 
* Qualitätsausdrücke:  Dabei handelt es sich um Benennungen, die die „Art“ des medizinischen Zustands beschreiben, z. B. „brennen“, „scharf“.
* Schweregradausdrücke: „schwerwiegend“, „mild“, „etwas“, „unkontrolliert“.
* Ausdehnungsausdrücke: „lokal“, „zentriert“, „diffus“.

:::image type="content" source="../media/entities/condition-qualifier-diagnosis-3.png" alt-text="Screenshot: Weiteres Beispiel für ein Zustandsqualifiziererattribut mit einer Diagnoseentität" lightbox="../media/entities/condition-qualifier-diagnosis-3.png" :::

**CONDITION_SCALE**: Qualitative Benennungen, die den Zustand durch eine Skala charakterisieren, bei der es sich um eine endliche sortierte Liste von Werten handelt.

:::image type="content" source="../media/entities/condition-scale-example.png" alt-text="Ein weiteres Beispiel für ein Zustandsqualifiziererattribut und eine Diagnoseentität." lightbox="../media/entities/condition-scale-example.png":::

## <a name="medication"></a>Medikament

### <a name="entities"></a>Entitäten

**MEDICATION_CLASS**: Eine Gruppe von Medikamenten, die einen ähnlichen Aktionsmechanismus, einen verwandten Aktionsmodus, eine ähnliche chemische Struktur haben und/oder zur Behandlung derselben Krankheit verwenden. Beispielsweise „ACE-Hemmer“, „Opioid“, „Antibiotika“, „Mittel zur Schmerzlinderung“.

:::image type="content" source="../media/entities/medication-entities-class.png" alt-text="Ein Beispiel für eine Medikamentenklassenentität." lightbox="../media/entities/medication-entities-class.png":::

**MEDICATION_NAME**: Erwähnungen von Medikamenten einschließlich urheberrechtlich geschützter Markennamen und Namen, die keine Markennamen sind. Beispiel: Ibuprofen.

**DOSAGE**: Menge der bestellten Medikamente. Beispielsweise „*1.000 ml* Natriumchlorid-Infusionslösung“.

**MEDICATION_FORM**: Die Form des Medikaments. Beispielsweise „Lösung“, „Pille“, „Kapsel“, „Tablette“, „Pflaster“, „Gel“, „Paste“, „Schaum“, „Spray“, „Drops“, „Creme“, „Sirup“.

:::image type="content" source="../media/entities/medication-dosage.png" alt-text="Ein Beispiel für ein Medikamentdosierungsattribut." lightbox="../media/entities/medication-dosage.png":::

**MEDICATION_ROUTE**: Die Methode der Medikamenteneinnahme. Beispielsweise „oral“, „topisch“, „inhaliert“.

:::image type="content" source="../media/entities/medication-form.png" alt-text="Ein Beispiel für ein Medikamentenformattribut." lightbox="../media/entities/medication-form.png"::: 

## <a name="social"></a>Soziale Netzwerke

### <a name="entities"></a>Entitäten

**FAMILY_RELATION**: Erwähnungen von Familienangehörigen des Patienten. Beispielsweise „Vater“, „Tochter“, „Geschwister“, „Eltern“.

:::image type="content" source="../media/entities/family-relation.png" alt-text="Beispiel für eine Entität einer Familienbeziehung." lightbox="../media/entities/family-relation.png":::

## <a name="treatment"></a>Behandlung

### <a name="entities"></a>Entitäten

**TREATMENT_NAME**: Therapeutische Verfahren. Beispielsweise „Knieprothesenchirurgie“, „Knochenmarktransplantation“, „TAVI“, „Diät“.

:::image type="content" source="../media/entities/treatment-entities-name.png" alt-text="Ein Beispiel für eine Behandlungsnamenentität." lightbox="../media/entities/treatment-entities-name.png":::


## <a name="supported-assertions"></a>Unterstützte Assertionen

Assertionsmodifizierer sind in drei Kategorien unterteilt, wobei sich jede auf einen anderen Aspekt konzentriert.
Jede Kategorie enthält eine Reihe von sich gegenseitig ausschließenden Werten. Jeder Entität wird nur ein Wert pro Kategorie zugewiesen. Der häufigste Wert für jede Kategorie ist der Standardwert. Die vom Dienst ausgegebene Antwort enthält nur Assertionsmodifizierer, die sich vom Standardwert unterscheiden.

### <a name="certainty"></a>Sicherheit  

gibt an, ob das Konzept vorhanden ist. Gibt außerdem den Grad der Gewissheit im Text in Bezug auf das Vorhandensein des Konzepts (eindeutig oder möglich) an.

**Positive** (Standard): Das Konzept ist vorhanden oder ist aufgetreten.

**Negative**: Das Konzept ist jetzt nicht vorhanden oder ist nie aufgetreten.

:::image type="content" source="../media/entities/negative-entity.png" alt-text="Ein Beispiel für eine negative Entität." lightbox="../media/entities/negative-entity.png":::

**Positive_Possible**: Das Konzept ist wahrscheinlich vorhanden, aber es gibt einen bestimmten Grad an Ungewissheit.

:::image type="content" source="../media/entities/positive-possible-entity.png" alt-text="Ein Beispiel für eine positive mögliche Entität." lightbox="../media/entities/positive-possible-entity.png" :::

**Negative_Possible**: Das Konzept ist wahrscheinlich nicht vorhanden, aber es gibt einen bestimmten Grad an Ungewissheit.

:::image type="content" source="../media/entities/negative-possible-entity.png" alt-text="Ein Beispiel für eine negative mögliche Entität." lightbox="../media/entities/negative-possible-entity.png" :::

**Neutral_Possible**: Das Konzept ist vorhanden oder nicht vorhanden, mit dem gleichen Grad an Gewissheit für beide Möglichkeiten.

:::image type="content" source="../media/entities/neutral-possible-entity.png" alt-text="Ein Beispiel für eine neutrale mögliche Entität." lightbox="../media/entities/neutral-possible-entity.png":::

### <a name="conditionality"></a>Konditionalität

gibt an, ob das Vorhandensein eines Konzepts von bestimmten Bedingungen abhängt. 

**None** (Standard): Das Konzept ist ein Fakt und weder hypothetisch noch von bestimmten Bedingungen abhängig.

**Hypothetical**: Das Konzept kann in Zukunft entstehen oder auftreten.

:::image type="content" source="../media/entities/hypothetical-entity.png" alt-text="Ein Beispiel für eine hypothetische Entität." lightbox="../media/entities/hypothetical-entity.png":::

**Conditional**: Das Konzept ist vorhanden oder tritt nur unter bestimmten Bedingungen auf.

:::image type="content" source="../media/entities/conditional-entity.png" alt-text="Ein Beispiel für eine bedingte Entität." lightbox="../media/entities/conditional-entity.png":::

### <a name="association"></a>Zuordnung

gibt an, ob das Konzept der im Text beschriebenen Person oder einer anderen Person zugeordnet ist.

**Subject** (Standard): Das Konzept ist der im Text beschriebenen Person (in der Regel der Patient) zugeordnet.

**Someone_Else**: Das Konzept ist einer anderen als der im Text beschriebenen Person zugeordnet.

:::image type="content" source="../media/entities/association-entity.png" alt-text="Ein Beispiel für eine Zuordnungsentität." lightbox="../media/entities/association-entity.png":::



## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über NER](../../named-entity-recognition/overview.md)
