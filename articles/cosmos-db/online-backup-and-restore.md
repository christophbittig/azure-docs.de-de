---
title: Onlinesicherung und bedarfsgesteuerte Datenwiederherstellung in Azure Cosmos DB
description: In diesem Artikel wird beschrieben, wie die automatische Sicherung und die bedarfsgesteuerte Datenwiederherstellung funktionieren. Außerdem wird der Unterschied zwischen den Modi für die fortlaufende und regelmäßige Sicherung erläutert.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9441cb178769b7d8cad6e60d0bf411c4c38710c5
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473458"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Onlinesicherung und bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Die automatischen Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit des Datenbankbetriebs. Alle Sicherungen werden separat in einem Speicherdienst gespeichert. Automatische Sicherungen sind in Situationen hilfreich, in denen Sie versehentlich Ihr Azure Cosmos-Konto, die Datenbank oder einen Container löschen oder aktualisieren und später eine Datenwiederherstellung durchführen möchten. Es gibt zwei Sicherungsmodi:

* **Regelmäßiger Sicherungsmodus:** Dieser Modus ist der Standardsicherungsmodus für alle vorhandenen Konten. In diesem Modus wird in regelmäßigen Abständen eine Sicherung durchgeführt, und die Datenwiederherstellung erfolgt über eine Anforderung an das Supportteam. In diesem Modus konfigurieren Sie ein Sicherungsintervall und eine Aufbewahrungsdauer für Ihr Konto. Die maximale Beibehaltungsdauer beträgt einen Monat. Das Mindestintervall für Sicherungen beträgt eine Stunde.  Weitere Informationen finden Sie im Artikel zum [regelmäßigen Sicherungsmodus](configure-periodic-backup-restore.md).

* **Fortlaufender Sicherungsmodus:** Sie wählen diesen Modus beim Erstellen des Azure Cosmos DB-Kontos aus. Dieser Modus ermöglicht Ihnen die Wiederherstellung zu jedem beliebigen Zeitpunkt innerhalb der letzten 30 Tage. Weitere Informationen finden Sie in der [Einführung in den fortlaufenden Sicherungsmodus](continuous-backup-restore-introduction.md). Informationen zum Bereitstellen der fortlaufenden Sicherung finden Sie in den Artikeln zum [Azure-Portal](provision-account-continuous-backup.md#provision-portal), zu [PowerShell](provision-account-continuous-backup.md#provision-powershell), zur [Befehlszeilenschnittstelle](provision-account-continuous-backup.md#provision-cli) und zu [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).

  > [!NOTE]
  > Wenn Sie ein neues Konto mit fortlaufender Sicherung konfigurieren, können Sie eine Self-Service-Wiederherstellung über Azure-Portal, PowerShell oder die Befehlszeilenschnittstelle durchführen. Wenn Ihr Konto im fortlaufenden Modus konfiguriert ist, können Sie nicht wieder in den regelmäßigen Modus wechseln.

Bei Konten mit aktiviertem Azure Synapse Link sind Analysespeicherdaten nicht in den Sicherungen und Wiederherstellungen enthalten. Wenn Synapse Link aktiviert ist, erstellt Azure Cosmos DB weiterhin automatisch im geplanten Sicherungsintervall Sicherungen Ihrer Daten im Transaktionsspeicher. Die automatische Sicherung und Wiederherstellung Ihrer Daten im Analysespeicher wird derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich als Nächstes, wie Sie die Modi für fortlaufende und regelmäßige Sicherung für Ihr Konto konfigurieren und verwalten:

* [Konfigurieren und verwalten Sie eine Richtlinie für die regelmäßige Sicherung](configure-periodic-backup-restore.md).
* Was ist der Modus für die [fortlaufende Sicherung](continuous-backup-restore-introduction.md)?
* Bereitstellen der fortlaufende Sicherung über das [Azure-Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), die [Befehlszeilenschnittstelle](provision-account-continuous-backup.md#provision-cli) oder [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)
* Wiederherstellen des Kontos für die fortlaufende Sicherung mit dem [Azure-Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), der [CLI](restore-account-continuous-backup.md#restore-account-cli) oder [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)
* [Migrieren eines Konto von der regelmäßigen Sicherung zur fortlaufenden Sicherung](migrate-continuous-backup.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
