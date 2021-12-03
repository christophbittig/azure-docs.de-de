---
title: Integration in Azure-Sicherheitsprodukte
description: In diesem Artikel wird beschrieben, wie Sie Azure-Sicherheitsdienste und Azure Purview verbinden, um die Sicherheitsfunktionalität zu erweitern.
author: aashishr
ms.author: aashishr
ms.service: purview
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 7369f64f16724eb5c660b54a3d14a30046a41685
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894921"
---
# <a name="integrate-azure-purview-with-azure-security-products"></a>Integrieren von Azure Purview in Azure-Sicherheitsprodukte

In diesem Dokument werden die erforderlichen Schritte zum Verbinden eines Azure Purview-Kontos mit verschiedenen Azure-Sicherheitsprodukten erläutert, um die Sicherheitsfunktionalität mit Datenklassifizierungen und Vertraulichkeitsbezeichnungen zu erweitern.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud
Azure Purview bietet umfassende Einblicke in die Vertraulichkeit Ihrer Daten. Dies erleichtert es Sicherheitsteams, die Microsoft Defender für Cloud verwenden, den Sicherheitsstatus der Organisation zu verwalten und ihre Workloads vor Bedrohungen zu schützen. Datenressourcen bleiben ein beliebtes Ziel für böswillige Akteure, sodass Sicherheitsteams unbedingt vertrauliche Datenressourcen in ihren Cloudumgebungen identifizieren, priorisieren und schützen müssen. Die Integration in Azure Purview erweitert den Einblick in die Datenebene, sodass Sicherheitsteams Ressourcen priorisieren können, die vertrauliche Daten enthalten.

Um die Vorteile dieser [Verbesserung in Microsoft Defender für Cloud](../security-center/information-protection.md) nutzen zu können, sind in Azure Purview keine zusätzlichen Schritte erforderlich. Einen ersten Überblick über die Sicherheitsverbesserungen erhalten Sie auf der [Bestandsseite](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/25) von Microsoft Defender für Cloud, auf der eine Liste der Datenquellen mit Klassifizierungen und Vertraulichkeitsbezeichnungen angezeigt wird.

### <a name="supported-data-sources"></a>Unterstützte Datenquellen
Die Integration unterstützt Datenquellen in Azure und AWS. Vertrauliche Daten, die in diesen Ressourcen ermittelt werden, werden von Microsoft Defender für Cloud geschützt:
- [Azure Blob Storage](./register-scan-azure-blob-storage-source.md)
- [Azure Cosmos DB](./register-scan-azure-cosmos-database.md)
- [Azure Data Explorer](./register-scan-azure-data-explorer.md)
- [Azure Data Lake Storage Gen1](./register-scan-adls-gen1.md)
- [Azure Data Lake Storage Gen2](./register-scan-adls-gen2.md)
- [Azure Files](./register-scan-azure-files-storage-source.md)
- [Azure Database for MySQL](./register-scan-azure-mysql-database.md)
- [Azure-Datenbank für PostgreSQL](./register-scan-azure-postgresql.md)
- [Verwaltete Azure SQL-Datenbank-Instanz](./register-scan-azure-sql-database-managed-instance.md)
- [Dedizierter Azure-SQL-Pool (vormals SQL DW)](./register-scan-azure-synapse-analytics.md)
- [Azure SQL-Datenbank](./register-scan-azure-sql-database.md)
- [Azure Synapse Analytics (Arbeitsbereich)](./register-scan-synapse-workspace.md)
- [Amazon S3](./register-scan-amazon-s3.md)

### <a name="known-issues"></a>Bekannte Probleme
1. Informationen zur Vertraulichkeit von Daten sind derzeit nicht für Quellen verfügbar, die auf virtuellen Computern gehostet werden, z. B. SAP, Erwin und Teradata.
2. Informationen zur Vertraulichkeit von Daten sind derzeit nicht für Amazon RDS verfügbar.
3. Informationen zur Vertraulichkeit von Daten sind derzeit nicht für Azure PaaS-Datenquellen verfügbar, die über eine Verbindungszeichenfolge registriert wurden. 
5. Durch das Aufheben der Registrierung der Datenquelle in Azure Purview werden die erweiterten Informationen zur Vertraulichkeit von Daten in Microsoft Defender für Cloud nicht entfernt.
6. Beim Löschen des Azure Purview-Kontos werden die erweiterten Informationen zur Vertraulichkeit von Daten 30 Tage lang in Microsoft Defender für Cloud beibehalten.
7. Benutzerdefinierte Klassifizierungen, die im Microsoft 365 Compliance Center oder in Azure Purview definiert sind, werden nicht für Microsoft Defender für Cloud verwendet.

## <a name="faq"></a>Häufig gestellte Fragen
### <a name="why-dont-i-see-the-aws-data-source-i-have-scanned-with-azure-purview-in-microsoft-defender-for-cloud"></a>**Warum wird die AWS-Datenquelle, die ich mit Azure Purview in Microsoft Defender für Cloud überprüft habe, nicht angezeigt?**

Für Datenquellen muss auch in Microsoft Defender für Cloud ein Onboarding durchgeführt werden. Erfahren Sie mehr darüber, wie Sie Ihre [AWS-Konten verbinden](../security-center/quickstart-onboard-aws.md) und Ihre AWS-Datenquellen in Microsoft Defender für Cloud anzeigen.

### <a name="why-dont-i-see-sensitivity-labels-in-microsoft-defender-for-cloud"></a>**Warum werden in Microsoft Defender für Cloud keine Vertraulichkeitsbezeichnungen angezeigt?**

Ressourcen müssen zunächst in Azure Purview eine Bezeichnung erhalten, damit die Bezeichnungen in Microsoft Defender für Cloud angezeigt werden. Überprüfen Sie, ob die [Voraussetzungen für Vertraulichkeitsbezeichnungen](./how-to-automatically-label-your-content.md) erfüllt sind. Nachdem die Daten überprüft wurden, werden die Bezeichnungen in Azure Purview und anschließend automatisch in Microsoft Defender für Cloud angezeigt.

## <a name="next-steps"></a>Nächste Schritte
- [Verbesserte Funktionalität in Microsoft Defender für Cloud durch Informationen zur Vertraulichkeit in Azure Purview](../security-center/information-protection.md)
