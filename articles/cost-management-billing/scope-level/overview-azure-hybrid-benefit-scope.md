---
title: Was ist der zentral verwaltete Azure-Hybridvorteil?
description: Azure-Hybridvorteil ist ein Lizenzierungsvorteil, mit dem Sie Ihre lokalen core-basierten Windows Server- und SQL Server-Lizenzen mit aktiver Software Assurance (oder aktivem Abonnement) in Azure bereitstellen können.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 8fb630db1d14fe7af1008979eb5b72ee62287d6a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235694"
---
# <a name="what-is-centrally-managed-azure-hybrid-benefit"></a>Was ist der zentral verwaltete Azure-Hybridvorteil?

Mit dem Azure-Hybridvorteil können Sie die Kosten für das Ausführen Ihrer Workloads in der Cloud beträchtlich senken. Hierbei können Sie Ihre lokalen Windows Server- und SQL Server-Lizenzen mit Software Assurance-Aktivierung in Azure nutzen. Weitere Informationen finden Sie unter [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/).

Sie können Ihren Azure-Hybridvorteil für SQL Server für einen gesamten Azure-Abonnementbereich oder ein gesamtes Abrechnungskonto zentral verwalten. Hier ist eine allgemeine Beschreibung der Funktionsweise angegeben:

1. Vergewissern Sie sich zunächst, dass Ihre gesamten SQL Server-VMs für Sie und für Azure sichtbar sind, indem Sie die automatische Registrierung der selbst installierten SQL Server-Images mit der IaaS-Erweiterung aktivieren. Weitere Informationen finden Sie unter [Registrieren mehrerer SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung in Azure](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md).
1. Im Azure-Portal unter **Kostenverwaltung + Abrechnung** wählen Sie (als Abrechnungsadministrator) den Bereich und die Anzahl von qualifizierten Lizenzen aus, die Sie zuweisen möchten, um die Ressourcen des Bereichs abzudecken.  
    :::image type="content" source="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" alt-text="Screenshot: Festlegen eines Bereichs und Zuweisen von Lizenzen" lightbox="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" :::

- Für jede Stunde der Ausführung von Ressourcen im Bereich werden von Azure dafür automatisch die Lizenzen zugewiesen und die Kosten richtig abgezogen. Für die einzelnen Stunden können jeweils unterschiedliche Ressourcen abgedeckt werden.
- Jegliche Nutzung, die über die Anzahl zugewiesener Lizenzen hinausgeht, wird zu den normalen Preisen für die nutzungsbasierte Bezahlung berechnet.
- Wenn Sie den Vorteil verwalten möchten, indem Sie Lizenzen auf Bereichsebene zuweisen, können Sie im Bereich keine einzelnen Ressourcen mehr verwalten.

Die ursprüngliche Methode zum Aktivieren des Azure-Hybridvorteils auf Ressourcenebene ist für SQL Server weiterhin verfügbar und derzeit die einzige Option für Windows Server. Hierbei wird eine DevOps-Rolle verwendet, mit der für jede einzelne Ressource (z. B. eine SQL-Datenbank oder Windows Server-VM) bei ihrer Erstellung bzw. der Verwaltung der Vorteil ausgewählt wird. Dies führt dazu, dass sich der Stundensatz für die jeweilige Ressource reduziert. Weitere Informationen finden Sie unter [Azure-Hybridvorteil für Windows Server](../../azure-sql/azure-hybrid-benefit.md).

Die Aktivierung der zentralen Verwaltung des Azure-Hybridvorteils für SQL Server auf Abonnement- oder Kontobereichsebene befindet sich derzeit in der Vorschauphase. Dieses Feature ist nur für Unternehmenskunden verfügbar. Wir arbeiten daran, diese Funktionalität auch auf Windows Server und einen größeren Kundenkreis zu erweitern.

## <a name="qualifying-sql-server-licenses"></a>Qualifizierte SQL Server-Lizenzen

SQL Server Enterprise- und Standard-Core-Lizenzen mit aktiver Software Assurance sind für diesen Vorteil qualifiziert. Dies gilt auch für Abonnements mit SQL Server-Core-Lizenzabonnements.

## <a name="qualifying-azure-resources"></a>Qualifizierte Azure-Ressourcen

Die zentrale Verwaltung des Azure-Hybridvorteils auf Bereichsebene deckt die folgenden allgemeinen Azure SQL-Ressourcen ab:

- SQL-DATENBANKEN
- Verwaltete SQL-Instanzen
- Pools für elastische SQL-Datenbanken
- SQL Server auf Azure-VMs

Alle obigen Punkte können auch mit der Verwaltung des Azure-Hybridvorteils auf Ressourcenebene abgedeckt werden. Dies ist derzeit die einzige Option zur Abdeckung der folgenden Ressourcen:

