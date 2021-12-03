---
title: Auswählen von Containerangeboten für Confidential Computing
description: Es wird beschrieben, wie Sie die richtigen Angebote für vertrauliche Container auswählen, um Ihre Sicherheits-, Isolations- und Entwickleranforderungen zu erfüllen.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 158e3e5a8191bc8e193ff9dab803feed7da2cc8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021507"
---
# <a name="choosing-confidential-container-offerings"></a>Auswählen von Angeboten für vertrauliche Container

Im Rahmen von Azure Confidential Computing werden mehrere Arten von vertraulichen Containern angeboten. Sie können diese Container verwenden, um die Datenintegrität und -vertraulichkeit und die Codeintegrität zu unterstützen.

Aufgrund der Verschlüsselung tragen vertrauliche Container auch zum Schutz des Codes bei. Sie können hardwarebasierte Zusicherungen und Hardware-Stammvertrauensstellungen erstellen. Darüber hinaus können Sie mit vertraulichen Containern die Angriffsfläche reduzieren.

## <a name="enclaves-confidential-containers"></a>Enclaves für vertrauliche Container

Sie können vertrauliche Container mit Enclaves bereitstellen. Diese Methode für Containerbereitstellungen verfügt über die höchste Sicherheit und Computeisolation mit einer niedrigeren vertrauten Computerbasis (Trusted Computing Base, TCB). Es sind vertrauliche Container verfügbar, die auf Intel Software Guard Extensions (SGX) basieren und in der hardwarebasierten Trusted Execution Environment (TEE) ausgeführt werden. Für diese Container werden Lift & Shift-Vorgänge für Ihre vorhandenen Container-Apps unterstützt. Eine weitere Möglichkeit besteht darin, das Erstellen von benutzerdefinierten Apps mit Enclavebereitschaft zuzulassen.

Es gibt zwei Programmier- und Bereitstellungsmodelle in Azure Kubernetes Service (AKS). 

**Unveränderte Container** unterstützen über das Azure-Partnernetzwerk für OSS-Projekte anspruchsvollere Programmiersprachen unter Intel SGX. Weitere Informationen finden Sie im Artikel mit dem [Bereitstellungsablauf und Beispielen für unveränderte Container](./confidential-containers.md).

Für **Enclave-fähige Container** wird ein benutzerdefiniertes Intel SGX-Programmiermodell verwendet. Weitere Informationen finden Sie im Artikel mit dem [Bereitstellungsablauf und Beispielen für Enclave-fähige Container](./enclave-aware-containers.md). 

![Diagramm: Enclave-fähige vertrauliche Container mit Intel SGX mit Isolations- und Sicherheitsgrenzen](./media/confidential-containers/confidential-container-intel-sgx.png)

## <a name="learn-more"></a>Weitere Informationen

- [Vertrauliche virtuelle Computer mit Intel SGX in Azure](./virtual-machine-solutions-sgx.md)
- [Vertrauliche Container in Azure](./confidential-containers.md)
