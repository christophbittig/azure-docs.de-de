---
title: 'Vorgehensweise: Erfüllen der Datenresidenzanforderungen in Azure Cosmos DB'
description: Erfahren Sie, wie Sie die Datenresidenzanforderungen in Azure Cosmos DB erfüllen, damit Ihre Daten und Sicherungen in einer einzelnen Region verbleiben.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 63892e4ed71ec435a7068c9c68b9981a71205fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345924"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Vorgehensweise: Erfüllen der Datenresidenzanforderungen in Azure Cosmos DB

In Azure Cosmos DB können Sie Ihre Daten und Sicherungen so konfigurieren, dass sie in einer einzelnen Region verbleiben, um die [Residenzanforderungen](https://azure.microsoft.com/global-infrastructure/data-residency/) zu erfüllen.

## <a name="residency-requirements-for-data"></a>Residenzanforderungen für Daten

In Azure Cosmos DB müssen Sie die regionsübergreifende Datenreplikation explizit konfigurieren. Erfahren Sie, wie Sie die Georeplikation mit [Azure-Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account) und [Azure CLI](scripts/cli/common/regions.md) konfigurieren. Um die Anforderungen an die Datenresidenz zu erfüllen, können Sie eine Azure-Richtlinie erstellen, die bestimmten Regionen ermöglicht, die Datenreplikation in unerwünschten Regionen zu verhindern.

## <a name="residency-requirements-for-backups"></a>Residenzanforderungen für Sicherungen

**Sicherungen im fortlaufenden Modus**: Diese Sicherungen sind standardmäßig resident, da sie entweder in lokal redundantem oder zonenredundantem Speicher gespeichert werden. Weitere Informationen finden Sie im Artikel [Konfigurieren und Verwalten von fortlaufender Sicherung und Zeitpunktwiederherstellung (Vorschau) – Verwenden des Azure-Portals](provision-account-continuous-backup.md).

**Sicherungen im periodischen Modus**: Standardmäßig werden Kontosicherungen im periodischen Modus im georedundanten Speicher gespeichert. Für Sicherungen in periodischen Modi können Sie die Datenredundanz auf Kontoebene konfigurieren. Es gibt drei Redundanzoptionen für den Sicherungsspeicher. Dabei handelt es sich um lokale Redundanz, Zonenredundanz oder Georedundanz. Informieren Sie sich über das [Ändern des Sicherungsintervalls und des Aufbewahrungszeitraums](configure-periodic-backup-restore.md#configure-backup-interval-retention) im Portal.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Erzwingen der Residenzanforderungen mit Azure Policy

Bei Datenresidenzanforderungen, laut denen Sie Ihre gesamten Daten in einer einzelnen Azure-Region speichern müssen, können Sie zonenredundante oder lokal redundante Sicherungen für Ihr Konto mit Azure Policy erzwingen.  Sie können auch eine Richtlinie erzwingen, dass die Azure Cosmos DB-Konten nicht in anderen Regionen georepliziert werden.

Azure Policy ist ein Dienst, mit dem Sie Richtlinien zum Anwenden von Regeln auf Azure-Ressourcen erstellen, zuweisen und verwalten können. Azure Policy hilft Ihnen, die Konformität dieser Ressourcen mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel sicherzustellen. Erfahren Sie, wie Sie mit [Azure Policy](policy.md) Governance und Kontrollen für Azure Cosmos DB-Ressourcen implementieren.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren und Verwalten der periodischen Sicherung mit dem [Azure-Portal](configure-periodic-backup-restore.md)
* Bereitstellen der fortlaufende Sicherung mit dem [Azure-Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), der [CLI](provision-account-continuous-backup.md#provision-cli) oder [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)
* Wiederherstellen des Kontos für die fortlaufende Sicherung mit dem [Azure-Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), der [CLI](restore-account-continuous-backup.md#restore-account-cli) oder [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)
* [Migrieren eines Konto von der regelmäßigen Sicherung zur fortlaufenden Sicherung](migrate-continuous-backup.md)

