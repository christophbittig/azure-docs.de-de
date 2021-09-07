---
title: Vertrauliche Container in Azure Kubernetes Service (AKS)
description: Erfahren Sie mehr über die Unterstützung von unveränderten Containern in vertraulichen Containern.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 35bf2fc96f93d1eed2b15c4ea87aa1f2a3aaa0a5
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113002886"
---
# <a name="confidential-containers"></a>Vertrauliche Container

## <a name="overview"></a>Übersicht

Ermöglichen Sie es Entwicklern, eine **vorhandene Docker-Anwendung (neu oder vorhanden)** mitzubringen und dank der Unterstützung von Confidential Computing-Knoten sicher in Azure Kubernetes Service (AKS) auszuführen.

Vertrauliche Container schützen Folgendes:

- Datenintegrität 
- Datenvertraulichkeit
- Codeintegrität
- Schutz des Containercodes durch Verschlüsselung
- Hardwarebasierte Sicherheit
- Ausführung von vorhandenen Apps
- Erstellen von Vertrauensanker in Hardware
- Entfernen von Hostadministrator, Kubernetes-Administrator, Hypervisor von der Vertrauensstellungsgrenze

Eine hardwarebasierte vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE) ist eine wichtige Komponente, die dank hardware- und softwarebasierter Messungen über TCB-Komponenten (Trusted Computing Base) umfassende Schutzmaßnahmen bietet. Die Überprüfung dieser Messungen hilft bei der Validierung des erwarteten Berechnungsergebnisses. Zudem wird damit kontrolliert, ob die Container-Apps manipuliert wurden.

Vertrauliche Container unterstützen benutzerdefinierte Anwendungen, die mit **Python, Java, Node JS usw. oder mit gepackten Containeranwendungen wie NGINX, Redis Cache, MemCache usw.** entwickelt wurden, um in AKS mit Confidential Computing-Knoten unverändert ausgeführt zu werden.

Vertrauliche Container sind der schnellste Weg zur Containervertraulichkeit und erfordern lediglich das erneute Packen der vorhandenen Docker-Containeranwendungen und keine Änderungen am Anwendungscode. Vertrauliche Container sind Docker-Containeranwendungen, die zum Ausführen in einer TEE gepackt sind. Einige Grundvoraussetzungen für vertrauliche Container bieten zudem Containerverschlüsselung, womit der Containercode während des Speichers und des Transports und während der Bereitstellung auf dem Host geschützt werden kann. Die Containerverschlüsselung bietet Ihnen noch weitere Möglichkeiten, um den im Container gepackten Code bzw. das gepackte Modell mit dem an die TEE angefügten Entschlüsselungsschlüssel zu schützen.

Nachfolgend ist der Prozess für vertrauliche Container von der Entwicklung bis zur Bereitstellung dargestellt. ![Prozess für vertrauliche Container](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Grundvoraussetzungen für vertrauliche Container
Um einen vorhandenen Docker-Container unverändert auszuführen, ist eine SGX-Software erforderlich, damit die Anwendungsaufrufe den speziellen CPU-Anweisungssatz verwenden können, der zum Verringern der Angriffsfläche verfügbar gemacht wird und nicht vom Gastbetriebssystem abhängig ist. Nach dem Einbinden in eine SGX-Laufzeitsoftware werden die Container automatisch in den geschützten Enklaven gestartet, wodurch das Gastbetriebssystem, das Hostbetriebssystem oder der Hypervisor von der Vertrauensstellungsgrenze entfernt werden. Diese isolierte Ausführung in einem Knoten (virtueller Computer) mit hardwaregestützter In-Memory-Datenverschlüsselung verringert die Angriffsflächen und Sicherheitsrisiken bei Betriebssystem- oder Hypervisorebenen insgesamt.

Vertrauliche Container werden in AKS vollständig unterstützt und durch Azure-Partner und OSS-Projekte (Open-Source-Software) ermöglicht. Entwickler können Softwareanbieter je nach Features, je nach Diensten zur Integration in Azure und je nach Toolunterstützung auswählen.

## <a name="partner-enablers"></a>Grundvoraussetzungen für Partner
> [!NOTE]
> Die folgenden Lösungen werden über Azure-Partner angeboten. Für diese Lösungen können Lizenzierungsgebühren anfallen. Prüfen Sie daher die Bestimmungen für jede einzelne Partnersoftware. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) stellt SGX-Plattformsoftware bereit, mit der Container unverändert in AKS ausgeführt werden können. [Hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) erfahren Sie mehr über die Funktionen und können sich die Beispielanwendungen ansehen.

