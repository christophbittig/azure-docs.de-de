---
title: Ressourcenverfügbarkeit in verschiedenen Region
description: Verfügbarkeit von Compute- und Arbeitsspeicherressourcen für den Azure Container Instances-Dienst in verschiedenen Azure-Regionen
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 13cdd53d345ed983fa4954662d3bb5905ccafbe3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843763"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Ressourcenverfügbarkeit für Azure Container Instances in Azure-Regionen

In diesem Artikel wird die Verfügbarkeit der Compute-, Arbeitsspeicher- und Speicherressourcen von Azure Container Instances in Azure-Regionen und nach Zielbetriebssystem erläutert. Eine allgemeine Liste der verfügbaren Regionen für Azure Container Instances finden Sie unter [verfügbare Regionen](https://azure.microsoft.com/regions/services/).

Die angezeigten Werte stellen die maximalen Ressourcen dar, die pro Bereitstellung einer [Containergruppe](container-instances-container-groups.md) zur Verfügung stehen. Die Werte sind zum Zeitpunkt der Veröffentlichung aktuell.

> [!NOTE]
> Containergruppen, die innerhalb dieser Ressourcenlimits erstellt werden, unterliegen der Verfügbarkeit in der Bereitstellungsregion. Wenn eine Region stark ausgelastet ist, kann bei der Bereitstellung von Instanzen ein Fehler auftreten. Um einen solchen Fehler bei der Bereitstellung zu beheben, versuchen Sie, Instanzen mit niedrigeren Ressourceneinstellungen bereitzustellen, oder führen Sie die Bereitstellung zu einem späteren Zeitpunkt oder in einer anderen Region mit verfügbaren Ressourcen durch.

Weitere Informationen zu Kontingenten und anderen Grenzwerten in Ihren Bereitstellungen finden Sie unter [Kontingente und Regionsverfügbarkeit für Azure Container Instances](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Linux-Containergruppen

Die folgenden Regionen und maximalen Ressourcen sind für Containergruppen mit Linux-Containern in allgemeinen Bereitstellungen, [Azure Virtual Network](container-instances-vnet.md)-Bereitstellungen und Bereitstellungen mit [GPU-Ressourcen](container-instances-gpu.md) (Vorschau) verfügbar.

> [!IMPORTANT]
> Die maximalen Ressourcen in einer Region können abhängig von Ihrer Bereitstellung abweichen. Eine Region kann z. B. in einer Azure Virtual Network-Bereitstellung andere Werte für die maximale CPU- und Arbeitsspeichergröße aufweisen als bei einer allgemeinen Bereitstellung. Dieselbe Region kann auch über andere Höchstwerte für eine Bereitstellung mit GPU-Ressourcen verfügen. Überprüfen Sie den Bereitstellungstyp, bevor Sie sich die maximalen Werte für Ihre Region in den unten aufgeführten Tabellen ansehen.

> [!NOTE]
> Einige Regionen unterstützen keine Verfügbarkeitszonen (in der folgenden Tabelle als „-“ (N/V) gekennzeichnet), und einige Regionen verfügen über Verfügbarkeitszonen, aber ACI nutzt die Funktion derzeit nicht (in der folgenden Tabelle als „N“ gekennzeichnet). Weitere Informationen finden Sie unter [Azure-Regionen mit Verfügbarkeitszonen][az-region-support].

| Region | Maximale CPU-Auslastung | Maximaler Speicher (GB) | VNET: max. CPU | VNET: max. Arbeitsspeicher (GB) | Speicher (GB) | GPU: SKUs (Vorschau) | Unterstützung von Verfügbarkeitszonen |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: | :----: |
| Australien (Osten) | 4 | 16 | 4 | 16 | 50 | – | J |
| Australien, Südosten | 4 | 14 | – | – | 50 | – | N |
| Brasilien Süd | 4 | 16 | 2 | 8 | 50 | – | J |
| Kanada, Mitte | 4 | 16 | 4 | 16 | 50 | – | N |
| Kanada, Osten | 4 | 16 | 4 | 16 | 50 | – | N |
| Indien, Mitte | 4 | 16 | 4 | 4 | 50 | V100 | N |
| USA (Mitte) | 4 | 16 | 4 | 16 | 50 | – | J |
| Asien, Osten | 4 | 16 | 4 | 16 | 50 | – | N |
| East US | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | J |
| USA (Ost) 2 | 4 | 16 | 4 | 16 | 50 | – | J |
| Frankreich, Mitte | 4 | 16 | 4 | 16 | 50 | – | J|
| Deutschland, Westen-Mitte | 4 | 16 | – | – | 50 | – | J |
| Japan, Osten | 2 | 8 | 4 | 16 | 50 | – | J |
| Japan, Westen | 4 | 16 | – | – | 50 | – | N |
| Korea, Mitte | 4 | 16 | – | – | 50 | – | N |
| USA Nord Mitte | 2 | 3,5 | 4 | 16 | 50 | K80, P100, V100 | N |
| Nordeuropa | 4 | 16 | 4 | 16 | 50 | K80 | J |
| Norwegen, Osten | 4 | 16 | – | – | 50 | – | N |
| USA Süd Mitte | 4 | 16 | 4 | 16 | 50 | V100 | J |
| Asien, Südosten | 4 | 16 | 4 | 16 | 50 | P100, V100 | J |
| Indien (Süden) | 4 | 16 | – | – | 50 | K80 | N |
| Schweiz, Norden | 4 | 16 | – | – | 50 | – | N |
| UK, Süden | 4 | 16 | 4 | 16 | 50 | – | J|
| UK, Westen | 4 | 16 | – | – | 50 | – | N |
| Vereinigte Arabische Emirate, Norden | 4 | 16 | – | – | 50 | – | N |
| USA, Westen-Mitte| 4 | 16 | 4 | 16 | 50 | – | N |
| Europa, Westen | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | J |
| USA (Westen) | 4 | 16 | 4 | 16 | 50 | – | N |
| USA, Westen 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 | J |

Für eine Containergruppe, die mit [GPU-Ressourcen](container-instances-gpu.md) (Vorschau) bereitgestellt wird, sind die folgenden maximalen Ressourcen verfügbar.

> [!IMPORTANT]
> Zurzeit werden Bereitstellungen mit GPU-Ressourcen in einer Azure Virtual Network-Bereitstellung nicht unterstützt und sind nur für Linux-Containergruppen verfügbar.

| GPU: SKUs | GPU-Anzahl | Maximale CPU-Auslastung | Maximaler Speicher (GB) | Speicher (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows-Containergruppen

Die folgenden Regionen und maximalen Ressourcen stehen Containergruppen mit [unterstützten und Vorschau-](./container-instances-faq.yml)Windows Server-Containern zur Verfügung.

> [!IMPORTANT]
> Zurzeit werden Bereitstellungen mit Windows-Containergruppen in einer Azure Virtual Network-Bereitstellung nicht unterstützt.

### <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> 1B- und 2B-Hosts wurden für Windows Server 2016 eingestellt. Weitere Informationen zu den 1B-, 2B- und 3B-Hosts finden Sie unter [Kompatibilität der Host- und Containerversionen](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility).

| Region |3B: max. CPU | 3B: max. Arbeitsspeicher (GB) | Speicher (GB) |
| -------- | :----: | :-----: | :-------: |
| Australien (Osten) | 2 | 8 | 20 |
| Brasilien Süd | 4 | 16 | 20 |
| Kanada, Mitte  | 2 | 3,5 | 20 |
| Indien, Mitte | 2 | 3,5 | 20 |
| USA (Mitte) | 2 | 3,5 | 20 |
| Asien, Osten | 2 | 3,5 | 20 |
| East US | 2 | 8 | 20 |
| USA (Ost) 2 | 4 | 16 | 20 |
| Japan, Osten | 4 | 16 | 20 |
| Korea, Mitte | 4 | 16 | 20 |
| USA Nord Mitte | 4 | 16 | 20 |
| Nordeuropa | 2 | 8 | 20 |
| USA Süd Mitte | 2 | 8 | 20 |
| Asien, Südosten | 2 | 3,5 | 20 |
| Indien (Süden) | 2 | 3,5 | 20 |
| UK, Süden | 2 | 3,5 | 20 |
| USA, Westen-Mitte | 2 | 8 | 20 |
| Europa, Westen | 4 | 16 | 20 |
| USA (Westen) | 2 | 8 | 20 |
| USA, Westen 2 | 2 | 3,5 | 20 |

### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> 1B- und 2B-Hosts wurden für Windows Server 2019 LTSC eingestellt. Weitere Informationen zu den 1B-, 2B- und 3B-Hosts finden Sie unter [Kompatibilität der Host- und Containerversionen](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility).

| Region | 3B: max. CPU | 3B: max. Arbeitsspeicher (GB) | Speicher (GB) | Unterstützung von Verfügbarkeitszonen |
| -------- | :----: | :-----: | :-------: |
| Australien (Osten) | 4 | 16 | 20 | J |
| Brasilien Süd | 4 | 16 | 20 | J |
| Kanada, Mitte | 4 | 16 | 20 | N |
| Indien, Mitte | 4 | 16 | 20 | N |
| USA (Mitte) | 4 | 16 | 20 | J |
| Asien, Osten | 4 | 16 | 20 | N |
| East US | 4 | 16 | 20 | J |
| USA (Ost) 2 | 2 | 3,5 | 20 | J |
| Frankreich, Mitte | 4 | 16 | 20 | J |
| Japan, Osten | 4 | 16 | 20 | J |
| Korea, Mitte | 4 | 16 | 20 | N |
| USA Nord Mitte | 4 | 16 | 20 | N |
| Nordeuropa | 4 | 16 | 20 | J |
| USA Süd Mitte | 4 | 16 | 20 | J |
| Asien, Südosten | 4 | 16 | 20 | J |
| Indien (Süden) | 4 | 16 | 20 | N |
| UK, Süden | 4 | 16 | 20 | J |
| USA, Westen-Mitte | 4 | 16 | 20 | N |
| Europa, Westen | 4 | 16 | 20 | J |
| USA (Westen) | 4 | 16 | 20 | N |
| USA, Westen 2 | 4 | 16 | 20 | J |

## <a name="next-steps"></a>Nächste Schritte

Teilen Sie dem Team auf [aka.ms/aci/feedback](https://aka.ms/aci/feedback) mit, wenn zusätzliche Regionen und eine Erhöhung der Ressourcenverfügbarkeit erforderlich sind.

Informationen zur Problembehandlung bei der Bereitstellung von Containerinstanzen finden Sie unter [Beheben von Bereitstellungsproblemen für Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[az-region-support]: /availability-zones/az-region.md#azure-regions-with-availability-zones