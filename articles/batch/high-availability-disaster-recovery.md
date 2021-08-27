---
title: Hochverfügbarkeit und Notfallwiederherstellung
description: Enthält Informationen zum Entwerfen Ihrer Batch-Anwendung für einen regionalen Ausfall.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: eb3d0cb218e607e38b7478b7cc7956963e450f46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339235"
---
# <a name="design-your-batch-application-for-high-availability"></a>Entwerfen Ihrer Batch-Anwendung für Hochverfügbarkeit

Azure Batch ist in allen Azure-Regionen verfügbar, aber wenn ein Batch-Konto erstellt wird, muss es einer spezifischen Region zugeordnet werden. Alle Vorgänge für das Batch-Konto gelten dann für diese Region. Beispielsweise werden Pools und die zugehörigen virtuellen Computer (VMs) in der gleichen Region wie das Batch-Konto erstellt.

Beim Entwerfen einer Anwendung, die Batch verwendet, müssen Sie die Möglichkeit in Betracht ziehen, dass Batch in einer Region nicht verfügbar ist. In seltenen Fällen liegt ein Problem mit der Region als Ganzes, dem gesamten Batch-Dienst in der Region oder mit Ihrem spezifischen Batch-Konto vor.

Wenn die Batch verwendende Anwendung oder Lösung immer verfügbar sein muss, dann sollte sie entweder für ein Failover zu einer anderen Region konzipiert sein, oder die Workload sollte stets auf zwei oder mehr Regionen aufgeteilt sein. Beide Ansätze erfordern mindestens zwei Batch-Konten, und jedes Konto muss sich in einer anderen Region befinden.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Mehrere Batch-Konten in mehreren Regionen

Bei Verwendung mehrerer Batch-Konten in verschiedenen Regionen kann Ihre Anwendung weiterhin ausgeführt werden, wenn ein Batch-Konto in einer Region nicht mehr verfügbar ist. Wenn Ihre Anwendung hochverfügbar sein muss, ist die Verwendung mehrerer Konten besonders wichtig.

In manchen Fällen können Anwendungen dazu konzipiert sein, zwei oder mehr Regionen zu verwenden. Wenn Sie z.B. eine beträchtliche Kapazität benötigen, kann die Verwendung mehrerer Regionen erforderlich sein, um entweder einer umfangreichen Anwendung gerecht zu werden oder für zukünftiges Wachstum vorzusorgen. Für diese Anwendungen sind ebenfalls mehrere Batch-Konten erforderlich (ein Konto pro Region).

## <a name="design-considerations-for-providing-failover"></a>Entwurfsüberlegungen für eine Failovermöglichkeit

Bei der Bereitstellung einer Option für ein Failover auf eine alternative Region müssen alle Komponenten der Lösung berücksichtigt werden. Es reicht nicht aus, lediglich ein zweites Batch-Konto einzurichten. Beispielsweise ist in den meisten Batch-Anwendungen ein Azure-Speicherkonto erforderlich, wobei Speicherkonto und Batch-Konto sich in derselben Region befinden müssen, damit die Leistung akzeptabel ist.

Beim Entwerfen einer Failoverlösung sollten Sie folgende Punkte berücksichtigen:

- Erstellen Sie vorab alle erforderlichen Dienste in jeder Region, z. B. das Azure Batch-Konto und das Speicherkonto. Für das Erstellen von Konten fallen oft keine Gebühren an. Es fallen lediglich Gebühren an, wenn das Konto verwendet wird oder Daten gespeichert werden.
- Stellen Sie sicher, dass die entsprechenden [Kontingente](batch-quota-limit.md) für alle Abonnements vorab festgelegt werden, damit Sie die erforderliche Anzahl der Kerne mithilfe des Azure Batch-Kontos zuordnen können.
- Verwenden Sie Vorlagen und/oder Skripte zum Automatisieren der Bereitstellung der Anwendung in einer Region.
- Halten Sie Binärdateien und Verweisdaten der Anwendung in allen Regionen auf dem neuesten Stand. Wenn die Region auf dem neuesten Stand bleibt, ist sichergestellt, dass sie ohne Warten auf das Hochladen und die Bereitstellung von Dateien schnell online geschaltet werden kann. Wird beispielsweise eine benutzerdefinierte Anwendung, die auf Poolknoten installiert werden soll, mit Batch-Anwendungspaketen gespeichert und referenziert, sollte sie, wenn eine neue Version der Anwendung erstellt wird, auf jedes Batch-Konto hochgeladen und von der Poolkonfiguration referenziert werden (oder machen Sie die neue Version zur Standardversion).
- Vereinfachen Sie den Wechsel von Clients oder das Laden in andere Regionen in der Anwendung, die Azure Batch, den Speicher und andere Dienste aufruft.
- Ziehen Sie häufige Wechsel zu einer alternativen Region für den normalen Betrieb in Erwägung. Wechseln Sie beispielsweise mit zwei Bereitstellungen in separaten Regionen jeden Monat zur alternativen Region.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen von Batch-Konten mit dem [Azure-Portal](batch-account-create-portal.md), der [Azure-Befehlszeilenschnittstelle](./scripts/batch-cli-sample-create-account.md), [PowerShell](batch-powershell-cmdlets-get-started.md) oder der [Batch-Verwaltungs-API](batch-management-dotnet.md).
- Erfahren Sie mehr über die [Standardkontingente eines Batch-Kontos](batch-quota-limit.md) und darüber, wie die Kontingente erhöht werden können.
