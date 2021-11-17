---
title: Auswählen zwischen Bereitstellungsmodellen
description: Auswählen zwischen Bereitstellungsmodellen
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8ed7f4b88e38581bea6eaf1a19ce945858031f1d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347687"
---
# <a name="confidential-computing-deployment-models"></a>Confidential Computing-Bereitstellungsmodelle

Für Azure Confidential Computing werden mehrere Bereitstellungsmodelle unterstützt. Mit diesen unterschiedlichen Modellen werden die vielen verschiedenen Sicherheitsanforderungen von Kunden beim modernen Cloud Computing unterstützt.

## <a name="infrastructure-as-a-service-iaas"></a>Infrastructure-as-a-Service (IaaS)

Für Infrastructure-as-a-Service (IaaS) können Sie beim Confidential Computing vertrauliche virtuelle Computer (VMs) verwenden. Sie können VMs nutzen, die auf [SGX-Anwendungsenclaves (Software Guard Extensions) von Intel](confidential-computing-enclaves.md) oder SEV-SNP-Technologie von AMD basieren.

## <a name="platform-as-a-service-paas"></a>Platform-as-a-Service (PaaS)

Für Platform-as-a-Service (PaaS) können Sie beim Confidential Computing [vertrauliche Container](confidential-containers.md) verwenden. Dieses Angebot enthält Enclave-fähige Container in Azure Kubernetes Service (AKS).

Die Auswahl des richtigen Bereitstellungsmodells hängt von vielen Faktoren ab. Möglicherweise müssen Sie vorhandene Legacy-Anwendungen, Betriebssystemfunktionen und die Migration aus lokalen Netzwerken berücksichtigen.

Es gibt zwar noch viele Gründe für die Nutzung von VMs, aber Container bieten zusätzliche Flexibilität für die vielen Softwareumgebungen der modernen IT. 

Container können Apps unterstützen, für die Folgendes gilt:

- Ausführung in mehreren Clouds
- Verbindungsherstellung mit Microservices
- Verwendung verschiedener Programmiersprachen und Frameworks
- Verwendung der Automatisierung und von Azure Pipelines, einschließlich CI/CD-Implementierung (Continuous Integration und Continuous Deployment)

Mit Containern wird auch die Portabilität von Anwendungen und die Ressourcenauslastung verbessert, indem die Elastizität der Azure-Cloud genutzt wird.

Sie stellen Ihre Lösung normalerweise auf vertraulichen virtuellen Computern bereit, wenn Folgendes gilt:

- Sie verfügen über Legacy-Anwendungen, die nicht geändert oder containerisiert werden können. Sie benötigen aber trotzdem den Schutz von Daten im Arbeitsspeicher während der Verarbeitung.
- Sie führen mehrere Anwendungen aus, für die die Nutzung unterschiedlicher Betriebssysteme in einer Infrastruktur erforderlich ist.
- Sie möchten eine gesamte Computingumgebung emulieren, einschließlich aller Betriebssystemressourcen.
- Sie migrieren Ihre vorhandenen virtuellen Computer aus der lokalen Umgebung zu Azure.

Ein Ansatz mit vertraulichen Containern ist ggf. in den folgenden Fällen geeignet:

- Faktoren wie Kosten und Ressourcenzuordnung sind für Sie wichtig. Sie benötigen aber eine flexiblere Plattform für die Bereitstellung Ihrer proprietären Apps und Datasets.
- Sie erstellen eine moderne cloudnative Lösung. Darüber hinaus haben Sie die vollständige Kontrolle über den Quellcode und den Bereitstellungsprozess.
- Sie benötigen Unterstützung für mehrere Clouds.

