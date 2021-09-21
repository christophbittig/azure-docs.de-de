---
title: Datei einfügen
description: include file
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 08/20/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: d3317bafa2ff03e83bd79e9a3efa0ecfaffd4124
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752805"
---
| Resource | Begrenzung |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximale Anzahl von Skalierungseinheiten | 12 pro Region<sup>1</sup> |
| Cachegröße | 5 GiB pro Einheit<sup>2</sup> |
| Gleichzeitige Back-End-Verbindungen<sup>3</sup> pro HTTP-Autorität | 2\.048 pro Einheit<sup>4</sup> |
| Maximale Größe zwischengespeicherter Antworten | 2 MiB |
| Maximale Größe eines Richtliniendokuments | 256 KiB<sup>5</sup> |
| Maximale Anzahl benutzerdefinierter Gatewaydomänen pro Dienstinstanz<sup>6</sup> | 20 |
| Maximale Anzahl von CA-Zertifikaten pro Dienstinstanz<sup>7</sup> | 10 |
| Maximale Anzahl von Dienstinstanzen pro Abonnement<sup>8</sup> | 20 |
| Maximale Anzahl von Abonnements pro Dienstinstanz<sup>8</sup> | 500 |
| Maximale Anzahl von Clientzertifikaten pro Dienstinstanz<sup>8</sup> | 50 |
| Maximale Anzahl von APIs pro Dienstinstanz<sup>8</sup> | 50 |
| Maximale Anzahl von API-Verwaltungsvorgängen pro Dienstinstanz<sup>8</sup> | 1\.000 |
| Maximale Gesamtdauer der Anforderungen<sup>8</sup> | 30 Sekunden |
| Maximale Größe der Anforderungspayload<sup>8</sup> | 1 GiB |
| Maximale gepufferte Nutzlastgröße<sup>8</sup> | 2 MiB |
| Maximale Größe der Anforderungs-URL<sup>9</sup> | 16384 Bytes |
| Maximale Länge des URL-Pfadsegments<sup>10</sup> | 260 Zeichen |
| Maximale Größe des von der [Überprüfungsrichtlinie](../articles/api-management/validation-policies.md) verwendeten API-Schemas<sup>10</sup> | 4 MB |
| Maximale Größe des Anforderungs- oder Antworttexts in der [validate-content-Richtlinie](../articles/api-management/validation-policies.md#validate-content)<sup>10</sup> | 100 KB |
| Maximale Anzahl von selbstgehosteten Gateways<sup>11</sup> | 25 |

<sup>1</sup>Die Skalierungsgrenzen sind vom Tarif abhängig. Details zu den Tarifen und den dazugehörigen Grenzwerten für die Skalierung finden Sie unter [API Management – Preise](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>Die Cachegröße pro Einheit ist vom Tarif abhängig. Die Tarife und die dazugehörigen Grenzwerte für die Skalierung finden Sie unter [API Management – Preise](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>Verbindungen werden in Pools zusammengefasst und wiederverwendet, sofern sie nicht explizit durch das Back-End geschlossen wurden.<br/>
<sup>4</sup>Dieser Grenzwert gilt pro Einheit der Tarife „Basic“, „Standard“ und „Premium“. Der Developer-Tarif ist auf 1.024 beschränkt. Dieser Grenzwert betrifft den Tarif „Consumption“ nicht.<br/>
<sup>5</sup>Dieser Grenzwert gilt für die Tarife „Basic“, „Standard“ und „Premium“. Im Tarif „Consumption“ ist die Größe des Richtliniendokuments auf 16 KiB beschränkt.<br/>
<sup>6</sup>Mehrere benutzerdefinierte Domänen werden nur in den Tarifen „Developer“ und „Premium“ unterstützt.<br/>
<sup>7</sup>Zertifizierungsstellenzertifikate werden im Tarif „Consumption“ nicht unterstützt.<br/>
<sup>8</sup>Dieser Grenzwert gilt nur für den Tarif „Consumption“. In diesen Kategorien gibt es keine Grenzwerte für andere Tarife.<br/>
<sup>9</sup> Gilt für nur den Tarif „Consumption“. Enthält eine Abfragezeichenfolge der Länge bis zu 2048 Bytes.<br/>
<sup>10</sup> Wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/), wenn dieser Grenzwert erhöht werden soll.<br/>
<sup>11</sup> Selbstgehostete Gateways werden nur in den Tarifen „Developer“ und „Premium“ unterstützt. Der Grenzwert gilt für die Anzahl von [selbstgehosteten Gatewayressourcen](/rest/api/apimanagement/2020-06-01-preview/gateway). Wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/), um diesen Grenzwert zu erhöhen. Beachten Sie, dass die Anzahl von Knoten (oder Replikaten), die einer selbstgehosteten Gatewayressource zugeordnet sind, im Premium-Tarif unbegrenzt ist und im Developer-Tarif auf einen einzelnen Knoten begrenzt ist.
