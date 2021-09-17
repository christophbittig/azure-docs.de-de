---
title: 'Tutorial: Konfigurieren von parallelen Bereitstellungen für virtuelle Linux-Computer in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie eine CD-Pipeline (Continuous Deployment) einrichten. Diese Pipeline aktualisiert inkrementell eine Gruppe von virtuellen Azure-Linux-Computern mithilfe der Strategie für die parallele Bereitstellung.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 5212357fe123d722b7dbb853c874190a3ef9dee0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697720"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial: Konfigurieren der Strategie für parallele Bereitstellungen für virtuelle Linux-Computer in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs

Azure DevOps ist ein integrierter Azure-Dienst, bei dem alle Teile des DevOps-Prozesses für alle Azure-Ressourcen automatisiert werden. Unabhängig davon, ob für Ihre App virtuelle Computer, Web-Apps, Kubernetes oder andere Ressourcen genutzt werden, gilt Folgendes: Sie können Infrastructure-as-Code (IaC), Continuous Integration, fortlaufende Tests, Continuous Delivery und fortlaufende Überwachung mit Azure und Azure DevOps implementieren.

![Azure-Portal mit ausgewählter Option „Azure DevOps“ unter „Dienste“](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure-as-a-Service (IaaS): Konfigurieren von CI/CD

Azure Pipelines verfügt über einen umfassenden Satz an CI/CD-Automatisierungstools für Bereitstellungen auf virtuellen Computern. Sie können eine Continuous Delivery-Pipeline für einen virtuellen Azure-Computer über das Azure-Portal konfigurieren.

In diesem Artikel wird gezeigt, wie Sie über das Azure-Portal eine CI/CD-Pipeline für parallele Bereitstellungen auf mehreren Computern einrichten. Vom Azure-Portal werden auch andere Strategien wie die [Canary-Strategie](./tutorial-azure-devops-canary-strategy.md) und die [Blau-Grün-Strategie](./tutorial-azure-devops-blue-green-strategy.md) unterstützt.

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurieren von CI/CD auf virtuellen Computern

Sie können virtuelle Computer als Ziele zu einer [Bereitstellungsgruppe](/azure/devops/pipelines/release/deployment-groups) hinzufügen. Diese können dann als Ziel für die Aktualisierung mehrerer Computer verwendet werden. Sehen Sie sich nach der Bereitstellung auf Computern den **Bereitstellungsverlauf** in einer Bereitstellungsgruppe an. In dieser Ansicht können Sie eine Ablaufverfolgung vom virtuellen Computer zur Pipeline und dann zum Commit durchführen.

### <a name="rolling-deployments"></a>Parallele Bereitstellungen

In jeder Iterationen ersetzt eine parallele Bereitstellung Instanzen der früheren Version einer Anwendung. Sie ersetzt sie durch Instanzen der neuen Version auf einer festgelegten Gruppe von Computern (Gruppe für parallele Vorgänge). In der folgenden exemplarischen Vorgehensweise wird gezeigt, wie Sie ein paralleles Update für virtuelle Computer konfigurieren.

Mithilfe der Continuous Delivery-Option können Sie im Azure-Portal parallele Updates für Ihre virtuellen Computer konfigurieren. Nachfolgend ist die ausführliche exemplarische Vorgehensweise angegeben:

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu einem virtuellen Computer.
1. Wählen Sie im Bereich ganz links der VM-Einstellungen die Option **Continuous Delivery** aus. Klicken Sie anschließend auf **Konfigurieren**.

   ![Bereich „Continuous Delivery“ mit der Schaltfläche „Konfigurieren“](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Wählen Sie im Konfigurationsbereich **Azure DevOps-Organisation** aus, um ein vorhandenes Konto auszuwählen oder ein neues Konto zu erstellen. Wählen Sie anschließend das Projekt aus, unter dem Sie die Pipeline konfigurieren möchten.  

   ![Bereich „Continuous Delivery“](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Eine Bereitstellungsgruppe ist eine logische Gruppe mit Bereitstellungszielcomputern, die die physischen Umgebungen darstellen. Beispiele hierfür sind „Entwicklung“, „Test“, „UAT“ und „Produktion“. Sie können eine neue Bereitstellungsgruppe erstellen oder eine vorhandene auswählen.
1. Wählen Sie die Buildpipeline aus, die das Paket veröffentlicht, das auf dem virtuellen Computer bereitgestellt werden soll. Das veröffentlichte Paket muss im Paketstammverzeichnis im Ordner „deployscripts“ ein Bereitstellungsskript namens „deploy.ps1“ oder „deploy.sh“ enthalten. Die Pipeline führt dieses Bereitstellungsskript aus.
1. Wählen Sie unter **Bereitstellungsstrategie** die Option **Parallel** aus.
1. Optional können Sie jeden Computer mit seiner Rolle taggen. Beispiele sind die Tags „“web“ und „db“. Durch diese Tags können Sie einfacher ausschließlich virtuelle Computer mit einer spezifischen Rolle als Ziel verwenden.
1. Wählen Sie **OK** aus, um die Continuous Delivery-Pipeline zu konfigurieren.
1. Nach der Konfiguration verfügen Sie über eine Continuous Delivery-Pipeline, die für die Bereitstellung auf dem virtuellen Computer konfiguriert ist.  

   ![Bereich „Continuous Delivery“ mit Bereitstellungsverlauf](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Die Bereitstellungsdetails für den virtuellen Computer werden angezeigt. Sie können den Link auswählen, um zur Pipeline **Release-1** zu navigieren und die Bereitstellung anzuzeigen. Wählen Sie alternativ **Bearbeiten** aus, um die Definition der Releasepipeline zu ändern.

1. Wenn Sie mehrere virtuelle Computer konfigurieren, wiederholen Sie die Schritte 2 bis 4 für andere virtuelle Computer, die der Bereitstellungsgruppe hinzugefügt werden sollen. Wenn Sie eine Bereitstellungsgruppe auswählen, für die bereits eine Pipeline ausgeführt wird, werden die virtuellen Computer nur der Bereitstellungsgruppe hinzugefügt. Es werden keine neuen Pipelines erstellt.
1. Wählen Sie nach Abschluss der Konfiguration die Pipelinedefinition aus, navigieren Sie zur Azure DevOps-Organisation, und wählen Sie **Bearbeiten** für die Releasepipeline aus.

   ![Bearbeiten der parallelen Pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Wählen Sie in der Phase **Entwicklung** die Option **1 job, 1 task** (1 Auftrag, 1 Aufgabe) aus. Wählen Sie die Phase **Bereitstellen** aus.

   ![Parallele Pipelineaufgaben mit ausgewählter Bereitstellungsaufgabe](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. Im Konfigurationsbereich ganz rechts können Sie die Anzahl von Computern angeben, die pro Iteration parallel bereitgestellt werden sollen. Falls die Bereitstellung gleichzeitig auf mehreren Computern erfolgen soll, können Sie die Anzahl von Computern mithilfe des Schiebereglers als Prozentsatz angeben.  

1. Durch die Aufgabe „Execute Deploy Script“ (Bereitstellungsskript ausführen) wird standardmäßig das Bereitstellungsskript „deploy.ps1“ oder „deploy.sh“ ausgeführt. Das Skript befindet sich im Ordner „deployscripts“ im Stammverzeichnis des veröffentlichten Pakets.

   ![Bereich „Artefakte“ mit „deploy.sh“ im Ordner „deployscripts“](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Weitere Bereitstellungsstrategien

- [Tutorial: Konfigurieren der Canary-Bereitstellungsstrategie für virtuelle Linux-Computer in Azure](./tutorial-azure-devops-canary-strategy.md)
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