---
title: Azure Kubernetes Service Plugin für vertrauliche VMs
description: So verwenden Sie das Intel SGX-Geräte-Plugin und die Intel SGX-Quote-Helfer-Daemon-Sets für vertrauliche VMs mit Azure Kubernetes Service.
author: agowdamsft
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: article
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f4a00e1c70427c6b467a52694340e10109b5f4f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096129"
---
# <a name="confidential-computing-plugin-for-confidential-vms"></a>Plugin für vertrauliche Datenverarbeitung für vertrauliche VMs

Azure Kubernetes Service (AKS) bietet ein Plugin für virtuelle Maschinen (VMs) für vertrauliche Datenverarbeitung in Azure. Das Plugin, `confcom`, ist ein Daemon-Set. Das Plugin läuft nur für Intel Software Guard Extensions (SGX) vertrauliche VMs in einem AKS-Cluster. Dieses Plugin wird auf der Ebene des AKS-Clusters registriert. Sie können das Plugin verwenden, um vertrauliche Knoten einfach zu verwalten. [Aktivieren Sie das Plugin auf Ihrem AKS-Cluster](./confidential-enclave-nodes-aks-get-started.md), bevor Sie beginnen.

## <a name="intel-sgx-device-plugin-for-aks"></a>Intel SGX-Geräte-Plugin für AKS

Das SGX-Geräte-Plugin implementiert die Kubernetes-Geräte-Plugin-Schnittstelle für Enklave-Page-Cache (EPC)-Speicher. Dieses Plugin macht den EPC-Speicher zu einem weiteren Ressourcentyp in Kubernetes. Nutzer können für EPC wie für andere Ressourcen Grenzen festlegen. Neben der Planungsfunktion ermöglicht das Geräte-Plug-In auch die Zuweisung von SGX-Gerätetreiberberechtigungen für vertrauliche Workloadcontainer. [Eine Beispielimplementierung des speicherbasierten EPC-Einsatzes](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) ist verfügbar.

## <a name="psm-with-sgx-quote-helper"></a>PSM mit SGX-Kurshelfern

Enklave-Anwendungen, die eine Fernbescheinigung durchführen, müssen ein Angebot erstellen. Das Zitat liefert einen kryptografischen Beweis für die Identität und den Zustand der Anwendung sowie für die Host-Umgebung der Enklave. Die Angebotserstellung stützt sich auf bestimmte vertrauenswürdige Softwarekomponenten von Intel, die Teil der SGX Platform Software Components (PSW/DCAP) sind. Diese Plattformsoftwarekomponenten werden als DaemonSet verpackt, das pro Knoten ausgeführt wird. Sie können das PSW verwenden, wenn Sie ein Bescheinigungsangebot von Enklave Apps anfordern. Die Verwendung des von AKS bereitgestellten Dienstes trägt dazu bei, die Kompatibilität zwischen dem PSW und anderen SW-Komponenten im Host besser zu gewährleisten. Lesen Sie die Details der Funktion unten.

[Enklave-Anwendungen](confidential-computing-enclaves.md), die eine Fernbescheinigung durchführen, benötigen ein generiertes Angebot. Dieses Zitat liefert einen kryptografischen Beweis für die Identität, den Zustand und die Betriebsumgebung der Anwendung. Die Generation erfordert vertrauenswürdige Softwarekomponenten, die Teil der PSW von Intel sind.

### <a name="overview"></a>Übersicht

> [!NOTE]
> Diese Funktion ist nur für DCsv2/DCsv3-VMs erforderlich, die spezielle Intel SGX-Hardware verwenden. 
 