- Dedizierte Azure-Hosts
- Azure Data Factory SQL Server Integration Services (SSIS)

## <a name="centralized-scope-level-management-advantages"></a>Vorteile der zentralen Verwaltung auf Bereichsebene

Für Sie ergeben sich die folgenden Vorteile:

- **Vereinfachter Ansatz mit mehr Kontrolle**: Der Abrechnungsadministrator weist einem oder mehreren Azure-Bereichen direkt verfügbare Lizenzen zu. Der ursprüngliche Ansatz umfasst die umfassende Koordinierung der Nutzung des Azure-Hybridvorteils für viele Ressourcen und DevOps-Besitzer.
- **Einfache Option zur Kostenoptimierung**: Ein Administrator kann die Nutzung des Azure-Hybridvorteils überwachen und Lizenzen, die Azure zugewiesen sind, direkt anpassen. Es kann beispielsweise sein, dass ein Administrator eine Möglichkeit zum Sparen von Kosten durch die Zuweisung weiterer Lizenzen in Azure erkennt. Anschließend tauscht er sich mit der Einkaufsabteilung aus, um die Verfügbarkeit der Lizenzen zu bestätigen. Die Lizenzen können dann problemlos in Azure zugewiesen werden, um sofort Kosten zu sparen.
- **Besseres Verfahren zum Verwalten von Kosten bei Nutzungsspitzen**: Bei temporären Spitzen ist es einfacher, eine Ressource hochzuskalieren oder weitere Ressourcen hinzuzufügen. Es ist nicht erforderlich, dass Sie weitere SQL Server-Lizenzen zuweisen (z. B. bei Schließungen oder erhöhter Nachfrage vor Weihnachten). Bei kurzen Workloadspitzen sind die nutzungsbasierten Gebühren für die zusätzliche Kapazität ggf. niedriger als der Erwerb weiterer Lizenzen für die Nutzung des Azure-Hybridvorteils für die Erweiterung der Kapazität. Bei der Verwaltung des Vorteils auf Bereichsebene (anstatt auf Ressourcenebene) können Sie Entscheidungen basierend auf der aggregierten Nutzung treffen.
- **Klare Aufgabentrennung zur Wahrung der Konformität**: Beim Modell mit dem Azure-Hybridvorteil auf Ressourcenebene treffen die Besitzer der Ressourcen ggf. Entscheidungen zur Wahl des Azure-Hybridvorteils, ohne sich über die Lizenzverfügbarkeit (per Erwerb) oder die Gesamtnutzung der SQL-Lizenzen (einschließlich lokal) im Klaren zu sein. Bei der Verwaltung des Azure-Hybridvorteils auf Bereichsebene besteht kein Risiko, dass Ressourcenbesitzer den Azure-Hybridvorteil auswählen, wenn keine Lizenzen verfügbar sind. Die Abrechnungsadministratoren, die den Azure-Hybridvorteil zentral verwalten, können bei den Abteilungen für Einkauf und Software Asset Management nachfragen, wie viele Lizenzen für die Zuweisung in Azure verfügbar sind. Dies ist im folgenden Diagramm dargestellt.

:::image type="content" source="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg" alt-text="Diagramm: Aufgabentrennung" border="false" lightbox="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg":::

## <a name="how-licenses-apply-to-azure-resources"></a>Anwenden von Lizenzen auf Azure-Ressourcen

Sowohl SQL Server Enterprise- (Core) als auch SQL Server Standard-Lizenzen (Core) mit Software Assurance sind qualifiziert. Wie in den [Microsoft-Produktbedingungen](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS) beschrieben ist, gelten bei der Verwendung in Azure mit dem Azure-Hybridvorteil aber andere Umrechnungsverhältnisse.

Eine wichtige Regel lautet: Mit einer SQL Server Enterprise Edition-Lizenz wird der gleiche Bereich wie mit _vier_ SQL Server Standard Edition-Lizenzen abgedeckt. Dies gilt für alle qualifizierten Azure SQL-Ressourcentypen.

Um die Beschreibung der Funktionsweise zu vereinfachen, wird hier der Begriff _normalisierte Core-Lizenz_ (Normalized Core License, NCL) verwendet. Basierend auf der obigen Regel ergibt eine SQL Server Standard-Core-Lizenz eine NCL. Eine SQL Server Enterprise-Core-Lizenz ergibt vier NCLs. Wenn Sie beispielsweise vier SQL Server Enterprise-Core-Lizenzen und sieben SQL Server Standard-Core-Lizenzen zuweisen, beträgt Ihre Gesamtabdeckung und Rabattsumme über den Azure-Hybridvorteil 23 NCLs (4\*4+7\*1).

