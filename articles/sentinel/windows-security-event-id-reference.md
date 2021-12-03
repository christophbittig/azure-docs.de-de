---
title: Windows Sicherheitsereignissätze, die an Microsoft Sentinel gesendet werden können
description: >-
  Erfahren Sie mehr über die vordefinierten Gruppen von Windows-Sicherheitsereignissen, die Sie sammeln und von Ihren Windows-Systemen an Ihren Microsoft Sentinel-Arbeitsbereich
   streamen können.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 82be0e0e42690c23157511a16e404b4cd0048a36
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520273"
---
# <a name="windows-security-event-sets-that-can-be-sent-to-microsoft-sentinel"></a>Windows Sicherheitsereignissätze, die an Microsoft Sentinel gesendet werden können

Wenn Sie Sicherheitsereignisse von Windows-Geräten mithilfe des [Datenconnectors für Windows-Sicherheitsereignisse](data-connectors-reference.md#windows-security-events-via-ama) (einschließlich der [Legacyversion](data-connectors-reference.md#security-events-via-legacy-agent-windows)) erfassen, können Sie auswählen, welche Ereignisse der folgenden Gruppen gesammelt werden sollen:

- **Alle Ereignisse:** alle Windows-Sicherheits- und AppLocker-Ereignisse.

- **Allgemein:** ein Standardsatz von Ereignissen zu Überwachungszwecken. In diesem Satz ist ein vollständiger Benutzerüberwachungspfad enthalten. Diese Gruppe enthält beispielsweise sowohl Ereignisse für Benutzeranmeldungen als auch Ereignisse für Benutzerabmeldungen (Ereignis-IDs 4624 und 4634). Außerdem enthält sie Überwachungsaktionen wie Sicherheitsgruppenänderungen, wichtige Domänencontroller-Kerberos-Vorgänge und andere Ereignisse, die Best Practices entsprechen.

    Die Ereignisgruppe **Common** (Häufige) enthält möglicherweise einige Arten von Ereignissen, die nicht so häufig auftreten.  Der Grund hierfür ist, dass der Hauptzweck der Gruppe **Common** (Häufige) darin besteht, die Anzahl von Ereignissen auf eine besser verwaltbare Anzahl zu senken und dennoch die Funktion eines vollständigen Überwachungspfads beizubehalten.

- **Minimal:** ein kleiner Satz von Ereignissen, die auf potenzielle Bedrohungen hinweisen können. Diese Gruppe enthält keinen vollständigen Überwachungspfad. Sie deckt nur Ereignisse ab, die möglicherweise auf eine erfolgreiche Sicherheitsverletzung hindeuten, sowie andere wichtige Ereignisse, die nur sehr selten auftreten. So enthält diese Gruppe beispielsweise erfolgreiche und nicht erfolgreiche Benutzeranmeldungen (Ereignis-IDs 4624 und 4625), aber keine Informationen zu Abmeldungen (4634). Letztere sind zwar wichtig für die Überwachung, aber nicht für das Erkennen von Sicherheitsverletzungen, und sie treten zudem relativ häufig auf. Der Großteil des Datenvolumens dieser Gruppe ist auf Anmeldeereignisse und auf Prozesserstellungsereignisse (Ereignis-ID 4688) zurückzuführen.

- **Benutzerdefiniert:** Ein Satz von Ereignissen, der von Ihnen (dem Benutzer) festgelegt und in einer Datensammlungsregel mithilfe von XPath-Abfragen definiert wird. [Weitere Informationen über die Datensammlungsregeln](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries).

## <a name="event-id-reference"></a>Ereignis-ID Referenz

Die folgende Liste bietet eine vollständige Aufschlüsselung der Sicherheits- und AppLocker-Ereignis-IDs für jeden Satz:

| Gruppe von Ereignissen | Erfasste Ereignis-IDs |
| --- | --- |
| **Minimal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **Common** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie die Sammlung Windows Ereignissen in Microsoft Sentinel filtern.

- Weitere Informationen über das [Sammeln von Windows-Sicherheitsereignissen](connect-windows-security-events.md).
- Erste Schritte beim Erkennen von Bedrohungen mit Microsoft Sentinel [mithilfe integrierter](detect-threats-built-in.md) oder [benutzerdefinierter](detect-threats-custom.md) Regeln.
