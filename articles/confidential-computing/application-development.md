---
title: Entwicklungstools für Azure Confidential Computing
description: Verwenden von Tools und Bibliotheken zum Entwickeln von Anwendungen für Confidential Computing für Intel SGX
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5faf8807047d0e73029d27f62af8acaab5e80026
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347649"
---
# <a name="application-enclave-development"></a>Entwicklung von Anwendungs-Enclaves 

Mit Azure Confidential Computing können Sie Anwendungs-Enclaves für virtuelle Computer (VMs) erstellen, auf denen Intel Software Guard Extensions (SGX) ausgeführt wird. Es ist wichtig, die zugehörigen Tools und die Software zu verstehen, bevor Sie mit der Entwicklung beginnen.

> [!NOTE]
> Wenn Sie die [Einführung in Intel SGX-VMs und Enclaves](confidential-computing-enclaves.md) noch nicht gelesen haben, tun Sie dies, bevor Sie fortfahren.

## <a name="application-enclaves"></a>Anwendungs-Enclaves

Anwendungs-Enclaves sind isolierte Umgebungen, die bestimmten Code und bestimmte Daten schützen. Beim Erstellen von Enclaves müssen Sie bestimmen, welcher Teil der Anwendung innerhalb der Enclave ausgeführt werden soll. Stellen Sie beim Erstellen oder Verwalten von Enclaves sicher, dass Sie mit dem ausgewählten Bereitstellungsstapel kompatible SDKs und Frameworks verwenden. 

Confidential Computing bietet derzeit Anwendungs-Enclaves. Insbesondere können Sie mit Anwendungs-Enclaves bereitstellen und entwickeln, indem Sie [vertrauliche VMs mit aktiviertem Intel SGX](virtual-machine-solutions-sgx.md) verwenden. 

## <a name="intel-sgx"></a>Intel SGX

Mit Intel SGX-Technologie können Sie Anwendungs-Enclaves oder Trusted Execution Environment-Instanzen mit einem nicht zugänglichen Schlüssel verschlüsseln, der in der CPU gespeichert ist. Die Entschlüsselung des Codes und der Daten innerhalb der Enclave erfolgt innerhalb des Prozessors. Nur die CPU hat Zugriff. Dieser Grad der Isolation schützt die verwendeten Daten und bietet Schutz vor Hardware- und Softwareangriffen. Weitere Informationen finden Sie auf der [Website zu Intel SGX](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html). 

Azure bietet Intel SGX in einer Virtualisierungsumgebung auf verschiedenen VM-Größen der DC-Serie an. Mehrere VM-Größen ermöglichen verschiedene EPC-Größen (Enclave Page Cache). EPC ist die maximale Menge an Arbeitsspeicher für eine Enclave auf der jeweiligen VM. Derzeit sind Intel SGX-VMs auf VMs der [DCsv2-Serie](../virtual-machines/dcv2-series.md) und [DCsv3/DCdsv3-Serie](../virtual-machines/dcv3-series.md) verfügbar.


### <a name="developing-applications"></a>Entwickeln von Anwendungen

Es gibt zwei Partitionen in einer Anwendung, die mit Enclaves erstellt wurde. 

Der **Host** ist die „nicht vertrauenswürdige“ Komponente. Ihre Enclave-Anwendung wird auf dem Host ausgeführt. Der Host ist eine nicht vertrauenswürdige Umgebung. Wenn Sie Enclave-Code auf dem Host bereitstellen, kann der Host nicht auf diesen Code zugreifen.

Die **Enclave** ist die „vertrauenswürdige“ Komponente. Der Anwendungscode und seine zwischengespeicherten Daten sowie der Arbeitsspeicher werden in der Enclave ausgeführt. Die Enclave-Umgebung schützt Ihre Geheimnisse und sensiblen Daten. Stellen Sie sicher, dass Ihre sicheren Berechnungen in einer Enclave erfolgen.

![Diagramm einer Anwendung mit den Host- und Enclave-Partitionen. In der Enclave befinden sich die Daten- und Anwendungscodekomponenten.](media/application-development/oe-sdk.png)

Um die Leistungsfähigkeit von Enclaves und isolierten Umgebungen ausschöpfen zu können, wählen Sie Tools, die Confidential Computing unterstützen. Verschiedene Tools unterstützen die Entwicklung von Enclave-Anwendungen. Beispielsweise können Sie die folgenden Open-Source-Frameworks verwenden: 

- [Das Open Enclave Software Development Kit (OE SDK)](enclave-development-oss.md#oe-sdk)
- [Intel SGX SDK](enclave-development-oss.md#intel-sdk)
- [Das EGo Software Development Kit](enclave-development-oss.md#ego)
- [Confidential Consortium Framework (CCF)](enclave-development-oss.md#ccf)

Ermitteln und bestimmen Sie beim Entwerfen einer Anwendung, welcher Teil der Anwendung in Enclaves ausgeführt werden muss. Code in der vertrauenswürdigen Komponente ist vom Rest Ihrer Anwendung isoliert. Nach Initialisierung der Enclave und Laden des Codes in den Arbeitsspeicher können nicht vertrauenswürdige Komponenten diesen Code weder lesen noch ändern.

## <a name="next-steps"></a>Nächste Schritte 

- [Bereitstellen einer Intel SGX-VM für Confidential Computing](quick-create-portal.md)
- [Einstieg in die Entwicklung von Anwendungen mit Open-Source-Software](enclave-development-oss.md)