Intel unterstützt zwei Bescheinigungsmodi, um die Angebotserstellung durchzuführen. Für die Wahl des Typs siehe die [Bestätigungsartenunterschiede](#attestation-type-differences).

- **In-Proc**: Die vertrauenswürdigen Softwarekomponenten werden innerhalb des Enklavenanwendungsprozesses gehostet.

- **Out-of-Proc**: Die vertrauenswürdigen Softwarekomponenten werden außerhalb der Enklavenanwendung gehostet.
 
Für SGX-Anwendungen, die mit dem Open Enclave SDK erstellt werden, wird standardmäßig der Modus für den prozessinternen Nachweis (In-Proc) verwendet. SGX-basierte Anwendungen erlauben out-of-proc und erfordern zusätzliches Hosting. Diese Anwendungen stellen die erforderlichen Komponenten, wie den Architectural Enclave Service Manager (AESM), außerhalb der Anwendung zur Verfügung.

Es wird dringend empfohlen, diese Funktion zu nutzen. Diese Funktion erhöht die Betriebszeit Ihrer Enklave-Anwendungen während Intel Platform Updates oder DCAP-Treiber-Updates.

### <a name="attestation-type-differences"></a>Unterschiede in der Bescheinigungsart

Für die Komponenten zur Angebotserstellung von PSW für die einzelnen Containeranwendungen sind keine Updates erforderlich.

Bei „Out-of-Proc“ müssen Containerbesitzer keine Updates in ihrem Container verwalten. Containerbesitzer verlassen sich stattdessen auf die bereitgestellte Schnittstelle, die den zentralisierten Dienst außerhalb des Containers aufruft. Der Anbieter update sand verwaltet diesen Dienst.

Bei Out-of-Proc ist nicht zu befürchten, dass es aufgrund veralteter PSW-Komponenten zu Ausfällen kommt. Die Angebotserstellung umfasst auch die vertrauenswürdigen Softwarekomponenten (Quoting Enclave (QE) und Provisioning Certificate Enclave (PCE)), die Teil der vertrauenswürdigen Computerbasis (Trusted Computing Base, TCB) sind. Diese Softwarekomponenten müssen auf dem neuesten Stand sein, um die Nachweisanforderungen zu erfüllen. Der Anbieter verwaltet die Updates für diese Komponenten. Die Kunden müssen sich nicht mit Zertifikatsfehlern aufgrund veralteter vertrauenswürdiger SW-Komponenten in ihrem Container herumschlagen.

Out-of-proc verwendet besser den EPC-Speicher. Im In-Proc-Bescheinigungsmodus instanziiert jede Enklave-Anwendung die Kopie von QE und PCE für die Fernbescheinigung. Bei out-of-proc hostet der Container diese Enklaven nicht und verbraucht auch keinen Enklaven-Speicher aus dem Container-Kontingent.

Es gibt auch Schutzmaßnahmen gegen die Durchsetzung des Kernels. Wenn der SGX-Treiber in den Linux-Kernel gestreamt wird, hat eine Enklave höhere Privilegien. Dieses Privileg erlaubt es der Enklave, PCE aufzurufen, wodurch die Enklave-Anwendung, die im In-Proc-Modus läuft, unterbrochen wird. Standardmäßig wird Enklaven diese Berechtigung nicht gewährt. Zum Gewähren dieser Berechtigung für eine Enklavenanwendung muss der Prozess für die Anwendungsinstallation geändert werden. Der Anbieter des Dienstes, der Out-of-Proc-Anfragen bearbeitet, stellt sicher, dass der Dienst mit diesem Recht installiert ist.

Sie müssen nicht auf Abwärtskompatibilität mit PSW und DCAP achten. Der Anbieter validiert Aktualisierungen der PSW-Komponenten zur Angebotserstellung auf Abwärtskompatibilität. Dieser Schritt behandelt Kompatibilitätsprobleme im Vorfeld und behebt sie, bevor Updates für vertrauliche Workloads bereitgestellt werden.

### <a name="out-of-proc-attestation-for-confidential-workloads"></a>Out-of-proc-Bescheinigung für vertrauliche Workloads

Das Modell der Out-of-Proc-Bescheinigung eignet sich für vertrauliche Workloads. Die Angebotsanforderung und die Angebotserstellung werden getrennt voneinander, aber auf demselben physischen Rechner ausgeführt. Die Angebotserstellung erfolgt zentral und bedient Anfragen nach Angeboten von allen Unternehmen. Definieren Sie die Schnittstelle ordnungsgemäß, und machen Sie die Schnittstelle für jede Einrichtung auffindbar, um Angebote anzufordern.

![Schema der Schnittstelle für die Angebotsanfrage und die Angebotserstellung.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Das abstrakte Modell gilt für vertrauliche Arbeitslastszenarien. Dieses Modell nutzt den bereits verfügbaren AESM-Dienst. AESM ist containerisiert und wird als Daemon-Set im Kubernetes-Cluster bereitgestellt. Durch Kubernetes wird garantiert, dass auf jedem Agent-Knoten eine einzelne Instanz eines AESM-Dienstcontainers (umschlossen von einem Pod) bereitgestellt wird. Der neue SGX-Quote-Dämonensatz ist vom `sgx-device-plugin`-Dämonensatz abhängig, da der AESM-Dienstcontainer EPC-Speicher von `sgx-device-plugin` anfordern würde, um QE- und PCE-Enklaven zu starten.

Jeder Container muss sich für die Verwendung der Out-of-Proc-Quote-Erzeugung entscheiden, indem er bei der Erstellung die Umgebungsvariable `SGX_AESM_ADDR=1` setzt. Der Container muss auch das Paket `libsgx-quote-ex` enthalten, das die Anfrage an den Standard-Unix-Domain-Socket weiterleitet

Eine Anwendung kann nach wie vor die In-Proc-Bescheinigung verwenden. Sie können jedoch nicht gleichzeitig in-proc und out-of-proc in einer Anwendung verwenden. Die Out-of-Proc-Infrastruktur ist standardmäßig verfügbar und verbraucht Ressourcen.

### <a name="sample-implementation"></a>Beispielimplementierung

Die unten angegebene Docker-Datei ist ein Beispiel für eine Anwendung, die auf Open Enclave basiert. Setzen Sie die Umgebungsvariable `SGX_AESM_ADDR=1` in der Docker-Datei. Oder Sie setzen die Variable in der Bereitstellungsdatei. Folgen Sie diesem Beispiel für die Details der Docker-Datei und der YAML-Bereitstellung. 

> [!Note] 
> Das Paket **libsgx-quote-ex** von Intel muss in den Anwendungscontainer gepackt werden, damit die Out-of-Proc-Attestierung richtig funktioniert.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Stattdessen setzen Sie den Out-of-Proc-Attestierungsmodus in der Deployment-YAML-Datei wie folgt:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Nächste Schritte

- [Vertrauliche Knoten (DCsv2/DCsv3-Serie) auf AKS einrichten](./confidential-enclave-nodes-aks-get-started.md)
- [Schnellstartproben für vertrauliche Behälter](https://github.com/Azure-Samples/confidential-container-samples)
- [DCsv2: SKU-Liste](../virtual-machines/dcv2-series.md)
- [DCSv3 SKU-Liste](../virtual-machines/dcv3-series.md)
- [Azure Attestation](../attestation/index.yml)
- [Intel SGX Confidential Virtual Machines auf Azure](/confidential-computing/virtual-machine-solutions-sgx.md)
