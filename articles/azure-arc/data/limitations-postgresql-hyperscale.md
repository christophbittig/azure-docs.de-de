---
title: Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung
description: Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 4fdcf88200b3c2a6b5371633c10d9246cfbf4c31
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730242"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung

Dieser Artikel enthält eine Beschreibung der Einschränkungen für PostgreSQL Hyperscale mit Azure Arc-Unterstützung. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

- Die Zeitpunktwiederherstellung (z. B. die Wiederherstellung mit einer bestimmten Datumsangabe und Uhrzeit) in derselben Servergruppe wird nicht unterstützt. Beim Durchführen einer Zeitpunktwiederherstellung müssen Sie eine andere Servergruppe verwenden, die Sie vor dem Wiederherstellen bereitgestellt haben. Nach der Wiederherstellung in der neuen Servergruppe können Sie die ursprüngliche Servergruppe löschen.
- Die Wiederherstellung des gesamten Inhalts einer Sicherung (im Gegensatz zur Wiederherstellung bis zu einem bestimmten Zeitpunkt) in derselben Servergruppe wird für PostgreSQL-Version 12 unterstützt. Aufgrund einer Einschränkung der PostgreSQL-Engine in Bezug auf Zeitachsen wird dies für PostgreSQL-Version 11 nicht unterstützt. Um den gesamten Inhalt einer Sicherungskopie für eine Servergruppe unter PostgreSQL-Version 11 wiederherzustellen, müssen Sie für den Vorgang eine andere Servergruppe nutzen.


## <a name="databases"></a>Datenbanken

Das Hosten von mehr als einer Datenbank in einer Servergruppe wird nicht unterstützt.


## <a name="security"></a>Sicherheit

Das Verwalten von Benutzern und Rollen wird nicht unterstützt. Verwenden Sie vorerst weiter den Postgres-Standardbenutzer.

## <a name="roles-and-responsibilities"></a>Rollen und Zuständigkeiten

Die Rollen und Zuständigkeiten zwischen Microsoft und seinen Kunden unterscheiden sich für Azure PaaS-Dienste (Platform-as-a-Service) und Azure-Hybriddienste (z. B. PostgreSQL Hyperscale mit Azure Arc-Unterstützung). 

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

- **Probieren Sie sie aus.** Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte ausführen. 

- **Erstellen Sie einen eigenen Cluster.** Führen Sie die folgenden Schritte aus, um einen eigenen Kubernetes-Cluster zu erstellen: 
   1. [Installieren der Clienttools](install-client-tools.md)
   2. [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md)
   3. [Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Weitere Informationen zu Datendiensten mit Azure Arc-Unterstützung](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Weitere Informationen zu Azure Arc](https://aka.ms/azurearc)
