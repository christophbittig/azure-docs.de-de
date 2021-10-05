---
title: Azure DevTest Labs-Konzepte
description: Lernen Sie die grundlegenden Konzepte von DevTest Labs kennen, und erfahren Sie, wie Sie virtuelle Azure-Computer damit einfach erstellen, verwalten und überwachen können.
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 0363eaec2d9485b34cc8dee10f50373363d43d51
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637235"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-Konzepte
## <a name="overview"></a>Übersicht
Die folgende Liste enthält wichtige DevTest Labs-Konzepte und Definitionen:

## <a name="labs"></a>Labs
Ein Lab ist die Infrastruktur, die eine Gruppe von Ressourcen wie z.B. virtuelle Computer (VMs) umfasst, mit der Sie diese Ressourcen besser verwalten können, indem Sie Einschränkungen und Kontingente angeben.

## <a name="virtual-machine"></a>Virtueller Computer
Ein virtueller Azure-Computer ist eine von mehreren [bedarfsgesteuerten, skalierbaren Computerressourcen](/azure/architecture/guide/technology-choices/compute-decision-tree), die von Azure angeboten werden. Virtuelle Azure-Computer bieten Ihnen flexible Virtualisierungsmöglichkeiten, ohne dass Sie für die Ausführung physische Hardware erwerben und verwalten müssen. Allerdings müssen Sie den virtuellen Computer weiterhin warten, indem Sie bestimmte Aufgaben ausführen, z.B. Konfigurieren, Patchen und Installieren der Software, die auf ihm ausgeführt wird.

Im Artikel [Übersicht über virtuelle Windows-Computer in Azure](../virtual-machines/windows/overview.md) erfahren Sie, was Sie vor der Erstellung eines virtuellen Computers berücksichtigen sollten und wie Sie ihn erstellen und verwalten.

## <a name="claimable-vm"></a>Anforderbarer virtueller Computer
Ein anforderbarer virtueller Azure-Computer ist ein virtueller Computer, der für jeden Benutzer mit Berechtigungen im Lab zur Verwendung verfügbar ist. Ein Lab-Administrator kann virtuelle Computer mit bestimmten Basisimages und Artefakten vorbereiten und in einem freigegebenen Pool speichern. Ein Lab-Benutzer kann dann einen funktionierenden virtuellen Computer aus dem Pool anfordern, wenn er einen Computer mit dieser spezifischen Konfiguration benötigt.

Ein anforderbarer virtueller Computer wird anfänglich keinem bestimmten Benutzer zugewiesen, er wird jedoch in der Liste jedes Benutzers unter „Claimable virtual machines“ (Anforderbare virtuelle Computer) angezeigt. Nachdem ein virtueller Computer von einem Benutzer angefordert wurde, wird er in dessen Bereich „My virtual machines“ (Meine virtuellen Computer) verschoben und kann von keinem anderen Benutzer mehr angefordert werden.

## <a name="environment"></a>Environment
In DevTest Labs bezieht sich eine Umgebung auf eine Sammlung von Azure-Ressourcen in einem Lab. In [diesem Artikel](./devtest-lab-create-environment-from-arm.md) erfahren Sie, wie Sie auf der Grundlage von Azure Resource Manager-Vorlagen Umgebungen mit mehreren VMs erstellen.

## <a name="base-images"></a>Basisimages
Basisimages sind ein VM-Images, in denen alle Tools und Einstellungen vorinstalliert und so konfiguriert sind, dass Sie schnell einen virtuellen Computer erstellen können. Sie können einen virtuellen Computer bereitstellen, indem Sie eine vorhandene Basis auswählen ein Artefakt hinzufügen, um Ihren Test-Agent zu installieren. Anschließend können Sie dann die bereitgestellte VM als Basis speichern, die dann verwendet werden kann, ohne den Test-Agent bei jeder Bereitstellung der VM erneut installieren zu müssen.

## <a name="artifacts"></a>Artefakte
Artefakte werden zum Bereitstellen und Konfigurieren Ihrer Anwendung nach der Bereitstellung eines virtuellen Computers verwendet. Bei Artefakten kann es sich um Folgendes handeln:

* Tools, die Sie auf dem virtuellen Computer installieren möchten, z. B. Agents, Fiddler oder Visual Studio.
* Aktionen, die auf dem virtuellen Computer ausgeführt werden sollen, z. B. das Klonen eines Repositorys.
* Anwendungen, die Sie testen möchten.

Artefakte sind [Azure Resource Manager](../azure-resource-manager/management/overview.md)-JSON-Dateien, die Anweisungen zum Ausführen der Bereitstellung und Übernehmen der Konfiguration enthalten.

## <a name="artifact-repositories"></a>Artefaktrepositorys
Artefaktrepositorys sind Git-Repositorys, in denen Artefakte eingecheckt werden. Artefaktrepositorys können mehreren Labs in Ihrer Organisation hinzugefügt werden, sodass sie wiederverwendet und geteilt werden können.

