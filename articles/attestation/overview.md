---
title: Azure Attestation – Übersicht
description: Eine Übersicht über Microsoft Azure Attestation, eine Lösung für den Nachweis von Trusted Execution Environments (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: fe025fad4955095ef16b546b7d326d80b4aea15c
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112427575"
---
# <a name="microsoft-azure-attestation"></a>Microsoft Azure Attestation 

Microsoft Azure Attestation ist eine einheitliche Lösung zur Remoteüberprüfung der Vertrauenswürdigkeit einer Plattform und der Integrität der darin ausgeführten Binärdateien. Der Dienst unterstützt den Nachweis der TPM-basierten (Trusted Platform Module) Plattformen sowie den Nachweis des Zustands vertrauenswürdiger Ausführungsumgebungen (Trusted Execution Environments, TEEs), wozu beispielsweise SGX-Enclaves ([Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html)) und VBS-Enclaves ([virtualisierungsbasierte Sicherheit](/windows-hardware/design/device-experiences/oem-vbs)) zählen. 

Der Nachweis ist ein Prozess, um zu veranschaulichen, dass Softwarebinärdateien auf einer vertrauenswürdigen Plattform ordnungsgemäß instanziiert wurden. Vertrauende Remoteseiten können dann sicher sein, dass nur die vorgesehene Software auf vertrauenswürdiger Hardware ausgeführt wird. Azure Attestation ist ein einheitlicher Service für die Kunden und ein Framework für den Nachweis.

Azure Attestation ermöglicht innovative Sicherheitsparadigmen wie [Confidential Computing unter Azure](../confidential-computing/overview.md) und Intelligent Edge-Schutz. Kunden haben die Möglichkeit angefordert, den Standort eines Computers, die Stellung eines virtuellen Computers (VM) auf diesem Computer und die Umgebung, in der die Enclaves auf diesem virtuellen Computer ausgeführt werden, einzeln zu überprüfen. Azure Attestation ermöglicht diese und viele zusätzliche Kundenanforderungen.

Azure Attestation erhält Beweise von Compute-Entitäten, wandelt sie in einen Satz von Ansprüchen um, überprüft sie anhand konfigurierbarer Richtlinien und erzeugt kryptografische Nachweise für anspruchsbasierte Anwendungen (z. B. vertrauende Seiten und Überwachungsbehörden).

## <a name="use-cases"></a>Anwendungsfälle

Azure Attestation bietet umfassende Nachweisdienste für mehrere Umgebungen und unterschiedliche Anwendungsfälle.

### <a name="sgx-attestation"></a>SGX-Nachweis

SGX bietet Isolation für Hardware, die auf bestimmten Intel CPUs-Modellen unterstützt wird. SGX ermöglicht das Ausführen von Code in bereinigten Depots, die als SGX-Enclaves bezeichnet werden. Zugriffs- und Speicherberechtigungen werden dann von der Hardware verwaltet, um eine minimale Angriffsfläche mit entsprechender Isolation zu gewährleisten.

Clientanwendungen können so entworfen werden, dass sie die Vorteile von SGX-Enclaves nutzen, indem sicherheitsrelevante Aufgaben in diese Enclaves delegiert werden. Solche Anwendungen können Azure Attestation nutzen, um routinemäßig eine Vertrauensstellung in der Enclave herzustellen und auf sensible Daten zuzugreifen.

Intel® Xeon® Scalable Processors unterstützen nur [ECDSA-basierte Nachweislösungen](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html#Elliptic%20Curve%20Digital%20Signature%20Algorithm%20(ECDSA)%20Attestation) für den Remotenachweis von SGX-Enklaven. Mithilfe des ECDSA-basierten Nachweismodells unterstützt Azure Attestation die Überprüfung von Intel® Xeon® E3-Prozessoren und auf Intel® Xeon® Scalable Processors basierenden Serverplattformen. 

> [!NOTE]
> Zum Nachweis von auf Intel® Xeon® Scalable Processors basierenden Serverplattformen mithilfe von Azure Attestation wird von Benutzern erwartet, dass sie [Azure DCAP-Version 1.10.0](https://github.com/microsoft/Azure-DCAP-Client) oder höher installieren.

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) ist eine Sammlung von Bibliotheken, die auf das Erstellen einer einzelnen vereinheitlichten Enclave-Abstraktion abzielen, damit Entwickler TEE-basierte Anwendungen erstellen können. Sie bietet ein universelles, sicheres App-Modell, das Plattformbesonderheiten minimiert. Für Microsoft ist sie eine Grundlage hin zur Demokratisierung hardwarebasierter Enclavetechnologien wie SGX und zur Steigerung der Akzeptanz in Azure.

OE standardisiert bestimmte Anforderungen für die Überprüfung eines Enclavenachweises. Das qualifiziert OE als einen passenden Nachweisconsumer von Azure Attestation.

### <a name="tpm-attestation"></a>TPM-Nachweis 

Der TPM-basierte Nachweis (Trusted Platform Module) ist wichtig, um den Zustand einer Plattform nachzuweisen. TPM fungiert als Vertrauensanker und Sicherheits-Coprozessor, um die kryptografische Gültigkeit der Messungen zu bestätigen (Beweis). Geräte mit TPM können nachweisen, dass die Startintegrität nicht beeinträchtigt ist, und mithilfe der Ansprüche den Aktivierungsstatus von Funktionen während des Starts erkennen. 

Clientanwendungen können so entworfen werden, dass sie den TPM-Nachweis nutzen, indem sicherheitsrelevante Aufgaben delegiert und somit nur ausgeführt werden, nachdem die Sicherheit einer Plattform überprüft wurde. Solche Anwendungen können Azure Attestation nutzen, um routinemäßig eine Vertrauensstellung auf der Plattform herzustellen und auf sensible Daten zuzugreifen.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation kann in einer TEE ausgeführt werden.

Azure Attestation ist für vertrauliche Computing-Szenarien von entscheidender Bedeutung, da es die folgenden Aktionen durchführt:

- Überprüft, ob der Enclavenachweis gültig ist
- Wertet den Enclavenachweis anhand einer vom Kunden definierten Richtlinie aus
- Verwaltet und speichert mandantenspezifische Richtlinien
- Generiert und signiert ein Token, das von den vertrauenden Seiten verwendet wird, um mit der Enclave zu interagieren

Azure Attestation ist so aufgebaut, dass es in zwei Arten von Umgebungen ausgeführt werden kann:
- Azure Attestation, ausgeführt in einer TEE (von SGX aktiviert)
- Azure Attestation, ausgeführt in einer anderen Umgebung als TEE

Azure Attestation-Kunden haben angefordert, dass Microsoft keine vertraute Computerbasis (Trusted Computing Base, TBC) mehr verwendet. Dadurch soll verhindert werden, dass Microsoft-Entitäten wie VM-Administratoren, Hostadministratoren und Microsoft-Entwickler Nachweisanforderungen, Richtlinien und von Azure Attestation ausgestellte Token ändern. Azure Attestation ist auch für die Durchführung in TEE konzipiert, wobei Features von Azure Attestation wie z. B. die Angebotsvalidierung, die Generierung von Token und Tokensignierung in eine SGX-Enclave verschoben werden.

## <a name="why-use-azure-attestation"></a>Gründe für die Verwendung von Azure Attestation

Azure Attestation ist die bevorzugte Wahl für den Nachweis von TEEs, da es die folgenden Vorteile bietet: 

- Einheitliches Framework für den Nachweis mehrerer Umgebungen (beispielsweise TPMs, SGC-Enclaves und VBS-Enclaves) 
- Ermöglicht die Erstellung von benutzerdefinierten Nachweisanbietern und die Konfiguration von Richtlinien, um die Generierung von Token einzuschränken
- Verfügt über regionale gemeinsam genutzte Anbieter, die ohne Konfiguration von Benutzern Nachweise durchführen können
- Schützt die Daten während der Verwendung mit der Implementierung in einer SGX-Enclave
- Hochverfügbarer Dienst 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Unterstützung für Business Continuity & Disaster Recovery (BCDR)

[Business Continuity & Disaster Recovery](../best-practices-availability-paired-regions.md) (BCDR) für Azure Attestation ermöglicht das Verringern von Dienstunterbrechungen, die durch erhebliche Verfügbarkeitsproblemen oder Notfallereignisse in einer Region entstehen.

Cluster, die in zwei Regionen bereitgestellt werden, werden unter normalen Umständen unabhängig voneinander betrieben. Bei einem Fehler oder einem Ausfall einer Region geschieht Folgendes:

- Azure Attestation BCDR bietet ein nahtloses Failover, bei dem Kunden keine zusätzlichen Schritte zur Wiederherstellung ausführen müssen.
- Die [Azure Traffic Manager](../traffic-manager/index.yml)-Instanz für die Region erkennt, dass der Integritätstest beeinträchtigt wird, und schaltet den Endpunkt in eine gekoppelte Region um.
- Vorhandene Verbindungen können nicht verwendet werden, und interne Serverfehler oder Timeoutprobleme werden empfangen.
- Alle Vorgänge auf Steuerungsebene werden blockiert. Kunden können keine Nachweisanbieter in der primären Region erstellen.
- Alle Vorgänge auf Datenebene, einschließlich der Nachweisaufrufe und der Richtlinienkonfiguration, werden von der sekundären Region behandelt. Kunden können Vorgänge auf Datenebene weiterhin ausführen. Der ursprüngliche URI entspricht dabei der primären Region.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [grundlegende Konzepte von Azure Attestation](basic-concepts.md).
- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Einrichten von Azure Attestation mithilfe von PowerShell](quickstart-powershell.md)
