---
title: Regionales Kapazitätskontingent für Azure NetApp Files | Microsoft-Dokumentation
description: Hier wird das regionale Kapazitätskontingent für Azure NetApp Files erläutert.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: b-juche
ms.openlocfilehash: 1ae89ff8b8593d96740b0140a1570ac19deacea3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270683"
---
# <a name="regional-capacity-quota-for-azure-netapp-files"></a>Regionales Kapazitätskontingent für Azure NetApp Files

In diesem Artikel wird das regionale Kapazitätskontingent für Azure NetApp Files erläutert.

## <a name="display-regional-capacity-quota"></a>Anzeigen des regionalen Kapazitätskontingents

Sie können in den Einstellungen für Azure NetApp Files auf **Kontingent** klicken, um die aktuellen und standardmäßigen Kontingentgrößen für die Region anzuzeigen. 

Zum Beispiel: 

![Screenshot: Anzeigen von Kontingentinformationen](../media/azure-netapp-files/quota-display.png) 

## <a name="request-regional-capacity-quota-increase"></a>Anfordern einer Erhöhung des regionalen Kapazitätskontingents

Sie können eine [Supportanfrage](azure-netapp-files-resource-limits.md#request-limit-increase) zur Erhöhung eines regionalen Kapazitätskontingent einreichen, ohne dass Ihnen zusätzliche Kosten entstehen. Die von Ihnen übermittelte Supportanfrage wird zur Verarbeitung an das Azure-Team für die Kapazitätsverwaltung gesendet. Sie erhalten in der Regel innerhalb von zwei Arbeitstagen eine Antwort. Wenn Sie eine umfangreiche Erhöhung anfordern, werden Sie möglicherweise vom Azure-Team für die Kapazitätsverwaltung kontaktiert.  

Eine Erhöhung des regionalen Kapazitätskontingents führt nicht zu einer Erhöhung der Abrechnung. Die Abrechnung basiert weiterhin auf den bereitgestellten Kapazitätspools.
Wenn Sie beispielsweise derzeit über eine bereitgestellte Kapazität von 25 TiB verfügen, können Sie eine Erhöhung des Kontingents auf 35 TiB anfordern.  Innerhalb von zwei Werktagen wird Ihre Kontingenterhöhung auf die angeforderte Region angewendet. Wenn die Kontingenterhöhung angewendet wird, zahlen Sie weiterhin nur für die derzeit bereitgestellte Kapazität (25 TiB). Wenn Sie jedoch die zusätzlichen 10 TiB tatsächlich bereitstellen, werden Ihnen 35 TiB in Rechnung gestellt.

Die aktuellen [Ressourcenlimits](azure-netapp-files-resource-limits.md#resource-limits) für Azure NetApp Files bleiben unverändert. Sie können weiterhin einen 500-TiB-Kapazitätspool bereitstellen. Vorher muss das regionale Kapazitätskontingent jedoch auf 500 TiB erhöht werden.

## <a name="next-steps"></a>Nächste Schritte  

- [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Grundlegendes zur Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
