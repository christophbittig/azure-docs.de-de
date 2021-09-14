---
title: Verbinden mehrerer privater Azure VMware Solution-Clouds in derselben Region (Vorschau)
description: Hier erfahren Sie, wie Sie eine Netzwerkverbindung zwischen zwei oder mehr privaten Azure VMware Solution-Clouds erstellen, die sich in derselben Region befinden.
ms.topic: how-to
ms.date: 07/02/2021
ms.openlocfilehash: 562980ce52216f892cb10689206211e23f3c8886
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586723"
---
# <a name="connect-multiple-azure-vmware-solution-private-clouds-in-the-same-region-preview"></a>Verbinden mehrerer privater Azure VMware Solution-Clouds in derselben Region (Vorschau)

Mit dem Feature **AVS Interconnect** können Sie eine Netzwerkverbindung zwischen zwei oder mehr privaten Azure VMware Solution-Clouds erstellen, die sich in derselben Region befinden. Es wird eine Routingverbindung zwischen den Verwaltungs- und Workloadnetzwerken der privaten Clouds erstellt, um die Netzwerkkommunikation zwischen den Clouds zu ermöglichen.

Sie können eine private Cloud mit mehreren privaten Clouds verbinden, und die Verbindungen sind nicht transitiv. Wenn beispielsweise die _private Cloud 1_ mit der _privaten Cloud 2_ und die _private Cloud 2_ mit der _privaten Cloud 3_ verbunden ist, kommunizieren die Clouds 1 und 3 erst dann, wenn sie direkt verbunden werden.

Sie können nur private Clouds in derselben Region verbinden. Verwenden Sie zum Verbinden privater Clouds in verschiedenen Regionen [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md), um Ihre privaten Clouds auf die gleiche Weise zu verbinden, wie Sie Ihre private Cloud mit Ihrer lokalen Umgebung verbinden. 

>[!IMPORTANT]
>Das Feature AVS Interconnect (Vorschau) ist derzeit als Public Preview verfügbar.  
>Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
>Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Unterstützte Regionen

Das Feature AVS Interconnect (Vorschau) ist in allen Regionen verfügbar, mit Ausnahme der Regionen „USA, Süden-Mitte“ (SAT20), „Europa, Norden“ (DUB21), „Asien, Südosten“ (SG2) und „Vereinigtes Königreich, Westen“ (CWL20). 

## <a name="prerequisites"></a>Voraussetzungen

- Schreibzugriff auf jede private Cloud, mit der Sie eine Verbindung herstellen
- Der Adressraum der gerouteten IP-Adressen in jeder Cloud ist eindeutig und überschneidet sich nicht.

>[!NOTE]
>Das Feature **AVS Interconnect** führt nicht wie beim nativen Azure VNet-Peering vor dem Herstellen der Peeringverbindung eine Überprüfung auf überlappenden IP-Adressraum durch. Daher liegt es in Ihrer Verantwortung sicherzustellen, dass es keine Überlappungen zwischen den privaten Clouds gibt.
>
>In Azure VMware Solution-Umgebungen ist es möglich, nicht geroutete, überlappende IP-Bereitstellungen in NSX-Segmenten zu konfigurieren, die nicht an Azure weitergeleitet werden.  Dies verursacht keine Probleme mit dem AVS Interconnect-Feature, da es nur zwischen NSX T0-Gateways in jeder privaten Cloud weiterleitet.


## <a name="add-connection-between-private-clouds"></a>Herstellen einer Verbindung zwischen privaten Clouds

1. Klicken Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** auf **Konnektivität**.

2. Klicken Sie auf die Registerkarte **AVS Interconnect** und dann auf **Hinzufügen**.

   :::image type="content" source="media/networking/private-cloud-to-private-cloud-no-connections.png" alt-text="Screenshot: Registerkarte „AVS Interconnect“ unter „Konnektivität“" border="true" lightbox="media/networking/private-cloud-to-private-cloud-no-connections.png":::

3. Wählen Sie die Informationen und die private Azure VMware Solution-Cloud für die neue Verbindung aus.

   >[!NOTE]
   >Sie können nur eine Verbindung mit privaten Clouds in derselben Region herstellen. Verwenden Sie zum Herstellen einer Verbindung mit privaten Clouds in verschiedenen Regionen [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md), um Ihre privaten Clouds auf die gleiche Weise zu verbinden, wie Sie Ihre private Cloud mit Ihrer lokalen Umgebung verbinden. 

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud.png" alt-text="Screenshot: Erforderliche Informationen zum Herstellen einer Verbindung mit einer anderen privaten Cloud" border="true":::


4. Aktivieren Sie das Kontrollkästchen **I confirm** (Bestätigen), um zu bestätigen, dass in den beiden privaten Clouds keine überlappenden Adressräume für geroutete IP-Adressen vorhanden sind. 

5. Klicken Sie auf **Erstellen**.  Sie können den Status der Verbindungsherstellung überprüfen.

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud-notification.png" alt-text="Screenshot: Informationen unter „Benachrichtigungen“ zum Fortschritt einer aktuell hergestellten Verbindung und einer vorhandenen Verbindung" border="true":::

   Sie können alle Ihre Verbindungen unter **Private AVS-Cloud** anzeigen.
   
   :::image type="content" source="media/networking/private-cloud-to-private-cloud-two-connections.png" alt-text="Screenshot: Registerkarte „AVS Interconnect“ unter „Konnektivität“ und zwei hergestellte private Cloudverbindungen" border="true" lightbox="media/networking/private-cloud-to-private-cloud-two-connections.png":::


## <a name="remove-connection-between-private-clouds"></a>Entfernen der Verbindung zwischen privaten Clouds

1. Klicken Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** auf **Konnektivität**.

2. Klicken Sie rechts für die zu löschende Verbindung auf **Löschen** (Papierkorb) und dann auf **Ja**.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehrere private Clouds in derselben Region verbunden haben, sollten Sie sich über Folgendes informieren:

- [Verschieben von Azure VMware Solution-Ressourcen in eine andere Region](move-azure-vmware-solution-across-regions.md)
- [Verschieben des Abonnements für Azure VMware Solution in ein anderes Abonnement](move-ea-csp-subscriptions.md)
