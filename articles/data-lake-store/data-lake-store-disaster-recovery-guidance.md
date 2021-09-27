---
title: Leitfaden zur Notfallwiederherstellung für Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihre Daten weiter vor regionsweiten Ausfällen oder versehentlichen Löschvorgängen über den lokal redundanten Speicher hinaus von Azure Data Lake Storage Gen1 schützen.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: normesta
ms.openlocfilehash: 59c2ff76f62e64e193b51ea479c04f8897c27717
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599561"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Leitfaden zu Hochverfügbarkeit und Notfallwiederherstellung für Data Lake Storage Gen1

Data Lake Storage Gen1 bietet lokal redundanten Speicher (Locally-Redundant Storage, LRS). Die Daten in Ihrem Data Lake Storage Gen1-Konto sind daher über automatisierte Replikate vor vorübergehenden Hardwareausfällen innerhalb eines Rechenzentrums geschützt. So wird für Dauerhaftigkeit und Hochverfügbarkeit gesorgt, und die Vereinbarung zum Servicelevel (SLA) für Data Lake Storage Gen1 wird erfüllt. In diesem Artikel wird beschrieben, wie Sie Ihre Daten weiter vor selten auftretenden regionsweiten Ausfällen oder dem versehentlichen Löschen schützen.

## <a name="disaster-recovery-guidance"></a>Leitfäden zur Notfallwiederherstellung

Es ist wichtig, einen Plan für die Notfallwiederherstellung vorzubereiten. Lesen Sie die Informationen in diesem Artikel und diese zusätzlichen Ressourcen, die Sie beim Erstellen eines eigenen Plans unterstützen.

* [Notfallwiederherstellung und Hochverfügbarkeit für Azure-Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Technischer Leitfaden zur Resilienz in Azure](/azure/architecture/framework/resiliency/app-design)

### <a name="best-practice-recommendations"></a>Empfehlungen zu bewährten Methoden

Es empfiehlt sich, Ihre kritischen Daten in ein anderes Data Lake Storage Gen1-Konto in einer anderen Region zu kopieren und die Häufigkeit des Vorgangs an die Anforderungen Ihres Plans für die Notfallwiederherstellung anzupassen. Es gibt viele verschiedene Methoden zum Kopieren von Daten, z. B. [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) oder [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory ist ein nützlicher Dienst zum regelmäßigen Erstellen und Bereitstellen von Pipelines für die Datenverschiebung.

Im Falle eines regionalen Ausfalls können Sie auf Ihre Daten in der Region zugreifen, in die die Daten kopiert wurden. Über das [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) können Sie den weltweiten Azure-Dienststatus ermitteln.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Anleitung zur Wiederherstellung bei Datenbeschädigung oder unbeabsichtigtem Löschen

Dank automatisierter Replikate sorgt Data Lake Storage Gen1 zwar für Datenresilienz, die Lösung kann aber nicht verhindern, dass Ihre Anwendungen (oder Entwickler/Benutzer) Daten beschädigen oder versehentlich löschen.

Zur Verhinderung des versehentlichen Löschens empfiehlt es sich, geeignete Zugriffsrichtlinien für Ihr Data Lake Storage Gen1-Konto festzulegen. Dies beinhaltet auch die Anwendung von [Azure-Ressourcensperren](../azure-resource-manager/management/lock-resources.md), um wichtige Ressourcen zu sperren, sowie die Anwendung einer Zugriffssteuerung auf Konto- und Dateiebene mithilfe der verfügbaren [Data Lake Storage Gen1-Sicherheitsfeatures](data-lake-store-security-overview.md). Darüber hinaus ist es ratsam, routinemäßig Kopien Ihrer kritischen Daten per [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) oder [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in einem anderen Data Lake Storage Gen1-Konto, Ordner oder Azure-Abonnement zu erstellen. Diese Vorgehensweise kann verwendet werden, um Daten nach einer Beschädigung oder nach dem Löschen wiederherzustellen. Azure Data Factory ist ein nützlicher Dienst zum regelmäßigen Erstellen und Bereitstellen von Pipelines für die Datenverschiebung.

Sie können die [Diagnoseprotokollierung](data-lake-store-diagnostic-logs.md) für ein Data Lake Storage Gen1-Konto aktivieren, um Überwachungsprotokolle zum Datenzugriff zu erfassen. Die Überwachungsprotokolle enthalten Informationen darüber, wer eine Datei möglicherweise gelöscht oder aktualisiert hat.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
