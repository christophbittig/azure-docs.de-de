---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: karlerickson
title: Azure Spring Cloud-Referenzarchitektur
ms.author: akaleshian
ms.service: spring-cloud
description: Diese Referenzarchitektur ist eine Grundlage unter Verwendung eines typischen Hub-and-Spoke-Designs für Unternehmen für den Einsatz von Azure Spring Cloud.
ms.openlocfilehash: a3e8cbb94500c92ba4d32e1fef4f02f1acf9c1c9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486841"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure Spring Cloud-Referenzarchitektur

Diese Referenzarchitektur ist eine Grundlage unter Verwendung eines typischen Hub-and-Spoke-Designs für Unternehmen für den Einsatz von Azure Spring Cloud. Im Design wird Azure Spring Cloud in einer einzigen Spoke bereitgestellt, die von den im Hub gehosteten gemeinsamen Diensten abhängig ist. Die Architektur wird mit Komponenten erstellt, um den Grundsätzen des [Microsoft Azure Well-Architected Framework][16] gerecht zu werden.

Eine Implementierung dieser Architektur finden Sie im [Azure Spring Cloud Reference Architecture][10]-Repository auf GitHub.

Zu den Bereitstellungsoptionen für diese Architektur gehören Azure Resource Manager (ARM), Terraform, die Azure CLI und Bicep. Die Artefakte in diesem Repository bilden eine Grundlage, die Sie für Ihre Umgebung anpassen können. Sie können Ressourcen wie Azure Firewall oder Application Gateway in verschiedenen Ressourcengruppen oder Abonnements gruppieren. Diese Gruppierung hilft dabei, verschiedene Funktionen wie z. B. IT-Infrastruktur, Sicherheit, Geschäftsanwendungsteams usw. voneinander getrennt zu halten.

## <a name="planning-the-address-space"></a>Planen des Adressraums

Azure Spring Cloud erfordert zwei dedizierte Subnetze:

* Dienstruntime
* Spring Boot-Anwendungen

Jedes dieser Subnetze erfordert einen dedizierten Azure Spring Cloud-Cluster. Mehrere Cluster können nicht die gleichen Subnetze gemeinsam verwenden. Die Mindestgröße jedes Subnetzes beträgt /28. Die Anzahl der von Azure Spring Cloud unterstützten Anwendungsinstanzen variiert je nach Größe des Subnetzes. Die detaillierten Anforderungen an das virtuelle Netzwerk (VNET) finden Sie im Abschnitt [Anforderungen für virtuelle Netzwerke][11] des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk][17].

> [!WARNING]
> Die ausgewählte Subnetzgröße darf sich weder mit dem vorhandenen VNET-Adressraum noch mit den Adressbereichen eines mit Peering verbundenen oder lokalen Subnetzes überlappen.

## <a name="use-cases"></a>Anwendungsfälle

Typische Einsatzmöglichkeiten für diese Architektur sind:

* Private Anwendungen: Interne Anwendungen, die in hybriden Cloud-Umgebungen bereitgestellt werden
* Öffentliche Anwendungen: Nach außen gerichtete Anwendungen

Diese Anwendungsfälle sind mit Ausnahme ihrer Sicherheits- und Netzwerkverkehrsregeln vergleichbar. Diese Architektur ist darauf ausgelegt, die einzelnen Nuancen zu unterstützen.

## <a name="private-applications"></a>Private Anwendungen

In der folgenden Liste werden die Infrastrukturanforderungen für private Anwendungen beschrieben. Diese Anforderungen sind für hochgradig regulierte Umgebungen typisch.

