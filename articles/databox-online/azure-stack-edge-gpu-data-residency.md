---
title: Datenresidenz und Resilienz für Azure Stack Edge Pro GPU-/Pro R-/Mini R-Geräte
description: Beschreibt den Datenresidenzstatus für Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: alkohli
ms.openlocfilehash: 01f1a0ca013dad76510df99b70a462b96bec07a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078653"
---
# <a name="data-residency-and-resiliency-for-azure-stack-edge"></a>Datenresidenz und Resilienz für Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel werden die Informationen beschrieben, die Sie benötigen, um das Datenresidenz- und Resilienzverhalten für Azure Stack Edge und die Aktivierung der Datenresidenz im Dienst zu verstehen.  

## <a name="about-data-residency-for-azure-stack-edge"></a>Informationen zur Datenresidenz für Azure Stack Edge 

Azure Stack Edge-Dienste verwenden [Azure-Regionspaare](../best-practices-availability-paired-regions.md#azure-regional-pairs), wenn Kundendaten in allen geografischen Regionen gespeichert und verarbeitet werden, in denen der Dienst verfügbar ist. Für die Region „Asien, Südosten“ (Singapur) ist der Dienst derzeit mit Hongkong (SAR) gekoppelt. Die Kopplung von Azure-Regionen impliziert, dass alle in Singapur gespeicherten Daten in Hongkong (SAR) repliziert werden. In Singapur gelten Gesetze, die erfordern, dass die Kundendaten die Ländergrenzen nicht verlassen dürfen. 

Um sicherzustellen, dass sich die Kundendaten nur in einer einzelnen Region befinden, wurde im Azure Stack Edge-Dienst eine neue Option aktiviert. Bei Auswahl dieser Option kann der Dienst die Kundendaten nur in der Region „Singapur“ speichern und verarbeiten. Die Kundendaten werden nicht nach Hongkong (SAR) repliziert. Es gibt dienstspezifische Metadaten (bei denen es sich nicht um vertrauliche Daten handelt), die weiterhin in die gekoppelte Region repliziert werden.  

Wenn diese Option aktiviert ist, ist der Dienst gegenüber zonenweiten Ausfällen resilient, aber nicht gegenüber regionsweiten Ausfällen. Wenn regionsweite Ausfälle für Sie wichtig sind, sollten Sie die auf Regionspaaren basierende Replikation auswählen.

Die Datenresidenzoption für einzelne Regionen ist nur für „Asien, Südosten“ (Singapur) verfügbar. Für alle anderen Regionen speichert Azure Stack Edge Kundendaten in der vom Kunden angegebenen geografischen Region und verarbeitet sie dort.

Der Datenresidenzstatus der Azure Stack Edge-Dienste lässt sich hinsichtlich der folgenden Aspekte so zusammenfassen:

- Vorhandener Azure Stack Edge-Auftrags- und -Verwaltungsdienst.
- Neues Azure Edge Hardware Center (Vorschauversion), das in Zukunft für neue Aufträge verwendet wird.
<!--- Telemetry for the device and the service.
- Proactive Support log collection where any logs that the service generates are stored in a single region and are not replicated to the paired region.-->

Azure Stack Edge-Dienst integriert sich auch in die folgenden abhängigen Dienste, und deren Verhalten wird ebenfalls zusammengefasst: 

- Azure Arc-fähiges Kubernetes
- Azure IoT Hub und Azure IoT Edge
<!--- Azure Key Vault -->


## <a name="azure-stack-edge-classic-ordering-and-management-resource"></a>Klassische Azure Stack Edge-Auftrags- und -Verwaltungsressource 

Wenn Sie die klassische Erfahrung verwenden, um einen Auftrag für Azure Stack Edge zu platzieren, verwendet der Dienst derzeit ein Azure-Regionspaar, um Datenresilienz gegenüber regionsweiten Ausfällen zu implementieren. Für vorhandene Azure Stack Edge-Ressourcen in Singapur werden die Daten nach Hongkong (SAR) repliziert.

Wenn Sie eine neue Azure Stack Edge-Ressource erstellen, haben Sie die Möglichkeit, Datenresidenz nur in Singapur zu aktivieren. Wenn diese Option ausgewählt ist, werden die Daten nicht nach Hongkong (SAR) repliziert. Bei einem regionsweiten Ausfall des Diensts haben Sie zwei Möglichkeiten:

- Warten, bis die Region „Singapur“ wiederhergestellt wurde.

- Erstellen einer Ressource in einer anderen Region, Zurücksetzen des Geräts und Verwalten Ihres Geräts über die neue Ressource. Ausführliche Anweisungen finden Sie unter [Zurücksetzen und Reaktivieren Ihres Azure Stack Edge-Geräts](azure-stack-edge-reset-reactivate-device.md).

## <a name="azure-edge-hardware-center-ordering-and-management-resource"></a>Azure Edge Hardware Center-Auftrags- und -Verwaltungsressource 

Der neue Azure Edge Hardware Center-Dienst (Vorschauversion) ist jetzt verfügbar und ermöglicht Ihnen das Erstellen und Verwalten von Azure Stack Edge-Ressourcen. Wenn Sie eine Bestellung in der Region „Asien, Südosten“ bestellen, können Sie die Option auswählen, dass Ihre Daten nur in Singapur aufbewahrt und nicht repliziert werden sollen. 

Bei einem regionsweiten Ausfall können Sie nicht auf die Auftragsressourcen zugreifen. Sie können die Ressourcen nicht zurückgeben, abbrechen/kündigen oder löschen. Wenn Sie Aktualisierungen zu Ihrem Auftragsstatus anfordern oder während des Dienstausfalls dringend eine Geräterückgabe initiieren müssen, bearbeitet der Microsoft-Support solche Anforderungen.

Ausführliche Anweisungen finden Sie unter [Erstellen eines Auftrags über das Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).


<!--## Azure Stack Edge telemetry

As Azure Stack Edge is a first-party Microsoft device, the telemetry from the device is automatically collected (without the user consent) and sent to Microsoft. This telemetry is stored in a common central location. This gathered telemetry provides valuable insights into enterprise deployments of Azure Stack Edge. This telemetry is also used for security, health, quality, and performance analysis.

- Microsoft collects telemetry for the infrastructure VMs (for example, Kubernetes master VM and Kubernetes worker VM) deployed on your Azure Stack Edge device and hosts. Telemetry is also gathered for other services that run on Azure Stack Edge device (for example, local Azure Resource Manager, Kubernetes dashboard). 
- The telemetry data is encrypted-in-transit as well at rest.
- Raw telemetry data sent to Microsoft is retained for 90 days. Aggregated data is retained for longer.
- For all the containerized workloads (deployed via IoT Edge and Kubernetes) and VM workloads, the application data is considered as the customer data. This data can only be accessed by the customer unless it pertains to the underlying infrastructure. 

For more information, see [Use the Kubernetes dashboard to monitor the Kubernetes cluster health on your Azure Stack Edge Pro device](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).-->

## <a name="azure-stack-edge-dependent-services"></a>Von Azure Stack Edge abhängige Dienste

Kubernetes mit Azure Arc-Unterstützung, Azure IoT Hub und Azure IoT Edge sowie Azure Key Vault sind Dienste, die sich in Azure Stack Edge integrieren lassen.

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-fähiges Kubernetes 

Kubernetes mit Azure Arc-Unterstützung ist als Add-On für Azure Stack Edge verfügbar. Für Singapur („Asien, Südosten“) befinden sich Azure Arc-Daten nur in Singapur und werden nicht nach Hongkong (SAR) repliziert. <!--If there is a region-wide outage, the service is not resilient.-->

<!--For all other regions, Azure Arc supports Azure Regional Pair and is resilient to any region-wide outages.--> 
<!--For more information, see [Data residency and resiliency for Azure Arc-enabled Kubernetes clusters]().-->


### <a name="azure-iot"></a>Azure IoT

Azure IoT ist als Add-On für die Azure Stack Edge verfügbar. Für Singapur („Asien, Südosten“) verwendet Azure IoT Regionspaare und repliziert Daten nach Hongkong (SAR). Dies bedeutet, dass Azure IoT gegenüber regionsweiten Ausfällen resilient ist. 

<!--For more information, see [Data residency and resiliency for Azure IoT]().-->


<!--### Azure Key Vault

Azure Key Vault currently uses Azure Regional Pair for region outage resiliency. For new Azure Key Vault resources, an option is now available that can be enabled at the subscription level. When enabled, if your service is deployed in Singapore (Southeast Asia), you can control the data replication to Hong Kong. 

If you choose to store and process the data only in Singapore region, then the service will not be resilient to region-wide outages. -->
<!--For more information, see [Data residency and resiliency for Azure Key Vault]().-->

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Anforderungen der Azure-Datenresidenz](https://azure.microsoft.com/global-infrastructure/data-residency/).