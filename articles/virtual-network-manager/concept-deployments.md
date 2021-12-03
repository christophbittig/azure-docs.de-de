---
title: Konfigurationsbereitstellung in Azure Virtual Network Manager (Vorschau)
description: Erfahren Sie, wie die Konfigurationsbereitstellung in Azure Virtual Network Manager funktioniert.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: f7e192133bac66d9ec593350a43d0d005bb05933
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866675"
---
# <a name="configuration-deployments-in-azure-virtual-network-manager-preview"></a>Konfigurationsbereitstellung in Azure Virtual Network Manager (Vorschau)

In diesem Artikel erfahren Sie, wie die Konfigurationen auf Ihre Netzwerkressourcen angewendet werden. Sie werden auch erfahren, wie sich die Aktualisierung einer Konfigurationsbereitstellung für jeden Mitgliedschaftstyp unterscheidet. Dann gehen wir ins Detail über *Einsatzstatus* und *Zielzustandsmodell*.

> [!IMPORTANT]
> Der Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deployment"></a>Bereitstellung

*Bereitstellung* ist die Methode, die Azure Virtual Network Manager verwendet, um Konfigurationen auf Ihre virtuellen Netzwerke in Netzwerkgruppen anzuwenden. Änderungen an den Netzwerkgruppen, der Konnektivität und der Sicherheitsadministrationskonfiguration werden erst wirksam, wenn eine Bereitstellung bestätigt wurde. Wenn Sie eine Bereitstellung bestätigen, wählen Sie die Region(en) aus, auf die die Konfiguration angewendet werden soll. Wenn eine Bereitstellungsanforderung an den Azure Virtual Network Manager gesendet wird, berechnet dieser den [Zielzustand](#goalstate) der Netzwerkressourcen und fordert die erforderlichen Änderungen an Ihrer Infrastruktur an. Die Änderungen können je nach Umfang der Konfiguration etwa 15-20 Minuten dauern.

## <a name="deployment-against-network-group-membership-types"></a><a name="deployment"></a>Bereitstellung gegen Netzgruppenmitgliedschaftstypen

Das Ändern der Definition einer Netzwerkgruppe hat keine Auswirkungen, es sei denn, die Konfiguration, die diese Netzwerkgruppe verwendet, wird bereitgestellt. Daher sind Aktualisierungen der Bereitstellung für statische und dynamische Gruppenmitglieder in einer Netzwerkgruppe unterschiedlich. Wenn Sie eine dynamische Gruppenmitgliedschaft definiert haben, z. B. alle virtuellen Netzwerke, deren Name die Angabe „production“ enthält, ermittelt Azure Virtual Network Manager automatisch, ob die dynamischen Mitglieder die Anforderungen der Konfiguration erfüllen, und passt sie an, ohne dass Sie die Konfiguration erneut bereitstellen müssen. Dies liegt daran, dass Sie die Bedingung der Mitgliedschaft bereits definiert haben und die Definition nicht geändert wurde. Wenn Sie jedoch virtuelle Netzwerke haben, die als statische Mitglieder hinzugefügt wurden, müssen Sie die Konfiguration erneut bereitstellen, damit die Änderungen übernommen werden. Wenn Sie beispielsweise ein neues virtuelles Netzwerk als statisches Mitglied hinzufügen, müssen Sie die Konfiguration erneut bereitstellen, damit sie wirksam wird.

## <a name="deployment-status"></a>Bereitstellungsstatus

Wenn Sie eine Konfigurationsbereitstellung festschreiben, führt die API einen POST-Vorgang durch, und Sie werden den Abschluss der Bereitstellung danach nicht sehen. Sobald die Anforderung zur Bereitstellung gestellt wurde, berechnet Azure Virtual Network Manager den Zielzustand Ihrer Netzwerke und fordert die zugrunde liegende Infrastruktur an, um die Änderungen vorzunehmen. Sie können den Status der Bereitstellung auf der Seite *Bereitstellung* des Virtual Network Manager einsehen.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Screenshot: „Bereitstellung wird ausgeführt“ in der Bereitstellungsliste":::

## <a name="goal-state-model"></a><a name = "goalstate"></a> Zielzustandsmodell

Wenn Sie eine Bereitstellung von Konfigurationen festlegen, beschreiben Sie den Zielzustand der Konfiguration, den Sie als Endergebnis haben möchten. Wenn Sie zum Beispiel Konfigurationen mit den Namen *Konfiguration1* und *Konfiguration2* in eine Region übertragen, werden diese beiden Konfigurationen angewendet. Wenn Sie eine Konfiguration mit den Namen *Konfig1* und *Konfig3* in dieselbe Region übertragen möchten, würde *Konfig2* entfernt und *Konfig3* hinzugefügt werden. Um alle Konfigurationen zu entfernen, müssen Sie eine **Keine**-Konfiguration für die Region(en) bereitstellen, für die keine Konfigurationen mehr gelten sollen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie man [eine Azure Virtual Network Manager-Instanz](create-virtual-network-manager-portal.md) anlegt.
