---
title: 'Conversational Language Understanding: Einschränkungen'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die Daten-, Regions- und Durchsatzgrenzen für Conversational Language Understanding (CLU).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: ec9a32c4f73e174798a3422a1c152f3972bd076f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095214"
---
# <a name="service-limits-for-conversational-language-understanding"></a>Dienstgrenzwerte für Conversational Language Understanding (CLU)

Hier erfahren Sie mehr über die Daten-, Regions- und Durchsatzgrenzen für den Conversational Language Understanding-Dienst.

## <a name="region-limits"></a>Regionale Einschränkungen

- Conversational Language Understanding ist nur in zwei Regionen verfügbar: **USA, Westen 2** und **Europa, Westen**. 
    - Orchestrierungsworkflowprojekte ermöglichen Verbindungen zu **Konversationsprojekten**, **QnA Maker** und **LUIS** in der Region Europa, Westen.
    - Orchestrierungsworkflowprojekte ermöglichen nur Verbindungen zu **Konversationsprojekten** und **QnA Maker** in der Region USA, Westen 2. Für LUIS gibt es keine Dokumenterstellungsberechtigungen in der Region USA, Westen 2. 
- Die einzige verfügbare SKU für den Zugriff auf CLU ist die **Language**-Ressource mit der SKU **S**.

## <a name="data-limits"></a>Datengrenzwerte

Die folgenden Grenzwerte gelten für den Conversational Language Understanding-Dienst (CLU).

|Element|Begrenzung|
| --- | --- |
|Äußerungen|15.000 pro Projekt*|
|Absichten|500 pro Projekt|
|Entitäten|100 pro Projekt|
|Länge der Äußerung|500 Zeichen|
|Länge des Absichts- und Entitätsnamens|50 Zeichen|
|Modelle|10 pro Projekt|
|Projekte|500 pro Ressource|
|Synonyme|20.000 pro Listenkomponente|

\**Schließt nur Äußerungen ein, die vom Benutzer bzw. der Benutzerin hinzugefügt wurden. Daten, die für Orchestrierungsworkflowprojekte abgerufen werden, werden nicht auf die Gesamtsumme angerechnet.*


## <a name="throughput-limits"></a>Durchsatzlimits

|Element | Begrenzung |
--- | --- 
|Dokumenterstellungsaufrufe| 60 Anforderungen pro Minute|
|Vorhersageaufrufe| 60 Anforderungen pro Minute|

## <a name="quota-limits"></a>Kontingentgrenzen

| Element | Begrenzung |
--- | --- 
|Dokumenterstellungsaufrufe| Unbegrenzt|
|Vorhersageaufrufe| 100.000 USD pro Monat|

## <a name="next-steps"></a>Nächste Schritte

[Conversational Language Understanding – Übersicht](overview.md)
