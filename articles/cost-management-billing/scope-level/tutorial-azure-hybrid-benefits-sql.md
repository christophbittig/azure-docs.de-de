---
title: 'Tutorial: Optimieren des zentral verwalteten Azure-Hybridvorteils für SQL Server'
description: Dieses Tutorial führt Sie durch die proaktive Zuweisung von SQL Server Lizenzen in Azure zum Verwalten und Optimieren des Azure-Hybridvorteils.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 6031bd83a5a32ffc5e76a4a967e305324f8d7ee8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218861"
---
# <a name="tutorial-optimize-centrally-managed-azure-hybrid-benefit-for-sql-server"></a>Tutorial: Optimieren des zentral verwalteten Azure-Hybridvorteils für SQL Server

Dieses Tutorial führt Sie durch die proaktive Zuweisung von SQL Server-Lizenzen in Azure zum zentralen Verwalten und Optimieren des [Azure-Hybridvorteils](https://azure.microsoft.com/pricing/hybrid-benefit/). Die Optimierung des Vorteils senkt die Kosten für die Ausführung von Azure SQL.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Sammeln von Details zur Nutzung und Verfügbarkeit von Lizenzen
> * Bedarfsbasiertes Erwerben weiterer Lizenzen
> * Zuweisen von Lizenzen zu Azure
> * Überwachen und Anpassen der Nutzung
> * Einrichten eines Zeitplans für die Verwaltung

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen die folgenden Voraussetzungen erfüllt sein:

Sie haben den Artikel [Worum handelt es sich bei der Verwaltung des Azure-Hybridvorteils auf Bereichsebene?](overview-azure-hybrid-benefit-scope.md) gelesen und verstanden. In diesem Artikel werden die verschiedenen Arten von SQL Server-Lizenzen vorgestellt, die für den Azure-Hybridvorteil qualifiziert sind. Außerdem wird erläutert, wie Sie den Azure-Hybridvorteil im ausgewählten Bereich auf Abonnementebene oder für das gesamte Abrechnungskonto aktivieren und verwenden.

> [!NOTE]
> Die zentrale Verwaltung des Azure-Hybridvorteils auf Bereichsebene befindet sich derzeit in der öffentlichen Vorschau und ist auf Unternehmenskunden beschränkt.

Vergewissern Sie sich, dass Ihre selbst installierten virtuellen Computer, auf denen SQL Server in Azure ausgeführt wird, registriert sind, bevor Sie mit der Verwendung der neuen Umgebung beginnen. Dadurch wird sichergestellt, dass die Azure-Ressourcen, die SQL Server ausführen, für Sie und Azure sichtbar sind. Weitere Informationen zum Registrieren von SQL-VMs in Azure finden Sie unter [Registrieren einer SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) und [Registrieren mehrerer SQL Server-VMs mit der SQL IaaS-Agent-Erweiterung in Azure](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md).

## <a name="gather-license-usage-and-availability-details"></a>Sammeln von Details zur Nutzung und Verfügbarkeit von Lizenzen

_Der erste Schritt ist die Vorbereitung._ Binden Sie andere Abteilungen in Ihrer Organisation ein, um wichtige Informationen zu zwei Aspekten zu erhalten:

- Wie sieht die aktuelle Nutzung von Azure SQL und SQL Server und die geplante Nutzung für das kommende Jahr aus?
- Wie viele SQL Server-Kernlizenzen sind für die Zuweisung zu Azure verfügbar?

Ermitteln Sie als Nächstes die aktuelle _und die geplante_ Azure SQL-Nutzung für das kommende Jahr oder einen anderen erweiterten Zeitraum (mindestens einen Monat).

Die Azure SQL-Nutzungsdetails werden angezeigt, wenn Sie [SQL Server-Lizenzzuweisungen für den Azure-Hybridvorteil erstellen](create-sql-license-assignments.md).

Sie sollten sich an die entsprechenden Personen in Ihrer Organisation wenden, um die oben genannten Informationen zu überprüfen. Dazu gehört auch die _geplante_ Nutzung und der erwartete Nutzungszuwachs von SQL Server. Für diese Planung kann eine einzige Person oder Gruppe zuständig sein, sie kann aber auch auf mehrere Teams verteilt sein.

Eine optionale, aber nützliche Methode zum Untersuchen der Azure SQL-Nutzung (einschließlich der Nutzung des Azure-Hybridvorteils auf Ressourcenebene) ist die Verwendung des [PowerShell-Skripts „sql-license-usage“](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit) für den Azure-Hybridvorteil. Es analysiert und verfolgt die kombinierte SQL Server Lizenznutzung aller SQL Ressourcen in einem bestimmten Abonnement oder in einem Konto insgesamt.

### <a name="determine-the-number-of-sql-server-core-licenses-available-to-assign-to-azure"></a>Bestimmen der Anzahl von SQL Server-Kernlizenzen, die Azure zugewiesen werden können

Die Anzahl hängt davon ab, wie viele Lizenzen Sie erworben haben und wie viele bereits auf Ihren lokalen Servern und Azure-VMs verwendet werden.

Sie erhalten ein 180 Tage gültiges Dualnutzungsrecht für die SQL Server-Lizenz, um sicherzustellen, dass die Migrationen reibungslos ausgeführt werden. Nach dem 180-Tage-Zeitraum können Sie die SQL Server-Lizenz nur noch in Azure verwenden. Berücksichtigen Sie diesen Punkt, wenn Sie die Lizenzverfügbarkeit planen. Beispielsweise können migrierte Lizenzen als zuweisbare Lizenzen betrachtet werden.

Wahrscheinlich verfügt die Abteilung für die Beschaffung bzw. Verwaltung von Softwareressourcen über diese Informationen.

## <a name="buy-more-licenses-if-needed"></a>Bedarfsbasiertes Erwerben weiterer Lizenzen

Wenn Sie nach dem Überprüfen der gesammelten Informationen sicher sind, dass die Anzahl der verfügbaren SQL Server-Lizenzen nicht ausreicht, um die geplante Azure SQL-Nutzung zu decken, bitten Sie die Beschaffungsabteilung, weitere SQL Server-Kernlizenzen mit Software Assurance (bzw. Abonnementlizenzen) zu erwerben.

Es ist kostengünstiger, weitere SQL Server-Lizenzen zu erwerben und den Azure-Hybridvorteil anzuwenden, als stundenbasiert für SQL Server in Azure zu zahlen. Indem Ihre Organisation genügend Lizenzen erwirbt, um die gesamte geplante Azure SQL-Nutzung zu decken, kann sie maximal von den Kosteneinsparungen durch den Vorteil profitieren.

## <a name="assign-licenses-to-azure"></a>Zuweisen von Lizenzen zu Azure

1. Befolgen Sie die Anweisungen im Azure-Portal und in der Azure-Dokumentation, um mindestens einen Bereich auszuwählen und diesem SQL Server-Lizenzen zuzuweisen. Weitere Informationen finden Sie unter [Erstellen von SQL Server- Lizenzzuweisungen für den Azure-Hybridvorteil](create-sql-license-assignments.md).
2. Überprüfen Sie beim Zuweisen der Lizenzen erneut die ermittelte Azure SQL-Nutzung, um sicherzustellen, dass die Details mit anderen gesammelten Informationen übereinstimmen.

## <a name="monitor-usage-and-adjust"></a>Überwachen und Anpassen der Nutzung

1. Navigieren Sie zu **Cost Management + Billing** > **Reservierungen + Hybridvorteile**.
1. Es wird eine Tabelle angezeigt, die die von Ihnen vorgenommenen Lizenzzuweisungen mit Azure-Hybridvorteil und den Prozentsatz der Nutzung der einzelnen Lizenzen enthält.
1. Wenn mindestens eine Nutzung als 100 % angezeigt wird, bedeutet dies, dass Ihre Organisation Stundensätze für einige SQL Server-Ressourcen bezahlt. Nehmen Sie erneut Kontakt mit anderen Gruppen in Ihrer Organisation auf, um zu bestätigen, ob die aktuellen Nutzungslevel temporär oder dauerhaft sind. Wenn Letzteres der Fall ist, sollte Ihre Organisation erwägen, weitere Lizenzen zu erwerben und in Azure zuzuweisen, um Kosten zu senken.
1. Wenn sich die Nutzung dem Wert 100 % nähert, ihn aber nicht überschreitet, müssen Sie ermitteln, ob die Nutzung in naher Zukunft voraussichtlich steigen wird. In diesem Fall können Sie proaktiv weitere Lizenzen erwerben und zuweisen.

## <a name="establish-a-management-schedule"></a>Einrichten eines Zeitplans für die Verwaltung

Im vorherigen Abschnitt wurde die fortlaufende Überwachung erläutert. Außerdem wird empfohlen, einen jährlichen oder vierteljährlichen Zeitplan aufzustellen und zu befolgen. Dieser Zeitplan umfasst die wichtigsten Schritte, die im vorliegenden Artikel beschrieben werden:

- Sammeln von Details zur Nutzung und Verfügbarkeit von Lizenzen
- Bedarfsbasiertes Erwerben weiterer Lizenzen, um einen bevorstehenden Nutzungszuwachs zu decken
- Zuweisen von Lizenzen zu Azure
- Überwachen der Nutzung und bei Bedarf Anpassen im laufenden Betrieb
- Wiederholen des Prozesses jedes Jahr oder mit der Häufigkeit, die Ihren Anforderungen am besten entspricht

## <a name="example-walkthrough"></a>Exemplarische Vorgehensweise

Nehmen Sie im folgenden Beispiel an, dass Sie Abrechnungsadministrator für das Unternehmen Contoso Insurance sind. Sie verwalten den Azure-Hybridvorteil von Contoso für SQL Server.

Sie werden von Ihrer Beschaffungsabteilung darüber informiert, dass Sie den Azure-Hybridvorteil für SQL Server auf Kontogesamtebene zentral verwalten können. Die Beschaffungsabteilung hat vom Microsoft-Kontoteam davon erfahren. Da es in letzter Zeit schwierig war, den Azure-Hybridvorteil verwalten, sind Sie interessiert. Die Schwierigkeiten entstanden zum Teil dadurch, dass die Entwickler den Vorteil willkürlich für Ressourcen ermöglichten (oder nicht), während sie Skripts miteinander teilten.

Sie finden die neuen Funktionen für den Azure-Hybridvorteil im Bereich „Cost Management + Billing“ des Azure-Portals.

Nachdem Sie die Anweisungen in diesem Artikel gelesen haben, ist Ihnen Folgendes klar:

  - Contoso muss SQL Server-VMs registrieren, bevor andere Aktionen durchgeführt werden.
  - Die ideale Möglichkeit, die neue Funktion zu verwenden, besteht in der proaktiven Zuweisung von Lizenzen, um die erwartete Nutzung zu decken.

Führen Sie dann die folgenden Schritte aus.

1. Stellen Sie anhand der oben stehenden Anweisungen sicher, dass selbst installierte SQL VMs registriert sind. Zu diesem Zweck müssen Sie mit Abonnementbesitzern kommunizieren, um die Registrierung für die Abonnements abschließen zu können, für die Sie nicht über ausreichende Berechtigungen verfügen.
1. Sie überprüfen die Azure-Ressourcennutzungsdaten der letzten Monate und sprechen mit anderen Personen in Contoso. Sie stellen fest, dass 2000 Kernlizenzen für SQL Server Enterprise Edition und 750 Kernlizenzen für SQL Server Standard Edition – bzw. 8750 normalisierte Kernlizenzen – erforderlich sind, um die erwartete Azure SQL-Nutzung für das nächste Jahr zu decken. Die erwartete Nutzung umfasst auch migrierte Workloads (1500 für SQL Server Enterprise Edition + 750 für SQL Server Standard Edition = 6750 normalisiert) sowie ganz neue Azure SQL-Workloads (weitere 500 SQL Server Enterprise Edition-Kernlizenzen bzw. 2000 normalisierte Kernlizenzen).
1. Lassen Sie sich als Nächstes vom Beschaffungsteam bestätigen, dass die erforderlichen Lizenzen bereits verfügbar sind oder in Kürze erworben werden. Mit dieser Bestätigung stellen Sie sicher, dass die Lizenzen für die Zuweisung zu Azure verfügbar sind.
   - Lokal verwendete Lizenzen können als für die Zuweisung zu Azure verfügbar angesehen werden, wenn die zugehörigen Workloads zu Azure migriert werden. Wie bereits erwähnt, ermöglicht der Azure-Hybridvorteil bis zu 180 Tage lang eine duale Nutzung.
   - Sie stellen fest, dass 1800 SQL Server Enterprise Edition-Lizenzen und 2000 SQL Server Standard Edition-Lizenzen verfügbar sind, die Azure zugewiesen werden können. Dies entspricht 9.200 normalisierten Kernlizenzen. Dies ist etwas mehr als die erforderlichen 8750 (2000 × 4 + 750 = 8750).
1. Anschließend weisen Sie Azure 1800 Lizenzen für SQL Server Enterprise Edition und 2000 Lizenzen für SQL Server Standard Edition zu. Diese Aktion führt zu 9200 normalisierten Kernlizenzen, die Sie bei der stündlichen Ausführung auf Azure SQL-Ressourcen anwenden können. Indem Sie jetzt mehr Lizenzen zuweisen als unbedingt erforderlich, verfügen Sie über einen Puffer, wenn die Nutzung schneller zunimmt als erwartet.

Anschließend überwachen Sie die Nutzung der zugewiesenen Lizenzen regelmäßig, idealerweise monatlich. Nach 10 Monaten nähert sich die Nutzung einem Wert von 95 % an, was auf einen schnelleren Zuwachs der Azure SQL-Nutzung als erwartet hinweist. Sie sprechen mit dem Beschaffungsteam, um weitere Lizenzen zu erhalten, die Sie zuweisen können.

Schließlich führen Sie einen Zeitplan für die jährliche Überprüfung der Lizenzen ein. Im Überprüfungsprozess führen Sie folgende Aktivitäten aus:

- Sammeln und Analysieren von Lizenznutzungsdaten
- Bestätigen der Verfügbarkeit von Lizenzen
- Bei Bedarf Zusammenarbeit mit dem Beschaffungsteam, um weitere Lizenzen zu erhalten
- Aktualisieren der Lizenzzuweisungen
- Überwachen der Entwicklung im Lauf der Zeit

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über den [Übergang zum zentral verwalteten Azure-Hybridvorteil](transition-existing.md).
- Lesen Sie die [häufig gestellten Fragen zum zentral verwalteten Azure-Hybridvorteil](faq-azure-hybrid-benefit-scope.yml).