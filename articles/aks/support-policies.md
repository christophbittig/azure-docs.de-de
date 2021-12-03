---
title: Unterstützungsrichtlinien für Azure Kubernetes Service (AKS)
description: Hier erhalten Sie Informationen zu AKS-Unterstützungsrichtlinien (Azure Kubernetes Service), zur gemeinsamen Verantwortung und zu Features, die in der Vorschauversion (bzw. Alpha- oder Betaversion) vorliegen.
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 759d2714f755c39e0b189d7578e90cd268cd53c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222455"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Unterstützungsrichtlinien für Azure Kubernetes Service

Dieser Artikel enthält Details zu den Richtlinien und Einschränkungen für den technischen Support von Azure Kubernetes Service (AKS). Der Artikel beschreibt auch die Agentknotenverwaltung, verwaltete Steuerungsebenenkomponenten, Open-Source-Komponenten von Drittanbietern sowie die Sicherheits- oder Patchverwaltung.

## <a name="service-updates-and-releases"></a>Dienstupdates und -versionen

* Versionsinformationen finden Sie unter [AKS-Versionshinweise](https://github.com/Azure/AKS/releases).
* Informationen zu den Features in der Vorschau finden Sie in der [AKS-Roadmap](https://github.com/Azure/AKS/projects/1).

## <a name="managed-features-in-aks"></a>Verwaltete Features in AKS

IaaS-Cloudkomponenten (Infrastructure-as-a-Service) der Basisinfrastruktur, z. B. Compute- oder Netzwerkkomponenten, bieten Ihnen Zugriff auf Steuerelemente und Anpassungsoptionen auf niedriger Ebene. Im Gegensatz dazu bietet AKS eine betriebsbereite Kubernetes-Bereitstellung, die Ihnen die üblichen erforderlichen Konfigurationen und Funktionen für Ihren Cluster zur Verfügung stellt. Als AKS-Benutzer verfügen Sie über eingeschränkte Anpassungs- und Bereitstellungsoptionen. Allerdings müssen Sie sich nicht direkt um Kubernetes-Cluster kümmern oder diese verwalten.

Mit AKS erhalten Sie eine vollständig verwaltete *Steuerungsebene*. Die Steuerungsebene enthält alle Komponenten und Dienste, die Sie benötigen, um die Kubernetes-Cluster zu betreiben und den Endbenutzern zur Verfügung zu stellen. Alle Kubernetes-Komponenten werden von Microsoft verwaltet und betrieben.

Microsoft verwaltet und überwacht die folgenden Komponenten über den Steuerungsbereich:

* Kubelet- oder Kubernetes-API-Server
* Etcd oder ein kompatibler Schlüssel-Wert-Speicher, der Servicequalität (QoS, Quality of Service), Skalierbarkeit und Laufzeit bietet
* DNS-Dienste (z. B. kube-dns oder CoreDNS)
* Kubernetes-Proxy oder -Netzwerk
* Alle zusätzlichen Add-On- oder Systemkomponenten, die im kube-system-Namespace ausgeführt werden

AKS ist keine PaaS-Lösung (Platform-as-a-Service). Einige Komponenten, z. B. Agentknoten, weisen eine *gemeinsame Verantwortung* auf, wobei Benutzer bei der Verwaltung des AKS-Clusters helfen müssen. Benutzereingaben sind z. B. erforderlich, um einen Sicherheitspatch für das Betriebssystem (OS) eines Agentknotens anzuwenden.

Die Dienste sind in dem Sinne *verwaltet*, dass Microsoft und das AKS-Team die Dienste bereitstellen, betreiben und für deren Verfügbarkeit und Funktionalität verantwortlich sind. Kunden können diese verwalteten Komponenten nicht ändern. Microsoft beschränkt die Anpassung, um eine konsistente und skalierbare Benutzererfahrung zu gewährleisten. Eine vollständig anpassbare Lösung finden Sie unter [AKS-Engine](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Gemeinsame Verantwortung

Wenn ein Cluster erstellt wird, definieren Sie die Kubernetes-Agentknoten, die von AKS erstellt werden. Auf diesen Knoten werden Ihre Workloads ausgeführt.

Da die Agentknoten privaten Code ausführen und vertrauliche Daten speichern, kann der Microsoft-Support nur eingeschränkt darauf zugreifen. Der Microsoft-Support kann sich ohne Ihre ausdrückliche Genehmigung oder Hilfe nicht bei diesen Knoten anmelden, Befehle ausführen oder Protokolle anzeigen.

Jede Änderung, die mithilfe einer der IaaS-APIs direkt an den Agentknoten vorgenommen wird, führt dazu, dass der Cluster nicht mehr unterstützt werden kann. Jede Änderung an den Agentknoten muss mithilfe nativer Kubernetes-Mechanismen, z. B. `Daemon Sets`, vorgenommen werden.

Ebenso führt das Ändern der vom System erstellten Metadaten dazu, dass der Cluster nicht mehr unterstützt werden kann. Sie können jedoch dem Cluster und den Knoten Metadaten, z. B. Tags und Bezeichnungen, hinzufügen.

## <a name="aks-support-coverage"></a>AKS-Supportabdeckung

In den folgenden Beispielen bietet Microsoft technischen Support:

* Konnektivität mit allen Kubernetes-Komponenten, die vom Kubernetes-Dienst bereitgestellt und unterstützt werden (beispielsweise der API-Server).
* Verwaltung, Betriebszeit, QoS und Vorgänge der Kubernetes-Steuerungsebenendienste (beispielsweise Kubernetes-Steuerungsebene, API-Server, etcd und coreDNS).
* etcd-Datenspeicher. Der Support beinhaltet automatisierte, transparente Sicherungen sämtlicher etcd-Daten im 30-Minuten-Takt für die Planung der Notfallwiederherstellung und die Wiederherstellung des Clusterzustands. Diese Sicherungen sind für Sie und andere Benutzer nicht direkt verfügbar. Sie stellen die Zuverlässigkeit und Konsistenz der Daten sicher. Etcd: Ein On-Demand-Rollback oder eine Wiederherstellung wird als Feature nicht unterstützt.
* Alle Integrationspunkte im Azure-Cloudanbietertreiber für Kubernetes. Dazu gehören Integrationen in andere Azure-Dienste wie Lastenausgleichsmodule, persistente Volumes oder Netzwerke (Kubernetes und Azure CNI).
* Fragen oder Probleme zur Anpassung von Komponenten der Steuerungsebene wie Kubernetes-API-Server, etcd und coreDNS.
* Probleme bei Netzwerken, z. B. Azure CNI, Kubenet oder andere Probleme in Verbindung mit Netzwerkzugriff und -funktionalität. Probleme können DNS-Auflösung, Paketverluste, Routing usw. umfassen. Microsoft unterstützt verschiedene Netzwerkszenarien:
  * Kubenet und Azure CNI mit verwalteten VNETs oder benutzerdefinierten Subnetzen (Bring Your Own).
  * Konnektivität mit anderen Azure-Diensten und -Anwendungen
  * Eingangscontroller und Eingangs- oder Lastenausgleichskonfigurationen
  * Netzwerkleistung und Wartezeit
  * [Netzwerkrichtlinien](use-network-policies.md#differences-between-azure-and-calico-policies-and-their-capabilities)


> [!NOTE]
> Alle von Microsoft/AKS ausgeführten Clusteraktionen werden mit Benutzereinwilligung unter der integrierten Kubernetes-Rolle `aks-service` und der integrierten Rollenbindung `aks-service-rolebinding` durchgeführt. Diese Rolle ermöglicht es AKS, Clusterprobleme zu beheben und zu diagnostizieren, damit können jedoch keine Berechtigungen geändert oder Rollen bzw. Rollenbindungen erstellt oder andere Aktionen mit hohen Berechtigungen ausgeführt werden. Der Rollenzugriff ist nur unter aktiven Supporttickets mit Just-in-time-Zugriff (JIT) aktiviert.

In den folgenden Beispielen bietet Microsoft keinen technischen Support:

* Fragen zur Verwendung von Kubernetes. Der Microsoft-Support bietet z. B. keine Empfehlungen zur Erstellung benutzerdefinierter Eingangscontroller, zur Verwendung von Anwendungsworkloads oder zur Anwendung von Open-Source-Softwarepaketen oder -Tools bzw. zu Softwarepaketen oder Tools von Drittanbietern.
  > [!NOTE]
  > Der Microsoft-Support kann Sie hinsichtlich der AKS-Clusterfunktionalität, der Anpassung und der Optimierung beraten (z. B. Kubernetes-Betriebsprobleme und -verfahren).
* Open-Source-Projekte von Drittanbietern, die nicht im Rahmen der Kubernetes-Steuerungsebene bereitgestellt oder mit AKS-Clustern bereitgestellt wurden. Diese Projekte können Istio, Helm, Envoy und andere einbeziehen.
  > [!NOTE]
  > Microsoft kann den bestmöglichen Support für Open-Source-Projekte von Drittanbietern wie Helm bereitstellen. Wenn das Open-Source-Tool eines Drittanbieters mit den vom Kubernetes Azure-Cloudanbieter stammenden oder anderen AKS-spezifischen Fehlern integriert wird, unterstützt Microsoft Beispiele und Anwendungen aus der Microsoft-Dokumentation.
* Closed-Source-Software von Drittanbietern. Diese Software kann z. B. Tools für Sicherheitsscans und Netzwerkgeräte oder -software umfassen.
* Andere als die in der [AKS-Dokumentation](./index.yml) aufgeführten Netzwerkanpassungen.


## <a name="aks-support-coverage-for-agent-nodes"></a>AKS-Supportabdeckung für Agentknoten

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Zuständigkeiten von Microsoft für AKS-Agentknoten

Microsoft und die Benutzer teilen sich die Verantwortung für die Kubernetes-Agentknoten, für die Folgendes gilt:

* Das Basisbetriebssystem-Image verfügt über die erforderlichen Ergänzungen (wie etwa Überwachungs- und Netzwerk-Agents)
* Die Agentknoten erhalten Betriebssystempatches automatisch.
* Probleme bei Komponenten der Kubernetes-Steuerungsebene, die auf den Agentknoten ausgeführt werden, werden automatisch behoben. Zu diesen Komponenten gehören die folgenden:
  * `Kube-proxy`
  * Netzwerktunnel zur Bereitstellung von Kommunikationspfaden zu Kubernetes-Masterkomponenten
  * `Kubelet`
  * Docker oder `containerd`

> [!NOTE]
> Wenn ein Agentknoten nicht funktionstüchtig ist, kann AKS einzelne Komponenten oder den gesamten Agentknoten neu starten. Diese Neustartvorgänge sind automatisiert und ermöglichen eine automatische Wiederherstellung bei häufigen Problemen. Weitere Informationen zu den Mechanismen für die automatische Wiederherstellung finden Sie unter [Automatisches Reparieren von AKS-Knoten](node-auto-repair.md).

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Kundenzuständigkeiten für AKS-Agentknoten

Microsoft stellt wöchentlich Patches und neue Images für Ihre Imageknoten bereit, patcht sie aber standardmäßig nicht automatisch. Damit das Betriebssystem und die Laufzeitkomponenten Ihrer Agentknoten gepatcht bleiben, sollten Sie ein regelmäßiges [Knotenimageupgrade](node-image-upgrade.md) planen oder automatisieren.

AKS veröffentlicht zudem regelmäßig Releases neuer Kubernetes-Patches und -Nebenversionen. Diese Updates können Verbesserungen der Sicherheit oder Funktionalität von Kubernetes enthalten. Sie sind dafür verantwortlich, dass die Kubernetes-Version Ihres Clusters stets aktualisiert wird und der [Richtlinie zur Unterstützung der Kubernetes-Version](supported-kubernetes-versions.md) entspricht.

#### <a name="user-customization-of-agent-nodes"></a>Benutzeranpassung von Agentknoten
> [!NOTE]
> AKS-Agentknoten werden im Azure Portal als reguläre Azure IaaS-Ressourcen angezeigt. Diese virtuellen Computer werden jedoch in einer benutzerdefinierten Azure-Ressourcengruppe bereitgestellt (in der Regel mit dem Präfix MC_\*). Sie können mit den IaaS-APIs- oder -Ressourcen keine Änderungen am Basisbetriebssystemimage oder direkte Anpassungen an diesen Knoten vornehmen. Benutzerdefinierte Änderungen, die nicht über die AKS-API erfolgen, werden nicht beibehalten, wenn Upgrades, Skalierungen, Updates oder Neustarts durchgeführt werden. Nehmen Sie keine Änderungen an den Agentknoten vor, es sei denn, Sie werden vom Microsoft-Support zum Vornehmen von Änderungen angewiesen.

AKS verwaltet den Lebenszyklus und die Vorgänge von Agentknoten für Sie. Das Ändern der IaaS-Ressourcen, die den Agentknoten zugeordnet sind, wird **nicht unterstützt**. Ein Beispiel für einen nicht unterstützten Vorgang ist das Anpassen einer VM-Skalierungsgruppe eines Knotenpools durch manuelles Ändern der Konfigurationen über das VMSS-Portal oder die VMSS-API.
 
Für workloadspezifische Konfigurationen oder Pakete empfiehlt AKS die Verwendung von [Kubernetes`daemon sets`](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

Durch die Verwendung von privilegierten Kubernetes-`daemon sets` und -Startcontainern können Sie Software von Drittanbietern auf Cluster-Agentknoten optimieren, ändern oder installieren. Beispiele für solche Anpassungen sind das Hinzufügen von benutzerdefinierter Software für Sicherheitsscans oder das Aktualisieren von sysctl-Einstellungen.

Dieser Ansatz wird empfohlen, wenn die oben genannten Anforderungen zutreffen. Das AKS-Engineeringteam und der AKS-Support können Sie nicht bei der Diagnose oder Behebung von Problemen unterstützen, durch die der Knoten aufgrund eines bereitgestellten benutzerdefinierten `daemon set` nicht verfügbar ist.

### <a name="security-issues-and-patching"></a>Sicherheitsprobleme und -patches

Wenn ein Sicherheitsproblem in einer oder mehreren verwalteten Komponenten von AKS auftritt, wird das AKS-Team alle betroffenen Cluster patchen, um das Problem zu beheben. Alternativ wird das Team den Benutzern eine Anleitung für ein Upgrade bereitstellen.

Bei Agentknoten, die von einem Sicherheitsrisiko betroffen sind, werden Sie von Microsoft ausführlich über die Auswirkungen und die Schritte zum Beheben oder Mindern des Sicherheitsproblems benachrichtigt (normalerweise durch ein Knotenimageupgrade oder Clusterpatchupgrade).

### <a name="node-maintenance-and-access"></a>Knotenwartung und -zugriff

Obwohl Sie sich bei Agentknoten anmelden und diese ändern können, ist dies nicht empfehlenswert, da Änderungen dazu führen können, dass ein Cluster nicht mehr unterstützt wird.

## <a name="network-ports-access-and-nsgs"></a>Netzwerkports, Zugriff und NSGs

Sie können die NGSs nur für benutzerdefinierte Subnetze anpassen. NSGs können nicht in verwalteten Subnetzen oder auf NIC-Ebene der Agentknoten angepasst werden. In AKS gelten Anforderungen für ausgehenden Datenverkehr an bestimmte Endpunkte, um den ausgehenden Datenverkehr zu steuern und die erforderliche Konnektivität sicherzustellen. Weitere Informationen finden Sie unter [Steuern des ausgehenden Datenverkehrs für Clusterknoten in Azure Kubernetes Service (AKS)](limit-egress-traffic.md). Für eingehenden Datenverkehr basieren die Anforderungen auf den Anwendungen, die Sie im Cluster bereitgestellt haben.

## <a name="stopped-or-de-allocated-clusters"></a>Beendete Cluster oder Cluster mit aufgehobener Zuordnung

Wie oben beschrieben, wird ein Cluster nicht mehr unterstützt, wenn die Zuordnung aller Clusterknoten manuell über die IaaS-APIs, die IaaS-CLI oder das IaaS-Portal aufgehoben wird. Das einzige unterstützte Verfahren zum Beenden aller Knoten/Aufheben der Zuordnung aller Knoten besteht darin, den [AKS-Cluster zu beenden](start-stop-cluster.md#stop-an-aks-cluster), wodurch der Clusterstatus bis zu 12 Monate lang erhalten bleibt.

Der Status von Clustern, die vor mehr als 12 Monaten beendet wurden, bleibt nicht erhalten. 

Die Erhaltung des Status von Clustern, deren Zuordnung außerhalb der AKS-APIs aufgehoben wird, wird nicht garantiert. Die Steuerungsebenen für Cluster in diesem Status werden nach 30 Tagen archiviert und nach 12 Monaten gelöscht.

AKS behält sich vor, Steuerungsebenen zu archivieren, die außerhalb der Supportrichtlinien für verlängerte Zeiträume ab 30 Tagen konfiguriert wurden. AKS verwaltet Sicherungen von etcd-Metadaten der Cluster und kann den Cluster jederzeit neu zuweisen. Diese erneute Zuweisung kann mit jedem PUT-Vorgang initiiert werden, der den Cluster wieder unterstützungsfähig macht, z. B. durch ein Upgrade oder eine Skalierung auf aktive Agent-Knoten.

Wenn Ihr Abonnement gesperrt oder gelöscht wird, werden die Steuerungsebene und der Status des Clusters nach 90 Tagen gelöscht.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nicht unterstützte Kubernetes-Features der Alpha- und Betaversion

AKS bietet nur Support für stabile Features und Betafeatures im Rahmen des Upstream-Kubernetes-Projekts. Sofern nicht anderweitig dokumentiert, unterstützt AKS keine Alphafeatures, die im Upstream-Kubernetes-Projekt verfügbar sind.

## <a name="preview-features-or-feature-flags"></a>Previewfunktionen oder Featureflags

Für Features und Funktionen, die ausführliche Tests und Benutzerfeedback erfordern, veröffentlicht Microsoft neue Previewfunktionen oder Features hinter einem Featureflag. Betrachten Sie diese Features als Features einer Vorab- oder Betaversion.

Previewfunktionen oder Features mit Featureflag sind nicht für die Produktionsumgebung vorgesehen. Kontinuierliche Änderungen an APIs und am Verhalten, Fehlerbehebungen und andere Änderungen können zu instabilen Clustern und Ausfallzeiten führen.

Features in der öffentlichen Vorschauversion fallen unter den „bestmöglichen“ Support, da sich diese Features in der Vorschauversion befinden und nicht für die Produktionsumgebung bestimmt sind und von den technischen AKS-Supportteams nur während der Geschäftszeiten unterstützt werden. Weitere Informationen finden Sie unter

* [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Upstream-Fehler und -Probleme

Angesichts des hohen Tempos bei der Entwicklung im Upstream-Kubernetes-Projekt können immer wieder Fehler auftreten. Einige dieser Fehler können innerhalb des AKS-Systems nicht gepatcht oder umgangen werden. Stattdessen erfordern Fehlerbehebungen größere Patches für Upstream-Projekte (z. B. Kubernetes, Betriebssysteme für Knoten oder Agents sowie Kernel). Bei Komponenten, die sich im Besitz von Microsoft befinden (z. B. der Azure-Cloudanbieter), kümmern sich die AKS-/Azure-Mitarbeiter um die Upstream-Behebung des Problems in der Community.

Sollte ein technisches Supportproblem auf einen oder mehrere Upstream-Fehler zurückzuführen sein, führen die Support- und Entwicklungsteams von AKS folgende Schritte aus:

* Identifizieren und Verknüpfen aller Upstream-Fehler mit unterstützenden Details, die Aufschluss darüber geben, warum sich dadurch Auswirkungen für Ihren Cluster und/oder Ihre Workload ergeben. Kunden erhalten Links zu den erforderlichen Repositorys, damit sie die Probleme beobachten und feststellen können, wann eine neue Version die Fehlerbehebung ermöglicht.
* Bereitstellen möglicher Problemumgehungen oder Gegenmaßnahmen. Wenn das Problem behoben werden kann, wird ein [Bekanntes Problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) im AKS-Repository erfasst. Bei der Erfassung des „Bekannten Problems“ wird Folgendes erläutert:
  * Das Problem, einschließlich Links zu upstream-Fehlern.
  * Die Problemumgehung und Details zu einem Upgrade oder einer anderen Persistenz der Lösung.
  * Grober Zeitplan für die Einbeziehung des Problems auf der Grundlage des Upstream-Releaserhythmus.
