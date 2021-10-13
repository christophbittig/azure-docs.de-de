---
title: 'Übersetzen hinter Firewalls: Translator'
titleSuffix: Azure Cognitive Services
description: Translator von Azure Cognitive Services kann hinter Firewalls entweder mithilfe der Domänennamen- oder der IP-Filterung Übersetzungen durchführen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: afb0e616f01342c94734155e96367f0b453e313a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401862"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Übersetzung hinter IP-Firewalls mithilfe von Translator

Translator kann hinter Firewalls entweder mithilfe der Domänennamen- oder der IP-Filterung Übersetzungen durchführen. Die Domänennamenfilterung ist hierbei die bevorzugte Methode. Wenn Sie weiterhin IP-Filter benötigen, sollten Sie die [Details der IP-Adressen mithilfe des Diensttags](../../virtual-network/service-tags-overview.md#service-tags-on-premises) erhalten. Translator befindet sich unter dem Diensttag „CognitiveServicesManagement“. 

Sie sollten Microsoft Translator **nicht** hinter einer spezifischen IP-gefilterten Firewall ausführen. Die Unterstützung für dieses Setup wird in Zukunft möglicherweise ohne Vorankündigung eingestellt.

Die IP-Adressen für geografische Translator-Endpunkte sind ab dem 21. September 2021:

|Gebiet|Basis-URL (geografischer Endpunkt)|IP-Adressen|
|:--|:--|:--|
|USA|api-nam.cognitive.microsofttranslator.com|20.42.6.144, 20.49.96.128, 40.80.190.224, 40.64.128.192|
|Europa|api-eur.cognitive.microsofttranslator.com|20.50.1.16, 20.38.87.129|
|Asien-Pazifik|api-apc.cognitive.microsofttranslator.com|40.80.170.160, 20.43.132.96, 20.37.196.160, 20.43.66.16|
