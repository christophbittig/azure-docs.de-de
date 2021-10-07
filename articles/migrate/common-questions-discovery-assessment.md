---
title: Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse in Azure Migrate
description: Erhalten Sie Antworten auf häufig gestellte Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse in Azure Migrate.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: a882e0e1f0a5cf0dd9ea3690453887478319268e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538954"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Häufig gestellte Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse in Azure Migrate. Wenn Sie weitere Fragen haben, sehen Sie sich die folgenden Ressourcen an:

- [Allgemeine Fragen](resources-faq.md) zu Azure Migrate
- Fragen zur [Azure Migrate-Appliance](common-questions-appliance.md)
- Fragen zur [Servermigration](common-questions-server-migration.md)
- Antworten auf Fragen im [Azure Migrate-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureMigrate

## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Welche geografischen Regionen werden für die Ermittlung und Bewertung mit Azure Migrate unterstützt?

In der Unterstützungsmatrix für Azure Migrate finden Sie die unterstützten geografischen Regionen für die [öffentliche Cloud](migrate-support-matrix.md#supported-geographies-public-cloud) und für [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Wie viele Server können mit einer Appliance ermittelt werden?

Sie können in der VMware-Umgebung bis zu 10.000, in der Hyper-V-Umgebung bis zu 5.000 und mit einer einzelnen Appliance bis zu 1000 physische Server ermitteln. Wenn Sie mehr Server haben, informieren Sie sich über die Skalierung der [Hyper-V-](scale-hyper-v-assessment.md), [VMware-](scale-vmware-assessment.md) oder [physischen](scale-physical-assessment.md) Bewertung.

## <a name="how-do-i-choose-the-assessment-type"></a>Wie wähle ich den Bewertungstyp aus?

- Verwenden Sie **Bewertungen vom Typ „Virtueller Azure-Computer“** , wenn Sie Server in der lokalen [VMware](how-to-set-up-appliance-vmware.md)- und [Hyper-V](how-to-set-up-appliance-hyper-v.md)-Umgebung sowie [physische Server](how-to-set-up-appliance-physical.md) für die Migration zu virtuellen Azure-Computern bewerten möchten. [Weitere Informationen](concepts-assessment-calculation.md)
- Verwenden Sie den Bewertungstyp **Azure SQL**, wenn Sie über die VMware-Umgebung die lokale Instanz von SQL Server bewerten möchten, um die Migration zu Azure SQL-Datenbank oder Azure SQL Managed Instance durchzuführen. [Weitere Informationen](concepts-assessment-calculation.md)
- Verwenden Sie den Bewertungstyp **Azure App Service**, wenn Sie über die VMware-Umgebung Ihre auf dem IIS-Webserver ausgeführten ASP.NET-Web-Apps bewerten möchten, um die Migration zu Azure App Service durchzuführen. [Weitere Informationen](concepts-assessment-calculation.md)
- Verwenden Sie Bewertungen vom Typ **Azure VMware Solution (AVS)** , wenn Sie Ihre lokalen [virtuellen VMware-Computer](how-to-set-up-appliance-vmware.md) für die Migration zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) mit diesem Bewertungstyp bewerten möchten. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)
- Eine allgemeine Gruppe mit VMware-Computern kann nur zum Ausführen beider Bewertungstypen verwendet werden. Wenn Sie erstmals AVS-Bewertungen in Azure Migrate ausführen, empfiehlt es sich, eine neue Gruppe mit VMware-Computern zu erstellen.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Warum fehlen in meinem Azure-VM- bzw. Azure VMware Solution-Bewertungsbericht die Leistungsdaten für einige oder alle Server?

Für eine Bewertung vom Typ „Leistungsbasiert“ enthält der Bewertungsbericht die Angabe „PercentageOfCoresUtilizedMissing“ oder „PercentageOfMemoryUtilizedMissing“, wenn die Azure Migrate-Appliance keine Leistungsdaten für die lokalen Server sammeln kann. Prüfen Sie Folgendes:

- Sind die Server für den Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet?
- Fehlen nur Leistungsindikatoren für den Arbeitsspeicher? Versuchen Sie, die Server in einer Hyper-V-Umgebung zu bewerten? Aktivieren Sie in diesem Szenario dynamischen Arbeitsspeicher auf den Servern, und führen Sie die erneute Berechnung der Bewertung durch, um die neuesten Änderungen widerzuspiegeln. Die Appliance kann Werte für die Arbeitsspeicherauslastung für Server in Hyper-V-Umgebungen nur dann sammeln, wenn der dynamische Arbeitsspeicher für den Server aktiviert ist.

- Falls alle Leistungsindikatoren fehlen, sollten Sie sicherstellen, dass ausgehende Verbindungen über Port 443 (HTTPS) zulässig sind.

    > [!Note]
    > Falls einer der Leistungsindikatoren fehlt, greift die Azure Migrate-Serverbewertung auf die lokalen zugeordneten Kerne bzw. auf den lokalen zugeordneten Arbeitsspeicher zurück und empfiehlt eine entsprechende VM-Größe.

## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Warum fehlen für einige/alle SQL-Instanzen/-Datenbanken in meiner Azure SQL-Bewertung Leistungsdaten?

Überprüfen Sie Folgendes, um sicherzustellen, dass Leistungsdaten erfasst werden:

