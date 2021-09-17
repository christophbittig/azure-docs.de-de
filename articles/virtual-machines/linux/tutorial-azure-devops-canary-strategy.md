---
title: Konfigurieren von Canary-Bereitstellungen für virtuelle Linux-Computer in Azure
description: Hier erfahren Sie, wie Sie eine CD-Pipeline (Continuous Deployment) einrichten. Diese Pipeline aktualisiert eine Gruppe virtueller Linux-Computer in Azure unter Verwendung der Canary-Bereitstellungsstrategie.
author: moala
tags: azure-devops-pipelines
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: f9ab4712e6419303f010d7cf1a417a8a907f9a05
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690947"
---
# <a name="configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Konfigurieren der Canary-Bereitstellungsstrategie für virtuelle Linux-Computer in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs 

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure-as-a-Service (IaaS): Konfigurieren von CI/CD

Azure Pipelines verfügt über einen umfassenden Satz an CI/CD-Automatisierungstools für Bereitstellungen auf virtuellen Computern. Sie können eine Continuous Delivery-Pipeline für einen virtuellen Azure-Computer über das Azure-Portal konfigurieren.

In diesem Artikel erfahren Sie, wie Sie eine CI/CD-Pipeline mit Canary-Strategie für Bereitstellungen auf mehreren Computern einrichten. Vom Azure-Portal werden auch andere Strategien wie etwa die [parallele Strategie](./tutorial-devops-azure-pipelines-classic.md) und die [Blau-Grün-Strategie](./tutorial-azure-devops-blue-green-strategy.md) unterstützt.

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurieren von CI/CD auf virtuellen Computern

Sie können virtuelle Computer als Ziele zu einer [Bereitstellungsgruppe](/azure/devops/pipelines/release/deployment-groups) hinzufügen. Diese können dann als Ziel für die Aktualisierung mehrerer Computer verwendet werden. Sehen Sie sich nach der Bereitstellung auf Computern den **Bereitstellungsverlauf** in einer Bereitstellungsgruppe an. In dieser Ansicht können Sie eine Ablaufverfolgung vom virtuellen Computer zur Pipeline und dann zum Commit durchführen.

### <a name="canary-deployments"></a>Canary-Bereitstellungen

Eine Canary-Bereitstellung trägt zur Risikominimierung bei, indem Änderungen vorerst nur für einen Teil der Benutzer eingeführt werden. Wenn Sie mehr Vertrauen in die neue Version haben, können Sie sie für weitere Server in Ihrer Infrastruktur freigeben und mehr Benutzer an diese Version weiterleiten.