* Ein Subnetz darf nur über eine Instanz von Azure Spring Cloud verfügen.
* Die Einhaltung mindestens eines Sicherheitsvergleichstests sollte erzwungen werden.
* Anwendungshost-DNS-Einträge (Domain Name Service) sollten im privaten DNS von Azure gespeichert werden.
* Azure-Dienstabhängigkeiten sollten über Dienstendpunkte oder Private Link kommuniziert werden.
* Ruhende Daten sollten verschlüsselt werden.
* In Übertragung begriffene Daten sollten verschlüsselt werden.
* DevOps-Bereitstellungspipelines können verwendet werden (z. B. Azure DevOps) und benötigen Netzwerkkonnektivität mit Azure Spring Cloud.
* Ausgehender Datenverkehr sollte durch ein zentrales virtuelles Netzwerkgerät (Network Virtual Appliance, NVA; z. B. Azure Firewall) geleitet werden.
* Wenn der [Azure Spring Cloud-Konfigurationsserver][8] verwendet wird, um Konfigurationseigenschaften aus einem Repository zu laden, muss das Repository privat sein.
* Der Zero Trust-Sicherheitsansatz von Microsoft erfordert, dass Geheimnisse, Zertifikate und Anmeldeinformationen in einem sicheren Tresor gespeichert werden. Azure Key Vault wird als Dienst empfohlen.
* Die Namensauflösung von Hosts sollte lokal und in der Cloud bidirektional sein.
* Mit Ausnahme des Datenverkehrs auf Steuerungsebene kein direkter Ausgang zum öffentlichen Internet.
* Von der Azure Spring Cloud-Bereitstellung verwaltete Ressourcengruppen dürfen nicht geändert werden.
* Von der Azure Spring Cloud-Bereitstellung verwaltete Subnetze dürfen nicht geändert werden.

Die folgende Liste enthält die Komponenten, die das Design ausmachen:

* Lokales Netzwerk
  * Domain Name Service (DNS)
  * Gateway
* Hubabonnement
  * Application Gateway-Subnetz
  * Azure Firewall-Subnetz
  * Shared Services-Subnetz
* Verbundenes Abonnement
  * Azure Bastion-Subnetz
  * VNET-Peer

In der folgenden Liste werden die Azure-Dienste in dieser Referenzarchitektur beschrieben:

* [Azure Key Vault][2]: Ein hardwaregestützter Dienst zur Verwaltung von Anmeldeinformationen, der eng in Microsoft-Identitätsdienste und -Computeressourcen integriert ist.

* [Azure Monitor][3]: Eine allumfassende Sammlung von Überwachungsdiensten für Anwendungen, die Dateien sowohl in Azure als auch in der lokalen Umgebung bereitstellen.

* [Azure Pipelines][5]: Ein CI/CD-Dienst (Continuous Integration/Continuous Delivery) mit vollem Funktionsumfang, der aktualisierte Spring Boot-Apps automatisch in der Azure Spring Cloud bereitstellen kann.

* [Microsoft Defender für Cloud][4]: Ein einheitliches System für Sicherheitsmanagement und Bedrohungsschutz für Workloads, die über lokale Umgebungen, mehrere Clouds und Azure hinweg genutzt werden.

* [Azure Spring Cloud][1]: Ein verwalteter Dienst, der speziell für Java-basierte Spring Boot-Anwendungen und NET-basierte [Steeltoe][9]-Anwendungen entworfen und optimiert wurde.

Das folgende Diagramm stellt ein gut konzipiertes Hub-and-Spoke-Design dar, das die oben genannten Anforderungen erfüllt:

