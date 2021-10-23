---
title: Zulassen der Auswahl des Standorts in Azure Lab Services durch den Ersteller des Labs
description: In diesem Artikel wird beschrieben, wie ein Labkontoadministrator Lab-Erstellern das Auswählen von Standorten für ihre Labs gestatten kann.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f9dbafe6b297d7b94b1d1ec5eb419345cb140457
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177249"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Zulassen der Auswahl des Standorts für das Lab in Azure Lab Services durch den Ersteller des Labs
In Azure Lab Services kann ein Labkontobesitzer Lab-Erstellern (Dozenten) erlauben, einen Standort für das Lab auszuwählen, das sie erstellen. Dieser Ort kann sich vom Standort des Labkontos unterscheiden. Ein Standort ist eine Gruppe von Azure-Regionen. Der Standort „USA“ ist beispielsweise eine Gruppe von Regionen wie „USA, Osten“, „USA, Westen“ usw. 

Sie als Labkontobesitzer können die Option **Auswahl des Labstandorts durch Lab-Ersteller zulassen** auswählen, wenn Sie ein Labkonto erstellen und nachdem Sie das Labkonto erstellt haben (oder ein vorhandenes Labkonto). 

## <a name="at-the-time-of-lab-account-creation"></a>Zum Zeitpunkt der Labkontoerstellung
Wenn Sie ein Labkonto erstellen, wird diese Option im ersten Bildschirm (Registerkarte **Grundlagen**) angezeigt. 

![Aktivieren der Option zum Zeitpunkt der Lab-Erstellung](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Diese Option ist deaktiviert, wenn Sie ein virtuelles Peernetzwerk für Ihr Labkonto auf der Registerkarte **Erweitert** auswählen.  

![Bei aktiviertem virtuellem Peernetzwerk](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Nach dem Erstellen des Labkontos
Nachdem Sie das Labkonto erstellt haben, können Sie diese Option mithilfe dieser Schritte aktivieren oder deaktivieren: 

1. Wählen Sie auf der Seite **Labkonto** im Menü auf der linken Seite die Option **Labeinstellungen** aus.
2. Wählen Sie die Option **Zulassen der Auswahl des Labstandorts durch den Lab-Ersteller** aus, wenn Sie dem Lab-Ersteller das Auswählen eines Standorts für das Lab gestatten möchten. Wenn diese Option deaktiviert ist, werden die Labs automatisch am gleichen Standort erstellt, in dem das Lab-Konto vorhanden ist. 
    
    Dieses Feld ist deaktiviert, wenn Sie ein virtuelles Netzwerk für das Feld **Virtuelles Peernetzwerk** auswählen. Der Grund dafür ist, dass sich die Labs im Labkonto in der gleichen Region befinden müssen wie das Labkonto, damit sie auf Ressourcen im virtuellen Peernetzwerk zugreifen können. 
1. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    ![Lab-Einstellungen](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Kein virtuelles Netzwerk, und Standortauswahl ist nicht zulässig
In diesem Szenario haben Sie die Option **Zulassen der Auswahl des Labstandorts durch den Lab-Ersteller** nicht aktiviert. 

![Kein Labstandort](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Dann wird Erstellern (Dozenten) keine Option zum Auswählen eines Standorts für das Lab angezeigt. Ihnen wird der Preis pro Stunde für jede Größenoption angezeigt, die ihnen zur Verfügung steht. Wenn sie ein Lab erstellen, wird es in einer Azure-Region erstellt, die sich am selben Standort wie die Azure-Region befindet, in der sich ihr Labkonto befindet. Wenn sich das Labkonto beispielsweise in **USA, Westen** befindet, könnte das Lab in **USA, Süden-Mitte** erstellt werden, würde aber nicht in **Kanada, Osten** erstellt. Wir geben bezüglich der Region, die wir auswählen, keinerlei Garantien, außer dass sie sich in dem Standort befindet. Wenn eine Größe zurzeit eingeschränkt ist, wird dem Lab-Ersteller ein Kontrollkästchen angezeigt, bei dem die Größen angezeigt werden, die wir normalerweise unterstützen, die aber zurzeit nicht verfügbar sind. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>In einem virtuellen Netzwerk, und Standortauswahl ist nicht zulässig
In diesem Szenario ist die Option **Zulassen der Auswahl des Labstandorts durch den Lab-Ersteller** deaktiviert, weil Sie ein virtuelles Peernetzwerk für das Labkonto ausgewählt haben. Den Lab-Erstellern wird dann derselbe Bildschirm wie bei der vorherigen Option angezeigt. Da sich alle VMs in derselben Azure-Region wie das virtuelle Netzwerk befinden müssen, wird das Lab in derselben Azure-Region erstellt, in der sich auch das virtuelle Netzwerk befindet. Wenn in dieser speziellen Region eine Größe eingeschränkt ist, wird die Größe als nicht verfügbar angezeigt. 

## <a name="location-selection-is-enabled"></a>Standortauswahl ist aktiviert
Wählen Sie **Zulassen der Auswahl des Labstandorts durch den Lab-Ersteller** auswählen, wird Lab-Erstellern (Dozenten) eine Option zum Auswählen eines Standorts beim Erstellen des Labs angezeigt. 

![Auswählen eines Labstandorts](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Lab-Erstellern wird die Presspanne für alle Standorte angezeigt, in diese Größe verfügbar ist, und sie können einen Standort auswählen. Das Lab wird in einer beliebigen Azure-Region erstellt, die diesem Standort entspricht.

Wenn ein Standort eingeschränkt ist, wird er standardmäßig nicht in der Liste angezeigt. Erweitern Sie die Dropdownliste, und wählen Sie **Nicht verfügbare Standorte für diese Größe anzeigen** aus. 

![Anzeigen nicht verfügbarer Standorte](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
Früher basierten die Preise auf der VM-Größe, die Sie für das Lab auswählten. Jetzt basiert der Preis auf der Kombination aus Betriebssystem (BS), Größe und Standort. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Verbinden des Netzwerks Ihres Labs mit einem virtuellen Peernetzwerk](how-to-connect-peer-virtual-network.md)
- [Anfügen eines Katalogs mit freigegebenen Images an ein Lab](how-to-attach-detach-shared-image-gallery.md)
- [Hinzufügen eines Benutzers als Labbesitzer](how-to-add-user-lab-owner.md)
- [Anzeigen von Firewalleinstellungen für ein Lab](how-to-configure-firewall-settings.md)
- [Konfigurieren von anderen Einstellungen für ein Lab](how-to-configure-lab-accounts.md)