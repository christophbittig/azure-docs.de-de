---
title: Vergleich zwischen benutzerdefinierten Images und Formeln
description: Erfahren Sie mehr zu den Unterschieden zwischen benutzerdefinierten Images und Formeln als Basis für virtuelle Computer, sodass Sie entscheiden können, welche Methode sich für Ihre Umgebung am besten eignet.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1eca53c8d38dc7cd51b7a91c2e40518e5adfc05f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286600"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>Vergleich zwischen benutzerdefinierten Images und Formeln in DevTest Labs
Sowohl [benutzerdefinierte Images](devtest-lab-create-template.md) als auch [Formeln](devtest-lab-manage-formulas.md) können als Basis für [neu erstellte virtuelle Lab-Computer](devtest-lab-add-vm.md) verwendet werden. Der wichtigste Unterschied zwischen benutzerdefinierten Images und Formeln ist, dass ein benutzerdefiniertes Image einfach ein Image ist, das auf einer virtuellen Festplatte (Virtual Hard Drive, VHD) basiert. Eine Formel ist ein Image, das auf einer virtuellen Festplatte plus vorkonfigurierten Einstellungen basiert. Vorkonfigurierte Einstellungen können die Größe eines virtuellen Computers, ein virtuelles Netzwerk, Subnetze und Artefakte umfassen. Diese vorkonfigurierten Einstellungen werden mit Standardwerten eingerichtet, die zum Zeitpunkt der Erstellung des virtuellen Computers überschrieben werden können. 

In diesem Abschnitt lernen Sie die Vor- und Nachteile der Verwendung von benutzerdefinierten Images im Vergleich zur Verwendung von Formeln kennen.  Dazu hilft Ihnen möglicherweise auch der Abschnitt [Erstellen eines benutzerdefinierten Images von einem virtuellen Computer aus](devtest-lab-create-custom-image-from-vm-using-portal.md) und die [Häufig gestellten Fragen bei DevTest Labs](devtest-lab-faq.yml).

## <a name="custom-image-benefits"></a>Vorteile benutzerdefinierter Images
Benutzerdefinierte Images stellen eine statische, unveränderliche Möglichkeit zum Erstellen virtueller Computer aus einer von Ihnen gewünschten Umgebung bereit. 

|Vorteile|Nachteile|
|----|----|
|<li>Die Bereitstellung eines virtuellen Computers über ein benutzerdefiniertes Image ist schnell, da sich nichts mehr ändert, nachdem der virtuelle Computer aus dem Image erstellt wurde. Anders gesagt: Es müssen keine Einstellungen angewendet werden, da ein benutzerdefiniertes Image ganz einfach ein Image ohne Einstellungen ist. <li>Virtuelle Computer, die aus einem einzigen benutzerdefinierten Image erstellt wurden, sind identisch.|<li>Wenn Sie einen Aspekt des benutzerdefinierten Images aktualisieren müssen, muss das Image neu erstellt werden. |

## <a name="formula-benefits"></a>Vorteile von Formeln
  
Formeln stellen eine dynamische Möglichkeit bereit, um virtuelle Computer mithilfe der von Ihnen gewünschten Konfigurationen und Einstellungen zu erstellen.

|Vorteile|Nachteile|
|----|----|
|<li>Änderungen der Umgebung können über Artefakte dynamisch erfasst werden. Wenn Sie z. B. einen virtuellen Computer mit den neuesten Bits aus Ihrer Releasepipeline installieren oder den neuesten Code aus Ihrem Repository eingeben möchten, verwenden Sie eine Formel. Die Formel kann ein Artefakt angeben, das die neuesten Bits bereitstellt oder den neuesten Code zusammen mit dem Zielbasisimage einträgt. Wann immer diese Formel zum Erstellen eines virtuellen Computers verwendet wird, werden die neuesten Bits bzw. der neueste Code in diesem virtuellen Computer bereitgestellt bzw. eingetragen.  <li>Formeln können Standardeinstellungen definieren, die benutzerdefinierte Images nicht bereitstellen können, wie z.B. Größen der virtuellen Computer und Einstellungen des virtuellen Netzwerks.  <li>Die in einer Formel gespeicherten Einstellungen werden als Standardwerte angezeigt, können jedoch bei der Erstellung des virtuellen Computers geändert werden. |<li> Die Erstellung eines virtuellen Computers aus einer Formel dauert länger als die Erstellung eines virtuellen Computers aus einem benutzerdefinierten Image.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