![Diagramm mit Referenzarchitektur für private Anwendungen](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Öffentliche Anwendungen

In der folgenden Liste werden die Infrastrukturanforderungen für öffentliche Anwendungen beschrieben. Diese Anforderungen sind für hochgradig regulierte Umgebungen typisch. Diese Anforderungen sind eine Ergänzung zu den Anforderungen im vorangegangenen Abschnitt. Zusätzliche Punkte sind durch Kursivschrift gekennzeichnet.

* Ein Subnetz darf nur über eine Instanz von Azure Spring Cloud verfügen.
* Die Einhaltung mindestens eines Sicherheitsvergleichstests sollte erzwungen werden.
* Anwendungshost-DNS-Einträge (Domain Name Service) sollten im privaten DNS von Azure gespeichert werden.
* _Azure DDoS Protection Standard sollte aktiviert sein._
* Azure-Dienstabhängigkeiten sollten über Dienstendpunkte oder Private Link kommuniziert werden.
* Ruhende Daten sollten verschlüsselt werden.
* In Übertragung begriffene Daten sollten verschlüsselt werden.
* DevOps-Bereitstellungspipelines können verwendet werden (z. B. Azure DevOps) und benötigen Netzwerkkonnektivität mit Azure Spring Cloud.
* Ausgehender Datenverkehr sollte durch ein zentrales virtuelles Netzwerkgerät (Network Virtual Appliance, NVA; z. B. Azure Firewall) geleitet werden.
* _Der eingehende Datenverkehr sollte mindestens von Application Gateway oder Azure Front Door verwaltet werden._
* _Adressen, die über das Internet geroutet werden können, sollten im öffentlichen DNS von Azure gespeichert werden._
* Der Zero Trust-Sicherheitsansatz von Microsoft erfordert, dass Geheimnisse, Zertifikate und Anmeldeinformationen in einem sicheren Tresor gespeichert werden. Azure Key Vault wird als Dienst empfohlen.
* Die Namensauflösung von Hosts sollte lokal und in der Cloud bidirektional sein.
* Mit Ausnahme des Datenverkehrs auf Steuerungsebene kein direkter Ausgang zum öffentlichen Internet.
* Von der Azure Spring Cloud-Bereitstellung verwaltete Ressourcengruppen dürfen nicht geändert werden.
* Von der Azure Spring Cloud-Bereitstellung verwaltete Subnetze dürfen nicht geändert werden.

Die folgende Liste enthält die Komponenten, die das Design ausmachen:

* Lokales Netzwerk
  * Domain Name Service (DNS)
  * Gateway
* Hubabonnement
  * Application Gateway-Subnetz
  * Azure Firewall-Subnetz
  * Shared Services-Subnetz
* Verbundenes Abonnement
  * Azure Bastion-Subnetz
  * VNET-Peer

In der folgenden Liste werden die Azure-Dienste in dieser Referenzarchitektur beschrieben:

* _[Azure Application Firewall][7]: eine Funktion von Azure Application Gateway, die zentralisierten Schutz von Anwendungen vor gängigen Exploits und Schwachstellen bietet._

* _[Azure Application Gateway][6]: ein Load Balancer, der für den Anwendungsverkehr mit Transport Layer Security (TLS) Offload zuständig ist und auf Layer 7 arbeitet._

* [Azure Key Vault][2]: Ein hardwaregestützter Dienst zur Verwaltung von Anmeldeinformationen, der eng in Microsoft-Identitätsdienste und -Computeressourcen integriert ist.

* [Azure Monitor][3]: Eine allumfassende Sammlung von Überwachungsdiensten für Anwendungen, die Dateien sowohl in Azure als auch in der lokalen Umgebung bereitstellen.

* [Azure Pipelines][5]: Ein CI/CD-Dienst (Continuous Integration/Continuous Delivery) mit vollem Funktionsumfang, der aktualisierte Spring Boot-Apps automatisch in der Azure Spring Cloud bereitstellen kann.

* [Microsoft Defender für Cloud][4]: Ein einheitliches System für Sicherheitsmanagement und Bedrohungsschutz für Workloads, die über lokale Umgebungen, mehrere Clouds und Azure hinweg genutzt werden.

* [Azure Spring Cloud][1]: Ein verwalteter Dienst, der speziell für Java-basierte Spring Boot-Anwendungen und NET-basierte [Steeltoe][9]-Anwendungen entworfen und optimiert wurde.

Das folgende Diagramm zeigt ein gut durchdachtes Hub-and-Spoke-Konzept, das die oben genannten Anforderungen erfüllt.  Beachten Sie, dass nur das virtuelle Hub-Netz mit dem Internet kommuniziert:

![Diagramm mit Referenzarchitektur für öffentliche Anwendungen](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Konnektivität zwischen Azure Spring Cloud und lokalen Systemen

Anwendungen in Azure Spring Cloud können mit verschiedenen Azure-, lokalen und externen Ressourcen kommunizieren. Das Hub-and-Spoke-Design ermöglicht Anwendungen, den Datenverkehr mithilfe von Express Route oder Site-to-Site-VPN (virtuelles privates Netzwerk) extern oder an das lokale Netzwerk weiterleiten.

## <a name="azure-well-architected-framework-considerations"></a>Überlegungen zu Azure Well-Architected Framework

Das [Azure Well-Architected Framework][16] ist eine Reihe von Leitfäden, die bei der Einrichtung einer starken Infrastrukturbasis befolgt werden müssen. Das Framework umfasst folgende Kategorien: Kostenoptimierung, erstklassige Betriebsprozesse, effiziente Leistung, Zuverlässigkeit und Sicherheit.

### <a name="cost-optimization"></a>Kostenoptimierung

Der Entwurf des verteilten Systems bringt die Ausdehnung der Infrastruktur mit sich. Dies führt zu unerwarteten und unkontrollierbaren Kosten. Azure Spring Cloud basiert auf Komponenten, die skaliert werden, um die Nachfrage zu erfüllen und die Kosten zu optimieren. Der Kern dieser Architektur ist der Azure Kubernetes Service (AKS). Der Dienst ist so konzipiert, dass die Komplexität und der betriebliche Aufwand bei der Verwaltung von Kubernetes reduziert werden. Dies umfasst auch die Effizienz der Betriebskosten des Clusters.

Sie können verschiedene Anwendungen und Anwendungstypen in einer einzelnen Instanz von Azure Spring Cloud bereitstellen. Der Dienst unterstützt die automatische Skalierung von Anwendungen, die durch Metriken oder Zeitpläne ausgelöst werden, die die Auslastung und Kosteneffizienz verbessern.

Sie können auch Application Insights und Azure Monitor verwenden, um die Betriebskosten zu senken. Mit den von der umfassenden Protokollierungslösung gebotenen Einblicken können Sie Automatisierung implementieren, um die Komponenten des Systems in Echtzeit zu skalieren. Sie können Protokolldaten auch analysieren, um Ineffizienzen im Anwendungscode zu erkennen, die Sie zur Verbesserung der Gesamtkosten und der Leistung des Systems beseitigen können.

### <a name="operational-excellence"></a>Optimaler Betrieb

Azure Spring Cloud spricht mehrere Aspekte erstklassige Betriebsprozesse an. Sie können diese Aspekte kombinieren, um sicherzustellen, dass der Dienst in Produktionsumgebungen effizient ausgeführt wird, wie in der folgenden Liste beschrieben:

* Mit Azure Pipelines können Sie sicherstellen, dass Bereitstellungen zuverlässig und konsistent sind, und dabei dazu beitragen, menschliche Fehler zu vermeiden.
* Sie können Azure Monitor und Application Insights verwenden, um Protokoll- und Telemetriedaten zu speichern.
  Sie können gesammelte Protokoll- und Metrikdaten bewerten, um die Integrität und Leistung Ihrer Anwendungen sicherzustellen. Die Überwachung der Anwendungsleistung (Application Performance Monitoring, APM) ist über einen Java-Agent vollständig in den Dienst integriert. Dieser Agent bietet Einblick in alle bereitgestellten Anwendungen und Abhängigkeiten, ohne dass zusätzlicher Code erforderlich ist. Weitere Informationen finden Sie im Blogbeitrag [Effortlessly monitor applications and dependencies in Azure Spring Cloud!][15] (Müheloses Überwachen von Anwendungen und Abhängigkeiten in Azure Spring Cloud).
* Indem Sie mit Microsoft Defender für Cloud eine Plattform zur Analyse und Bewertung der bereitgestellten Daten einsetzen, können Sie sicherstellen, dass die Sicherheit der Anwendungen aufrechterhalten wird.
* Der Dienst unterstützt verschiedene Bereitstellungsmuster. Weitere Informationen finden Sie unter [Einrichten einer Stagingumgebung in Azure Spring Cloud][14].

### <a name="reliability"></a>Zuverlässigkeit

Azure Spring Cloud ist auf AKS aufgebaut. Während AKS durch Clustering ein gewisses Maß an Ausfallsicherheit bietet, geht diese Referenzarchitektur sogar noch weiter, indem sie Dienste und architektonische Überlegungen einbezieht, um die Verfügbarkeit der Anwendung im Falle eines Komponentenausfalls zu erhöhen.

Da diese Architektur auf einem gut definierten Hub-and-Spoke-Design aufgebaut ist, gewährleistet ihre Grundlage, dass Sie sie in mehreren Regionen bereitstellen können. Für den Anwendungsfall der privaten Anwendung nutzt die Architektur privates Azure-DNS, um die fortlaufende Verfügbarkeit während eines geografischen Ausfalls sicherzustellen. Für den Anwendungsfall der öffentlichen Anwendung stellen Azure Front Door und Azure Application Gateway die Verfügbarkeit sicher.

### <a name="security"></a>Sicherheit

Für die Sicherheit dieser Architektur sorgt die Einhaltung branchendefinierter Steuerungen und Benchmarks. In diesem Zusammenhang bedeutet "Kontrolle" eine prägnante und klar definierte bewährte Praxis, wie z. B. "Anwendung des Prinzips der geringsten Privilegien bei der Implementierung des Zugriffs auf Informationssysteme. IAM-05" Die Kontrollen in dieser Architektur stammen aus der [Cloud Control Matrix][19] (CCM) der [Cloud Security Alliance][18] (CSA) und dem [Microsoft Azure Foundations Benchmark][20] (MAFB) des [Center for Internet Security][21] (CIS). In den angewendeten Steuerungen liegt der Schwerpunkt auf den primären Sicherheitsentwurfsprinzipien von Governance, Netzwerk und Anwendungssicherheit. Es liegt in ihrer Verantwortung, die Entwurfsprinzipien für Identität, Zugriffsverwaltung und Speicher zu beachten, da sie sie sich auf Ihre Zielinfrastruktur beziehen.

#### <a name="governance"></a>Governance

Der wichtigste Aspekt der Governance, den diese Architektur berücksichtigt, ist Trennung durch die Isolierung von Netzwerkressourcen. In der CCM empfiehlt DCS-08 die Eingangs- und Ausgangssteuerung für das Rechenzentrum. Für diese Steuerung verwendet die Architektur ein auf Netzwerksicherheitsgruppen (NSGs) basierendes Hub-and-Spoke-Design, um den Ost-West-Datenverkehr zwischen Ressourcen zu filtern. Die Architektur filtert auch den Datenverkehr zwischen zentralen Diensten im Hub und Ressourcen in der Spoke. Die Architektur verwendet eine Instanz von Azure Firewall zur Verwaltung des Datenverkehrs zwischen dem Internet und den Ressourcen innerhalb der Architektur.

In der folgenden Liste wird die Steuerung angezeigt, die die Sicherheit des Rechenzentrums in dieser Referenz behandelt:

| CSA CCM Control ID | CSA CCM Control Domain |
| :----------------- | :----------------------|
| DCS-08 | Rechenzentrumssicherheit-Eintrag nicht autorisierter Personen |

#### <a name="network"></a>Netzwerk

Der Netzwerkentwurf, der diese Architektur unterstützt, wird aus dem herkömmlichen Hub-and-Spoke-Modell abgeleitet. Diese Entscheidung stellt sicher, dass die Netzwerkisolation ein grundlegendes Konstrukt ist. CCM-Steuerung IVS-06 empfiehlt, den Datenverkehr zwischen Netzwerken und virtuellen Computern zwischen vertrauenswürdigen und nicht vertrauenswürdigen Umgebungen einzuschränken und zu überwachen. Bei dieser Architektur wird die Kontrolle durch die Implementierung der NSGs für den Ost-West-Verkehr (innerhalb des "Rechenzentrums") und der Azure Firewall für den Nord-Süd-Verkehr (außerhalb des "Rechenzentrums") übernommen. CCM-Steuerung IPY-04 empfiehlt, dass die Infrastruktur sichere Netzwerkprotokolle für den Datenaustausch zwischen Diensten verwenden soll. Alle Azure-Dienste, die diese Architektur unterstützen, verwenden standardmäßige sichere Protokolle wie TLS für HTTP und SQL.

In der folgenden Liste werden die CCM-Steuerungen angezeigt, die die Netzwerksicherheit in dieser Referenz behandeln:

| CSA CCM Control ID | CSA CCM Control Domain |
| :----------------- | :----------------------|
| IPY-04             | Netzwerkprotokolle      |
| IVS-06             | Netzwerksicherheit       |

Die Netzwerkimplementierung wird durch die Definition von MAFB-Steuerungen weiter gesichert. Die Steuerungen stellen sicher, dass der vom öffentlichen Internet in die Umgebung eingehende Datenverkehr eingeschränkt wird.

In der folgenden Liste werden die CIS-Steuerungen angezeigt, die die Netzwerksicherheit in dieser Referenz behandeln:

| CIS Control ID | CIS Control-Beschreibung |
| :------------- | :---------------------- |
| 6.2 | Stellt sicher, dass der SSH-Zugriff aus dem Internet eingeschränkt ist. |
| 6.3 | Stellt sicher, dass keine SQL-Datenbank-Instanzen Eingang 0.0.0.0/0 (BELIEBIGE IP) zulassen. |
| 6,5 | Stellt sicher, dass Network Watcher „aktiviert“ ist. |
| 6.6 | Stellt sicher, dass der Eingang mit UDP aus dem Internet eingeschränkt ist. |

Azure Spring Cloud erfordert bei der Bereitstellung in einer gesicherten Umgebung, dass der Verwaltungsdatenverkehr von Azure kommt. Um dies zu erreichen, müssen Sie die unter [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET](./vnet-customer-responsibilities.md) aufgeführten Netzwerk- und Anwendungsregeln zulassen.

#### <a name="application-security"></a>Anwendungssicherheit

Dieser Entwurfsprinzipal deckt die grundlegenden Komponenten von Identität, Datenschutz, Schlüsselverwaltung und Anwendungskonfiguration ab. Entwurfsbedingt wird eine in Azure Spring Cloud bereitgestellte Anwendung mit den geringsten Berechtigungen ausgeführt, die zur Funktion erforderlich sind. Bei Verwendung des Diensts ist der Satz der Autorisierungssteuerungen direkt auf den Datenschutz bezogen. Die Schlüsselverwaltung verstärkt diesen mehrstufigen Anwendungssicherheitsansatz.

In der folgenden Liste werden die CCM-Steuerungen angezeigt, die die Schlüsselverwaltung in dieser Referenz behandeln:

| CSA CCM Control ID | CSA CCM Control Domain |
| :----------------- | :--------------------- |
| EKM-01 | Verschlüsselung und Schlüsselverwaltung: Berechtigung |
| EKM-02 | Verschlüsselung und Schlüsselverwaltung: Schlüsselgenerierung |
| EKM-03 | Verschlüsselung und Schlüsselverwaltung: Schutz von sensiblen Daten |
| EKM-04 | Verschlüsselung und Schlüsselverwaltung: Speicherung und Zugriff |

Von CCM, EKM-02 und EKM-03 werden Richtlinien und Verfahren zum Verwalten von Schlüsseln und Verwenden von Verschlüsselungsprotokollen zum Schutz sensibler Daten empfohlen. EKM-01 empfiehlt, dass alle kryptografischen Schlüssel über identifizierbare Besitzer verfügen, damit sie verwaltet werden können. EKM-04 empfiehlt die Verwendung von Standardalgorithmen.

In der folgenden Liste werden die CIS-Steuerungen angezeigt, die die Schlüsselverwaltung in dieser Referenz behandeln:

| CIS Control ID | CIS Control-Beschreibung |
| :------------- | :---------------------- |
| 8.1 | Stellt sicher, dass das Ablaufdatum für alle Schlüssel festgelegt ist. |
| 8,2 | Stellt sicher, dass das Ablaufdatum für alle Geheimnisse festgelegt ist. |
| 8,4 | Stellt sicher, dass der Schlüsseltresor wiederhergestellt werden kann. |

Die CIS-Steuerungen 8.1 und 8.2 empfehlen, dass die Ablaufdaten für Anmeldeinformationen festgelegt werden, um sicherzustellen, dass die Rotation erzwungen wird. Die CIS-Steuerung 8.4 stellt sicher, dass der Inhalt des Schlüsseltresors wiederhergestellt werden kann, um die Geschäftskontinuität aufrechtzuerhalten.

Die Aspekte der Anwendungssicherheit legen eine Grundlage für die Verwendung dieser Referenzarchitektur fest, um eine Spring-Workload in Azure zu unterstützen.

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie diese Referenzarchitektur mit den ARM-, Terraform- und Azure CLI-Bereitstellungen, die im [Azure Spring Cloud Reference Architecture][10]-Repository verfügbar sind.

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./how-to-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./how-to-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/
