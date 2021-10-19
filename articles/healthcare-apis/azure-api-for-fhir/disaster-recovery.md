---
title: Notfallwiederherstellung für Azure API for FHIR
description: In diesem Artikel erfahren Sie, wie Sie Features für die Notfallwiederherstellung für Azure API for FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/03/2021
ms.author: zxue
ms.openlocfilehash: c060581bb25e8708893ac9c3e48e694a0b86c50d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340075"
---
# <a name="disaster-recovery-for-azure-api-for-fhir"></a>Notfallwiederherstellung für Azure API for FHIR

Der Azure API for FHIR® ist ein vollständig verwalteter Dienst, der auf Fast Healthcare Interoperability Resources (FHIR®. Um Geschäfts- und Complianceanforderungen zu erfüllen, können Sie das Notfallwiederherstellungsfeature für Azure API for FHIR.

Das Feature für die Notfallwiederherstellung bietet ein Recovery Point Objective (RPO) von 15 Minuten und ein Recovery Time Objective (RTO) von 60 Minuten.

 ## <a name="how-to-enable-dr"></a>Aktivieren der Dr. 
  
Erstellen Sie zum Aktivieren des Dr-Features ein einmaliges Supportticket. Sie können eine Azure-gekoppelte Region oder eine andere Region auswählen, in der Azure API for FHIR unterstützt wird. Das Microsoft-Supportteam aktiviert das Feature für die Dr.-Entwicklung basierend auf der Supportpriorität.

## <a name="how-the-dr-process-works"></a>Funktionsweise des Dr-Prozesses

Der DR-Prozess umfasst die folgenden Schritte: 
* Datenreplikation
* Automatisches Failover
* Wiederherstellung der betroffenen Region
* Manuelles Failback

### <a name="data-replication-in-the-secondary-region"></a>Datenreplikation in der sekundären Region

Standardmäßig bietet die Azure API for FHIR Schutz von Daten durch Sicherung und Wiederherstellung. Wenn das Feature für die Notfallwiederherstellung aktiviert ist, beginnt die Datenreplikation. Ein Datenreplikat wird automatisch in der sekundären Azure-Region erstellt und synchronisiert. Die erste Datenreplikation kann je nach Datenmenge einige Minuten bis zu einigen Stunden oder länger dauern. Das sekundäre Datenreplikat ist eine Replikation der primären Daten. Er wird direkt zum Wiederherstellen des Diensts verwendet und hilft dabei, den Wiederherstellungsprozess zu beschleunigen.

Es ist zu sehen, dass die Durchsatz-RU/s in den primären und sekundären Regionen die gleichen Werte haben müssen.

[![Azure Traffic Manager. ](media/disaster-recovery/azure-traffic-manager.png) ](media/disaster-recovery/azure-traffic-manager.png#lightbox)

### <a name="automatic-failover"></a>Automatisches Failover

Während eines Ausfalls der primären Region führt Azure API for FHIR automatisch ein Azure API for FHIR zur sekundären Region aus, und es wird derselbe Dienstendpunkt verwendet. Es wird erwartet, dass der Dienst in einer Stunde oder weniger fortgesetzt wird, und ein potenzieller Datenverlust beträgt daten von bis zu 15 Minuten. Möglicherweise sind weitere Konfigurationsänderungen erforderlich. Weitere Informationen finden Sie unter [Konfigurationsänderungen in der Dr.](#configuration-changes-in-dr)

[![Failover bei der Notfallwiederherstellung. ](media/disaster-recovery/failover-in-disaster-recovery.png) ](media/disaster-recovery/failover-in-disaster-recovery.png#lightbox)

### <a name="affected-region-recovery"></a>Wiederherstellung der betroffenen Region

Nach der Wiederherstellung der betroffenen Region ist sie automatisch als sekundäre Region verfügbar, und die Datenreplikation wird neu gestartet. Sie können den Datenwiederherstellungsprozess starten oder warten, bis der Failbackschritt abgeschlossen ist.

[![Replikation bei der Notfallwiederherstellung. ](media/disaster-recovery/replication-in-disaster-recovery.png) ](media/disaster-recovery/replication-in-disaster-recovery.png#lightbox)

Wenn die Computeressourcen in die wiederhergestellte Region zurückgewonnen sind und die Daten nicht zurückgewonnen wurden, kann es zu potenziellen Netzwerklatenzen kommen. Der Hauptgrund ist, dass sich compute und die Daten in zwei verschiedenen Regionen befinden. Die Netzwerklatenzen sollten automatisch verschwinden, sobald die Daten über einen manuellen Trigger wieder in die wiederhergestellte Region zurückfallen.

[![Netzwerklatenz. ](media/disaster-recovery/network-latency.png) ](media/disaster-recovery/network-latency.png#lightbox)


### <a name="manual-failback"></a>Manuelles Failback

Die Computeressourcen werden automatisch in die wiederhergestellte Region zurückgewonnen. Die Daten werden vom Microsoft-Supportteam manuell mithilfe des Skripts zurück in die wiederhergestellte Region umgeschaltet. 

[![Failback bei der Notfallwiederherstellung. ](media/disaster-recovery/failback-in-disaster-recovery.png) ](media/disaster-recovery/failback-in-disaster-recovery.png#lightbox)

## <a name="configuration-changes-in-dr"></a>Konfigurationsänderungen in der Dr.

Andere Konfigurationsänderungen sind möglicherweise erforderlich, wenn Private Link, der vom Kunden verwaltete Schlüssel (Customer Managed Key, CMK), der IoMT FHIR-Connector (das Internet der medizinischen Dinge) und $export verwendet werden.

### <a name="private-link"></a>Private Link

Sie können das Private Link-Feature aktivieren, bevor oder nachdem Azure API for FHIR bereitgestellt wurde. Sie können private Links auch vor oder nach der Aktivierung des Dr-Features bereitstellen. Lesen Sie die folgende Liste, wenn Sie bereit sind, die Private Link für die Dr.

* Konfigurieren Azure Private Link in der primären Region. Dieser Schritt ist in der sekundären Region nicht erforderlich. Weitere Informationen finden Sie unter [Konfigurieren von Private Link.](/azure/healthcare-apis/fhir/configure-private-link)

* Erstellen Sie ein Azure-VNET in der primären Region und ein anderes VNET in der sekundären Region. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks mithilfe Azure-Portal](../../virtual-network/quick-create-portal.md).

* In der primären Region erstellt das VNET ein VNET-Peering mit dem VNET der sekundären Region. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../../virtual-network/virtual-network-peering-overview.md).

* Verwenden Sie die Standardeinstellungen, oder passen Sie die Konfiguration nach Bedarf an. Wichtig ist, dass der Datenverkehr zwischen den beiden virtuellen Netzwerken fließen kann.

* Wenn das private DNS eingerichtet ist, muss das VNET in der sekundären Region manuell als "Virtuelle Netzwerkverbindungen" eingerichtet werden. Das primäre VNET sollte bereits als Teil des Erstellungsablaufs Private Link Endpunkts hinzugefügt worden sein. Weitere Informationen finden Sie unter [Links zu virtuellen Netzwerken.](../../dns/private-dns-virtual-network-links.md)

* Richten Sie optional einen virtuellen Computer im VNET der primären Region und einen virtuellen Computer im VNET der sekundären Region ein. Sie können auf die Azure API for FHIR von beiden VMs aus zugreifen.

Das Private Link-Feature sollte während eines regionalen Ausfalls und nach Abschluss des Failbacks weiterhin funktionieren. Weitere Informationen finden Sie unter [Konfigurieren von Private Link.](/azure/healthcare-apis/fhir/configure-private-link)

> [!NOTE]
> Das Konfigurieren von virtuellen Netzwerken und VNET-Peering wirkt sich nicht auf die Datenreplikation aus.

### <a name="cmk"></a>CMK

Ihr Zugriff auf die Azure API for FHIR erhalten, wenn auf den Schlüsseltresor zugegriffen werden kann, der den verwalteten Schlüssel in Ihrem Abonnement hosten soll. Es gibt eine mögliche vorübergehende Ausfallzeit, da Key Vault Verbindung bis zu 20 Minuten dauern kann. Weitere Informationen finden Sie unter [Azure Key Vault: Verfügbarkeit und Redundanz](../../key-vault/general/disaster-recovery-guidance.md).  

### <a name="export"></a>$export

Der Exportauftrag wird nach 10 Minuten ohne Aktualisierung des Auftragsstatus aus einer anderen Region entfernt. Befolgen Sie die Anweisungen für Azure Storage, um Ihr Speicherkonto im Falle eines regionalen Ausfalls wiederhergestellt zu haben. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Speicherkontofailover](../../storage/common/storage-disaster-recovery-guidance.md). 

Stellen Sie sicher, dass Sie der Systemidentität des Benutzers die gleichen Berechtigungen Azure API for FHIR. Wenn das Speicherkonto mit ausgewählten Netzwerken konfiguriert ist, finden Sie weitere Informationen unter [Exportieren von FHIR-Daten.](/azure/healthcare-apis/fhir/export-data)

### <a name="iomt-fhir-connector"></a>IoMT FHIR-Connector

Vorhandene Verbindungen funktionieren erst, wenn die fehlerhafte Region wiederhergestellt wurde. Sie können eine neue Verbindung erstellen, sobald das Failover abgeschlossen ist und auf Ihren FHIR-Server zugegriffen werden kann. Diese neue Verbindung funktioniert weiterhin, wenn ein Failback auftritt.

> [!NOTE]
> Der IoMT-Connector ist ein Vorschaufeature und bietet keine Unterstützung für die Notfallwiederherstellung. 

## <a name="how-to-test-dr"></a>Testen der Dr

Obwohl dies nicht erforderlich ist, können Sie das Dr-Feature in einer Nicht-Produktionsumgebung testen. Für den DR-Test werden nur die Daten eingeschlossen, und die Computeressourcen werden nicht einbezogen. 

Ziehen Sie die folgenden Schritte für den Dr-Test in Betracht.

* Bereiten Sie eine Testumgebung mit Testdaten vor. Es wird empfohlen, eine Dienstinstanz mit kleinen Datenmengen zu verwenden, um die Zeit zum Replizieren der Daten zu reduzieren.
 
* Erstellen Sie ein Supportticket, und geben Sie Ihr Azure-Abonnement und den Dienstnamen für Azure API for FHIR Testumgebung an.

* Erstellen Sie wie bei jedem DR-Test einen Testplan.
 
* Das Microsoft-Supportteam aktiviert das Dr-Feature und bestätigt, dass das Failover stattgefunden hat.

* Führen Sie Ihren DR-Test durch, und zeichnen Sie die Testergebnisse auf, die probleme mit Datenverlust und Netzwerklatenz enthalten sollten. 

* Benachrichtigen Sie das Microsoft-Supportteam bei einem Failback, dass der Failbackschritt abgeschlossen werden soll.
 
* (Optional) Senden Sie Feedback an das Microsoft-Supportteam.


> [!NOTE]
> Der DR-Test verdoppelt die Kosten Ihrer Testumgebung während des Tests. Nach Abschluss des Dr-Tests und deaktivierter Dr-Funktion entstehen keine zusätzlichen Kosten.

## <a name="cost-of-disaster-recovery"></a>Kosten für die Notfallwiederherstellung

Das Feature für die Notfallwiederherstellung verursacht zusätzliche Kosten, da Daten des Compute- und Datenreplikats in der Umgebung in der sekundären Region ausgeführt werden. Weitere Preisdetails finden Sie auf der Azure API for FHIR [Preisseite.]( https://azure.microsoft.com/pricing/details/azure-api-for-fhir)

> [!NOTE]
> Das DR-Angebot unterliegt der [SLA für Azure API for FHIR](https://azure.microsoft.com/support/legal/sla/azure-api-for-fhir/v1_0/), 1.0.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie die Dr. für Azure API for FHIR funktioniert und wie Sie sie aktivieren. Weitere Informationen zu Azure API for FHIR anderen unterstützten Features finden Sie unter:

>[!div class="nextstepaction"]
>[Von FHIR unterstützte Features](fhir-features-supported.md)