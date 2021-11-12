---
title: Vertrauliche Container auf Azure
description: Erfahren Sie mehr über die Unterstützung von unveränderten Containern in vertraulichen Containern.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 11/04/2021
ms.author: amgowda
ms.service: container-service
ms.custom: ignite-fall-2021
ms.openlocfilehash: 69045817c081ebe4eccf6044829b074d23ecd83c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853541"
---
# <a name="confidential-containers-on-azure"></a>Vertrauliche Container auf Azure

Azure Confidential Computing bietet vertrauliche Container. Es gibt mehrere [Optionen, die Sie für vertrauliche Container auswählen können](choose-confidential-containers-offerings.md). Geschützte und isolierte Umgebungen mit Nachweis verbessern die allgemeine Sicherheit Ihrer Containerbereitstellungen. 

Eine hardwarebasierte Trusted Execution Environment (TEE) bietet starke Zusicherungen. Eine TEE stellt Hardware- und Softwaremessungen von TCB-Komponenten (Trusted Computing Base) bereit. Angebote für vertrauliche Container in Azure ermöglichen die Überprüfung dieser Messungen und überprüfen, ob die Container-Apps in einer überprüfbaren Ausführungsumgebung ausgeführt werden.

Vertrauliche Container unterstützen benutzerdefinierte Anwendungen, die mit beliebigen Programmiersprachen entwickelt wurden. Sie können Docker-Container-Apps auch im Standard ausführen.

:::image type="content" source="./media/confidential-containers/sgx-confidential-container.png" alt-text="Diagramm der Grenze für den Schutz vertraulicher Container in Kubernetes; kein Zugriff für Administratoren.":::

## <a name="enablers-with-intel-sgx-on-azure-kubernetes-serviceaks"></a>Enablers mit Intel SGX auf Azure Kubernetes Service (AKS)

 Damit ein vorhandener Docker-Container ausgeführt werden kann, müssen Anwendungen auf Confidential Computing-Knoten über eine Abstraktionsschicht oder SGX-Software verfügen, um den speziellen CPU-Anweisungssatz nutzen zu können. Konfigurieren Sie SGX, um Ihren vertraulichen Anwendungscode zu schützen. SGX erstellt eine direkte Ausführung an die CPU, um das Gastbetriebssystem (Os), das Hostbetriebssystem oder den Hypervisor aus der Vertrauensgrenze zu entfernen. Durch diesen Schritt werden die Angriffsfläche und Sicherheitsrisiken insgesamt reduziert.

Azure Kubernetes Service (AKS) unterstützt vertrauliche Container vollständig. Sie können vorhandene Container vertraulich in AKS ausführen.

:::image type="content" source="./media/confidential-containers/confidential-containers-deploy-steps.png" alt-text="Diagramm der Konvertierung vertraulicher Container mit neuen Schritten zum Aktivieren von Intel SGX und AKS.":::

## <a name="partner-enablers"></a>Grundvoraussetzungen für Partner

Sie können vertrauliche Container in Azure-Partner- und Open-Source-Software (OSS)-Projekten aktivieren. Entwickler können Softwareanbieter je nach Features, je nach Diensten zur Integration in Azure und je nach Toolunterstützung auswählen. 

