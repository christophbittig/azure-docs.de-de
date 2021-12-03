---
title: Azure DevTest Labs-Konzepte
description: Lernen Sie die grundlegenden Konzepte von DevTest Labs kennen, und erfahren Sie, wie Sie virtuelle Azure-Computer damit einfach erstellen, verwalten und überwachen können.
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: e66e3180900495d0d481742ab0fed172e25d9f3b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475899"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-Konzepte

In diesem Artikel sind die wichtigsten DevTest Labs-Konzepte und -Definitionen beschrieben:

## <a name="lab"></a>Labor
Ein Lab ist die Infrastruktur, die eine Gruppe von Ressourcen wie z.B. virtuelle Computer (VMs) umfasst, mit der Sie diese Ressourcen besser verwalten können, indem Sie Einschränkungen und Kontingente angeben.

## <a name="virtual-machine"></a>Virtueller Computer
Ein virtueller Azure-Computer ist eine Art [bedarfsgesteuerter, skalierbarer Computerressourcen](/azure/architecture/guide/technology-choices/compute-decision-tree), die von Azure angeboten werden. Virtuelle Azure-Computer bieten Ihnen die Flexibilität der Virtualisierung, ohne Zeit und Geld für den Kauf und die Verwaltung der Hardware aufwenden zu müssen, mit der sie betrieben werden.

Im Artikel [Übersicht über virtuelle Windows-Computer in Azure](../virtual-machines/windows/overview.md) erfahren Sie, was Sie vor der Erstellung eines virtuellen Computers berücksichtigen sollten und wie Sie ihn erstellen und verwalten.

## <a name="claimable-vm"></a>Anforderbarer virtueller Computer
Ein anforderbarer virtueller Azure-Computer ist ein virtueller Computer, der jedem Benutzer mit Berechtigungen im Lab zur Verfügung steht. Labadministratoren können virtuelle Computer mit bestimmten Basisimages und Artefakten vorbereiten und dann in einem freigegebenen Pool speichern. Lab-Benutzer können einen virtuellen Computer aus dem Pool anfordern, wenn er einen Computer mit dieser spezifischen Konfiguration benötigt.

Ein anforderbarer virtueller Computer wird anfänglich keinem bestimmten Benutzer zugewiesen, er wird jedoch in der Liste jedes Benutzers unter „Claimable virtual machines“ (Anforderbare virtuelle Computer) angezeigt. Nachdem ein virtueller Computer von einem Benutzer angefordert wurde, wird er in **My virtual machines** (Meine virtuellen Computer) verschoben und kann von keinem anderen Benutzer mehr angefordert werden.

## <a name="environment"></a>Environment
In DevTest Labs bezieht sich eine Umgebung auf eine Sammlung von Azure-Ressourcen in einem Lab. In [Erstellen einer Umgebung](./devtest-lab-create-environment-from-arm.md) wird erläutert, wie Sie auf der Grundlage von Azure Resource Manager-Vorlagen Umgebungen mit mehreren virtuellen Computern erstellen.

## <a name="base-images"></a>Basisimages
Basisimages sind VM-Images, in denen alle Tools und Einstellungen vorinstalliert und konfiguriert sind. Sie können einen virtuellen Computer erstellen, indem Sie eine vorhandene Basis auswählen und ein Artefakt hinzufügen, um Ihren Test-Agent zu installieren. Die Verwendung von Basisimages verkürzt die Zeit zur Erstellung der VM.

## <a name="artifacts"></a>Artefakte
Artefakte werden zum Bereitstellen und Konfigurieren Ihrer Anwendung nach der Bereitstellung eines virtuellen Computers verwendet. Bei Artefakten kann es sich um Folgendes handeln:

* Tools, die Sie auf dem virtuellen Computer installieren möchten, z. B. Agents, Fiddler oder Visual Studio.
* Aktionen, die auf dem virtuellen Computer ausgeführt werden sollen, z. B. das Klonen eines Repositorys.
* Anwendungen, die Sie testen möchten.

Artefakte sind [Azure Resource Manager](../azure-resource-manager/management/overview.md)-JSON-Dateien, die Anweisungen zum Bereitstellen und Übernehmen der Konfigurationen enthalten.

## <a name="artifact-repositories"></a>Artefaktrepositorys
Artefaktrepositorys sind Git-Repositorys, in denen Artefakte eingecheckt werden. Artefaktrepositorys können mehreren Labs in Ihrer Organisation hinzugefügt werden, sodass sie wiederverwendet und geteilt werden können.