Beginnen Sie mit einer Redis Cache-Beispielanwendung und einer benutzerdefinierten Python-Anwendung [hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

![Anjuna-Prozess](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) bietet Entwicklern die Möglichkeit, ihre containerisierten Anwendungen in einem Portal oder in einer CLI-basierten Benutzeroberfläche zu nutzen und in SGX-fähige vertrauliche Container zu konvertieren, ohne sie ändern oder neu kompilieren zu müssen. Dadurch ist es möglich, mit Fortanix eine große Vielzahl unterschiedlichster Anwendungen flexibel auszuführen und zu verwalten. Das schließt auch bereits vorhandene Anwendungen, neue Enclave-native Anwendungen sowie vorab gepackte Anwendungen ein. Benutzer können über die Benutzeroberfläche von [Confidential Computing Manager](https://em.fortanix.com/) oder mit [REST-APIs](https://www.fortanix.com/api/em/) vertrauliche Container anhand der [Kurzanleitung](https://fortanix.com/blog/2020/10/fortanix-confidential-containers-on-microsoft-azure-kubernetes-service-aks/) für Azure Kubernetes Service erstellen.

![Bereitstellungsprozess mit Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[SCONE](https://scontain.com/index.html?lang=en) unterstützt Sicherheitsrichtlinien, die Zertifikate, Schlüssel und Geheimnisse generieren können, und stellt sicher, dass diese nur für bestätigte Dienste einer Anwendung sichtbar sind. So bestätigen sich die Dienste einer Anwendung automatisch gegenseitig über TLS, ohne dass die Anwendung oder TLS geändert werden muss. Dies wird anhand einer einfachen Flask-Anwendung hier erläutert: https://sconedocs.github.io/flask_demo/  

SCONE kann die meisten vorhandenen Binärdateien in Anwendungen konvertieren, die innerhalb von Enclaves ausgeführt werden, ohne dass die Anwendung geändert oder neu kompiliert werden muss. Darüber hinaus schützt SCONE interpretierte Sprachen wie Python, da sowohl Datendateien als auch Python-Codedateien **verschlüsselt** werden. Mithilfe einer SCONE-Sicherheitsrichtlinie können die verschlüsselten Datei vor unbefugten Zugriffen, Änderungen und Rollbacks geschützt werden. Wie eine vorhandene Python-Anwendung „sconifiziert“ wird, wird [hier](https://sconedocs.github.io/sconify_image/) erläutert.

![Scontain-Workflow](./media/confidential-containers/scone-workflow.png)

Scone-Bereitstellungen auf Confidential Computing-Knoten mit AKS werden umfassend unterstützt und sind in andere Azure-Dienste integriert. Beginnen Sie mit einer Beispielanwendung hier https://sconedocs.github.io/aks/.


## <a name="oss-enablers"></a>Grundvoraussetzungen für OSS 
> [!NOTE]
> Die folgenden Lösungen werden über Open-Source-Projekte bereitgestellt und sind nicht direkt mit Azure Confidential Computing (ACC) oder Microsoft verbunden.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) ist ein einfaches Gastbetriebssystem zur Ausführung einer einzelnen Linux-Anwendung mit minimalen Hostanforderungen. Mit Graphene können Anwendungen in einer isolierten Umgebung mit Vorteilen ausgeführt werden, die mit denen einer Ausführung in einem vollständigen Betriebssystem vergleichbar sind. Zudem bietet diese Lösung eine umfassende Toolunterstützung für die Konvertierung vorhandener Docker-Containeranwendungen in Graphene Shielded Containers (GSC).

Beginnen Sie [hier](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) mit einer Beispielanwendung und -bereitstellung in AKS.

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) ist ein arbeitsspeichersicheres Multiprozess-Bibliotheksbetriebssystem (LibOS) für Intel SGX. Damit können ältere Anwendungen mit nur geringfügigen oder ganz ohne Änderungen am Quellcode unter SGX ausgeführt werden. Occlum schützt die Vertraulichkeit von Benutzerworkloads auf transparente Weise und erlaubt gleichzeitig eine einfache Migration per Lift & Shift zu vorhandenen Dockeranwendungen.

Occlum unterstützt AKS-Bereitstellungen. Befolgen Sie [hier](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md) die Anweisungen zur Bereitstellung anhand verschiedener Beispielanwendungen.

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) ist ein Orchestrierungsframework für vertrauliche Container. Es erleichtert die Ausführung und Skalierung vertraulicher Dienste auf SGX-fähigem Kubernetes. Marblerun übernimmt Standardaufgaben wie das Überprüfen der Dienste in Ihrem Cluster, Verwalten der zugehörigen Geheimnisse und Herstellen von Enclave-to-Enclave-mTLS-Verbindungen zwischen ihnen. Marblerun stellt außerdem sicher, dass Ihr Cluster vertraulicher Container einem Manifest entspricht, das in einfachem JSON-Code definiert ist. Das Manifest kann von externen Clients per Remotenachweis überprüft werden. 

![Marblerun-Flow](./media/confidential-containers/marblerun-workflow.png)

Kurz gesagt, erweitert Marblerun die Vertraulichkeits-, Integritäts- und Überprüfbarkeitseigenschaften einer einzelnen Enclave auf einen Kubernetes-Cluster. 

Marblerun unterstützt vertrauliche Container, die mit Graphene, Occlum und EGo erstellt wurden. Beispiele für jedes SDK finden Sie [hier](https://www.marblerun.sh/docs/examples/). Marblerun wird neben Ihren vorhandenen cloudnativen Tools auf Kubernetes ausgeführt. Es verfügt über eine benutzerfreundliche Befehlszeilenschnittstelle (Command Line Interface, CLI) sowie Helm-Diagramme und bietet erstklassige Unterstützung für Confidential Computing-Knoten in AKS. Informationen zum Bereitstellen von Marblerun in AKS finden Sie [hier](https://www.marblerun.sh/docs/deployment/cloud/).

## <a name="confidential-containers-demo"></a>Demo zu vertraulichen Containern
Sehen Sie sich die Demo zu vertraulichen Gesundheitsdaten in vertraulichen Containern an. Das Beispiel ist [hier](/azure/architecture/example-scenario/confidential/healthcare-inference) verfügbar. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Setzen Sie sich mit uns in Verbindung

Haben Sie Fragen zu ihrer Implementierung, oder möchten Sie Enabler werden? Senden Sie eine E-Mail an das Produktteam **acconaks@microsoft.com** .

## <a name="reference-links"></a>Referenzlinks

[Microsoft Azure Attestation](../attestation/overview.md)

[Virtuelle DCsv2-Computer](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
