---
title: Datei einfügen
description: include file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/07/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0afa9814a7fb6b726d07fe5f1a5b2c863df72e2e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778838"
---
Die folgenden Grenzwerte gelten für Azure Event Grid-**Themen** (Systemthemen, benutzerdefinierte Themen und Partnerthemen). 

> [!NOTE]
> Hierbei handelt um regionsspezifische Grenzwerte. 

| Resource | Begrenzung |
| --- | --- |
| Benutzerdefinierte Themen pro Azure-Abonnement | 100 |
| Ereignisabonnements pro Thema | 500 |
| Veröffentlichungsrate für ein benutzerdefiniertes Thema oder Partnerthema (eingehend) | 5\.000 Ereignisse/Sek. oder 5 MB/Sek. (je nachdem, welcher Wert zuerst erreicht wird) |
| Ereignisgröße | 1 MB  |
| Private Endpunktverbindungen pro Thema  | 64 | 
| IP-Firewallregeln pro Thema | 16 | 

Für Azure Event Grid-**Domänen** gelten die folgenden Grenzwerte: 

| Resource | Begrenzung |
| --- | --- |
| Themen pro Ereignisdomäne | 100.000 |
| Ereignisabonnements pro Thema innerhalb einer Domäne | 500 |
| Domänenbereich-Ereignisabonnements | 50 |
| Veröffentlichungsrate für eine Ereignisdomäne (Eingang) | 5\.000 Ereignisse/Sek. oder 5 MB/Sek. (je nachdem, welcher Wert zuerst erreicht wird) |
| Ereignisdomänen pro Azure-Abonnement | 100 |
| Private Endpunktverbindungen pro Domäne | 64 | 
| IP-Firewallregeln pro Domäne | 16 | 


