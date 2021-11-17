---
title: Azure Confidential Computing-Produkte
description: Erfahren Sie mehr über alle von Azure bereitgestellten Confidential Computing-Dienste.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: overview
ms.date: 11/04/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77cd51a87ba1f7ba7f17ed69c102a4020d7bae2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284396"
---
# <a name="confidential-computing-on-azure"></a>Confidential Computing unter Azure

Heutzutage verschlüsseln Kunden ihre ruhenden und in Übertragung begriffenen Daten. Dies gilt jedoch nicht, während die Daten im Arbeitsspeicher verwendet werden. Das von Microsoft mitbegründete [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC) definiert Confidential Computing als Schutz von in Verwendung begriffenen Daten anhand von [hardwarebasierten vertrauenswürdigen Ausführungsumgebungen](https://en.wikipedia.org/wiki/Trusted_execution_environment) (Trusted Execution Environments, TEEs). Diese TEEs verhindern den unbefugten Zugriff oder die Änderung von Anwendungen und Daten, während diese verwendet werden. So lässt sich die Sicherheitsstufe von Organisationen erhöhen, die sensible und regulierte Daten verwalten. Die TEEs sind eine vertrauenswürdige Umgebung, die ein Maß an Sicherheit für Datenintegrität, Datenvertraulichkeit und Codeintegrität bietet. Das Confidential Computing-Bedrohungsmodell zielt darauf ab, den Zugriff auf Code und Daten während der Ausführung für Cloudanbieter und andere Akteure in der Domäne des Mandanten zu verhindern oder zu reduzieren.

Technologien wie [Intel Software Guard Extensions](https://www.intel.com.au/content/www/au/en/architecture-and-technology/software-guard-extensions-enhanced-data-protection.html) (Intel SGX) oder [AMD Secure Encrypted Virtualization](https://www.amd.com/en/processors/amd-secure-encrypted-virtualization) (SEV-SNP) sind aktuelle CPU-Verbesserungen, die Confidential Computing-Implementierungen unterstützen. Diese Technologien sind als Virtualisierungserweiterungen konzipiert und stellen Featuresätze für die Entwicklung des Confidential Computing-Bedrohungsmodells bereit, darunter Speicherverschlüsselung und -integrität, Vertraulichkeit und Integrität des CPU-Zustands sowie Nachweisfunktionen.

:::image type="content" source="media/overview-azure-products/three-states.png" alt-text="Grafische Darstellung der drei Zustände des Datenschutzes mit Hervorhebung der zurzeit verwendeten Confidential Computing-Daten.":::

Bei Verwendung mit der Verschlüsselung ruhender und in der Übertragung begriffener Daten bewältigt Confidential Computing die einzige größere Verschlüsselungsherausforderung – die Verschlüsselung während der Verwendung – durch den Schutz sensibler oder stark regulierter Datasets und Anwendungsworkloads in einer sicheren öffentlichen Cloudplattform. Confidential Computing geht über den generischen Schutz von Daten hinaus. TEEs werden auch verwendet, um proprietäre Geschäftslogik, Analysefunktionen, Machine Learning-Algorithmen oder ganze Anwendungen zu schützen.

## <a name="navigating-azure-confidential-computing"></a>Übersicht über Azure Confidential Computing

Die [Microsoft-Angebote](https://aka.ms/azurecc) für Confidential Computing reichen von IaaS (Infrastructure-as-a-Service) bis hin zu PaaS (Platform as a Service) und Entwicklertools, um Ihre Journey beim Erzielen von Vertraulichkeit für Daten und Code in der Cloud zu unterstützen.

## <a name="reducing-the-attack-surface"></a>Verkleinern der Angriffsfläche
Die vertrauenswürdige Rechenbasis (Trusted Computing Base, TCB) bezieht sich auf alle Hardware-, Firmware- und Softwarekomponenten eines Systems, die eine sichere Umgebung bereitstellen. Die Komponenten innerhalb der TCB werden als kritisch eingestuft. Ist eine einzelne Komponente innerhalb der TCB kompromittiert, kann die Sicherheit des gesamten Systems gefährdet sein. Eine kleinere TCB bedeutet höhere Sicherheit. Es besteht eine geringere Gefahr durch verschiedene Sicherheitsrisiken, Schadsoftware, Angriffe und böswillige Personen. Das Ziel von Azure Confidential Computing besteht darin, die TCB für Ihre Cloudworkloads durch TEEs zu verkleinern. 

### <a name="reducing-your-tcb-in-azure"></a>Reduzieren Ihrer TCB in Azure

Wenn Sie vertrauliche Azure-VMs bereitstellen, können Sie Ihre TCB reduzieren. Für vertrauliche VM-Bereitstellungslösungen, die unter AMD SEV-SNP ausgeführt werden, können Sie vorhandene Workloads per Lift & Shift migrieren und Daten vor dem Cloudbetreiber durch Vertraulichkeit auf VM-Ebene schützen. Vertrauliche VMs mit Intel SGX-Anwendungsenclaves bieten Codezeilensteuerung in Anwendungen, um Ihre TCB zu minimieren und Daten sowohl vor Cloudbetreibern als auch vor Ihren Operatoren zu schützen.  Für Anwendungsenclaves mit Intel SGX sind möglicherweise einige Änderungen an Konfigurationsrichtlinien oder Anwendungscode erforderlich.  Sie können auch einen ISV-Partner (Independent Software Vendor) oder Open-Source-Software (OSS) nutzen, um Ihre vorhandenen Apps in einer Anwendungsenclave ausführen zu können. 

### <a name="trust-ladder"></a>Vertrauensleiter

Azure stellt verschiedene VMs für Confidential Computing-IaaS-Workloads bereit, und Kunden können je nach bevorzugtem Sicherheitsstatus diejenige auswählen, die für sie am besten geeignet ist. Die Abbildung der „Vertrauensleiter“ zeigt, was Kunden aus der Sicht des Sicherheitsstatus von diesen IaaS-Angeboten erwarten dürfen.

![Screenshot: Azure-Vertrauensleiter mit Intel SGX-Enclaves am oberen Ende](media/overview-azure-products/trust-ladder.png)

## <a name="azure-offerings"></a>Azure-Angebote

Folgende Dienste sind derzeit allgemein für die Öffentlichkeit verfügbar:

- [Vertrauliche VMs mit Intel SGX-Anwendungsenclaves](confidential-computing-enclaves.md). Azure bietet die Serien [DCsv2](../virtual-machines/dcv2-series.md), [DCsv3 und DCdsv3](../virtual-machines/dcv3-series.md), die auf der Intel SGX-Technologie basieren, für die Erstellung hardwarebasierter Enclaves an. Sie können sichere Enclave-basierte Anwendungen entwickeln, die auf einer VM-Serie ausgeführt werden, um Ihre Anwendungsdaten und den verwendeten Code zu schützen.
- [Enclave-fähige Container](enclave-aware-containers.md), die in Azure Kubernetes Service (AKS) ausgeführt werden. Confidential Computing-Knoten in AKS verwenden Intel SGX zum Erstellen isolierter Enclave-Umgebungen in den Knoten zwischen den einzelnen Containeranwendungen.
- [Always Encrypted mit Secure Enclaves in Azure SQL](/sql/relational-databases/security/encryption/always-encrypted-enclaves). Die Vertraulichkeit sensibler Daten wird vor Schadsoftware und nicht autorisierten Benutzern mit hohen Berechtigungen geschützt, indem SQL-Abfragen direkt innerhalb einer TEE ausgeführt werden, wenn die SQL-Anweisung Vorgänge für verschlüsselte Daten enthält, die die Verwendung der Secure Enclave erfordern, auf der die Datenbank-Engine ausgeführt wird.
- [Microsoft Azure Attestation](../attestation/overview.md). Hierbei handelt es sich um einen Remotenachweisdienst zum Überprüfen der Vertrauenswürdigkeit mehrerer TEEs (Trusted Execution Environments) und zum Verifizieren der Integrität der binären Dateien, die in den TEEs ausgeführt werden.
- [Verwaltetes HSM in Azure Key Vault](/azure/key-vault/managed-hsm/). Hierbei handelt es sich um einen vollständig verwalteten, hochverfügbaren und standardkonformer Einzelmandanten-Clouddienst. Dieser Dienst ermöglicht es Ihnen, kryptografische Schlüssel für Ihre Cloudanwendungen über Hardwaresicherheitsmodule (HSMs) zu schützen, die mit FIPS 140-2 Level 3 überprüft wurden.
- [Azure IoT Edge](../iot-edge/deploy-confidential-applications.md) unterstützt vertrauliche Anwendungen, die innerhalb sicherer Enclaves auf einem IoT-Gerät (Internet of Things, Internet der Dinge) ausgeführt werden. IoT-Geräte sind häufig Manipulations- und Fälschungsversuchen ausgesetzt, weil sie physisch für böswillige Akteure zugänglich sind. Vertrauliche IoT Edge-Geräte sorgen im Edgebereich für Vertrauenswürdigkeit und Integrität, indem sie den Zugriff auf Daten schützen, die von dem Gerät selbst erfasst und gespeichert werden, bevor sie in die Cloud gestreamt werden.

Weitere Dienste befinden sich derzeit in der Vorschau:

- Vertrauliche VMs, die auf [AMD SEV-SNP-Technologie](https://azure.microsoft.com/blog/azure-and-amd-enable-lift-and-shift-confidential-computing/) basieren, befinden sich derzeit in der Vorschau und stehen ausgewählten Kunden zur Verfügung.
- Der [vertrauenswürdige Start](../virtual-machines/trusted-launch.md) ist auf allen VMs der Generation 2 verfügbar und bietet verstärkte Sicherheitsfeatures wie den sicheren Start, ein virtuelles vertrauenswürdiges Plattformmodul und die Überwachung der Startintegrität, um Schutz vor Bootkits, Rootkits und Schadsoftware auf Kernelebene zu gewährleisten.
- [Azure Confidential Ledger](../confidential-ledger/overview.md). ACL ist ein manipulationssicheres Register zum Speichern vertraulicher Daten für die Aufzeichnung und Überwachung oder für die Datentransparenz in Szenarien mit mehreren Parteien. Es bietet WORM-Garantien (Write Once, Read Many), die Daten als nicht löschbar und nicht änderbar festlegen. Der Dienst basiert auf dem [Confidential Consortium Framework](https://www.microsoft.com/research/project/confidential-consortium-framework/) von Microsoft Research.
- [Confidential Inference ONNX Runtime ](https://github.com/microsoft/onnx-server-openenclave). Hierbei handelt es sich um einen ML-Rückschlussserver (Machine Learning), der den ML-Host daran hindert, sowohl auf die Rückschlussanforderung als auch auf die entsprechende Antwort zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über die Entwicklung von Anwendungsenclaves](application-development.md).
