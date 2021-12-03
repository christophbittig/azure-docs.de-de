---
title: Confidential Computing-Knoten in Azure Kubernetes Service (AKS)
description: Confidential Computing-Knoten in AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/04/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a0a6452b978b6924574f1499916a0781413a24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290227"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Confidential Computing-Knoten in Azure Kubernetes Service

Mit [Azure Confidential Computing](overview.md) können Sie Ihre vertraulichen Daten während der Nutzung schützen. Die zugrunde liegende Confidential Computing-Infrastruktur schützt diese Daten mit einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung für Container vor anderen Anwendungen, Administratoren und Cloudanbietern. Das Hinzufügen von Confidential Computing-Knoten ermöglicht es Ihnen, eine Containeranwendung für die Ausführung in einer isolierten, per Hardware geschützten und attestierbaren Umgebung auszuwählen.

## <a name="overview"></a>Übersicht

Azure Kubernetes Service (AKS) unterstützt das Hinzufügen von [DCsv2 Confidential Computing-Knoten](confidential-computing-enclaves.md) auf der Grundlage von Intel SGX. Auf diesen Knoten können Sie vertrauliche Workloads in einer hardwarebasierten vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE) ausführen. In diesen Umgebungen können mit Code auf Benutzerebene aus Containern private Arbeitsspeicherregionen zugeordnet werden, um den Code direkt per CPU auszuführen. Diese privaten Arbeitsspeicherregionen, die direkt per CPU ausgeführt werden, werden als „Enclaves“ (Enklaven) bezeichnet. Enclaves tragen zum Schutz der Datenvertraulichkeit, Datenintegrität und Codeintegrität vor der Beeinträchtigung durch andere Prozesse bei, die auf denselben Knoten ausgeführt werden. Mit dem Intel SGX-Ausführungsmodell werden auch die Zwischenschichten des Gastbetriebssystems, Hostbetriebssystems und von Hypervisor entfernt, um die Angriffsfläche zu verkleinern. Das Modell für die *hardwarebasierte Ausführung mit containerspezifischer Isolierung* auf einem Knoten ermöglicht Anwendungen die direkte Ausführung mit der CPU, während der spezielle Arbeitsspeicherblock pro Container verschlüsselt bleibt. Confidential Computing-Knoten mit vertraulichen Containern sind ein wertvoller Beitrag zu Ihrer Zero-Trust-Sicherheitsplanung und Strategie für die tiefgehende Verteidigung von Containern.

:::image type="content" source="./media/confidential-nodes-aks-overview/sgx-aks-node.png" alt-text="Grafik: AKS Confidential Compute-Knoten mit vertraulichen Containern und darin gesichertem Code bzw. darin gesicherten Daten":::

## <a name="aks-confidential-nodes-features"></a>Features von vertraulichen AKS-Knoten

- Hardwarebasierte Containerisolation auf Prozessebene über eine vertrauenswürdige Intel SGX-Ausführungsumgebung (Trusted Execution Environment, TEE) 
- Heterogene Knotenpoolcluster (Mischung aus vertraulichen und nicht vertraulichen Knotenpools)
- Arbeitsspeicherbasierte Podplanung mit verschlüsseltem Seitencache (Encrypted Page Cache, EPC) (Add-On erforderlich)
- Intel SGX DCAP-Treiber vorinstalliert
- Auf CPU-Auslastung basierende horizontale automatische Podskalierung und automatische Clusterskalierung
- Unterstützung für Linux-Container über Ubuntu 18.04 Gen2-VM-Workerknoten

## <a name="confidential-computing-add-on-for-aks"></a>Confidential Computing-Add-On für AKS
Das Add-On-Feature ermöglicht die Nutzung von zusätzlichen Funktionen in AKS, wenn im Cluster Pools mit Confidential Computing-Knoten ausgeführt werden. Mit diesem Add-On können die unten aufgeführten Features genutzt werden.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Azure-Geräte-Plug-In für Intel SGX <a id="sgx-plugin"></a>

