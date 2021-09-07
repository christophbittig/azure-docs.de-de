---
title: Unterstützung für die Verwendung von Azure Site Recovery mit Azure Backup
description: Bietet einen Überblick darüber, wie Azure Site Recovery und Azure Backup zusammen verwendet werden können.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: 4f96ba97537763bb4aec0ce558a12d675369ad57
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567756"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Unterstützung für die Verwendung von Site Recovery mit Azure Backup

Dieser Artikel fasst die Unterstützung für die Verwendung von [Site Recovery](site-recovery-overview.md) mit [Azure Backup](../backup/backup-overview.md) zusammen.

**Aktion** | **Site Recovery-Unterstützung** | **Details**
--- | --- | ---
**Gemeinsame Bereitstellung der Dienste** | Unterstützt | Dienste sind interoperabel und können zusammen konfiguriert werden.
**Datensicherung/-wiederherstellung** | Unterstützt | Wenn Sicherung und Replikation für eine VM aktiviert sind und Sicherungen durchgeführt werden, gibt es kein Problem bei der Wiederherstellung von Dateien auf den quellseitigen VMs oder Gruppen von VMs. Die Replikation wird wie gewohnt fortgesetzt, ohne dass sich der Zustand der Replikation ändert.
**Datenträgerwiederherstellung** | Aktuell keine Unterstützung | Wenn Sie einen gesicherten Datenträger wiederherstellen, müssen Sie die Replikation für den virtuellen Computer deaktivieren und wieder aktivieren.
**VM-Wiederherstellung** | Aktuell keine Unterstützung | Wenn Sie einen virtuellen Computer oder eine Gruppe virtueller Computer wiederherstellen, müssen Sie die Replikation für den virtuellen Computer deaktivieren und wieder aktivieren.  

Beachten Sie, dass die obige Tabelle für alle unterstützten Azure Site Recovery-Szenarien gilt.
