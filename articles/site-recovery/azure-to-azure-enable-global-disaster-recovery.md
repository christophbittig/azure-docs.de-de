---
title: Aktivieren der Notfallwiederherstellung in Azure-Regionen auf der ganzen Welt
description: In diesem Artikel wird das Feature der globalen Notfallwiederherstellung in Azure Site Recovery beschrieben.
author: JYOTHIRMAISURI
manager: evansma
ms.service: site-recovery
ms.topic: article
ms.date: 08/09/2021
ms.author: v-jysur
ms.openlocfilehash: 1179558e01ff23c66db652395e10c7d6259af09b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355702"
---
# <a name="enable-global-disaster-recovery-using-azure-site-recovery"></a>Aktivieren der globalen Notfallwiederherstellung mithilfe von Azure Site Recovery

In diesem Artikel wird beschrieben, wie Sie Replikation, Failover und Failback für virtuelle Azure-Computer aus einer Verfügbarkeitszone in eine andere in der gleichen oder einer anderen Azure-Region durchführen.

>[!NOTE]
> Azure Site Recovery verschiebt oder speichert keine Daten außerhalb der von Ihnen ausgewählten Quell- und Zielregion. Sie können einen Recovery Services-Tresor aus einer anderen (dritten) Region auswählen. Der Recovery Services-Tresor enthält Metadaten, aber keine tatsächlichen Kundendaten.

Der Azure Site Recovery-Dienst unterstützt Ihre Strategien für Geschäftskontinuität und Notfallwiederherstellung, indem die Verfügbarkeit Ihrer Geschäftsanwendungen bei geplanten und ungeplanten Ausfällen gewährleistet wird. Wir empfehlen die Notfallwiederherstellungsoption, um Ihre Anwendungen bei regionalen Ausfällen aktiv und betriebsbereit zu halten.

## <a name="disaster-recovery-for-global-azure-regions"></a>Notfallwiederherstellung für globale Azure-Regionen

Azure war der erste große öffentliche Cloudanbieter, der eine erstklassige Cloud-native Notfallwiederherstellungslösung mit Azure-zu-Azure-Notfallwiederherstellung gestartet hat. Mit diesem Angebot konnten Sie eine Replikation oder ein Failover für Ihre Anwendungen von einer Azure-Region in eine andere auf demselben Kontinent ausführen. Dabei wurde auch die Notfallwiederherstellung zwischen Zonen unterstützt.   

Azure Site Recovery unterstützt jetzt die globale Notfallwiederherstellung. Sie können für ihre Anwendungen jetzt eine Replikation oder ein Failover aus jeder Azure-Region über Kontinente hinweg ausführen.

Viele von Ihnen führen globale Unternehmen und möchten ihre Anwendungen auf der ganzen Welt hosten und replizieren, indem sie von der globalen Präsenz von Azure profitieren. Hier können Sie die globale Notfallwiederherstellung über Azure Site Recovery nutzen. Dieses neue Angebot beseitigt die Grenzen der Azure-zu-Azure-Notfallwiederherstellung. Unterstützt durch unseren leistungsstarken globalen Netzwerkbackbone können Sie für Ihre Anwendungen eine Replikation oder ein Failover in eine beliebige Azure-Region auf der ganzen Welt ausführen.  

Dieses Angebot vervollständigt das nativ-öffentliche Cloud-Notfallwiederherstellungsportfolio der Notfallwiederherstellung zwischen Zonen, der Notfallwiederherstellung innerhalb des Kontinents und der globalen Notfallwiederherstellung.

>[!NOTE]
>Die Auswahl einer Notfallwiederherstellungsregion auf einem entfernten Kontinent hat geringfügige Auswirkungen auf den RPO-Wert (Recovery Point Objective), diese sind jedoch nicht signifikant genug, um die SLAs der Azure Site Recovery zu beeinflussen.

Dieses Angebot vervollständigt das nativ-öffentliche Cloud-Notfallwiederherstellungsportfolio der Notfallwiederherstellung zwischen Zonen, der Notfallwiederherstellung innerhalb des Kontinents und der globalen Notfallwiederherstellung.  

## <a name="before-you-begin"></a>Vorbereitung
In diesem Artikel wird vorausgesetzt, dass Sie die Azure Site Recovery-Bereitstellung entsprechend der Beschreibung im [Tutorial zur Azure-zu-Azure-Notfallwiederherstellung](azure-to-azure-tutorial-enable-replication.md) vorbereitet haben.

Stellen Sie sicher, dass die Voraussetzungen erfüllt sind und Sie einen Recovery Services-Tresor erstellt haben.

>[!NOTE]
> Schauen Sie sich die [Unterstützungsmatrix](azure-to-azure-support-matrix.md) an, bevor Sie die Replikation auf Ihren virtuellen Computern aktivieren.

## <a name="supported-platform-features"></a>Features unterstützter Plattformen

| **Feature** | **Supporthinweis** |
| --- | --- |
| Klassische virtuelle Computer | Nein |
| ARM-VMs |  Ja |
| Azure Disk Encryption v1 (zwei Durchläufe, mit Azure Active Directory (Azure AD)) |  Ja |
| Azure Disk Encryption v2 (Einzeldurchlauf, ohne Azure AD) |  Ja |
|     |  Nein |
| Verwaltete Datenträger |  Ja |
| Vom Kunden verwaltete Schlüssel |   Ja |
| Näherungsplatzierungsgruppen |  Ja |
| Sicherungsinteroperabilität | Sicherung und Wiederherstellung auf Dateiebene wird unterstützt. Sicherung und Wiederherstellung auf Datenträger- und VM-Ebene wird nicht unterstützt. |
| Hinzufügen/Entfernen von Datenträgern im laufenden Betrieb | Datenträger können hinzugefügt werden, nachdem die Replikation zwischen Zonen aktiviert wurde. Das Entfernen von Datenträgern nach dem Aktivieren der Replikation zwischen Zonen wird nicht unterstützt. |

## <a name="enable-replication"></a>Aktivieren der Replikation

Führen Sie die folgenden Schritte aus, um Ihre Appliances schnell zu replizieren:

1. Navigieren Sie im Portal zum virtuellen Computer.

2. Wählen Sie links unter **Vorgänge** die Option **Notfallwiederherstellung** aus.

3. Wählen als **Basics** als **Zielregion** aus.

   Alle verfügbaren Azure-Regionen auf der ganzen Welt werden im Dropdownmenü angezeigt. Wählen Sie die gewünschte Zielregion aus.  

   ![Einstellungen für die globale Notfallwiederherstellung](./media/azure-to-azure-enable-global-disaster-recovery/enable-global-disaster-recovery.png)

   Sie können nach Bedarf weitere Konfigurationsänderungen vornehmen.

4. Klicken Sie auf **Erweiterte Einstellungen**, um weitere Einstellungen wie **Zielabonnement**, **Virtuelles Zielnetzwerk**, **Zielverfügbarkeit**, **Näherungsplatzierungsgruppe** anzuzeigen.

5. Klicken Sie auf **Replikation überprüfen und starten**.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Notfallwiederherstellung zwischen Zonen](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).
- Weitere Informationen zur [Architektur der Notfallwiederherstellung von Azure zu Azure](azure-to-azure-architecture.md)