## <a name="formulas"></a>Formeln
Formeln bieten einen Mechanismus für die schnelle VM-Bereitstellung. Eine Formel in DevTest Labs ist eine Liste standardmäßiger Eigenschaftswerte, die zum Erstellen eines virtuellen Labcomputers verwendet werden.
Mit Formeln können virtuelle Computer mit dem gleichen Satz von Eigenschaften (z.B. Basisimage, VM-Größe, virtuelles Netzwerk und Artefakte) erstellt werden, ohne dass diese Eigenschaften jedes Mal angeben werden müssen. Beim Erstellen eines virtuellen Computers aus einer Formel können die Standardwerte wie vorhanden verwendet oder geändert werden.

## <a name="policies"></a>Richtlinien
Richtlinien helfen dabei, die Kosten im Lab zu kontrollieren. Beispielsweise können Sie eine Richtlinie erstellen, um virtuelle Computer automatisch anhand eines definierten Zeitplans herunterzufahren.

## <a name="caps"></a>Obergrenzen
Obergrenzen sind ein Mechanismus zum Minimieren unnötiger Ressourcennutzung im Lab. Beispielsweise können Sie eine Obergrenze für die Anzahl der virtuellen Computer festlegen, die pro Benutzer oder in einem Lab erstellt werden können.

## <a name="security-levels"></a>Sicherheitsstufen
Der Sicherheitszugriff wird durch die rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC) bestimmt. Die Funktionsweise des Zugriffs ist leichter zu verstehen, wenn Sie die Unterschiede zwischen Berechtigungen, Rollen und Bereichen in Azure RBAC kennen.

|Begriff | BESCHREIBUNG |
|---|---|
|Berechtigung|Ein definierter Zugriff auf eine bestimmte Aktion (z. B. Lesezugriff auf alle virtuellen Computer).|
|Rolle| Ein Satz von Berechtigungen, die gruppiert und einem Benutzer zugewiesen werden können. Zum Beispiel die Rolle *Abonnementbesitzer* verfügt über Zugriff auf alle Ressourcen in einem Abonnement.|
|`Scope`| Eine Ebene innerhalb der Azure-Ressourcenhierarchie (z. B. eine Ressourcengruppe, ein einzelnes Lab oder das gesamte Abonnement).|


Innerhalb des Bereichs von DevTest Labs gibt es zwei Arten von Rollen zum Definieren von Benutzerberechtigungen: Labbesitzer und Labbenutzer.

|Rolle | BESCHREIBUNG |
|---|---|
|Lab&nbsp;besitzer| Hat Zugriff auf alle Ressourcen innerhalb des Labs. Ein Labbesitzer kann u.a. Richtlinien sowie das virtuelle Netzwerk ändern und verfügt über Lese- und Schreibberechtigungen für alle VMs.|
|Labbenutzer | Kann alle Labressourcen anzeigen (z. B. virtuelle Computer, Richtlinien und virtuelle Netzwerke), aber keine Richtlinien oder von anderen Benutzern erstellte virtuelle Computer ändern.|

Informationen zum Erstellen benutzerdefinierter Rollen in DevTest Labs finden Sie im Artikel [Gewähren von Benutzerberechtigungen für bestimmte Labrichtlinien](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Da Bereiche hierarchisch sind, gelten die Berechtigungen, die einem Benutzer für einen bestimmten Bereich gewährt werden, auch für alle Bereiche auf niedrigerer Ebene. Abonnementbesitzer haben Zugriff auf alle Ressourcen in einem Abonnement (z. B. virtuelle Computer, virtuelle Netzwerke und Labs). Ein Abonnementbesitzer erbt automatisch die Rolle des Labbesitzers. Umgekehrt ist dies jedoch nicht der Fall. Ein Labbesitzer hat Zugriff auf ein Lab, d.h. auf einen Bereich, der sich innerhalb der Hierarchie auf einer niedrigeren Ebene befindet als das Abonnement. Aus diesem Grund kann ein Labbesitzer keine virtuellen Computer, virtuellen Netzwerke oder anderen Ressourcen außerhalb des Labs anzeigen.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-Vorlagen
Die in diesem Artikel besprochenen Konzepte können mithilfe von Vorlagen für Azure Resource Manager (ARM) konfiguriert werden. Mit ARM-Vorlagen können Sie die Infrastruktur/Konfiguration Ihrer Azure-Lösung definieren und wiederholt in einem konsistenten Zustand bereitstellen.

[Vorlagenformat](../azure-resource-manager/templates/syntax.md#template-format) beschreibt die Struktur einer Azure Resource Manager-Vorlage und die Eigenschaften, die in den verschiedenen Abschnitten einer Vorlage verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Labs in Azure DevTest Labs](devtest-lab-create-lab.md)