Mithilfe der Continuous Delivery-Option können Sie Canary-Bereitstellungen für Ihre virtuellen Computer über das Azure-Portal konfigurieren. Gehen Sie dazu wie folgt vor:

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu einem virtuellen Computer.
1. Wählen Sie im Bereich ganz links der VM-Einstellungen die Option **Continuous Delivery** aus. Klicken Sie anschließend auf **Konfigurieren**.

   ![Bereich „Continuous Delivery“ mit der Schaltfläche „Konfigurieren“](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Wählen Sie im Konfigurationsbereich **Azure DevOps-Organisation** aus, um ein vorhandenes Konto auszuwählen oder ein neues Konto zu erstellen. Wählen Sie anschließend das Projekt aus, unter dem Sie die Pipeline konfigurieren möchten.  

   ![Bereich „Continuous Delivery“](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Eine Bereitstellungsgruppe ist eine logische Gruppe mit Bereitstellungszielcomputern, die die physischen Umgebungen darstellen. Beispiele hierfür sind „Entwicklung“, „Test“, „UAT“ und „Produktion“. Sie können eine neue Bereitstellungsgruppe erstellen oder eine vorhandene auswählen.
1. Wählen Sie die Buildpipeline aus, die das Paket veröffentlicht, das auf dem virtuellen Computer bereitgestellt werden soll. Das veröffentlichte Paket muss im Paketstammverzeichnis im Ordner „deployscripts“ ein Bereitstellungsskript namens „deploy.ps1“ oder „deploy.sh“ enthalten. Die Pipeline führt dieses Bereitstellungsskript aus.
1. Wählen Sie unter **Bereitstellungsstrategie** die Option **Canary** aus.
1. Fügen Sie virtuellen Computern, die Teil von Canary-Bereitstellungen sein sollen, ein Tag vom Typ „canary“ hinzu. Versehen Sie virtuelle Computer, die für Bereitstellungen nach erfolgreicher Canary-Bereitstellung vorgesehen sind, mit einem Tag vom Typ „prod“. Durch Tags können Sie einfacher ausschließlich virtuelle Computer mit einer spezifischen Rolle als Ziel verwenden.

   ![Bereich „Continuous Delivery“ mit ausgewählter Canary-Bereitstellungsstrategie](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Wählen Sie **OK** aus, um die Continuous Delivery-Pipeline für die Bereitstellung auf dem virtuellen Computer zu konfigurieren.

   ![Canary-Pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Die Bereitstellungsdetails für den virtuellen Computer werden angezeigt. Sie können den Link auswählen, um zur Releasepipeline in Azure DevOps zu gelangen. Wählen Sie in der Releasepipeline **Bearbeiten** aus, um die Pipelinekonfiguration anzuzeigen. Die Pipeline umfasst drei Phasen:

   1. Diese Phase ist eine Bereitstellungsgruppenphase. Anwendungen werden auf virtuellen Computern bereitgestellt, die mit dem Tag „canary“ versehen wurden.
   1. In dieser Phase wird die Pipeline angehalten und auf einen manuellen Eingriff zur Fortsetzung der Ausführung gewartet.
   1. Diese Phase ist wieder eine Bereitstellungsgruppenphase. Das Update wird nun auf virtuellen Computern bereitgestellt, die mit dem Tag „prod“ versehen wurden.

      ![Bereich „Bereitstellungsgruppe“ für die Canary-Bereitstellungsaufgabe](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Vergewissern Sie sich, dass mindestens ein virtueller Computer mit dem Tag „prod“ vorhanden ist, bevor Sie die Pipelineausführung fortsetzen. In der dritten Phase der Pipeline werden Anwendungen nur auf virtuellen Computern bereitgestellt, die über das Tag „prod“ verfügen.

1. Durch die Aufgabe „Execute Deploy Script“ (Bereitstellungsskript ausführen) wird standardmäßig das Bereitstellungsskript „deploy.ps1“ oder „deploy.sh“ ausgeführt. Das Skript befindet sich im Ordner „deployscripts“ im Stammverzeichnis des veröffentlichten Pakets. Vergewissern Sie sich, dass die Bereitstellung durch die ausgewählte Buildpipeline im Stammordner des Pakets veröffentlicht wird.

   ![Bereich „Artefakte“ mit „deploy.sh“ im Ordner „deployscripts“](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Weitere Bereitstellungsstrategien
- [Tutorial: Konfigurieren der Strategie für parallele Bereitstellungen für virtuelle Linux-Computer in Azure](./tutorial-devops-azure-pipelines-classic.md)
- [Tutorial: Konfigurieren der Blau-Grün-Bereitstellungsstrategie für virtuelle Linux-Computer in Azure](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Der Einstieg ist mit Azure ganz einfach. Starten Sie mit Azure DevOps Projects die Ausführung Ihrer Anwendung in einem beliebigen Azure-Dienst mit nur drei Schritten, indem Sie Folgendes auswählen:

- Anwendungssprache
- Runtime
- Azure-Dienst

[Weitere Informationen](https://azure.microsoft.com/features/devops-projects/)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorial: Bereitstellen einer ASP.NET-App auf virtuellen Azure-Computern mithilfe von Azure DevOps Starter](../../devops-project/azure-devops-project-vms.md)
- [Implementieren von Continuous Deployment für Ihre App in einer VM-Skalierungsgruppe von Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)