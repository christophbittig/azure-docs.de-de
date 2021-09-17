---
title: Vergleich zwischen benutzerdefinierten Images und Formeln in DevTest Labs | Microsoft Docs
description: Erfahren Sie mehr zu den Unterschieden zwischen benutzerdefinierten Images und Formeln als Basis für virtuelle Computer, sodass Sie entscheiden können, welche Methode sich für Ihre Umgebung am besten eignet.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f1fdb46decda2f72e64eff95cf57dba002168943
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634003"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>Vergleich zwischen benutzerdefinierten Images und Formeln in DevTest Labs
Sowohl [benutzerdefinierte Images](devtest-lab-create-template.md) als auch [Formeln](devtest-lab-manage-formulas.md) können als Basis für [neu erstellte virtuelle Computer](devtest-lab-add-vm.md) verwendet werden.  Der grundlegende Unterschied zwischen benutzerdefinierten Images und Formeln ist, dass ein benutzerdefiniertes Image einfach ein auf einer virtuellen Festplatte basierendes Image ist. Eine Formel ist ein Image, das ebenfalls auf einer virtuellen Festplatte, aber *zusätzlich* auch auf vorkonfigurierten Einstellungen basiert, wie beispielsweise VM-Größe, virtuelles Netzwerk, Subnetz und Artefakten. Diese vorkonfigurierten Einstellungen werden mit Standardwerten eingerichtet, die zum Zeitpunkt der Erstellung des virtuellen Computers überschrieben werden können. 

In diesem Artikel lernen Sie die Vor- und Nachteile der Verwendung von benutzerdefinierten Images im Vergleich zur Verwendung von Formeln kennen.  Dazu hilft Ihnen möglicherweise auch der Artikel [Erstellen eines benutzerdefinierten Images von einem virtuellen Computer](devtest-lab-create-custom-image-from-vm-using-portal.md) und die [Häufig gestellten Fragen](devtest-lab-faq.yml).

## <a name="custom-image-benefits"></a>Vorteile benutzerdefinierter Images
Benutzerdefinierte Images stellen eine statische, unveränderliche Möglichkeit zum Erstellen virtueller Computer aus einer bestimmten Umgebung bereit. 

|Vorteile|Nachteile|
|----|----|
|<li>Die VM-Bereitstellung über ein benutzerdefiniertes Image ist schnell, da sich nichts mehr ändert, nachdem der virtuelle Computer aus dem Image erstellt wurde. Anders gesagt: Es müssen keine Einstellungen angewendet werden, da ein benutzerdefiniertes Image ganz einfach ein Image ohne Einstellungen ist. <li>Virtuelle Computer, die aus einem einzigen benutzerdefinierten Image erstellt wurden, sind identisch.|<li>Wenn Sie einen Aspekt des benutzerdefinierten Images aktualisieren müssen, muss das Image neu erstellt werden. |

## <a name="formula-benefits"></a>Vorteile von Formeln
  
Formeln stellen eine dynamische Möglichkeit bereit, um virtuelle Computer mithilfe der gewünschten Konfigurationseinstellungen zu erstellen.

|Vorteile|Nachteile|
|----|----|
|<li>Änderungen der Umgebung können über Artefakte dynamisch erfasst werden. Wenn Sie z. B. einen virtuellen Computer mit den neuesten Aktualisierungen aus Ihrer Releasepipeline installieren oder den neuesten Code aus Ihrem Repository eintragen möchten, können Sie einfach ein Artefakt angeben, das diese Aktualisierungen bereitstellt bzw. den neuesten Code in der Formel zusammen mit <li>dem Basiszielimage einträgt. Wann immer diese Formel zum Erstellen eines virtuellen Computers verwendet wird, werden die neuesten Aktualisierungen bzw. der neueste Code in diesem virtuellen Computer bereitgestellt bzw. eingetragen.  <li>Formeln können Standardeinstellungen definieren, die benutzerdefinierte Images nicht bereitstellen können, wie z.B. VM-Größen und Einstellungen des virtuellen Netzwerks.  <li>Die in einer Formel gespeicherten Einstellungen werden als Standardwerte angezeigt, können jedoch bei der Erstellung des virtuellen Computers geändert werden. |<li> Die Erstellung eines virtuellen Computers aus einer Formel dauert länger als die Erstellung eines virtuellen Computers aus einem benutzerdefinierten Image.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
