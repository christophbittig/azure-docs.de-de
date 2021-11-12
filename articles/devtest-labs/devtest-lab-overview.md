---
title: Was ist Azure DevTest Labs?
description: Erfahren Sie, wie DevTest Labs das Erstellen, Verwalten und Überwachen von virtuellen Azure-Computern erleichtern kann.
ms.topic: overview
ms.date: 10/20/2021
ms.openlocfilehash: f521b1fcd886d56387c9b6c44451d1858f95d62f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229055"
---
# <a name="what-is-azure-devtest-labs"></a>Was ist Azure DevTest Labs?
Azure DevTest Labs ist ein Dienst, mit dem Entwickler virtuelle Computer (VMs) und PaaS-Ressourcen (Platform-as-a-Service) effizient selbst verwalten können, ohne auf Genehmigungen warten zu müssen. DevTest Labs erstellt Labs, die aus vorkonfigurierten Datenbanken oder Azure Resource Manager-Vorlagen bestehen. Diese Labs umfassen alle notwendigen Tools und die Software, mit denen Sie Umgebungen erstellen können.

Mit DevTest Labs können Sie die neueste Version Ihrer Anwendungen testen, indem Sie die folgenden Aufgaben ausführen:

- Schnelles Erstellen von Linux- und Windows-Umgebungen mithilfe wiederverwendbarer Vorlagen und Artefakte
- Einfaches Integrieren Ihrer Bereitstellungspipeline in DevTest Labs, um Umgebungen bedarfsabhängig zu erstellen
- Hochskalieren Ihrer Auslastungstests, indem mehrere Test-Agents und bereits vorbereitete Umgebungen für Training und Demos erstellt werden

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="cost-control-and-governance"></a>Kostenkontrolle und Governance
DevTest Labs erleichtert die Kostenkontrolle, da Sie die folgenden Aufgaben erledigen können:

- [Festlegen von Richtlinien für Ihre Labs](devtest-lab-set-lab-policy.md), z.B. die Anzahl der virtuellen Computer pro Benutzer oder pro Lab. 
- Festlegen von [Richtlinien für das automatische Herunter- und Hochfahren](devtest-lab-set-lab-policy.md) von VMs.
- Nachverfolgen der Kosten für virtuelle Computer und PaaS-Ressourcen, die in den Labs entstanden sind, um [Ihr Budget](devtest-lab-configure-cost-management.md) einzuhalten. Benachrichtigungen über hohe voraussichtliche Kosten für Labs, damit Sie die notwendigen Maßnahmen ergreifen können.
- Einhalten des Kontextbegrenzungen Ihrer Labs, damit Sie keine Ressourcen außerhalb verbrauchen.

## <a name="quickly-get-to-ready-to-test"></a>Schnelles Erreichen der Testphase
Mit DevTest Labs können Sie vorbereitete Umgebungen zum Entwickeln und Testen von Anwendungen erstellen. [Fordern Sie einfach die Umgebung](devtest-lab-add-claimable-vm.md) des letzten als funktionierend bekannten Builds Ihrer Anwendung an, und beginnen Sie mit der Arbeit. Um die Erstellung der Umgebung zusätzlich zu beschleunigen und zu vereinfachen, können Sie alternativ auch Container verwenden.

## <a name="create-once-use-everywhere"></a>Einmal erstellen, überall verwenden
Indem Sie Paas-[Umgebungsvorlagen](devtest-lab-create-environment-from-arm.md) und [-Artefakte](add-artifact-repository.md) erfassen und innerhalb Ihres Teams oder Ihrer Organisation freigeben, können Sie problemlos Entwicklungs- und Testumgebungen erstellen – und das alles im Rahmen der Quellcodeverwaltung.

## <a name="worry-free-self-service"></a>Self-Service-Bereitstellung für sorgenfreies Arbeiten
Mit DevTest Labs können Ihre Entwickler und Tester schnell und einfach [IaaS-VMs](devtest-lab-add-vm.md) und [PaaS-Ressourcen erstellen](devtest-lab-create-environment-from-arm.md), indem sie eine Reihe von für sie vorkonfigurierten Ressourcen nutzen.

## <a name="use-iaas-and-paas-resources"></a>Verwenden von IaaS- und PaaS-Ressourcen 
Richten Sie Ressourcen wie z. B. Azure Service Fabric-Cluster oder SharePoint-Farmen mithilfe von Resource Manager-Vorlagen ein. Nutzen Sie die Vorlagen im [Repository für öffentliche Umgebungen](devtest-lab-configure-use-public-environments.md), oder [verbinden Sie das Lab mit Ihrem eigenen Git-Repository](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Sie können auch eine leere Ressourcengruppe (Sandbox) mithilfe einer Resource Manager-Vorlage einrichten, um Azure im Kontext des Labs zu erkunden.

## <a name="integrate-with-your-existing-toolchain"></a>Integration in Ihre bestehende Toolkette
Nutzen Sie unsere sofort einsatzbereiten Plug-Ins oder die API, um Entwicklungs-/Testumgebungen direkt über Ihr bevorzugtes [CI-Tool (Continuous Integration)](devtest-lab-integrate-ci-cd.md), Ihre bevorzugte integrierte Entwicklungsumgebung (IDE) oder Ihre bevorzugte automatisierte Veröffentlichungspipeline zu erstellen. Darüber hinaus steht Ihnen das umfassende Befehlszeilentool zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- Weitere Informationen zu DevTest Labs finden Sie unter [DevTest Labs-Konzepte](devtest-lab-concepts.md).
- Eine exemplarische Vorgehensweise mit Schritt-für-Schritt-Anleitung finden Sie im Tutorial [Erstellen eines Labs mithilfe von Azure DevTest Labs](tutorial-create-custom-lab.md).