- Sind die SQL Server-Instanzen für den Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet?
- Weist der SQL-Agent in Azure Migrate den Verbindungsstatus „Verbunden“ auf? (Überprüfen Sie auch den letzten Heartbeat.) 
- Lautet der Azure Migrate-Verbindungsstatus für alle SQL-Instanzen auf dem Blatt der ermittelten SQL-Instanz „Verbunden“?
- Sollten alle Leistungsindikatoren fehlen, vergewissern Sie sich, dass ausgehende Verbindungen am Port 443 (HTTPS) zugelassen werden.

Wenn einer der Leistungsindikatoren fehlt, empfiehlt die Azure SQL-Bewertung die kleinste Azure SQL-Konfiguration für die Instanz bzw. Datenbank.

## <a name="why-confidence-rating-is-not-available-for-azure-app-service-assessments"></a>Warum sind für Azure App Service-Bewertungen keine Zuverlässigkeitsstufen verfügbar?

Da Leistungsdaten in Azure App Service-Bewertungen nicht erfasst werden, gibt es für diesen Bewertungstyp keine Zuverlässigkeitsstufen. Bei der Azure App Service-Bewertung werden die Konfigurationsdaten von Web-Apps während der Berechnung berücksichtigt.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Weshalb weist meine Bewertung eine niedrige Zuverlässigkeitsstufe auf?

