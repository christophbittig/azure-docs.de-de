---
title: Erstellen mit SGX-Enclaves - Virtuelle Maschinen in Azure
description: Hier erfahren Sie mehr über Intel SGX-Hardware zur Ermöglichung Ihrer Confidential Computing-Workloads.
author: stempesta
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ac8c9ddced2fd37c829277c925b8953e7c71151
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310013"
---
# <a name="build-with-sgx-enclaves"></a>Erstellen mit SGX-Enclaves 

Azure Confidential Computing bietet [DCsv2-series](../virtual-machines/dcv2-series.md) und [DCsv3/DCdsv3-series](../virtual-machines/dcv3-series.md)* virtuelle Maschinen (VMs). Diese VMs verfügen über [Intel® Software Guard Extensions (SGX)](https://intel.com/sgx). 

Mit der Intel SGX-Technologie können Kunden Enklaven erstellen, die Daten schützen und verschlüsselt halten, während die CPU die Daten verarbeitet. Das Betriebssystem (OS) und der Hypervisor können nicht auf die Daten zugreifen. Administratoren des Rechenzentrums mit physischem Zugang haben ebenfalls keinen Zugriff auf die Daten.

## <a name="enclaves-concept"></a>Enclaves-Konzept

Enklaven sind gesicherte Teile des Prozessors und des Speichers der Hardware. Innerhalb der Enklave können Sie weder Daten noch Code einsehen, auch nicht mit einem Debugger. Wenn nicht vertrauenswürdiger Code versucht, Inhalte im Enklave-Speicher zu ändern, deaktiviert SGX die Umgebung und verweigert die Operationen. Diese einzigartigen Funktionen helfen Ihnen, Ihre Geheimnisse vor dem Zugriff durch Unbefugte zu schützen.  

![Diagramm des VM-Modells, das die in den Enklaven gesicherten Daten zeigt.](media/overview/hardware-backed-enclave.png)

Betrachten Sie eine Enklave als ein gesichertes Schließfach. Sie legen verschlüsselte Codes und Daten in das Schließfach. Von außen kann man nichts sehen. Sie geben der Enklave einen Schlüssel zur Entschlüsselung der Daten. Die Enklave verarbeitet die Daten und verschlüsselt sie erneut, bevor sie wieder nach außen sendet.

Jede Enklave verfügt über einen verschlüsselten Seiten-Cache (EPC) mit einer bestimmten Größe. Der EPC bestimmt die Speichermenge, die eine Enklave aufnehmen kann. [DCsv2-series](../virtual-machines/dcv2-series.md) VMs fassen bis zu 168 MiB. [DCsv3/DCdsv3-series](../virtual-machines/dcv3-series.md)* VMs fassen bis zu 256 GB für speicherintensivere Workloads.

> [!NOTE]
> *DCsv3 und DCdsv3 befinden sich ab dem 1. November 2021 in der **öffentlichen Vorschau**.

Weitere Informationen finden Sie unter [Bereitstellung von Intel SGX-VMs mit hardwarebasierten vertrauenswürdigen Enklaven](virtual-machine-solutions-sgx.md).

## <a name="developing-for-enclaves"></a>Entwickeln für Enclaves

Für die Entwicklung von Anwendungen, die in Enklavenlaufen, stehen verschiedene [Softwaretools zur Verfügung](application-development.md). Mit diesen Tools können Sie Teile Ihres Codes und Ihrer Daten innerhalb der Enklave schützen. Stellen Sie sicher, dass niemand außerhalb Ihrer vertrauenswürdigen Umgebung Ihre Daten mit diesen Tools einsehen oder ändern kann.

## <a name="next-steps"></a>Nächste Schritte
- [Einsatz einer virtuellen Maschine der DCsv2- oder DCsv3/DCdsv3-Serie](quick-create-portal.md)
- [Entwickeln einer enklavefähigen Anwendung](application-development.md) mit dem OE SDK
