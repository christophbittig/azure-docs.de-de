---
title: Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung
description: Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a2a63e62598b291388375e0eb520d390dbdc4278
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562825"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung

Dieser Artikel enthält eine Beschreibung der Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen
Sicherungs-/Wiederherstellungsfunktionen wurden vorübergehend entfernt, während wir Entwürfe und Funktionen fertig stellen.

## <a name="high-availability"></a>Hochverfügbarkeit
Das Konfigurieren von Hochverfügbarkeit und das Sicherstellen eines Failovers bei einem Ausfall der Infrastruktur ist noch nicht verfügbar.

## <a name="databases"></a>Datenbanken
Das Hosten mehrerer Datenbanken in einer Servergruppe wird nicht unterstützt, wenn Sie die Bereitstellung auf mehrere Workerknoten aufskaliert haben.

## <a name="roles-and-responsibilities"></a>Rollen und Zuständigkeiten

Die Rollen und Zuständigkeiten zwischen Microsoft und seinen Kunden unterscheiden sich für von Azure verwaltete Dienste (Platform-as-a-Service, PaaS) und Azure-Hybriddienste (z. B. PostgreSQL Hyperscale mit Azure Arc-Unterstützung). 

### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
In der Tabelle unten sind die Antworten auf häufig gestellte Fragen zu den Supportrollen und Zuständigkeiten zusammengefasst.

| Frage                      | Azure PaaS (Platform-as-a-Service) | Azure Arc-Hybriddienste |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Von wem wird die Infrastruktur bereitgestellt?  | Microsoft                          | Kunde                  |
| Von wem wird die Software bereitgestellt?*       | Microsoft                          | Microsoft                 |
| Wer übernimmt den Betrieb? | Microsoft                          | Kunde                  |
| Bietet Microsoft SLAs an?      | Ja                                | Nein                        |
| Wer ist für die SLAs verantwortlich? | Microsoft                          | Kunde                  |

\* Azure-Dienste

__Warum werden von Microsoft keine SLAs zu Azure Arc-Hybriddiensten bereitgestellt?__ Der Grund ist, dass Microsoft nicht der Besitzer der Infrastruktur ist und diese nicht betreibt. Bei Kunden ist dies anders.

## <a name="next-steps"></a>Nächste Schritte

- **Probieren Sie sie aus.** Mit dem [Azure Arc-Schnelleinstieg](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte ausführen. 

- **Erstellen Sie einen eigenen Cluster.** Führen Sie die folgenden Schritte aus, um einen eigenen Kubernetes-Cluster zu erstellen: 
   1. [Installieren der Clienttools](install-client-tools.md)
   2. [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md)
   3. [Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Weitere Informationen zu Datendiensten mit Azure Arc-Unterstützung](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Weitere Informationen zu Azure Arc](https://aka.ms/azurearc)