Die Zuverlässigkeitsstufe wird für Bewertungen vom Typ „Leistungsbasiert“ anhand des Prozentsatzes von [verfügbaren Datenpunkten](./concepts-assessment-calculation.md#ratings) berechnet, die zum Berechnen der Bewertung benötigt werden. Im Folgenden werden mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung aufgeführt:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil Ihrer Umgebung erstellt. Wenn Sie beispielsweise eine Bewertung mit einer auf eine Woche festgelegten Leistungsdauer erstellen, müssen Sie nach dem Start der Ermittlung mindestens eine Woche warten, damit alle Datenpunkte erfasst werden können. Falls Sie nicht so lange warten können, sollten Sie die Leistungsdauer in einen kürzeren Zeitraum ändern und die Bewertung neu berechnen (Option **Neu berechnen**).
- Die Bewertung kann die Leistungsdaten für einige oder alle Server im Bewertungszeitraum nicht erfassen. Für eine Bewertung mit hoher Konfidenz stellen Sie Folgendes sicher: 
    - Server sind für die Dauer der Bewertung eingeschaltet.
    - Ausgehende Verbindungen am Port 443 sind zugelassen.
    - Für Hyper-V-Server ist der dynamische Arbeitsspeicher aktiviert.
    - Agents in Azure Migrate weisen den Verbindungsstatus „Verbunden“ auf. Überprüfen Sie auch den letzten Heartbeat.
    - In Azure SQL-Bewertungen lautet der Azure Migrate-Verbindungsstatus für alle SQL-Instanzen auf dem Blatt der ermittelten SQL-Instanz „Verbunden“.

    Führen Sie eine **Neuberechnung** der Bewertung durch, um die neuesten Änderungen bei der Zuverlässigkeitsstufe zu berücksichtigen.

- Für Azure-VM- und Azure VMware Solution-Bewertungen: Nach dem Start der Ermittlung wurden einige Server erstellt. Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, einige Server in der Umgebung wurden jedoch erst letzte Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen Server zur Verfügung, und die Zuverlässigkeitsstufe wäre gering. [Weitere Informationen](./concepts-assessment-calculation.md#confidence-ratings-performance-based)
- Bei Azure SQL-Bewertungen wurden einige SQL-Instanzen oder -Datenbanken nach dem Start der Ermittlung erstellt. Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, einige SQL-Instanzen oder Datenbanken in der Umgebung wurden jedoch erst letzte Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen Server zur Verfügung, und die Zuverlässigkeitsstufe wäre gering. [Weitere Informationen](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="the-number-of-azure-vm-or-avs-assessments-on-the-discovery-and-assessment-tool-are-incorrect"></a>Die Anzahl der Azure VM- oder AVS-Bewertungen auf dem Discovery- und Bewertungstool ist inkorrekt

 Um dies zu beheben, klicken Sie auf die Gesamtzahl der Bewertungen, um zu allen Bewertungen zu navigieren und die Azure VM- oder AVS-Bewertung neu zu berechnen. Das Erkennungs- und Bewertungstool zeigt dann die korrekte Anzahl für diesen Bewertungstyp an.

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Ich möchte die neue Azure SQL-Bewertung testen

Das Feature zur Ermittlung und Bewertung von SQL Server-Instanzen und -Datenbanken, die in Ihrer VMware-Umgebung ausgeführt werden, befindet sich nun in der Vorschauphase. Erste Schritte mit [diesem Tutorial](tutorial-discover-vmware.md). Falls Sie dieses Feature in einem vorhandenen Projekt ausprobieren möchten, sollten Sie sicherstellen, dass Sie die in diesem Artikel beschriebenen [Voraussetzungen](how-to-discover-sql-existing-project.md) erfüllt haben.

## <a name="i-want-to-try-out-the-new-azure-app-service-assessment"></a>Ich möchte die neue Azure App Service-Bewertung testen

Das Feature für die Ermittlung und Bewertung von .NET-Web-Apps, die in Ihrer VMware-Umgebung ausgeführt werden, befindet sich nun in der Vorschauphase. Erste Schritte mit [diesem Tutorial](tutorial-discover-vmware.md). Falls Sie dieses Feature in einem vorhandenen Projekt ausprobieren möchten, sollten Sie sicherstellen, dass Sie die in diesem Artikel beschriebenen [Voraussetzungen](how-to-discover-sql-existing-project.md) erfüllt haben.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Beim Erstellen einer Azure SQL-Bewertung werden einige Server nicht angezeigt.

- Azure SQL-Bewertungen können nur für aktive Server durchgeführt werden, auf denen SQL-Instanzen erkannt wurden. Falls die Server und SQL-Instanzen, die Sie bewerten möchten, nicht angezeigt werden, warten Sie, bis die Ermittlung abgeschlossen ist, und erstellen Sie dann die Bewertung.
- Sollte im Rahmen der Bewertungserstellung eine zuvor erstellte Gruppe nicht angezeigt werden, entfernen Sie alle VMware-fremden Server bzw. alle Server ohne SQL-Instanz aus der Gruppe.
- Wenn Sie erstmals Azure SQL-Bewertungen in Azure Migrate ausführen, empfiehlt es sich, eine neue Servergruppe zu erstellen.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-app-service-assessment"></a>Beim Erstellen einer Azure App Service-Bewertung werden einige Server nicht angezeigt.

- Die Azure App Service-Bewertung kann nur auf Servern mit Webserverrolle durchgeführt werden. Falls die Server, die Sie bewerten möchten, nicht angezeigt werden, warten Sie, bis die Ermittlung abgeschlossen ist, und erstellen Sie dann die Bewertung.
- Sollte im Rahmen der Bewertungserstellung eine zuvor erstellte Gruppe nicht angezeigt werden, entfernen Sie alle VMware-fremden Server bzw. alle Server ohne Web-App aus der Gruppe.
- Wenn Sie erstmals Azure App Service-Bewertungen in Azure Migrate ausführen, empfiehlt es sich, eine neue Servergruppe zu erstellen.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Wie wurde die Bereitschaft für meine Instanz berechnet?

Die Bereitschaft Ihrer SQL-Instanzen wurde im Anschluss an eine Featurekompatibilitätsprüfung mit dem gewünschten Azure SQL-Bereitstellungstyp (Azure SQL-Datenbank oder Azure SQL Managed Instance) berechnet. [Weitere Informationen](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="i-want-to-understand-how-was-the-readiness-for-my-web-apps-is-computed"></a>Wie wird die Bereitschaft meiner Web-Apps berechnet?

Die Bereitschaft Ihrer Web-Apps wird mithilfe eine Reihe von technischen Überprüfungen berechnet, anhand derer ermittelt wird, ob Ihre Web-App erfolgreich in Azure App Service ausgeführt wird. Diese Überprüfungen sind [hier](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Readiness-Checks) dokumentiert.

## <a name="why-is-my-web-app-marked-as-ready-with-conditions-or-not-ready-in-my-azure-app-service-assessment"></a>Warum wird meine Web-App in der Azure App Service-Bewertung als „Bereit mit Bedingungen“ oder als „Nicht bereit“ gekennzeichnet?

Dies kann passieren, wenn mindestens eine der technischen Überprüfungen bei einer bestimmten Web-App fehlschlägt. Sie können auf den Bereitschaftsstatus der Web-App klicken, um weitere Details zu erhalten und um Korrekturmaßnahmen für fehlgeschlagene Überprüfungen durchzuführen.

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Warum ist die Bereitschaft aller SQL-Instanzen als „Unbekannt“ gekennzeichnet?

Wenn die Ermittlung vor Kurzem gestartet wurde und immer noch ausgeführt wird, wird möglicherweise die Bereitschaft einiger oder aller SQL-Instanzen als „Unbekannt“ angezeigt. Warten Sie etwas, bis die Appliance ein Profil der Umgebung erstellt hat, und führen Sie anschließend eine Neuberechnung der Bewertung durch.
Die SQL-Ermittlung wird alle 24 Stunden ausgeführt. Es kann also bis zu einem Tag dauern, bis die aktuellen Konfigurationsänderungen berücksichtigt wurden.

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Warum ist die Bereitschaft einiger SQL-Instanzen als „Unbekannt“ gekennzeichnet?

Mögliche Ursachen:

- Die Ermittlung wird immer noch ausgeführt. Warten Sie etwas, bis die Appliance ein Profil der Umgebung erstellt hat, und führen Sie anschließend eine Neuberechnung der Bewertung durch.
- Möglicherweise gibt es auf dem Blatt für Fehler und Benachrichtigungen Erkennungsprobleme, die behoben werden müssen.

Die SQL-Ermittlung wird alle 24 Stunden ausgeführt. Es kann also bis zu einem Tag dauern, bis die aktuellen Konfigurationsänderungen berücksichtigt wurden.

## <a name="my-assessment-is-in-outdated-state"></a>Meine Bewertung ist veraltet.

### <a name="azure-vmavs-assessment"></a>Azure-VM- bzw. Azure VMware Solution-Bewertung

Wenn es lokale Änderungen an Servern gibt, die sich in einer Gruppe befinden, die bewertet wurde, wird die Bewertung als veraltet gekennzeichnet. Eine Bewertung kann aufgrund von Änderungen in den folgenden Eigenschaften als veraltet gekennzeichnet werden:

- Anzahl der Prozessorkerne
- Zugeordneter Arbeitsspeicher
- Starttyp oder Firmware
- Name, Version und Architektur des Betriebssystems
- Anzahl der Datenträger
- Anzahl der Netzwerkadapter
- Änderung der Datenträgergröße (zugeordnete GB)
- Aktualisierung der NIC-Eigenschaften Beispiel: Änderungen der MAC-Adresse, Hinzufügung einer IP-Adresse usw.

Führen Sie eine **Neuberechnung** der Bewertung durch, um die neuesten Änderungen bei der Bewertung zu berücksichtigen.

### <a name="azure-sql-assessment"></a>Azure SQL-Bewertung

Wenn Änderungen an lokalen SQL-Instanzen und -Datenbanken vorgenommen wurden, die einer bewerteten Gruppe angehören, wird die Bewertung als **Veraltet** gekennzeichnet:

- Eine SQL-Instanz wurde einem Server hinzugefügt oder von einem Server entfernt.
- Eine SQL-Datenbank wurde einer SQL-Instanz hinzugefügt oder aus einer SQL-Instanz entfernt.
- Die Gesamtgröße einer Datenbank in einer SQL-Instanz hat sich um mehr als 20 Prozent geändert.
- Änderung der Anzahl von Prozessorkernen und/oder zugeordnetem Arbeitsspeicher

Führen Sie eine **Neuberechnung** der Bewertung durch, um die neuesten Änderungen bei der Bewertung zu berücksichtigen.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Warum wurde mir ein bestimmter Zielbereitstellungstyp empfohlen?

Von Azure Migrate wird ein bestimmter Azure SQL-Bereitstellungstyp empfohlen, der mit Ihrer SQL-Instanz kompatibel ist. Eine Migration zu einem von Microsoft empfohlenen Ziel ist insgesamt weniger aufwendig. Diese Azure SQL-Konfiguration (SKU) wurde unter Berücksichtigung der Leistungsmerkmale Ihrer SQL-Instanz und der von ihr verwalteten Datenbanken empfohlen. Sollten mehrere Azure SQL-Konfigurationen in Frage kommen, wird die kostengünstigste Lösung empfohlen. [Weitere Informationen](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Welches Bereitstellungsziel sollte ich auswählen, wenn meine SQL-Instanz für Azure SQL-Datenbank und für Azure SQL Managed Instance bereit ist?

Falls Ihre Instanz sowohl für Azure SQL-Datenbank als auch für Azure SQL Managed Instance bereit ist, empfiehlt sich die Verwendung des Zielbereitstellungstyps, bei dem die voraussichtlichen Kosten für die Azure SQL-Konfiguration geringer sind.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Warum ist meine Instanz in meiner Azure SQL-Bewertung als „Potenziell für virtuellen Azure-Computer bereit“ gekennzeichnet?

Dieser Fall kann eintreten, wenn der in den Bewertungseigenschaften ausgewählte Zielbereitstellungstyp **Empfohlen** lautet und die SQL-Instanz nicht für Azure SQL-Datenbank und Azure SQL Managed Instance bereit ist. Dem Benutzer wird empfohlen, in Azure Migration eine Bewertung mit dem Bewertungstyp **Azure-VM** zu erstellen, um zu ermitteln, ob der Server, auf dem die Instanz ausgeführt wird, zu einem virtuellen Azure-Computer migriert werden kann.
Dem Benutzer wird empfohlen, in Azure Migrate eine Bewertung mit dem Bewertungstyp **Azure-VM** zu erstellen, um zu ermitteln, ob der Server, auf dem die Instanz ausgeführt wird, stattdessen zu einem virtuellen Azure-Computer migriert werden kann.

- Bei Bewertungen vom Typ „Azure-VM“ in Azure Migrate liegt das Hauptaugenmerk aktuell auf Lift & Shift-Migrationen, und die spezifischen Leistungsmetriken für die Ausführung von SQL-Instanzen und -Datenbanken auf dem virtuellen Azure-Computer werden nicht berücksichtigt.
- Wenn Sie eine Bewertung vom Typ „Azure-VM“ auf einem Server ausführen, gelten die Größenempfehlung und die Kostenschätzungen für alle Instanzen, die auf dem Server ausgeführt werden und mithilfe des Servermigrationstools zu einem virtuellen Azure-Computer migriert werden können. Lesen Sie vor der Migration die [Leistungsrichtlinien](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md) für SQL Server auf virtuellen Azure-Computern.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>In meiner Bewertung werden einige Datenbanken nicht angezeigt, obwohl die Instanz Teil der Bewertung ist.

Die Azure SQL-Bewertung enthält nur Datenbanken, die online sind. Falls sich eine Datenbank in einem anderen Status befindet, werden bei der Bewertung die Bereiche Bereitschaft, Dimensionierung und Kostenberechnung ignoriert. Wenn Sie Datenbanken dieser Art bewerten möchten, ändern Sie den Status der Datenbank, warten Sie etwas, und berechnen Sie die Bewertung neu.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Ich möchte die Kosten für das Ausführen meiner SQL-Instanzen auf einem virtuellen Azure-Computer mit den Kosten für Azure SQL-Datenbank-Instanzen/Azure SQL Managed Instance vergleichen.

Sie können eine Bewertung mit dem Typ **Azure-VM** in der gleichen Gruppe erstellen, die in Ihrer Bewertung vom Typ **Azure SQL** verwendet wurde. Anschließend können Sie die beiden Berichte miteinander vergleichen. Bei Bewertungen vom Typ „Azure-VM“ in Azure Migrate liegt das Hauptaugenmerk allerdings aktuell auf Lift & Shift-Migrationen, und die spezifischen Leistungsmetriken für die Ausführung von SQL-Instanzen und -Datenbanken auf dem virtuellen Azure-Computer werden nicht berücksichtigt. Wenn Sie eine Bewertung vom Typ „Azure-VM“ auf einem Server ausführen, gelten die Größenempfehlung und die Kostenschätzungen für alle Instanzen, die auf dem Server ausgeführt werden und mithilfe des Servermigrationstools zu einem virtuellen Azure-Computer migriert werden können. Lesen Sie vor der Migration die [Leistungsrichtlinien](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md) für SQL Server auf virtuellen Azure-Computern.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Die Speicherkosten in meiner Azure SQL-Bewertung liegen bei null.

Für Azure SQL Managed Instance werden für die ersten 32 GB Speicher pro Instanz und Monat keine Speicherkosten hinzugefügt, und zusätzliche Speicherkosten werden in 32-GB-Schritten hinzugefügt. [Weitere Informationen](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Es werden bei der Erstellung einer Bewertung vom Typ „Azure VMware Solution (AVS)“ einige Gruppen nicht angezeigt.

- AVS-Bewertungen können nur für Gruppen durchgeführt werden, die ausschließlich VMware-Computer enthalten. Entfernen Sie alle VMware-fremden Computer aus der Gruppe, wenn Sie eine AVS-Bewertung durchführen möchten.
- Wenn Sie erstmals AVS-Bewertungen in Azure Migrate ausführen, empfiehlt es sich, eine neue Gruppe mit VMware-Computern zu erstellen.

## <a name="queries-regarding-ultra-disks"></a>Abfragen zu Disk Ultra-Datenträgern

### <a name="can-i-migrate-my-disks-to-ultra-disk-using-azure-migrate"></a>Kann ich meine Datenträger mithilfe von Azure Migrate zu Disk Ultra-Datenträgern migrieren?

Nein. Derzeit unterstützen sowohl Azure Migrate als auch Azure Site Recovery keine Migration zu Disk Ultra-Datenträgern. Schritte zum Bereitstellen von Disk Ultra-Datenträgern finden Sie [hier](../virtual-machines/disks-enable-ultra-ssd.md?tabs=azure-portal#deploy-an-ultra-disk).

### <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>Warum sind die bereitgestellten IOPS und der Durchsatz meines Disk Ultra-Datenträgers höher als die IOPS und der Durchsatz in meiner lokalen Umgebung?

Gemäß der [offiziellen Preisseite](https://azure.microsoft.com/pricing/details/managed-disks/) wird Disk Ultra auf Grundlage der bereitgestellten Größe und IOPS sowie des bereitgestellten Durchsatzes abgerechnet. Beispiel:

Wenn Sie einen Disk Ultra-Datenträger mit 200 GiB, 20.000 IOPS und 1.000 MB/s bereitgestellt und nach 20 Stunden gelöscht haben, gehört dieser zum Angebot für die Datenträgergröße 256 GiB. Daher werden Ihnen 256 GiB, 20.000 IOPS und 1.000 MB/s für 20 Stunden in Rechnung gestellt.

Bereitzustellende IOPS = (ermittelter Durchsatz) x 1024 : 256

### <a name="does-the-ultra-disk-recommendation-consider-latency"></a>Wird bei der Disk Ultra-Empfehlung Latenz berücksichtigt?

Nein. Derzeit werden nur der Datenträgergröße, der Gesamtdurchsatz und IOPS-Gesamtwert für die Größen- und Kostenkalkulation verwendet.

### <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>Ich sehe, dass die M-Serie Disk Ultra-Datenträger unterstützt, aber in meiner Bewertung, in der Disk Ultra-Datenträger empfohlen wurden, heißt es „Keine VM für diese Standort gefunden“?

Dies ist möglich, da nicht alle VM-Größen, die Disk Ultra unterstützen, in allen Regionen mit Unterstützung von Disk Ultra angeboten werden. Ändern Sie die Zielbewertungsregion, um die VM-Größe für diesen Server abzurufen.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Einige VM-Typen und -Größen werden in Azure Government nicht angezeigt

Die für die Bewertung und Migration unterstützten VM-Typen und -Größen hängen von der Verfügbarkeit am Azure Government-Standort ab. Sie können die VM-Typen in Azure Government [hier anzeigen und vergleichen](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines).

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>Die Größe meines Servers hat sich geändert. Kann ich die Bewertung erneut ausführen?

Die Azure Migrate-Appliance sammelt kontinuierlich Informationen zur lokalen Umgebung.  Eine Bewertung ist eine Point-in-Time-Momentaufnahme von lokalen Servern. Wenn Sie die Einstellungen für einen Server, den Sie bewerten möchten, ändern, verwenden Sie die Option „Neu berechnen“, um die Bewertung mit den neuesten Änderungen zu aktualisieren.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Wie ermittle ich Server in einer mehrinstanzenfähigen Umgebung?

- **VMware**: Wenn eine Umgebung von Mandanten gemeinsam genutzt wird und Sie die Server eines Mandanten nicht im Abonnement eines anderen Mandanten ermitteln möchten, erstellen Sie VMware vCenter Server-Anmeldeinformationen mit Zugriff nur auf die Server, die Sie ermitteln möchten. Verwenden Sie diese Anmeldeinformationen dann, wenn Sie die Ermittlung in der Azure Migrate-Appliance starten.
- **Hyper-V:** Bei der Ermittlung werden Anmeldeinformationen für den Hyper-V-Host verwendet. Wenn Server denselben Hyper-V-Host nutzen, gibt es derzeit keine Möglichkeit, die Ermittlung zu trennen.  

## <a name="do-i-need-vcenter-server"></a>Benötige ich vCenter Server?

Ja, Azure Migrate erfordert in einer VMware-Umgebung vCenter Server für die Ermittlung. Die Ermittlung von ESXi-Hosts, die nicht von vCenter Server verwaltet werden, wird von Azure Migrate nicht unterstützt.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Welche Größenoptionen gibt es bei einer Azure VM-Bewertung?

Bei der lokal orientierten Größenanpassung berücksichtigt Azure Migrate keine Serverleistungsdaten für die Bewertung. Azure Migrate bewertet VM-Größen auf Grundlage der lokalen Konfiguration. Bei der leistungsbasierten Größenanpassung basiert diese auf Auslastungsdaten.

Wenn z. B. ein lokaler Server über vier Kerne und 8 GB Arbeitsspeicher bei einer CPU- und Arbeitsspeicherauslastung von jeweils 50 % verfügt:

- Bei der lokal orientierten Größenanpassung wird eine Azure-VM-SKU mit vier Kernen und 8 GB Arbeitsspeicher empfohlen.
- Bei der leistungsbasierten Größenanpassung wird jedoch eine VM-SKU mit zwei Kernen und 4 GB Arbeitsspeicher empfohlen, da auch der Auslastungsprozentsatz berücksichtigt wird.

Entsprechend hängt die Größenanpassung der Datenträger von dem Größenkriterium und dem Speichertyp ab:

- Wenn das „leistungsbasierte“ Größenkriterium und der Speichertyp „Automatisch“ verwendet werden, berücksichtigt Azure Migrate die IOPS- und Durchsatzwerte des Datenträgers, um den Typ des Zieldatenträgers zu ermitteln (Standard, Premium oder Disk Ultra).
- Wenn das „lokale“ Größenkriterium und der Speichertyp „Premium“ verwendet werden, empfiehlt Azure Migrate eine Premium-Datenträger-SKU basierend auf der Größe des lokalen Datenträgers. Die gleiche Logik wird verwendet, um die Größenanpassung für Datenträger durchzuführen, wenn die lokal orientierte Größenanpassung verwendet wird und der Speichertyp „Standard“, „Premium“ oder „Disk Ultra“ lautet.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Wirken sich Leistungsverlauf und Auslastung auf die Größenanpassung in einer Azure VM-Bewertung aus?

Ja, der Leistungsverlauf und die Auslastung wirken sich auf die Größenanpassung in einer Azure VM-Bewertung aus.

### <a name="performance-history"></a>Leistungsverlauf

Nur bei der leistungsbasierten Größenanpassung sammelt Azure Migrate den Leistungsverlauf von lokalen Computern und nutzt diese Daten dann, um die VM-Größe und den Datenträgertyp in Azure zu empfehlen:

1. Die Appliance erstellt für die lokale Umgebung kontinuierlich Profildaten, um alle 20 Sekunden in Echtzeit Auslastungsdaten erfassen zu können.
2. Die Appliance führt für die gesammelten 20-Sekunden-Stichproben einen Rollup aus und erstellt daraus alle 15 Minuten einen einzelnen Datenpunkt.
3. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen 20-Sekunden-Stichproben aus.
4. Die Appliance sendet den Datenpunkt an Azure.

### <a name="utilization"></a>Auslastung

Beim Erstellen einer Bewertung in Azure berechnet Azure Migrate in Abhängigkeit von der Leistungsdauer und dem festgelegten Perzentilwert des Leistungsverlaufs den geltenden Auslastungswert und verwendet ihn dann für die Größenanpassung.

Wenn Sie die Leistungsdauer beispielsweise auf einen Tag und den Perzentilwert auf das 95. Perzentil festgelegt haben, sortiert Azure Migrate die vom Collector gesendeten 15-Minuten-Stichprobenpunkte für den gesamten letzten Tag in aufsteigender Reihenfolge. Dann wird der Wert des 95. Perzentils als gültige Auslastung ausgewählt.

Die Verwendung des Werts des 95. Perzentils stellt sicher, dass Ausreißer ignoriert werden. Ausreißer können eingeschlossen werden, wenn Azure Migrate das 99. Perzentil verwendet. Wenn Sie die Spitzenauslastung für den Zeitraum erfassen möchten, ohne Ausreißer auszulassen, legen Sie Azure Migrate auf die Verwendung des 99. Perzentils fest.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Worin unterscheiden sich importbasierte Bewertungen von Bewertungen mit der Ermittlungsquelle als Appliance?

Bei importbasierten Azure VM-Bewertungen handelt es sich um Bewertungen, die mit Computern erstellt werden, die mithilfe einer CSV-Datei in Azure Migrate importiert werden. Für den Import sind nur vier Felder erforderlich: Servername, Kerne, Arbeitsspeicher und Betriebssystem. Die folgenden Punkte sind zu berücksichtigen:

 - Das Bereitschaftskriterium ist bei importbasierten Bewertungen für den Starttypparameter weniger streng. Ist der Starttyp nicht angegeben, wird davon ausgegangen, dass der Computer den Starttyp „BIOS“ aufweist und nicht als **Bedingt bereit** gekennzeichnet ist. Bei Bewertungen mit der Ermittlungsquelle als Appliance wird die Bereitschaft als **Bedingt bereit** gekennzeichnet, wenn der Starttyp fehlt. Dieser Unterschied bei der Bereitschaftsberechnung ergibt sich dadurch, dass Benutzer in den frühen Phasen der Migrationsplanung möglicherweise nicht über alle Informationen zu den Computern verfügen, wenn importbasierte Bewertungen durchgeführt werden.
 - Bei leistungsbasierten Importbewertungen wird der vom Benutzer bereitgestellte Auslastungswert für die Berechnungen zur Größenanpassung verwendet. Da der Auslastungswert vom Benutzer bereitgestellt wird, werden die Optionen für **Leistungsverlauf** und **Perzentilwert der Nutzung** in den Bewertungseigenschaften deaktiviert. Bei Bewertungen mit der Ermittlungsquelle als Appliance wird der ausgewählte Perzentilwert den von der Appliance gesammelten Leistungsdaten entnommen.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Warum wird das vorgeschlagene Migrationstool in der importbasierten AVS-Bewertung als unbekannt markiert?

Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool in einer AVS-Bewertung unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension). [Weitere Informationen](../azure-vmware/install-vmware-hcx.md).

## <a name="what-is-dependency-visualization"></a>Was ist die Visualisierung von Abhängigkeiten?

Anhand der Visualisierung von Abhängigkeiten können Sie Gruppen von Servern für eine Migration mit größerer Sicherheit bewerten. Die Visualisierung von Abhängigkeiten überprüft die Abhängigkeiten zwischen Computern, bevor Sie eine Bewertung ausführen. Sie stellt sicher, dass nichts übersehen wird, und hilft, unerwartete Ausfälle bei der Migration zu Azure zu vermeiden. Azure Migrate verwendet die Dienstzuordnungslösung in Azure Monitor, um Abhängigkeitsvisualisierung zu ermöglichen. [Weitere Informationen](concepts-dependency-visualization.md)

> [!NOTE]
> Die Agent-basierte Abhängigkeitsanalyse ist in Azure Government nicht verfügbar. Sie können die Abhängigkeitsanalyse ohne Agent verwenden.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Was ist der Unterschied zwischen „Agent-basiert“ und „ohne Agent“?

Die Unterschiede zwischen der Visualisierung ohne Agent und der Agent-basierten Visualisierung sind in der Tabelle zusammengefasst.

**Anforderung** | **Ohne Agent** | **Agent-basiert**
--- | --- | ---
Support | Diese Option befindet sich derzeit in der Vorschauphase und ist nur für Server in einer VMware-Umgebung verfügbar. [Lesen](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Sie den Artikel zu den unterstützten Betriebssystemen. | In der allgemeinen Verfügbarkeit (General Availability, GA).
Agent | Sie müssen keine Agents auf Computern installieren, die Sie überprüfen möchten. | Auf jedem lokalen Computer, den Sie analysieren möchten, müssen Agents installiert sein: Der [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md) und der [Dependency-Agent](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Voraussetzungen | [Lesen](concepts-dependency-visualization.md#agentless-analysis) Sie den Abschnitt zu Voraussetzungen und Anforderungen für die Bereitstellung. | [Lesen](concepts-dependency-visualization.md#agent-based-analysis) Sie den Abschnitt zu Voraussetzungen und Anforderungen für die Bereitstellung.
Log Analytics | Nicht erforderlich. | Azure Migrate verwendet für die Abhängigkeitsvisualisierung die [Dienstzuordnung](../azure-monitor/vm/service-map.md) in [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md). [Weitere Informationen](concepts-dependency-visualization.md#agent-based-analysis)
Funktionsweise | Erfasst TCP-Verbindungsdaten auf Computern, die für die Visualisierung von Abhängigkeiten aktiviert sind. Nach der Ermittlung werden Daten in Abständen von fünf Minuten gesammelt. | Auf einem Computer installierte Agents für die Dienstzuordnung sammeln Daten über TCP-Prozesse und ein-/ausgehende Verbindungen für die einzelnen Prozesse.
Daten | Servername des Quellcomputers, Prozess, Anwendungsname.<br/><br/> Servername des Zielcomputers, Prozess, Anwendungsname und Port. | Servername des Quellcomputers, Prozess, Anwendungsname.<br/><br/> Servername des Zielcomputers, Prozess, Anwendungsname und Port.<br/><br/> Anzahl der Verbindungen, Wartezeiten und Datenübertragungsinformationen werden gesammelt und stehen für Log Analytics-Abfragen zur Verfügung. 
Visualisierung | Das Abhängigkeitsdiagramm eines einzelnen Servers kann über eine Dauer von einer Stunde bis hin zu 30 Tagen angezeigt werden. | Abhängigkeitsdiagramm eines einzelnen Servers.<br/><br/> Das Diagramm kann nur über eine Stunde angezeigt werden.<br/><br/> Abhängigkeitsdiagramm für eine Gruppe von Servern.<br/><br/> Hinzufügen und Entfernen von Servern in einer Gruppe aus der Diagrammansicht.
Datenexport | Die Daten der letzten 30 Tage können im CSV-Format heruntergeladen werden. | Daten können mit Log Analytics abgefragt werden.

## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Muss ich die Appliance für die Abhängigkeitsanalyse ohne Agent bereitstellen?

Ja, die [Azure Migrate-Appliance](migrate-appliance.md) muss bereitgestellt werden.

## <a name="do-i-pay-for-dependency-visualization"></a>Ist die Visualisierung der Abhängigkeiten kostenpflichtig?

Nein. Weitere Informationen zu den Preisen von Azure Migrate finden Sie [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Was muss ich für die Agent-basierte Visualisierung der Abhängigkeiten installieren?

Wenn Sie die Agent-basierte Visualisierung von Abhängigkeiten verwenden möchten, müssen Sie Agents auf alle lokalen Computer, die Sie bewerten möchten, herunterladen und dort installieren:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Dependency-Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Falls Sie über Computer ohne Internetverbindung verfügen, ist es erforderlich, auf diese das Log Analytics-Gateway herunterzuladen und dort zu installieren.

Sie benötigen diese Agents nur, wenn Sie die Agent-basierte Visualisierung von Abhängigkeiten verwenden.

## <a name="can-i-use-an-existing-workspace"></a>Kann ich einen bestehenden Arbeitsbereich verwenden?

Ja, für die Agent-basierte Visualisierung von Abhängigkeiten können Sie einen vorhandenen Arbeitsbereich an das Migrationsprojekt anfügen und für die Visualisierung von Abhängigkeiten nutzen.

## <a name="can-i-export-the-dependency-visualization-report"></a>Kann ich den Bericht über die Abhängigkeitsvisualisierung exportieren?

Nein, der Bericht für die Visualisierung von Abhängigkeiten in der Agent-basierten Visualisierung kann nicht exportiert werden. Azure Migrate verwendet jedoch die Dienstzuordnung, sodass Sie die Abhängigkeiten mithilfe der [Service Map-REST-API](/rest/api/servicemap/machines/listconnections) im JSON-Format abrufen können.

## <a name="can-i-automate-agent-installation"></a>Kann ich die Agent-Installation automatisieren?

Für die Agent-basierte Visualisierung von Abhängigkeiten:

- Verwenden Sie ein [Skript für die Installation des Dependency-Agents](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Für MMA [verwenden Sie die Befehlszeile oder die Automatisierung](../azure-monitor/agents/log-analytics-agent.md#installation-options), oder verwenden Sie ein [Skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Zusätzlich zu Skripts können Sie Bereitstellungstools wie Microsoft Endpoint Configuration Manager und Intigua zum Bereitstellen der Agents verwenden.

## <a name="what-operating-systems-does-mma-support"></a>Welche Betriebssysteme unterstützt MMA?

- Überprüfen Sie die Liste der [von MMA unterstützten Windows-Betriebssysteme](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- Überprüfen Sie die Liste der [von MMA unterstützten Linux-Betriebssysteme](../azure-monitor/agents/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kann ich Abhängigkeiten für mehr als eine Stunde visualisieren?

Sie können für die Agent-basierte Visualisierung entsprechende Abhängigkeiten bis zu einer Stunde visualisieren. Sie können bis zu einem Monat zu einem bestimmten Datum im Verlauf zurückkehren, aber die maximale Dauer der Visualisierung beträgt eine Stunde. Sie können beispielsweise den Zeitraum im Abhängigkeitsdiagramm verwenden, um Abhängigkeiten für den gestrigen Tag anzuzeigen. Sie können die Abhängigkeiten allerdings nur für ein Zeitfenster von einer Stunde anzeigen. Sie können jedoch zum [Abfragen der Abhängigkeitsdaten](./how-to-create-group-machine-dependencies.md) für einen längeren Zeitraum Azure Monitor-Protokolle verwenden.

Zur Visualisierung ohne Agents können Sie das Abhängigkeitsdiagramm eines einzelnen Servers für eine Dauer zwischen einer Stunde und 30 Tagen anzeigen.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>Kann ich für Gruppen mit mehr als zehn Servern Abhängigkeiten visualisieren?

Sie können [Abhängigkeiten für Gruppen visualisieren](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping), die bis zu zehn Server umfassen. Falls eine Gruppe aus mehr als zehn Servern besteht, sollten Sie die Gruppe in kleinere Gruppen aufteilen und die Abhängigkeiten anschließend visualisieren.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Übersicht zu Azure Migrate](migrate-services-overview.md) an.
