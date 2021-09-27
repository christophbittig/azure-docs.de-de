---
title: Informationen zu Azure DevTest Labs
description: Erfahren Sie, wie DevTest Labs das Erstellen, Verwalten und Überwachen von virtuellen Azure-Computern erleichtern kann.
ms.topic: overview
ms.date: 08/20/2021
ms.openlocfilehash: cad1bb218722c72d6b7f7c2d28ad92a460e3d051
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563168"
---
# <a name="about-azure-devtest-labs"></a>Informationen zu Azure DevTest Labs
Azure DevTest Labs ermöglicht es Entwicklern in Teams, virtuelle Computer (VMs) und PaaS-Ressourcen effizient selbst zu verwalten, ohne auf Genehmigungen warten zu müssen.

DevTest Labs erstellt Labs, die aus vorkonfigurierten Datenbanken oder Azure Resource Manager-Vorlagen bestehen. Diese verfügen über alle notwendigen Tools und die Software, mit denen Sie Umgebungen erstellen können. Sie können so Ihre Umgebungen in wenigen Minuten statt mehreren Stunden oder Tagen erstellen.

Mit DevTest Labs können Sie die neueste Version Ihrer Anwendungen testen, indem Sie die folgenden Aufgaben ausführen:

- Schnelles Bereitstellen von Linux- und Windows-Umgebungen mithilfe wiederverwendbarer Vorlagen und Artefakte.
- Integrieren Sie Ihre Bereitstellungspipeline einfach mit DevTest Labs, um Umgebungen bei Bedarf bereitstellen zu können.
- Zentrales Hochskalieren Ihrer Auslastungstests, indem mehrere Test-Agents bereitgestellt werden, und Erstellen vorab bereitgestellter Umgebungen für Training und Demos

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funktionen
DevTest Labs bietet Entwicklern, die mit virtuellen Computern arbeiten, die folgenden Möglichkeiten:

- Schnelle Erstellung von virtuellen Computern mit weniger als fünf einfachen Schritten.
- Wählen Sie aus einer übersichtlichen Liste von Basis-VMs, die vom Teamleiter oder der zentralen IT-Abteilung konfiguriert, genehmigt und autorisiert werden.
- Erstellen Sie VMs anhand von vorab erstellten benutzerdefinierten Images, bei denen sämtliche Software und Tools bereits installiert sind. 
- Erstellen Sie VMs aus Formeln, die im Wesentlichen benutzerdefinierte Images darstellen, die mit den neuesten Builds der beim Erstellen der VMs installierten Software kombiniert werden. 
- Installieren Sie Artefakte, bei denen es sich um Erweiterungen handelt, die auf virtuellen Computern nach ihrer Inbetriebnahme bereitgestellt werden.
- Legen Sie die Zeitpläne für das automatische Herunterfahren und Starten für virtuelle Computer fest.
- Beanspruchen Sie einen vorab erstellten virtuellen Computer, ohne den Erstellungsprozess zu durchlaufen.

DevTest Labs bietet Entwicklern, die mit PaaS-Umgebungen arbeiten, die folgenden Möglichkeiten:

- Mit dem Resource Manager können Sie PaaS-Umgebungen schnell erstellen, indem Sie weniger als drei einfache Schritte ausführen.
- Wählen Sie aus einer übersichtlichen Liste von Resource Manager-Vorlagen, die vom Teamleiter oder der zentralen IT-Abteilung konfiguriert und autorisiert werden.
- Richten Sie eine leere Ressourcengruppe (Sandbox) mithilfe einer Resource Manager-Vorlage ein, um Azure zu erkunden, während Sie im Kontext des Labs bleiben.

DevTest Labs ermöglicht der zentralen IT-Abteilung, die Verschwendung von Ressourcen zu kontrollieren, deren Kosten zu optimieren und das Budget einzuhalten, indem folgende Aufgaben ausgeführt werden:  

- Legen Sie die Zeitpläne für das automatische Herunterfahren und Starten für virtuelle Computer fest.
- Legen Sie Richtlinien für die Anzahl virtueller Computer fest, die der Benutzer erstellen kann.
- Legen Sie Richtlinien für die Größen der virtuellen Computer und Katalogimages fest, aus denen die Benutzer auswählen können.
- Verfolgen Sie die Kosten nach, und legen Sie Ziele in den Labs fest.
- Weisen Sie auf hohe voraussichtliche Kosten für Labs hin, damit Sie die notwendigen Maßnahmen ergreifen können.