Bei beiden Optionen wird jeweils die höchste Sicherheitsstufe für Azure-Dienste erzielt. Es gibt einige Unterschiede bei den Sicherheitsstatus von [vertraulichen VMs](#confidential-vms-on-amd-sev-snp) und [vertraulichen Containern](#secure-enclaves-on-intel-sgx). Diese werden im Folgenden beschrieben.

### <a name="confidential-vms-on-amd-sev-snp"></a>Vertrauliche VMs unter AMD SEV-SNP

**Vertrauliche VMs unter AMD SEV-SNP** ermöglichen per Hardwareverschlüsselung den Schutz des gesamten virtuellen Computers vor unberechtigtem Zugriff durch den Hostadministrator. Diese Ebene enthält in der Regel den Hypervisor, der vom Clouddienstanbieter (Cloud Service Provider, CSP) verwaltet wird. Sie können diese Art von vertraulicher VM verwenden, um zu verhindern, dass der CSP auf die Daten und den auf dem virtuellen Computer ausgeführten Code zugreift.

Für VM-Administratoren oder andere Apps oder Dienste, die innerhalb der Grenzen des virtuellen Computers ausgeführt werden, gelten die Schutzbereiche nicht. Diese Benutzer und Dienste können innerhalb des virtuellen Computers auf Daten und Code zugreifen.

Die AMD SEV-SNP-Technologie ermöglicht die Isolation der VM vom Hypervisor. Der hardwarebasierte Schutz der Speicherintegrität trägt dazu bei, schädliche Hypervisor-basierte Angriffe zu verhindern. Beim SEV-SNP-Modell werden der AMD Secure Processor und der virtuelle Computer als vertrauenswürdig angesehen. Andere Hardware- und Softwarekomponenten werden bei diesem Modell nicht als vertrauenswürdig eingestuft. Zu den nicht vertrauenswürdigen Komponenten gehören auch das BIOS und der Hypervisor auf dem Hostsystem.

:::image type="content" source="media/confidential-computing-deployment-models/amd-sev-snp-vm.png" alt-text="Diagramm: AMD SEV-SNP-Architektur für VM mit vertrauenswürdigen und nicht vertrauenswürdigen Komponenten":::

### <a name="secure-enclaves-on-intel-sgx"></a>Secure Enclaves unter Intel SGX

Bei Verwendung von **Secure Enclaves unter Intel SGX** werden Speicherbereiche innerhalb eines virtuellen Computers per hardwarebasierter Verschlüsselung geschützt. Die Sicherheitsgrenze von Anwendungsenclaves ist restriktiver als bei vertraulichen VMs unter AMD SEV-SNP. Bei Intel SGX gilt die Sicherheitsgrenze für Teile des Speichers innerhalb eines virtuellen Computers. Benutzer, Apps und Dienste, die auf der VM mit Intel SGX ausgeführt werden, können nicht auf die entsprechenden Daten und den Code zugreifen, der innerhalb der Enclave ausgeführt wird.

Bei Intel SGX werden die verwendeten Daten per Anwendungsisolation geschützt. Indem bestimmter Code und bestimmte Daten vor Änderungen geschützt werden, können Entwickler ihre Anwendung in gehärtete Enclaves oder vertrauenswürdige Ausführungsmodule partitionieren, um die Anwendungssicherheit zu erhöhen. Entitäten außerhalb der Enclave können unabhängig von ihren Berechtigungsstufen keine Lese- und Schreibvorgänge für den Enclavespeicher durchführen. Der Hypervisor oder das Betriebssystem können diesen Zugriff über einfache Aufrufe auf Betriebssystemebene ebenfalls nicht erhalten. Um eine Enclavefunktion aufzurufen, müssen Sie für die Intel SGX-CPUs einen neuen Satz mit Anweisungen verwenden. Dieser Prozess umfasst mehrere Schutzüberprüfungen.

:::image type="content" source="media/confidential-computing-deployment-models/intel-sgx-enclave.png" alt-text="Diagramm: Intel SGX-Architektur mit Enclaves und Anzeige sicherer Informationen innerhalb der App-Enclave":::

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu vertraulichen Containern](confidential-containers.md)
- [Informationen zu Confidential Computing-Enclaves](confidential-computing-enclaves.md)
