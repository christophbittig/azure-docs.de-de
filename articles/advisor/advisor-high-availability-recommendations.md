---
title: Verbessern der Zuverlässigkeit Ihrer Anwendung mit Advisor
description: Verwenden Sie Azure Advisor, um die Zuverlässigkeit Ihrer geschäftskritischen Azure-Bereitstellungen sicherzustellen und zu verbessern.
ms.topic: article
ms.date: 10/26/2021
ms.openlocfilehash: f7ea986424271315843af9557555aa82a708a12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045860"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Verbessern der Zuverlässigkeit Ihrer Anwendung mit Azure Advisor

Der Azure Advisor hilft Ihnen, die ununterbrochene Verfügbarkeit Ihrer unternehmenskritischen Anwendungen zu gewährleisten und zu verbessern. Sie können Empfehlungen zur Zuverlässigkeit auf der Registerkarte **Zuverlässigkeit** von [Azure Advisor](https://aka.ms/azureadvisordashboard) erhalten.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Überprüfen der Version des Images Ihres virtuellen Netzwerkgeräts von Check Point

Azure Advisor kann ermitteln, ob Ihr virtueller Computer ggf. eine Version des Check Point-Images ausführt, bei der das bekannte Problem besteht, dass bei einem Wartungsvorgang für die Plattform Netzwerkkonnektivität verloren geht. Die Empfehlung von Azure Advisor hilft Ihnen, ein Upgrade auf eine neuere Version des Images durchzuführen, um dieses Problem zu beheben. Diese Prüfung stellt die Geschäftskontinuität durch bessere Netzwerkkonnektivität sicher.

## <a name="ensure-application-gateway-fault-tolerance"></a>Sicherstellen von Fehlertoleranz für Anwendungsgateways

Bei Einhaltung dieser Empfehlung wird die Geschäftskontinuität unternehmenskritischer Anwendungen sichergestellt, die von Anwendungsgateways unterstützt werden. Advisor identifiziert Application Gateway-Instanzen, die nicht für Fehlertoleranz konfiguriert sind. Anschließend werden Ihnen Aktionen zur Problembehebung vorgeschlagen, die Sie ergreifen können. Advisor identifiziert mittelgroße oder große Einzelinstanz-Anwendungsgateways und empfiehlt, mindestens eine weitere Instanz hinzufügen. Identifiziert außerdem kleine Einzel- und Mehrinstanz-Anwendungsgateways und empfiehlt die Migration zu mittelgroßen oder großen SKUs. Der Advisor empfiehlt diese Aktionen, um sicherzustellen, dass Ihre Application Gateway-Instanzen so konfiguriert sind, dass sie die aktuellen SLA-Anforderungen für diese Ressourcen erfüllen.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Schützen Ihrer VM-Daten vor zufälligem Löschen

Durch das Einrichten der Sicherung eines virtuellen Computers wird die Verfügbarkeit Ihrer geschäftskritischen Daten sichergestellt, und Ihre Daten werden vor versehentlichem Löschen oder vor Beschädigung geschützt. Advisor identifiziert virtuelle Computer, auf denen die Sicherung nicht aktiviert ist, und empfiehlt das Aktivieren der Sicherung. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Sicherstellen, dass Sie bei Bedarf Zugang zu Azure-Experten haben

Bei unternehmenskritischen Workloads ist es wichtig, bei Bedarf Zugang zu technischem Support zu haben. Advisor identifiziert potenzielle geschäftskritische Abonnements, die keinen technischen Support in den Supportplänen enthalten. Es wird ein Upgrade auf eine Option empfohlen, die den technischen Support umfasst.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Erstellen von Azure Service Health-Warnungen für Benachrichtigungen bei Azure-Problemen

Es wird empfohlen, Azure Service Health-Warnungen einzurichten, um über Azure-Dienstprobleme benachrichtigt zu werden. [Azure Service Health](https://azure.microsoft.com/features/service-health/) ist ein kostenloser Dienst, der personalisierte Anleitungen und Unterstützung bietet, wenn Sie von einem Azure-Dienstproblem betroffen sind. Advisor identifiziert Abonnements, für die keine Warnungen konfiguriert sind, und empfiehlt deren Konfiguration.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurieren von Traffic Manager-Endpunkten für Resilienz

Azure Traffic Manager-Profile mit mehr als einem Endpunkt haben eine höhere Verfügbarkeit, wenn ein bestimmter Endpunkt ausfällt. Das Platzieren von Endpunkten in verschiedenen Regionen verbessert die Dienstzuverlässigkeit noch mehr. Advisor ermittelt Traffic Manager-Profile mit nur einem Endpunkt und empfiehlt, mindestens einen weiteren Endpunkt in einer anderen Region hinzuzufügen.

Wenn sich alle Endpunkte in einem Traffic Manager-Profil, das für das Näherungsrouting konfiguriert ist, in derselben Region befinden, kann es bei Benutzern in anderen Regionen zu Verbindungsverzögerungen kommen. Das Hinzufügen oder Verschieben eines Endpunkts in eine andere Region verbessert die Gesamtleistung und bietet eine höhere Verfügbarkeit, wenn alle Endpunkte in einer Region ausfallen. Advisor identifiziert für das Näherungsrouting konfigurierte Traffic Manager-Profile, bei denen sich alle Endpunkte in derselben Region befinden. Advisor empfiehlt das Hinzufügen oder Verschieben eines Endpunkts in eine andere Azure-Region.

Wenn ein Traffic Manager-Profil für das geografische Routing konfiguriert ist, wird der Datenverkehr basierend auf definierten Regionen an die Endpunkte weitergeleitet. Wenn eine Region ausfällt, ist kein vordefiniertes Failover verfügbar. Wenn Sie über einen Endpunkt verfügen, an dem die regionale Gruppierung auf **Alle (Welt)** konfiguriert ist, können Sie das Verwerfen von Datenverkehr vermeiden und die Dienstverfügbarkeit verbessern. Advisor identifiziert Traffic Manager-Profile, die für das geografische Routing konfiguriert sind und keinen Endpunkt haben, der mit der regionalen Gruppierung **Alle (Welt)** konfiguriert ist. Advisor empfiehlt, die Konfiguration zu ändern, sodass ein Endpunkt mit **Alle (Welt)** konfiguriert ist.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Speichern und Wiederherstellen von Daten nach versehentlichem Überschreiben oder Löschen durch vorläufiges Löschen in Ihrem Azure Storage-Konto

Aktivieren Sie [vorläufiges Löschen](../storage/blobs/soft-delete-blob-overview.md) in Ihrem Speicherkonto, sodass gelöschte Blobs in einen vorläufig gelöschten Zustand übergehen, anstatt endgültig gelöscht zu werden. Wenn Daten überschrieben werden, wird eine vorläufig gelöschte Momentaufnahme generiert, um den Zustand der überschriebenen Daten zu speichern. Durch vorläufiges Löschen können Sie Daten wiederherstellen, wenn diese versehentlich gelöscht oder überschrieben wurden. Advisor identifiziert Azure-Speicherkonten, bei denen das vorläufige Löschen nicht aktiviert ist, und schlägt vor, es zu aktivieren.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurieren des VPN-Gateway zu aktiv-aktiv für Verbindungsresilienz

In der Aktiv-Aktiv-Konfiguration richten beide Instanzen eines VPN-Gateways S2S-VPN-Tunnel zu Ihrem lokalen VPN-Gerät ein. Wenn ein geplantes Wartungsereignis oder ein ungeplantes Ereignis mit einer Gateway-Instanz eintritt, wird der Datenverkehr automatisch auf den anderen aktiven IPsec-Tunnel umgeschaltet. Azure Advisor identifiziert VPN-Gateways, die nicht als aktiv-aktiv konfiguriert sind, und schlägt vor, dass Sie sie für Hochverfügbarkeit konfigurieren.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Verwenden Sie zum Ausführen Ihrer Produktionsworkloads Produktions-VPN-Gateways.

Azure Advisor überprüft alle VPN-Gateways, die eine Basic-SKU verwenden, und empfiehlt, stattdessen eine Produktions-SKU zu verwenden. Die Basic-SKU ist für Entwicklungs- und Testzwecke konzipiert. Produktions-SKUs bieten Folgendes:
- Weitere Tunnel 
- BGP-Unterstützung 
- Aktiv/Aktiv-Konfigurationsoptionen 
- Benutzerdefinierte IPsec-/IKE-Richtlinie 
- Höhere Stabilität und Verfügbarkeit

## <a name="ensure-reliable-outbound-connectivity-with-vnet-nat"></a>Sicherstellen einer zuverlässigen ausgehenden Konnektivität mit VNet NAT
Die Verwendung von ausgehenden Standardverbindungen, die von einem Standardlastenausgleich oder anderen Azure-Ressourcen bereitgestellt werden, wird für Workloads in der Produktion nicht empfohlen, da dies zu Verbindungsfehlern führt (auch als SNAT-Portauslastung bezeichnet). Der empfohlene Ansatz besteht darin, eine VNet NAT zu verwenden, die diesbezügliche Verbindungsfehler verhindert. Die NAT kann problemlos skaliert werden, um sicherzustellen, dass Ihre Anwendung immer über Ports verfügt. [Erfahren Sie mehr über die VNet NAT](../virtual-network/nat-gateway/nat-overview.md).

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Sicherstellen von Fehlertoleranz für virtuelle Computer (vorübergehend deaktiviert)

Um Redundanz für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehr virtuellen Computern in einer Verfügbarkeitsgruppe. Advisor identifiziert virtuelle Computer, die nicht zu einer Verfügbarkeitsgruppe gehören und empfiehlt, diese in eine Verfügbarkeitsgruppe zu verschieben. Durch diese Konfiguration wird sichergestellt, dass während einer geplanten oder ungeplanten Wartung mindestens ein virtueller Computer verfügbar ist und die Azure-SLA für virtuelle Computer eingehalten wird. Sie können eine Verfügbarkeitsgruppe für den virtuellen Computer erstellen oder diesen einer vorhandenen Verfügbarkeitsgruppe hinzufügen.

> [!NOTE]
> Wenn Sie eine Verfügbarkeitsgruppe erstellen möchten, müssen Sie ihr mindestens einen weiteren virtuellen Computer hinzufügen. Wir empfehlen das Gruppieren von mindestens zwei virtuellen Computern in einer Verfügbarkeitsgruppe, um sicherzustellen, dass mindestens ein Computer bei einem Ausfall verfügbar ist.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Sicherstellen von Fehlertoleranz für Verfügbarkeitsgruppen (vorübergehend deaktiviert)

Um Redundanz für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehr virtuellen Computern in einer Verfügbarkeitsgruppe. Der Advisor ermittelt Verfügbarkeitsgruppen mit nur einem virtuellen Computer und empfiehlt, dieser weitere hinzuzufügen.  Durch diese Konfiguration wird sichergestellt, dass während einer geplanten oder ungeplanten Wartung mindestens ein virtueller Computer verfügbar ist und die Azure-SLA für virtuelle Computer eingehalten wird.  Sie können einen virtuellen Computer erstellen oder der Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzufügen.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Verwenden von verwalteten Datenträgern zur Erhöhung der Datenzuverlässigkeit

Virtuelle Computer in einer Verfügbarkeitsgruppe mit Datenträgern, die entweder Speicherkonten oder Speicherskalierungseinheiten gemeinsam nutzen, sind nicht resilient gegen Ausfälle einzelner Speicherskalierungseinheiten während eines Ausfalls. Stellen Sie mit einer Migration zu verwalteten Azure-Datenträgern sicher, dass die Datenträger verschiedener virtueller Computer in der Verfügbarkeitsgruppe ausreichend isoliert sind, um einen Single Point of Failure zu vermeiden.

## <a name="repair-invalid-log-alert-rules"></a>Reparieren ungültiger Protokollwarnungsregeln

Azure Advisor erkennt Protokollwarnungsregeln mit ungültigen Abfragen im Bedingungsabschnitt. Azure Monitor-Protokollwarnungsregeln führen Abfragen mit der angegebenen Häufigkeit aus und lösen auf Grundlage der Ergebnisse Warnungen aus. Es kann vorkommen, dass Abfragen aufgrund von Änderungen an referenzierten Ressourcen, Tabellen oder Befehlen im Laufe der Zeit ungültig werden. Advisor empfiehlt Korrekturen für Warnungsabfragen, um eine automatische Deaktivierung der Regeln zu verhindern und die Überwachungsabdeckung zu gewährleisten. Weitere Informationen finden Sie unter [Problembehandlung von Warnungsregeln](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid).

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Konfigurieren eines konsistenten Indizierungsmodus für Ihre Azure Cosmos DB-Sammlung

Die Konfiguration von Azure Cosmos DB-Containern mit dem Indizierungsmodus „Verzögert“ könnte die Aktualität der Abfrageergebnisse beeinträchtigen. Advisor erkennt Container mit dieser Konfiguration und empfiehlt, den Modus in „Konsistent“ zu ändern. [Weitere Informationen zu Indizierungsrichtlinien in Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md).

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurieren Ihres Azure Cosmos DB-Containers mit einem Partitionsschlüssel

Azure Advisor erkennt nicht partitionierte Azure Cosmos DB-Sammlungen, deren bereitgestelltes Speicherkontingent nahezu erschöpft ist. Daraufhin wird empfohlen, dass Sie diese Sammlungen zu neuen Sammlungen mit einer Partitionsschlüsseldefinition migrieren, um das automatische Aufskalieren durch den Dienst zu ermöglichen. [Weitere Informationen zur Wahl eines Partitionsschlüssels](../cosmos-db/partitioning-overview.md).

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Upgraden Ihres Azure Cosmos DB .NET SDK auf die aktuelle Version über NuGet

Azure Advisor identifiziert Azure Cosmos DB-Konten, die alte Versionen des .NET SDK verwenden. Es wird empfohlen, ein Upgrade auf die neueste Version von NuGet durchzuführen, um aktuelle Fixes, Leistungsverbesserungen und Funktionen zu erhalten. [Weitere Informationen zum Azure Cosmos DB .NET SDK](../cosmos-db/sql-api-sdk-dotnet-standard.md).

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Upgraden Ihres Azure Cosmos DB Java SDK auf die aktuelle Version über Maven

Azure Advisor identifiziert Azure Cosmos DB-Konten, die alte Versionen des Java-SDK verwenden. Es wird empfohlen, ein Upgrade auf die neueste Version von Maven durchzuführen, um aktuelle Fixes, Leistungsverbesserungen und Funktionen zu erhalten. [Weitere Informationen zum Azure Cosmos DB Java-SDK](../cosmos-db/sql-api-sdk-java-v4.md).

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Upgraden Ihres Azure Cosmos DB-Spark-Connectors auf die aktuelle Version über Maven

Azure Advisor identifiziert Azure Cosmos DB-Konten, die alte Versionen des Azure Cosmos DB-Spark-Connectors verwenden. Es wird empfohlen, ein Upgrade auf die neueste Version von Maven durchzuführen, um aktuelle Fixes, Leistungsverbesserungen und Funktionen zu erhalten. [Weitere Informationen zum Azure Cosmos DB-Spark-Connector.](../cosmos-db/create-sql-api-spark.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Erwägen Sie einen Wechsel zu Kafka 2.1 unter HDInsight 4.0

Ab dem 1. Juli 2020 wird es nicht mehr möglich sein, neue Kafka-Cluster mit Kafka 1.1 unter Azure HDInsight 4.0 zu erstellen. Vorhandene Cluster werden unverändert ohne Unterstützung durch Microsoft ausgeführt. Es empfiehlt sich, in HDInsight 4.0 bis zum 30. Juni 2020 auf Kafka 2.1 umzustellen, um potenzielle System-/Supportunterbrechungen zu vermeiden.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Erwägen Sie ein Upgrade älterer Spark-Versionen in HDInsight Spark-Clustern

Ab dem 1. Juli 2020 können Sie keine neuen Spark-Cluster mehr erstellen, wenn Sie Spark 2.1 oder 2.2 unter HDInsight 3.6 verwenden. Sie können keine neuen Spark-Cluster erstellen, wenn Sie Spark 2.3 unter HDInsight 4.0 verwenden. Vorhandene Cluster werden unverändert ohne Unterstützung durch Microsoft ausgeführt. 

## <a name="enable-virtual-machine-replication"></a>Aktivieren der Replikation der virtuellen Computer
Virtuelle Computer, für die die Replikation in eine andere Region nicht aktiviert ist, sind gegenüber regionalen Ausfällen nicht resilient. Durch eine Replikation der VMs werden negative Auswirkungen auf das Geschäft bei Ausfällen einer Azure-Region reduziert. Advisor erkennt virtuelle Computer, auf denen die Replikation nicht aktiviert ist, und empfiehlt deren Aktivierung. Wenn Sie die Replikation aktivieren, können Sie Ihre virtuellen Computer im Falle eines Ausfalls schnell in einer Azure-Remoteregion hochfahren. [Weitere Informationen zur Replikation virtueller Computer.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Upgrade auf die aktuelle Version des Azure Connected Machine-Agents
Der [Azure Connected Machine-Agent](../azure-arc/servers/manage-agent.md) wird in Bezug auf Fehlerbehebungen, Stabilitätsverbesserungen und neue Funktionen regelmäßig aktualisiert. Wir haben Ressourcen identifiziert, die nicht mit der neuesten Version des Computer-Agents arbeiten, und diese Advisor-Empfehlung empfiehlt Ihnen, ein Upgrade Ihres Agents auf die neueste Version vorzunehmen, um die beste Azure Arc-Erfahrung zu erzielen.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Keine Außerkraftsetzung des Hostnamens, um die Integrität der Website sicherzustellen
Advisor empfiehlt, dass Sie versuchen, bei der Application Gateway-Konfiguration die Außerkraftsetzung des Hostnamens zu vermeiden. Die Verwendung einer anderen Domäne auf dem Application Gateway-Front-End als die, die für den Zugriff auf das Back-End genutzt wird, kann ggf. zur Beschädigung von Cookies oder Umleitungs-URLs führen. Beachten Sie hierbei Folgendes: Dies gilt unter Umständen nicht in allen Fällen, und bestimmte Kategorien von Back-Ends (z. B. REST-APIs) sind hierfür normalerweise weniger anfällig. Vergewissern Sie sich, dass das Back-End dies verarbeiten kann, oder aktualisieren Sie die Application Gateway-Konfiguration, damit der Hostname für das Back-End nicht überschrieben werden muss. Fügen Sie bei Verwendung von App Service einen benutzerdefinierten Domänennamen an die Web-App an, und vermeiden Sie die Nutzung des Hostnamens `*.azurewebsites.net` für das Back-End. [Erfahren Sie mehr über benutzerdefinierte Domänen](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Advisor-Empfehlungen finden Sie unter:
* [Einführung in Advisor](advisor-overview.md)
* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Advisor-Bewertung](azure-advisor-score.md)
* [Reduzieren der Dienstkosten mithilfe des Azure Advisors](advisor-cost-recommendations.md)
* [Verbessern der Leistung von Azure-Anwendungen mit dem Azure Advisor](advisor-performance-recommendations.md)
* [Sicherheitsempfehlungen von Advisor](advisor-security-recommendations.md)
* [Sicherstellen des optimalen Betriebs mit dem Azure Advisor](advisor-operational-excellence-recommendations.md)