## <a name="formulas"></a>Formeln
Formeln bieten neben Basisimages einen Mechanismus für die schnelle Bereitstellung virtueller Computer. Eine Formel in DevTest Labs ist eine Liste standardmäßiger Eigenschaftswerte, die zum Erstellen eines virtuellen Labcomputers verwendet werden.
Mit Formeln können virtuelle Computer mit dem gleichen Satz von Eigenschaften (z.B. Basisimage, VM-Größe, virtuelles Netzwerk und Artefakte) erstellt werden, ohne dass diese Eigenschaften jedes Mal angeben werden müssen. Beim Erstellen eines virtuellen Computers aus einer Formel können die Standardwerte wie vorhanden verwendet oder geändert werden.

## <a name="policies"></a>Richtlinien
Richtlinien helfen dabei, die Kosten im Lab zu kontrollieren. Beispielsweise können Sie eine Richtlinie erstellen, um virtuelle Computer automatisch anhand eines definierten Zeitplans herunterzufahren.

## <a name="caps"></a>Obergrenzen
Obergrenzen sind ein Mechanismus zum Minimieren unnötiger Ressourcennutzung im Lab. Beispielsweise können Sie eine Obergrenze für die Anzahl der virtuellen Computer festlegen, die pro Benutzer oder in einem Lab erstellt werden können.

## <a name="security-levels"></a>Sicherheitsstufen
Der Sicherheitszugriff wird durch die rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC) bestimmt. Die Funktionsweise des Zugriffs ist leichter zu verstehen, wenn Sie die Unterschiede zwischen Berechtigungen, Rollen und Bereichen in Azure RBAC kennen.

* Berechtigung: Eine Berechtigung ist ein definierter Zugriff auf eine bestimmte Aktion (z.B. Lesezugriff auf alle virtuellen Computer).
* Rolle: Eine Rolle ist ein Satz von Berechtigungen, die gruppiert und einem Benutzer zugewiesen werden können. Zum Beispiel die Rolle *Abonnementbesitzer* verfügt über Zugriff auf alle Ressourcen in einem Abonnement.
* Bereich: Ein Bereich ist eine Ebene innerhalb der Azure-Ressourcenhierarchie (z.B. eine Ressourcengruppe, ein einzelnes Lab oder das gesamte Abonnement).

Innerhalb des Bereichs von DevTest Labs gibt es zwei Arten von Rollen zum Definieren von Benutzerberechtigungen: Labbesitzer und Labbenutzer.

* Labbesitzer: Ein Labbesitzer verfügt über Zugriff auf alle Ressourcen innerhalb des Labs. Daher kann ein Labbesitzer Richtlinien und das virtuelle Netzwerk ändern, verfügt über Lese- und Schreibberechtigungen für alle virtuellen Computer usw.
* Labbenutzer: Ein Labbenutzer kann alle Labressourcen anzeigen (z.B. virtuelle Computer, Richtlinien und virtuelle Netzwerke), aber keine Richtlinien oder von anderen Benutzern erstellte virtuelle Computer ändern.

Informationen zum Erstellen benutzerdefinierter Rollen in DevTest Labs finden Sie im Artikel [Gewähren von Benutzerberechtigungen für bestimmte Labrichtlinien](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Da Bereiche hierarchisch sind, gelten die Berechtigungen, die einem Benutzer für einen bestimmten Bereich gewährt werden, automatisch für alle darin enthaltenen Bereiche auf niedrigerer Ebene. Ist ein Benutzer beispielsweise der Rolle „Abonnementbesitzer“ zugewiesen, hat er Zugriff auf alle Ressourcen in einem Abonnement, d.h. auf alle virtuellen Computer, alle virtuellen Netzwerke und alle Labs. Der Abonnementbesitzer erbt somit automatisch die Rolle des Labbesitzers. Umgekehrt ist dies jedoch nicht der Fall. Ein Labbesitzer hat Zugriff auf ein Lab, d.h. auf einen Bereich, der sich innerhalb der Hierarchie auf einer niedrigeren Ebene befindet als das Abonnement. Aus diesem Grund kann ein Labbesitzer keine virtuellen Computer, virtuellen Netzwerke oder anderen Ressourcen außerhalb des Labs anzeigen.

## <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen
Alle in diesem Artikel erörterten Konzepte können mithilfe von Azure Resource Manager-Vorlagen konfiguriert werden. Dies ermöglicht Ihnen das Definieren der Infrastruktur/Konfiguration Ihrer Azure-Lösung und deren wiederholte und konsistente Bereitstellung.

In [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/syntax.md#template-format) werden die Struktur einer Azure Resource Manager-Vorlage und die verfügbaren Eigenschaften in den verschiedenen Abschnitten einer Vorlage beschrieben.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
[Erstellen eines Labs in Azure DevTest Labs](devtest-lab-create-lab.md)