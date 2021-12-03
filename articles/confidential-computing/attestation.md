---
title: Bescheinigung für SGX-Enclaves
description: Überprüfen Sie die Sicherheit Ihrer SGX-Enclave für vertrauliche Datenverarbeitung mit einer Bescheinigung.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03a389facebbfcb242cf63afd0a8fcca6ad7b670
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347668"
---
# <a name="attestation-for-sgx-enclaves"></a>Bescheinigung für SGX-Enclaves

Vertrauliches Computing auf Azure bietet Intel SGX-basierte virtuelle Maschinen, die einen Teil Ihres Codes oder Ihrer Daten isolieren können. Wenn Sie mit diesen [Enklaven](confidential-computing-enclaves.md) arbeiten, können Sie verifizieren und validieren, dass Ihre vertrauenswürdige Umgebung sicher ist. Diese Verifizierung wird als Nachweisvorgang bezeichnet. 

## <a name="overview"></a>Übersicht 

Durch die Attestierung kann eine vertrauende Partei darauf vertrauen, dass ihre Software korrekt ist:

1. Ausführen in einer Enclave
1. Aktuell
1. Sicher

So kann eine Enklave beispielsweise die zugrunde liegende Hardware auffordern, einen Berechtigungsnachweis zu erstellen. Diese Bescheinigung enthält den Nachweis, dass die Enklave auf der Plattform existiert. Eine zweite Enklave kann den Bericht empfangen und überprüfen, ob er von derselben Plattform stammt.

![Schema des Bescheinigungsverfahrens, das den sicheren Austausch des Kunden mit der Enklave zeigt, die Daten und den Anwendungscode enthält.](media/attestation/attestation.png)

Implementieren Sie die Bescheinigung mit einem sicheren Bescheinigungsdienst, der mit der Systemsoftware und dem Silizium kompatibel ist. Beispiel:

- [Microsoft Azure Attestation](../attestation/overview.md) 
- [Nachweis- und Bereitstellungsdienste von Intel](https://software.intel.com/sgx/attestation-services)


Beide Dienste sind kompatibel mit vertraulichen Azure-Computing-VMs der Intel SGX DCsv2-Reihe. VMs der DCsv3-Serie und DCdsv3-Serie sind nicht mit dem Intel-Attestierungsdienst kompatibel. 

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Microsoft Azure Attestation Beispiele für Enclave aware apps](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)