> [!IMPORTANT]
> Azure-Partner bieten die folgenden Lösungen an. Für diese Lösungen fallen möglicherweise Lizenzgebühren an. Prüfen Sie daher die Bestimmungen für jede einzelne Partnersoftware. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) verfügt über Portal- und CLI-Funktionen (Command Line Interface), um ihre Containeranwendungen in SGX-fähige vertrauliche Container zu konvertieren. Sie müssen die Anwendung nicht ändern oder neu kompilieren. Fortanix bietet die Flexibilität, eine vielzahl von Anwendungen auszuführen und zu verwalten. Sie können vorhandene Anwendungen, neue native Enclave-Anwendungen und vorgepackte Anwendungen verwenden. Beginnen Sie mit der [Enclave](https://em.fortanix.com/) Manager-Benutzeroberfläche oder [den REST-APIs](https://www.fortanix.com/api/em/)von Fortanix. Erstellen Sie vertrauliche Container mithilfe der [Fortanix-Schnellstartanleitung für AKS](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service).

![Diagramm des Fortanix-Bereitstellungsprozesses, das die Schritte zum Verschieben von Anwendungen in vertrauliche Container und bereitstellen zeigt.](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>SCONE (Scontain)

[SCONE-Sicherheitsrichtlinien](https://scontain.com/) (Scontain) generieren Zertifikate, Schlüssel und Geheimnisse. Nur Dienste mit Nachweis für eine Anwendung sehen diese Anmeldeinformationen. Anwendungsdienste stellen automatisch über TLS einen Nachweis füreinander her. Sie müssen die Anwendungen oder TLS nicht ändern. Weitere Informationen finden Sie in der [Flask-Anwendungsdemo](https://sconedocs.github.io/flask_demo/)von SCONE.

SCONE kann die meisten vorhandenen Binärdateien in Anwendungen konvertieren, die in Enclaves ausgeführt werden. Sie müssen die Anwendung nicht ändern oder neu kompilieren. Darüber hinaus schützt SCONE interpretierte Sprachen wie Python, da sowohl Datendateien als auch Python-Codedateien verschlüsselt werden. Sie können SCONE-Sicherheitsrichtlinien verwenden, um verschlüsselte Dateien vor unberechtigtem Zugriff, Änderungen und Rollbacks zu schützen. Weitere Informationen finden Sie in der SCONE-Dokumentation [zur Verwendung von SCONE mit einer vorhandenen Python-Anwendung](https://sconedocs.github.io/sconify_image/).

![Diagramm des SCONE-Workflows, das zeigt, wie SCONE Binärbilder verarbeitet.](./media/confidential-containers/scone-workflow.png)

Sie können SCONE auf Azure Confidential Computing-Knoten mit AKS bereitstellen. Dieser Prozess wird vollständig unterstützt und integriert. Weitere Informationen finden Sie unter [Ausführen der SCONE-Beispielanwendung](https://sconedocs.github.io/aks/).

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) stellt SGX-Plattformsoftware bereit, mit der Container unverändert in AKS ausgeführt werden können. Weitere Informationen finden Sie in der [Dokumentation zu Funktionen und Beispielanwendungen](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)von Anjuna.

Beginnen Sie mit einer Redis Cache-Beispielanwendung und einer benutzerdefinierten Python-Anwendung [hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

![Diagramm des Prozesses von Anjuna, das zeigt, wie Container in Azure Confidential Computing ausgeführt werden.](media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Grundvoraussetzungen für OSS

> [!NOTE]
> Open-Source-Projekte bieten die folgenden Lösungen. Azure Confidential Computing und Microsoft sind diesen Projekten und Lösungen nicht direkt zugeordnet.  

### <a name="gramine"></a>Gramine

[Gramine](https://grapheneproject.io/) ist ein einfaches Gastbetriebssystem zur Ausführung einer einzelnen Linux-Anwendung mit minimalen Hostanforderungen. Gramine kann Anwendungen in einer isolierten Umgebung ausführen. Gramine unterstützt verschiedene Tools für das Konvertieren vorhandener Docker-Containeranwendungen in Graphene Shielded Containers-Instanzen (GSC).

Weitere Informationen finden Sie in Gramines [Beispielanwendung und -bereitstellung in AKS](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum

[Occlum](https://occlum.io/) ist ein arbeitsspeichersicheres Multiprozess-Bibliotheksbetriebssystem (LibOS) für Intel SGX. Damit können ältere Anwendungen mit nur geringfügigen oder ganz ohne Änderungen am Quellcode unter SGX ausgeführt werden. Occlum schützt die Vertraulichkeit von Benutzerworkloads auf transparente Weise und erlaubt gleichzeitig eine einfache Migration per Lift & Shift zu vorhandenen Dockeranwendungen.

Occlum unterstützt AKS-Bereitstellungen. Weitere Informationen finden Sie unter Occlums [Bereitstellungsanweisungen und Beispiel-Apps](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md).

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) ist ein Orchestrierungsframework für vertrauliche Container. Es erleichtert die Ausführung und Skalierung vertraulicher Dienste auf SGX-fähigem Kubernetes. Marblerun übernimmt Standardaufgaben wie das Überprüfen der Dienste in Ihrem Cluster, Verwalten der zugehörigen Geheimnisse und Herstellen von Enclave-to-Enclave-mTLS-Verbindungen zwischen ihnen. Marblerun stellt außerdem sicher, dass Ihr Cluster vertraulicher Container einem Manifest entspricht, das in einfachem JSON-Code definiert ist. Sie können das Manifest mit externen Clients per Remotenachweis überprüfen.

Kurz gesagt, erweitert Marblerun die Vertraulichkeits-, Integritäts- und Überprüfbarkeitseigenschaften einer einzelnen Enclave auf einen Kubernetes-Cluster.

Marblerun unterstützt vertrauliche Container, die mit Graphene, Occlum und EGo erstellt wurden, mit [Beispielen für jedes SDK](https://docs.edgeless.systems/marblerun/#/examples?id=examples). Das Framework wird in Kubernetes zusammen mit Ihren vorhandenen cloudnativen Tools ausgeführt. Es gibt eine CLI und Helm-Diagramme. Auch Confidential Computing-Knoten werden in AKS unterstützt. Befolgen Sie die Anleitung von Ergrun, [um Zulauf in AKS bereitzustellen.](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment)

## <a name="confidential-containers-demo"></a>Demo zu vertraulichen Containern

Eine Beispielanwendung finden Sie in der Demo zum [Gesundheitswesen mit vertraulichen Containern](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Setzen Sie sich mit uns in Verbindung

Haben Sie Fragen zu Ihrer Implementierung? Möchten Sie ein Enabler für vertrauliche Container werden? Senden Sie eine E-Mail mit Ihren Abonnement-IDs an „<acconaks@microsoft.com>.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines AKS-Clusters mit Vertraulichen Intel SGX-VM-Knoten](./confidential-enclave-nodes-aks-get-started.md)
- [Microsoft Azure Attestation](../attestation/overview.md)
- [Intel SGX Vertrauliche Virtuelle Maschinen](virtual-machine-solutions-sgx.md)
- [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
