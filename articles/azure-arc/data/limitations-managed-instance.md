---
title: Einschränkungen für Azure Arc-fähigen SQL Managed Instance-Instanzen
description: Einschränkungen für Azure Arc-fähigen SQL Managed Instance-Instanzen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
ms.openlocfilehash: 8709504d93f22ee5f2704b7b1a8d71a5e66df796
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838230"
---
# <a name="limitations-of-azure-arc-enabled-sql-managed-instance"></a>Einschränkungen für Azure Arc-fähigen SQL Managed Instance-Instanzen

Dieser Artikel enthält eine Beschreibung der Einschränkungen für Azure Arc-fähige SQL Managed Instance-Instanzen. 

Die Dienstebene Unternehmenskritisch befindet sich derzeit in der öffentlichen Vorschauphase. Die Dienstebene „Universell“ ist allgemein verfügbar.

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

### <a name="automated-backups"></a>Automatisierte Sicherungen 

-  Benutzerdatenbanken mit einfachem Wiederherstellungsmodell werden nicht gesichert.
-  Die Systemdatenbank `model` wird nicht gesichert, um Konflikte beim Erstellen/Löschen der Datenbank zu verhindern. Die Datenbank wird gesperrt, wenn Administratorvorgänge ausgeführt werden.

### <a name="point-in-time-restore-pitr"></a>Point-in-Time-Wiederherstellung

-  Die Wiederherstellung zwischen Azure Arc-fähigen SQL Managed Instance-Instanzen wird nicht unterstützt.  Die Datenbank kann nur in derselben Instanz von SQL Managed Instance mit Azure Arc-Unterstützung hergestellt werden, in der die Sicherungen erstellt wurden.
-  Das Umbenennen einer Datenbank bei einer Zeitpunktwiederherstellung wird derzeit nicht unterstützt.
-  Die Wiederherstellung einer Datenbank mit aktivierter TDE (Transparent Data Encryption) wird aktuell nicht unterstützt.
-  Gelöschte Datenbanken können derzeit nicht wiederhergestellt werden.

## <a name="other-limitations"></a>Weitere Einschränkungen 

-  Die Transaktionsreplikation wird derzeit nicht unterstützt.
-  Der Protokollversand wird derzeit blockiert.
-  Nur die SQL Server-Authentifizierung wird unterstützt.

## <a name="roles-and-responsibilities"></a>Rollen und Zuständigkeiten

Die Rollen und Zuständigkeiten zwischen Microsoft und den Kunden*innen unterscheiden sich für Azure PaaS-Dienste (Platform as a Service) und Azure-Hybriddienste (z. B. Azure Arc-fähige SQL Managed Instance-Instanzen). 

### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

In der Tabelle unten sind die Antworten auf häufig gestellte Fragen zu den Supportrollen und Zuständigkeiten zusammengefasst.

| Frage                          | Azure PaaS (Platform-as-a-Service) | Azure Arc-Hybriddienste |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Von wem wird die Infrastruktur bereitgestellt?  | Microsoft                          | Kunde                  |
| Von wem wird die Software bereitgestellt?*       | Microsoft                          | Microsoft                 |
| Wer übernimmt den Betrieb?          | Microsoft                          | Kunde                  |
| Bietet Microsoft SLAs an?      | Ja                                | Nein                        |
| Wer ist für die SLAs verantwortlich?          | Microsoft                          | Kunde                  |

\* Azure-Dienste

__Warum werden von Microsoft keine SLAs zu Azure Arc-Hybriddiensten bereitgestellt?__ Der Grund ist, dass Microsoft nicht der Besitzer der Infrastruktur ist und diese nicht betreibt. Bei Kunden ist dies anders.

## <a name="next-steps"></a>Nächste Schritte

- **Probieren Sie sie aus.** Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte ausführen. 

- **Erstellen Sie einen eigenen Cluster.** Führen Sie die folgenden Schritte aus, um einen eigenen Kubernetes-Cluster zu erstellen: 
   1. [Installieren der Clienttools](install-client-tools.md)
   2. [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md)
   3. [Erstellen von Azure Arc-fähigen SQL Managed Instance-Instanzen](create-sql-managed-instance.md) 

- **Learn**
   - [Weitere Informationen zu Datendiensten mit Azure Arc-Unterstützung](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Weitere Informationen zu Azure Arc](https://aka.ms/azurearc)
