---
title: Übersicht über das Blaupausenbeispiel „Azure Security Benchmark Foundation“
description: Übersicht und Architektur für das Blaupausenbeispiel „Azure Security Benchmark Foundation“.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 82521251ca395a0d5a1409dae62954950b7da8a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614729"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Übersicht über das Blaupausenbeispiel „Azure Security Benchmark Foundation“

Das Blaupausenbeispiel „Azure Security Benchmark Foundation“ enthält eine Reihe grundlegender Infrastrukturmuster, die Sie bei der Erstellung einer sicheren und konformen Azure-Umgebung unterstützen. Mithilfe der Blaupause können Sie eine cloudbasierte Architektur mit Lösungen für Szenarien bereitstellen, in denen Akkreditierungs- oder Konformitätsanforderungen erfüllt werden müssen. Es dient zum Bereitstellen und Konfigurieren von Netzwerkgrenzen, Überwachungsmöglichkeiten und anderen Ressourcen im Einklang mit den Richtlinien und anderen Schutzmaßnahmen, die im [Azure-Sicherheitsvergleichstest](../../../../security/benchmarks/index.yml) definiert sind.

## <a name="architecture"></a>Aufbau

Die grundlegende Umgebung, die durch dieses Blaupausenbeispiel erstellt wird, basiert auf den Architekturprinzipien eines [Hub-and-Spoke-Modells](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
Durch die Blaupause wird ein virtuelles Hub-Netzwerk mit folgenden Komponenten bereitgestellt: allgemeine und gemeinsam genutzte Ressourcen, Dienste und Artefakte wie Bastion, Gateway und Firewall für Konnektivität sowie Verwaltungs- und Jumpbox-Subnetze zum Hosten zusätzlicher/optionaler Wartungs-, Verwaltungs- und Konnektivitätsinfrastruktur. Darüber hinaus wird mindestens ein virtuelles Spoke-Netzwerk zum Hosten von Anwendungsworkloads wie etwa Web- und Datenbankdienste bereitgestellt. Virtuelle Spoke-Netzwerke werden mittels Peering in virtuellen Azure-Netzwerken mit dem virtuellen Hub-Netzwerk verbunden, um eine nahtlose und sichere Konnektivität zu gewährleisten. Durch erneutes Zuweisen der Beispielblaupause oder manuelles Erstellen eines virtuellen Azure-Netzwerks und anschließendes Peering mit dem virtuellen Hub-Netzwerk können weitere Spokes hinzugefügt werden. Die gesamte externe Konnektivität mit den virtuellen Spoke-Netzwerken und Subnetzen wird so konfiguriert, dass Datenverkehr das virtuelle Hub-Netzwerk durchläuft und über Firewall, Gateway und Verwaltungs-Jumpboxes geleitet wird.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Architekturdiagramm für das Blaupausenbeispiel „Azure Security Benchmark Foundation“" border="false":::

Durch diese Blaupause werden mehrere Azure-Dienste bereitgestellt, um eine sichere, überwachte und für Unternehmen geeignete Grundlage zu erhalten. Diese Umgebung besteht aus den folgenden Komponenten:

- [Azure Monitor-Protokolle](../../../../azure-monitor/logs/data-platform-logs.md) und ein Azure-Speicherkonto, um sicherzustellen, dass Ressourcenprotokolle, Aktivitätsprotokolle, Metriken und Netzwerkdatenverkehrsflüsse an einem zentralen Ort gespeichert werden, was Abfragen, Analysen, Warnungen und die Archivierung erleichtert.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (Standardversion), um Azure-Ressourcen vor Bedrohungen zu schützen.
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) im Hub-Netzwerk zur Unterstützung von Subnetzen für Konnektivität mit einem lokalen Netzwerk, ein Eingangs- und Ausgangsstapel für Internetkonnektivität sowie optionale Subnetze für die Bereitstellung zusätzlicher administrativer oder verwaltungsbezogener Dienste. Virtual Network im Spoke-Netzwerk enthält Subnetze zum Hosten von Anwendungsworkloads. Nach der Bereitstellung können bei Bedarf weitere Subnetze erstellt werden, um bestimmte Szenarien zu unterstützen.
- [Azure Firewall](../../../../firewall/overview.md), um sämtlichen ausgehenden Internetdatenverkehr weiterzuleiten und eingehenden Internetdatenverkehr per Jumpbox zu ermöglichen. (Durch die standardmäßigen Firewallregeln wird sowohl ein- als auch ausgehender Internetdatenverkehr blockiert. Daher müssen nach der Bereitstellung entsprechende Regeln konfiguriert werden.)
- [Netzwerksicherheitsgruppen](../../../../virtual-network/network-security-group-how-it-works.md) (NSGs), die allen Subnetzen (mit Ausnahme diensteigener Subnetze wie Azure Bastion, Gateway und Azure Firewall) zugewiesen und so konfiguriert sind, dass sämtlicher ein- und ausgehender Internetdatenverkehr blockiert wird.
- [Anwendungssicherheitsgruppen](../../../../virtual-network/application-security-groups.md), um virtuelle Azure-Computer zu gruppieren und allgemeine Netzwerksicherheitsrichtlinien anzuwenden.
- [Routingtabellen](../../../../virtual-network/manage-route-table.md), um sämtlichen ausgehenden Internetdatenverkehr aus Subnetzen durch die Firewall zu leiten. (Azure Firewall- und NSG-Regeln müssen nach der Bereitstellung konfiguriert werden, um Konnektivität zu ermöglichen.)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) für die Überwachung, Diagnose und Anzeige von Metriken für Ressourcen im virtuellen Azure-Netzwerk.
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md) zum Schutz von Azure-Ressourcen vor DDoS-Angriffen.
- [Azure Bastion](../../../../bastion/bastion-overview.md) für nahtlose und sichere Konnektivität mit einem virtuellen Computer ohne öffentliche IP-Adresse, Agent oder spezielle Clientsoftware.
- [Azure VPN Gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md), um verschlüsselten Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem lokalen Standort über das öffentliche Internet zu senden.

> [!NOTE]
> Von Azure Security Benchmark Foundation wird eine grundlegende Architektur für Workloads eingerichtet. Das obige Architekturdiagramm enthält mehrere fiktive Ressourcen, um die potenzielle Verwendung von Subnetzen zu veranschaulichen. Für diese grundlegende Architektur müssen allerdings noch Workloads bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Sie haben sich mit der Übersicht und Architektur für das Blaupausenbeispiel „Azure Security Benchmark Foundation“ vertraut gemacht.

> [!div class="nextstepaction"]
> [Blaupause „Azure Security Benchmark Foundation“: Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
