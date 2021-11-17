---
title: Entwickeln von Anwendungsenklaven mit Open-Source-Lösungen in Azure Confidential Computing
description: Erfahren Sie, wie Sie Tools zum Entwickeln von Intel SGX-Anwendungen für Azure Confidential Computing verwenden.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71be688b3f69860468ce6c638e484caae0ba72f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290208"
---
# <a name="open-source-solutions-to-build-enclave-applications"></a>Open-Source-Lösungen zum Erstellen von Enklavenanwendungen

In diesem Artikel werden Open-Source-Lösungen zum Erstellen von Anwendungen behandelt, die Anwendungsenklaven verwenden. Lesen Sie vor dem Artikel unbedingt die Konzeptseite für [Enklavenanwendungen](application-development.md) durch. 

## <a name="intel-sgx-compatible-tools"></a>Intel SGX-kompatible Tools
Azure bietet Anwendungsenklaven über [vertrauliche virtuelle Computer mit aktivierter SGX (Intel Software Guard Extensions) an](virtual-machine-solutions-sgx.md). Nachdem Sie einen virtuellen Intel SGX-Computer bereitgestellt haben, benötigen Sie spezielle Tools, um Ihre Anwendung enklavefähig (Enclave aware) zu machen. Auf diese Weise können Sie Anwendungen erstellen, die sowohl vertrauenswürdige als auch nicht vertrauenswürdige Codeteile enthalten. 

Beispielsweise können Sie die folgenden Open-Source-Frameworks verwenden: 

- [Open Enclave Software Development Kit](#oe-sdk)
- [EGo SDK](#ego)
- [Intel SGX SDK](#intel-sdk)
- [Confidential Consortium Framework (CCF)](#ccf)

Wenn Sie keinen neuen Anwendungscode schreiben möchten, können Sie eine containerisierte Anwendung mithilfe von [Grundvoraussetzungen für vertrauliche Container](confidential-containers.md) umschließen.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Verwenden Sie eine Bibliothek oder ein Framework, die bzw. das von Ihrem Anbieter unterstützt wird, falls Sie Code für die Ausführung in einer Enklave schreiben möchten. Das [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) ist ein Open-Source-SDK, das die Abstraktion über unterschiedliche Confidential Computing-fähige Hardware ermöglicht. 

Das OE SDK ist so konzipiert, dass es als einzelne Abstraktionsschicht für beliebige Hardware und CSPs genutzt wird. Das OE SDK kann zusätzlich zu virtuellen Azure Confidential Computing-Computern verwendet werden, um Anwendungen basierend auf Enklaven zu erstellen und auszuführen. Das Open Enclave-Repository wird von Microsoft verwaltet.

### <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) ist ein Open-Source-SDK, mit dem Sie in der Programmiersprache Go geschriebene Anwendungen innerhalb von Enclaves ausführen können. EGo baut auf dem OE SDK auf und verfügt über eine In-Enclave-Go-Bibliothek für Nachweis und Versiegelung. Viele vorhandene Go-Anwendungen werden ohne Änderungen in EGo ausgeführt.  

### <a name="intel-sgx-software-development-kit"></a>Intel SGX Software Development Kit <a id="intel-sdk"></a>
Das [Intel SGX SDK](https://01.org/intel-softwareguard-extensions) wird vom SGX-Team bei Intel entwickelt und verwaltet. Das SDK ist ein Sammlungstool, mit dem Softwareentwickler Intel SGX-fähige Anwendungen in C/C++ erstellen und debuggen können.

### <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

Das [Confidential Consortium Framework](https://www.microsoft.com/research/project/confidential-consortium-framework/) ([CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/)) ist ein Beispiel für ein verteiltes Blockchain-Framework. Das CCF basiert auf Azure Confidential Computing. Dieses Framework wurde von Microsoft Research eingeführt und nutzt vertrauenswürdige Ausführungsumgebungen (Trusted Execution Environments, TEEs), um ein Netzwerk mit Remoteenklaven für Nachweise zu erstellen. Knoten können auf virtuellen Azure Intel SGX Computern ausgeführt werden und die Enklaveninfrastruktur nutzen. Über Nachweisprotokolle können Benutzer der Blockchain die Integrität eines einzelnen CCF-Knotens verifizieren und effektiv das gesamte Netzwerk überprüfen.

Im CCF besteht der dezentralisierte Ledger aus erfassten Änderungen an einem Schlüssel-Wert-Speicher, der auf allen Netzwerkknoten repliziert wird. Auf jedem dieser Knoten wird eine Transaktionsengine ausgeführt, die von Benutzern der Blockchain über TLS ausgelöst werden kann. Wenn Sie einen Endpunkt auslösen, wird der Schlüssel-Wert-Speicher mutiert. Damit die verschlüsselte Änderung im dezentralisierten Ledger erfasst wird, ist die Zustimmung von mehr als einem Knoten erforderlich, um eine Vereinbarung zu erreichen.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines virtuellen Confidential Computing Intel SGX Computers](quick-create-portal.md)