In der folgenden Tabelle ist zusammengefasst, wie viele NCLs Sie benötigen, um für die SQL Server-Lizenzkosten für unterschiedliche Ressourcentypen einen vollständigen Rabatt zu erzielen. Bei der Verwaltung des Azure-Hybridvorteils auf Bereichsebene werden die Regeln der Produktbedingungen streng angewendet. Dies ist unten zusammengefasst.

| **Azure-Datendienst** | **Dienstebene** | **Erforderliche Anzahl von NCLs** |
| --- | --- | --- |
| SQL Managed Instance oder Instanzpool | Unternehmenskritisch | 4 pro virtuellem Kern |
| SQL Managed Instance oder Instanzpool | Universell | 1 pro virtuellem Kern |
| SQL-Datenbank oder Pool für elastische Datenbanken<sup>1</sup> | Unternehmenskritisch | 4 pro virtuellem Kern |
| SQL-Datenbank oder Pool für elastische Datenbanken<sup>1</sup> | Universell | 1 pro virtuellem Kern |
| SQL-Datenbank oder Pool für elastische Datenbanken<sup>1</sup> | Hyperscale | 1 pro virtuellem Kern |
| Azure Data Factory SQL Server Integration Services | Enterprise | 4 pro virtuellem Kern |
| Azure Data Factory SQL Server Integration Services | Standard | 1 pro virtuellem Kern |
| Virtuelle SQL Server-Computer<sup>2</sup> | Enterprise | 4 pro vCPU |
| Virtuelle SQL Server-Computer<sup>2</sup> | Standard | 1 pro vCPU |

<sup>1</sup> *Azure-Hybridvorteil ist auf der serverlosen Computeebene von Azure SQL-Datenbank nicht verfügbar.*

<sup>2</sup> *Mindestens vier V-Kern-Lizenzen pro virtuellem Computer.*

## <a name="ongoing-scope-level-management"></a>Fortlaufende Verwaltung auf Bereichsebene

Wir empfehlen Ihnen den folgenden Wiederholungsrhythmus für die Verwaltung des Azure-Hybridvorteils auf Bereichsebene:

- Ermitteln Sie in Ihrer Organisation, wie viele Azure SQL-Ressourcen und virtuelle Kerne im nächsten Monat, Quartal oder Jahr voraussichtlich genutzt werden.
- Fragen Sie bei Ihren Abteilungen für Einkauf und Software Asset Management nach, ob eine ausreichende Zahl von SQL-Core-Lizenzen mit Software Assurance verfügbar ist. Bei Nutzung des Vorteils können Lizenzen, für die die Migration von Workloads unterstützt wird, sowohl lokal als auch in Azure bis zu 180 Tage lang verwendet werden. Diese Lizenzen können also als verfügbar gezählt werden.
- Weisen Sie verfügbare Lizenzen zu, um Ihre derzeitige Nutzung _und_ Ihre geplante Nutzung für den anstehenden Zeitraum abzudecken.
- Überwachen Sie die Nutzung der zugewiesenen Lizenzen.
  - Wenn sich die Nutzung dem Wert 100 % nähert, sollten Sie sich in Ihrer Organisation an die entsprechenden Personen wenden, um Informationen zum erwarteten Anstieg bei der Nutzung zu erhalten. Vergewissern Sie sich bei Bedarf, ob genügend Lizenzen verfügbar sind, und weisen Sie dem Bereich dann weitere Lizenzen zu.
  - Falls die Nutzung 100 % erreicht, verwenden Sie unter Umständen mehr Ressourcen, als durch die Anzahl zugewiesener Lizenzen abgedeckt sind. Kehren Sie zur [Umgebung für die Erstellung von Lizenzzuweisungen](create-sql-license-assignments.md) zurück, und überprüfen Sie die in Azure angezeigten Nutzungsdaten. Sehen Sie sich auch die Details zur Lizenznutzung an. Stimmen Sie sich dann mit Ihren Kollegen ab, um die Anzahl von Lizenzen zu bestätigen bzw. weitere zu beschaffen, bevor Sie sie dem Bereich zur Erweiterung der Abdeckung zuweisen.
- Wiederholen Sie diesen proaktiven Prozess regelmäßig.

## <a name="next-steps"></a>Nächste Schritte

- Folgen Sie dem Tutorial [Verwalten und Optimieren des Azure-Hybridvorteils für SQL Server](tutorial-azure-hybrid-benefits-sql.md).
- Informieren Sie sich über den [Übergang zum zentral verwalteten Azure-Hybridvorteil](transition-existing.md).
- Lesen Sie die [häufig gestellten Fragen zum zentral verwalteten Azure-Hybridvorteil](faq-azure-hybrid-benefit-scope.yml).