DevTest Labs bietet beim Erstellen, Konfigurieren und Verwalten von Umgebungen in der Cloud die folgenden Vorteile.

## <a name="cost-control-and-governance"></a>Kostenkontrolle und Governance
DevTest Labs erleichtert die Kostenkontrolle, da Sie die folgenden Aufgaben erledigen können:

- [Festlegen von Richtlinien für Ihre Labs](devtest-lab-set-lab-policy.md), z.B. die Anzahl der virtuellen Computer pro Benutzer oder pro Lab. 
- Festlegen von [Richtlinien für das automatische Herunter- und Hochfahren](devtest-lab-set-lab-policy.md) von VMs.
- Nachverfolgen der Kosten für virtuelle Computer und PaaS-Ressourcen, die in den Labs entstanden sind, um [Ihr Budget](devtest-lab-configure-cost-management.md) einzuhalten.
- Einhalten des Kontextbegrenzungen Ihrer Labs, damit Sie keine Ressourcen außerhalb verbrauchen.

## <a name="quickly-get-to-ready-to-test"></a>Schnelles Erreichen der Testphase
DevTest Labs ermöglicht Ihnen, Umgebungen mit allen erforderlichen Elementen, die Ihr Team zum Entwickeln und Testen von Anwendungen benötigt, im Voraus zu erstellen und bereitzustellen. [Übernehmen Sie einfach die Umgebungen](devtest-lab-add-claimable-vm.md), in denen der letzte funktionsfähige Build Ihrer Anwendung installiert ist, und beginnen Sie mit der Arbeit. Um die Erstellung der Umgebung zusätzlich zu beschleunigen und zu vereinfachen, können Sie alternativ auch Container verwenden.

## <a name="create-once-use-everywhere"></a>Einmal erstellen, überall verwenden
Indem Sie Paas-[Umgebungsvorlagen](devtest-lab-create-environment-from-arm.md) und [-Artefakte](add-artifact-repository.md) erfassen und innerhalb Ihres Teams oder Ihrer Organisation freigeben, können Sie problemlos Entwicklungs- und Testumgebungen erstellen – und das alles im Rahmen der Quellcodeverwaltung.

## <a name="worry-free-self-service"></a>Self-Service-Bereitstellung für sorgenfreies Arbeiten
Mit DevTest Labs können Ihre Entwickler und Tester schnell und einfach [IaaS-VMs](devtest-lab-add-vm.md) und [PaaS-Ressourcen erstellen](devtest-lab-create-environment-from-arm.md), indem sie eine Reihe von für sie vorkonfigurierten Ressourcen nutzen.

## <a name="use-iaas-and-paas-resources"></a>Verwenden von IaaS- und PaaS-Ressourcen 
Entwickler können auch PaaS-Ressourcen wie Azure Service Fabric-Cluster, das Web-Apps-Feature von Azure App Service und SharePoint-Farmen mithilfe von Resource Manager-Vorlagen entwickeln. Nutzen Sie die Vorlagen im [Repository für die öffentliche Umgebung](devtest-lab-configure-use-public-environments.md), oder [verbinden Sie das Lab mit Ihrem eigenen Git-Repository](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories), um in Labs die ersten Schritte mit PaaS zu unternehmen. Sie können auch die Kosten für diese Ressourcen nachverfolgen, um innerhalb Ihres Budgets zu bleiben.

## <a name="integrate-with-your-existing-toolchain"></a>Integration in Ihre bestehende Toolkette
Nutzen Sie unsere sofort einsatzbereiten Plug-Ins oder die API, um Entwicklungs-/Testumgebungen direkt über Ihr bevorzugtes [CI-Tool (Continuous Integration)](devtest-lab-integrate-ci-cd.md), Ihre bevorzugte integrierte Entwicklungsumgebung (IDE) oder Ihre bevorzugte automatisierte Veröffentlichungspipeline bereitzustellen. Darüber hinaus steht Ihnen das umfassende Befehlszeilentool zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- Weitere Informationen zu DevTest Labs finden Sie unter [DevTest Labs-Konzepte](devtest-lab-concepts.md).
- Eine exemplarische Vorgehensweise mit Schritt-für-Schritt-Anleitung finden Sie im Tutorial [Erstellen eines Labs mithilfe von Azure DevTest Labs](tutorial-create-custom-lab.md).