Mit dem Geräte-Plug-In wird die Kubernetes-Geräte-Plug-In-Schnittstelle für Arbeitsspeicher vom Typ „Verschlüsselter Seitencache“ (Encrypted Page Cache, EPC) implementiert, und die Gerätetreiber werden über die Knoten verfügbar gemacht. Mit diesem Plug-In wird der EPC-Arbeitsspeicher in Kubernetes quasi zu einem zusätzlichen Ressourcentyp. Benutzer können Grenzwerte für diese Ressource genauso wie für andere Ressourcen angeben. Neben der Planungsfunktion ermöglicht das Geräte-Plug-In auch die Zuweisung von Intel SGX-Gerätetreiberberechtigungen für vertrauliche Workloadcontainer. Mit diesem Plug-In können Entwickler die Einbindung der Intel SGX-Treibervolumes in die Bereitstellungsdateien vermeiden. Eine Beispielimplementierung der arbeitsspeicherbasierten EPC-Bereitstellung (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) finden Sie [hier](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml).


## <a name="programming-models"></a>Programmmodelle

### <a name="confidential-containers"></a>Vertrauliche Container

Mit [vertraulichen Containern](confidential-containers.md) können Sie vorhandene unveränderte Containeranwendungen der meisten **gängigen Runtimes von Programmiersprachen** (Python, Node, Java usw.) auf vertrauliche Weise ausführen. Für dieses Paketerstellungsmodell müssen keine Änderungen des Quellcodes oder erneuten Kompilierungen durchgeführt werden. Dies ist der schnellste Weg zur Sicherstellung der Vertraulichkeit. Sie binden hierbei Ihre Docker-Standardcontainer in Pakete mit Open-Source-Projekten oder Azure-Partnerlösungen ein. Bei diesem Paketerstellungs- und Ausführungsmodell werden alle Teile der Containeranwendung in den vertrauenswürdigen Bereich (Enclave) geladen. Dieses Modell funktioniert gut für fertige Containeranwendungen, die auf dem Markt erhältlich sind, oder für benutzerdefinierte Apps, die derzeit auf universellen Knoten ausgeführt werden.

### <a name="enclave-aware-containers"></a>Für Enklaven geeignete Container
Für Confidential Computing-Knoten unter AKS werden auch Container unterstützt, die für die Ausführung in einer Enclave programmiert sind, um **spezielle Anweisungen** der CPU zu nutzen. Dieses Programmiermodell ermöglicht eine stärkere Kontrolle Ihres Ausführungsablaufs und erfordert die Verwendung von speziellen SDKs und Frameworks. Das Programmiermodell ermöglicht bei einer geringstmöglichen Basis für vertrauenswürdiges Computing (Trusted Computing Base, TCB) die beste Kontrolle über Ihre Anwendungsabläufe. Bei der Entwicklung von Enclave-fähigen Containern werden nicht vertrauenswürdige und vertrauenswürdige Teile einer Containeranwendung genutzt, sodass Sie den regulären Arbeitsspeicher und den EPC-Arbeitsspeicher (Encrypted Page Cache) verwalten können, in dem die Enclave ausgeführt wird. [Erfahren Sie mehr](enclave-aware-containers.md) zu Containern, die für Enklaven geeignet sind.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen eines AKS-Clusters mit Confidential Computing-Knoten](./confidential-enclave-nodes-aks-get-started.md)

[Schnellstart: Beispiele für vertrauliche Container](https://github.com/Azure-Samples/confidential-container-samples)

[Intel SGX vertrauliche VM - DCsv2 SKU Liste](../virtual-machines/dcv2-series.md)

[Intel SGX vertrauliche VM - DCsv3 SKU Liste](../virtual-machines/dcv3-series.md)

[Tiefgehende Verteidigung mit vertraulichen Containern: Webinarsitzung](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions-sgx.md
