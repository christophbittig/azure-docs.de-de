---
title: Best Practices für Entwickler – Containerimageverwaltung in Azure Kubernetes Service (AKS)
description: Lernen Sie die bewährten Methoden für den Clusteroperator zum Verwalten und Absichern von Containerimages in Azure Kubernetes Service (AKS) kennen.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e6c767d1fdf765739f361b91df21e5bc104bc171
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306527"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Best Practices für Containerimageverwaltung und Sicherheit in Azure Kubernetes Service (AKS)

Beim Entwickeln und Ausführen von Anwendungen in Azure Kubernetes Service (AKS) hat die Sicherheit von Containern und Containerimages höchste Priorität. Container mit veralteten Basisimages oder nicht gepatchten Anwendungsruntimes stellen ein Sicherheitsrisiko und einen möglichen Angriffsvektor dar. 

Minimieren Sie Risiken, indem Sie Überprüfungs- und Wartungstools zur Build- und Laufzeit in Ihre Container integrieren und ausführen. Je früher Sie das Sicherheitsrisiko oder das veraltete Basisimage erkennen, desto sicherer ist Ihr Cluster. 

In diesem Artikel hat *Container* zwei Bedeutungen:
* In einer Containerregistrierung gespeicherte Containerimages
* Ausgeführte Container

In diesem Artikel wird erläutert, wie Container in AKS gesichert werden. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Überprüfen auf und Beheben von Imageschwachstellen
> * Automatisches Triggern und erneutes Bereitstellen von Containerimages, wenn ein Basisimage aktualisiert wird

Weitere Informationen finden Sie in den Best Practices für [Clustersicherheit][best-practices-cluster-security] und [Podsicherheit][best-practices-pod-security].

Sie können auch [Containersicherheit in Defender für Cloud][security-center-containers] verwenden, um die Container auf Sicherheitsrisiken zu überprüfen. Dank der [Integration von Azure Container Registry][security-center-acr] in Defender für Cloud können Sie Ihre Images und die Registrierung vor Sicherheitsrisiken schützen.

## <a name="secure-the-images-and-run-time"></a>Sichern von Images und Runtime

> **Best Practices-Leitfaden** 
>
> Überprüfen Sie Ihre Containerimages auf Sicherheitsrisiken. Stellen Sie nur überprüfte Images bereit. Aktualisieren Sie regelmäßig die Basisimages und die Anwendungsruntime. Stellen Sie Workloads erneut im AKS-Cluster bereit.

Wenn Sie containerbasierte Workloads einführen, sollten Sie die Sicherheit von Images und Runtime überprüfen, die zum Erstellen eigener Anwendungen verwendet werden. Wie vermeiden Sie die Einführung von Sicherheitsrisiken in Ihre Bereitstellungen? 
* Fügen Sie in Ihren Bereitstellungsworkflow einen Prozess zum Überprüfen von Containerimages mit Tools wie [Twistlock][twistlock] oder [Aqua][aqua] ein.
* Lassen Sie nur die Bereitstellung verifizierter Images zu.

![Überprüfen von Containerimages, Beheben von Schwachstellen, Validieren und Bereitstellen](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Sie können beispielsweise eine CI/CD-Pipeline (Continuous Integration/Continuous Deployment) verwenden, um Imagescans, Verifizierung und Bereitstellung zu automatisieren. Die Azure Container Registry enthält diese Funktionen zur Überprüfung auf Schwachstellen.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatisches Erstellen neuer Images bei der Aktualisierung des Basisimages

> **Best Practices-Leitfaden** 
>
> Verwenden Sie bei der Nutzung von Basisimages für Anwendungsimages die Automatisierung, um neue Images zu erstellen, wenn das Basisimage aktualisiert wird. Da aktualisierte Basisimages in der Regel Sicherheitskorrekturen enthalten, aktualisieren Sie alle nachfolgenden Containerimages der Anwendung.

Jedes Mal, wenn ein Basisimage aktualisiert wird, sollten Sie auch alle nachfolgenden Containerimages aktualisieren. Integrieren Sie diesen Buildprozess in Validierungs- und Bereitstellungspipelines wie [Azure Pipelines][azure-pipelines] oder Jenkins. Diese Pipelines stellen sicher, dass Ihre Anwendungen weiterhin auf den aktualisierten Basisimages ausgeführt werden. Sobald Ihre Anwendungscontainerimages validiert wurden, können die AKS-Bereitstellungen aktualisiert werden, um die neuesten, sicheren Images auszuführen.

Azure Container Registry Tasks können Containerimages auch automatisch aktualisieren, wenn das Basisimage aktualisiert wird. Mit diesem Feature erstellen Sie einige Basisimages und halten sie mit Fehlerkorrekturen und Sicherheitsfixes auf dem neuesten Stand.

Weitere Informationen zu Basisimageaktualisierungen finden Sie unter [Automatisieren von Buildvorgängen für Images nach der Aktualisierung des Basisimages mit Azure Container Registry Tasks][acr-base-image-update].

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Container gesichert werden. Wenn Sie einige dieser Best Practices implementieren möchten, lesen Sie folgende Artikel:

* [Automatisieren von Buildvorgängen für Images nach der Aktualisierung des Basisimages mit Azure Container Registry Tasks][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md
