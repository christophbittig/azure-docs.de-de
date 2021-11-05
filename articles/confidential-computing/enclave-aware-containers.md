---
title: Enklavefähige Container in Azure
description: Unterstützung enklavefähiger Anwendungscontainer in Azure Kubernetes Service (AKS)
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2f1f13a67bb827aa29b9d2de83f2fb46f95095d6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056659"
---
# <a name="enclave-aware-containers-with-intel-sgx"></a>Enklave-fähige Container mit Intel SGX

Bei einer Enklave handelt es sich um einen geschützten Arbeitsspeicherbereich, der Vertraulichkeit für Daten und für die Codeausführung bietet. Sie ist eine Instanz einer hardwaregeschützten vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE). Der Support von Confidential Computing-VMs in AKS verwendet [Intel SGX (Software Guard Extensions)](https://software.intel.com/sgx), um Umgebungen mit isolierten Enklaven in den Knoten zwischen den einzelnen Containeranwendungen zu erstellen.

Genau wie virtuelle Intel SGX-Computer verfügen auch Containeranwendungen, die für die Ausführung in Enklaven entwickelt werden, über zwei Komponenten:

- Eine nicht vertrauenswürdige Komponente (Host)
- Eine vertrauenswürdige Komponente (Enklave)

![Architektur enklavefähiger Container](./media/enclave-aware-containers/enclaveawarecontainer.png)

Die Anwendungsarchitektur enklavefähiger Container bietet maximale Kontrolle über die Implementierung und sorgt gleichzeitig dafür, den Codeumfang der Enklave gering zu halten. Die Minimierung des in der Enklave ausgeführten Codes trägt zur Verringerung der Angriffsfläche bei.   

## <a name="enablers"></a>Grundvoraussetzungen

### <a name="open-enclave-sdk"></a>Open Enclave SDK
Das [Open Enclave SDK](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs) ist eine hardwareunabhängige Open-Source-Bibliothek für die Entwicklung von C- und C++-Anwendungen, von denen hardwarebasierte vertrauenswürdige Ausführungsumgebungen genutzt werden. Die aktuelle Implementierung unterstützt Intel SGX und bietet eine Vorschauunterstützung für [OP-TEE OS on Arm TrustZone](https://optee.readthedocs.io/en/latest/general/about.html).

Informationen zu den ersten Schritten mit Open Enclave-basierten Containeranwendungen finden Sie [hier](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs).

### <a name="intel-sgx-sdk"></a>Intel SGX SDK
Intel pflegt das Software Development Kit für die Erstellung von SGX-Anwendungen für Linux- und Windows-Containerworkloads. Windows-Container werden derzeit von Confidential Computing-Knoten in AKS nicht unterstützt.

Informationen zu den ersten Schritten mit Intel SGX-basierten Anwendungen finden Sie [hier](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html).

### <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF)
Das Confidential Consortium Framework (CCF) ist ein Open-Source-Framework für die Erstellung einer neuen Kategorie sicherer, hochverfügbarer und leistungsfähiger Anwendungen mit Schwerpunkt auf Computing und Daten mehrerer Parteien. Das CCF ermöglicht die Erstellung umfangreicher, vertraulicher Netzwerke, die die wichtigsten Unternehmensanforderungen erfüllen, was wiederum zur Beschleunigung der Produktion sowie der Einführung von konsortiumbasierten Blockchain-Lösungen und Mehrparteien-Computetechnologien in Unternehmen beitragen kann.

Informationen zu den ersten Schritten mit Azure Confidential Computing und CCF finden Sie [hier](https://github.com/Microsoft/CCF).

### <a name="confidential-inferencing-onnx-runtime"></a>ONNX-Runtime für vertrauliches Rückschließen

Von der enklavebasierten Open-Source-ONNX-Runtime wird ein sicherer Kanal zwischen dem Client und dem Rückschlussdienst eingerichtet, um sicherzustellen, dass weder die Anforderung noch die Antwort die sichere Enklave verlässt. 

Mit dieser Lösung können Sie ein bereits vorhandenes, per Machine Learning trainiertes Modell verwenden und vertraulich ausführen. Gleichzeitig sorgt die Lösung mittels Nachweis und Überprüfungen für Vertrauen zwischen Client und Server. 

Informationen zu den ersten Schritten für eine Lift & Shift-Migration eines ML-Modells zur ONNX-Runtime finden Sie [hier](https://aka.ms/confidentialinference).

### <a name="ego"></a>EGo

Das Open-Source [EGo-SDK](https://www.ego.dev) bringt Unterstützung für die Programmiersprache Go in Enklaven. EGo baut auf dem Open-Source Enclave-SDK auf. Ziel ist es, die Erstellung vertraulicher Microservices zu vereinfachen. Folgen Sie dieser [Schritt-für-Schritt-Anleitung](https://github.com/edgelesssys/ego/tree/master/samples/aks), um einen EGo-basierten Dienst auf AKS einzurichten.

## <a name="container-based-sample-implementations"></a>Containerbasierte Beispielimplementierungen

[Azure-Beispiele für enklavefähige Container in AKS](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

[Bereitstellen eines AKS-Clusters mit Vertraulichen Intel SGX-VM-Knoten](./confidential-enclave-nodes-aks-get-started.md)

<!-- LINKS - external -->
[Azure Attestation](../attestation/overview.md)


<!-- LINKS - internal -->
[Intel SGX Confidential Virtual Machine in Azure](./virtual-machine-solutions-sgx.md)
[Vertrauliche Container](./confidential-containers.md)
