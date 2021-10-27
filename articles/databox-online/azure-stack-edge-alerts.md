---
title: Überprüfen von Warnungen auf Azure Stack Edge
description: Hier werden Warnungen beschrieben, die auf Azure Stack Edge-Geräten auftreten.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/14/2021
ms.author: alkohli
ms.openlocfilehash: 6db661698c0e7ee2ca19cd7ef17a5397b174667d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047976"
---
# <a name="review-alerts-on-azure-stack-edge"></a>Überprüfen von Warnungen auf Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie Warnungen anzeigen und den Warnungsschweregrad für Ereignisse auf Ihren Azure Stack Edge-Geräten interpretieren. Die Warnungen generieren Benachrichtigungen im Azure-Portal. Der Artikel enthält eine Kurzübersicht über Azure Stack Edge-Warnungen.

## <a name="overview"></a>Übersicht

Auf dem Blatt „Warnungen“ für ein Azure Stack Edge-Gerät können Sie Warnungen, die sich auf Azure Stack Edge-Geräte beziehen, in Echtzeit überprüfen. Auf diesem Blatt können Sie die Integrität Ihrer Azure Stack Edge-Geräte und die gesamte Azure Stack Edge-Lösung von Microsoft Azure zentral überwachen.

Die erste Anzeige ist eine Zusammenfassung der Warnungen der einzelnen Schweregrade. Sie können einen Drilldown ausführen, um einzelne Warnungen des jeweiligen Schweregrads anzuzeigen.

[ ![Screenshot: Blatt „Warnungen“ für ein Azure Stack Edge-Gerät im Azure-Portal; Warnungsdetails sind überlagert; das Menüelement „Warnungen“ ist hervorgehoben](media/azure-stack-edge-alerts/azure-stack-edge-alerts-summary-and-detail.png) ](media/azure-stack-edge-alerts/azure-stack-edge-alerts-summary-and-detail.png#lightbox)


### <a name="alert-severity-levels"></a>Schweregrade von Warnungen

Für Warnungen gelten abhängig von den Auswirkungen der Warnungssituation und dem Erfordernis einer Reaktion auf die Warnung verschiedene Schweregrade. Die Schweregrade lauten:

- **Kritisch** – Diese Warnung wird als Reaktion auf einen Zustand ausgegeben, der den erfolgreichen Betrieb Ihres Systems beeinträchtigt. Es ist eine Aktion erforderlich, um sicherzustellen, dass der Azure Stack Edge-Dienst nicht unterbrochen wird.
- **Warnung** – Dieser Zustand kann kritisch werden, wenn er nicht behoben wird. Sie sollten den Fall untersuchen und alle Maßnahmen ergreifen, die zum Beheben des Problems erforderlich sind.
- **Information** – Diese Warnung enthält Informationen, die für die Nachverfolgung und Verwaltung Ihres Systems hilfreich sind.

### <a name="configure-alert-notifications"></a>Konfigurieren von Warnungsbenachrichtigungen

Sie können auch Warnungsbenachrichtigungen per E-Mail für Ereignisse auf Ihren Azure Stack Edge-Geräten senden. Zum Verwalten dieser Warnungsbenachrichtigungen erstellen Sie Aktionsregeln. Die Aktionsregeln können Warnungsbenachrichtigungen für Geräteereignisse innerhalb einer Ressourcengruppe, eines Azure-Abonnements oder auf einem Gerät auslösen oder unterdrücken. Weitere Informationen finden Sie unter [Verwenden von Aktionsregeln zum Verwalten von Warnungsbenachrichtigungen](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).

## <a name="alerts-quick-reference"></a>Kurzübersicht zu Warnungen

In den folgenden Tabellen sind einige der Azure Stack Edge-Warnungen aufgeführt, auf die Sie möglicherweise stoßen, sowie Beschreibungen und empfohlene Aktionen. Die Warnungen sind in den folgenden Kategorien gruppiert:

* [Warnungen zur Cloudkonnektivität](#cloud-connectivity-alerts)
* [Warnungen für Edgecomputing](#edge-compute-alerts)
* [Warnungen für lokalen Azure Resource Manager](#local-azure-resource-manager-arm-alerts)
* [Leistungswarnungen](#performance-alerts)
* [Storagewarnungen](#storage-alerts)
* [Sicherheitswarnungen](#security-alerts)
* [Key Vault-Warnungen](#key-vault-alerts)
* [Hardwarewarnungen](#hardware-alerts)
* [Updatewarnungen](#update-alerts)
* [VM-Warnungen](#virtual-machine-alerts)

> [!NOTE]
> In den folgenden Warnungstabellen werden einige Warnungen von mehreren Ereignistypen ausgelöst. Wenn die Ereignisse unterschiedliche empfohlene Aktionen aufweisen, verfügt die Tabelle über einen Warnungseintrag für jedes der Ereignisse.

## <a name="cloud-connectivity-alerts"></a>Warnungen zur Cloudkonnektivität

Die folgenden Warnungen werden durch eine fehlgeschlagene Verbindung mit einem Azure Stack Edge-Gerät ausgelöst, oder wenn kein Heartbeat erkannt wird.

| Warnungstext                       | severity | Beschreibung/Empfohlene Aktion |
|----------------------------------|----------|----------------------------------|
| Es konnte keine Verbindung mit Azure hergestellt werden.  | Kritisch | Überprüfen Sie Ihre Internetverbindung. Wechseln Sie auf der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**. Führen Sie den Diagnosetest für die **Internetkonnektivität** aus. |
| Von Ihrem Gerät wird kein Heartbeat mehr empfangen. | Kritisch | Wenn das Gerät offline ist, kann es nicht mit dem Azure-Dienst kommunizieren. Dies kann einen der folgenden Gründe haben:<ul><li>Die Internetkonnektivität ist unterbrochen.<br>Überprüfen Sie Ihre Internetverbindung. Wechseln Sie auf der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**. Führen Sie die Diagnosetests aus. Beheben Sie die gemeldeten Probleme.</li><li>Das Gerät wird in Hypervisor deaktiviert oder angehalten. Aktivieren Sie Ihr Gerät! Weitere Informationen finden Sie unter [Verwalten der Energieeinstellungen](..\databox-gateway\data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).</li><li>Unter Umständen wurde Ihr Gerät aufgrund eines Updates neu gestartet. Warten Sie einige Minuten, und versuchen Sie erneut, eine Verbindung herzustellen.</li></ul>|


## <a name="edge-compute-alerts"></a>Warnungen für Edgecomputing

Die folgenden Warnungen werden für Edgecomputing oder die Computebeschleunigungskarte ausgelöst, bei der es sich je nach Gerätemodell um eine GPU (Graphical Processing Unit) oder eine Vision Processing Unit (VPU) handeln kann.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Edgecomputing ist fehlerhaft. |Kritisch | Starten Sie Ihr Gerät neu, um das Problem zu beheben. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**.<br>Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Problem bei der Namensauflösung beim Edgecomputing. |Kritisch |Stellen Sie sicher, dass Ihr DNS-Server {15} online und erreichbar ist. Wenn das Problem weiterhin besteht, wenden Sie sich an Ihren Netzwerkadministrator. |
|Fehler bei der Konfiguration der Computebeschleunigungskarte.<sup>*</sup> |Kritisch |Wir haben eine nicht unterstützte Kartenkonfiguration für die Computebeschleunigung erkannt.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Fehler bei der Konfiguration der Computebeschleunigungskarte.<sup>*</sup> |Kritisch |Wir haben eine nicht unterstützte Karte für die Computebeschleunigung erkannt.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Fehler bei der Konfiguration der Computebeschleunigungskarte.<sup>*</sup> |Kritisch |Dies kann einen der folgenden Gründe haben:<ol><li>Wenn die Karte ein FPGA ist, ist das Image ungültig.</li><li>Die Computebeschleunigungskarte ist nicht ordnungsgemäß eingesetzt.</li><li>Zugrunde liegende Probleme mit dem Computebeschleunigungstreiber.</li></ol>Stellen Sie das Azure IoT Edge-Modul erneut bereit, um das Problem zu beheben. Nachdem das Problem behoben wurde, wird die Warnung entfernt.<br>Wenn das Problem weiterhin besteht, gehen Sie wie folgt vor: <ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Fehler bei der Konfiguration der Computebeschleunigungskarte.<sup>*</sup> |Kritisch |Die Ursache ist ein interner Fehler.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Fehler bei der Konfiguration der Computebeschleunigungskarte.<sup>*</sup> |Kritisch |Wenn Ihr Azure IoT Machine Learning-Modul gestartet wird, tritt möglicherweise dieses vorübergehende Problem auf. Warten Sie einige Minuten, um zu sehen, ob das Problem behoben wurde.<br>Wenn das Problem weiterhin besteht, gehen Sie wie folgt vor:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Die Treibersoftware für die Computebeschleunigungskarte wird nicht ausgeführt. |Kritisch |Die Ursache ist ein interner Fehler.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Die Computebeschleunigungskarte auf Ihrem Gerät ist fehlerhaft. |Kritisch |Die Ursache ist ein interner Fehler.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Die Computebeschleunigungskarte wird heruntergefahren, weil die Kartentemperatur den Grenzwert für den Betrieb überschritten hat. |Kritisch |Die Ursache ist ein interner Fehler.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Die Leistung der Computebeschleunigungskarte ist beeinträchtigt. |Warnung |Dies kann daran liegen, dass die Computebeschleunigungskarte eine hohe Auslastung aufweist. Erwägen Sie, die Workload auf dem Azure IoT Machine Learning-Modul zu beenden oder zu reduzieren.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Die Temperatur der Computebeschleunigungskarte steigt. |Warnung |Dies kann daran liegen, dass die Computebeschleunigungskarte eine hohe Auslastung aufweist. Erwägen Sie, die Workload auf dem Azure IoT Machine Learning-Modul zu beenden oder zu reduzieren.<br>Bevor Sie den Microsoft-Support kontaktieren, führen Sie diese Schritte aus:<ol><li>Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Support**.</li><li>Erstellen Sie ein Supportpaket, und laden Sie es herunter.</li><li>[Erstellen Sie eine Supportanfrage](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Fügen Sie das Paket an die Supportanfrage an.</li></ol> |
|Edgecomputing konnte nicht auf Daten in der Freigabe {16} zugreifen. |Warnung |Überprüfen Sie, ob Sie auf Freigabe {16} zugreifen können. Wenn Sie auf die Freigabe zugreifen können, deutet dies auf ein Problem mit dem Edgecomputing hin. <br> Starten Sie Ihr Gerät neu, um das Problem zu beheben. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**. <br> Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Edgecomputing konnte nicht auf Daten in der Freigabe {16} zugreifen. Dies kann daran liegen, dass die Freigabe nicht mehr vorhanden ist. |Warnung |Wenn die Freigabe {16} nicht vorhanden ist, starten Sie Ihr Gerät neu, um das Problem zu beheben. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**. <br> Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|IoT Edge-Agent wird nicht ausgeführt. |Warnung |Starten Sie Ihr Gerät neu, um das Problem zu beheben. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**. <br> Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |
|Der IoT Edge-Dienst wird nicht ausgeführt. |Warnung |Starten Sie Ihr Gerät neu, um das Problem zu beheben. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**. <br> Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |
|Der für das Edgecomputing verwendete Speicherplatz geht zur Neige. | Warnung | [Wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md) für weitere Schritte. |
|Ihr Edgecomputingmodul {20} ist von IoT Edge getrennt. |Warnung |Starten Sie Ihr Gerät neu, um das Problem zu beheben. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**. <br> Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Ihre Edgecomputingmodule verwenden möglicherweise einen lokalen Bereitstellungspunkt {15}, der sich vom lokalen, von einer Freigabe verwendeten Bereitstellungspunkt unterscheidet. |Warnung |Stellen Sie sicher, dass der verwendete lokale Bereitstellungspunkt {15} der Freigabe zugeordnet ist.<ul><li>Navigieren Sie im Azure-Portal in Ihrer Data Box Edge-Ressource zu **Freigaben**.</li><li>Wählen Sie eine Freigabe aus, um den lokalen Bereitstellungspunkt für das Edgecomputingmodul anzeigen zu können.</li><li>Stellen Sie sicher, dass dieser Pfad im Modul verwendet wird, und stellen Sie das Modul erneut bereit.</li></ol>Starten Sie das Gerät neu. Wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Wartung** > **Energieeinstellungen**, und klicken Sie auf **Neu starten**. <br> Wenn die Warnung weiterhin auftritt, [wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |

<sup>*</sup> Diese Warnung wird von mehreren Ereignistypen mit unterschiedlichen empfohlenen Aktionen ausgelöst.


## <a name="local-azure-resource-manager-arm-alerts"></a>Warnungen für lokalen Azure Resource Manager (ARM)

Die folgenden Warnungen werden vom lokalen Azure Resource Manager (ARM) ausgelöst, der zum Herstellen einer Verbindung mit den lokalen APIs auf Azure Stack Edge-Geräten verwendet wird.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Das angegebene Dienstauthentifizierungszertifikat mit dem Fingerabdruck „{0}“ verfügt nicht über einen privaten Schlüssel |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Das Zertifikat mit dem Fingerabdruck „{0}“ am Speicherort „{1}“ wurde nicht gefunden oder ist nicht zugänglich. |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Endpunkt kann nicht verbunden werden: „{0}“ |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Fehler während der Webanforderung: „{0}“ |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Timeout der Anforderung für URL: „{0}“ |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Token kann nicht mithilfe des Anmeldeendpunkts „{0}“ für Ressource „{1}“ abgerufen werden |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Unbekannter Fehler. ErrorCode: „{0}“. Details: „{1}“ | Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Der VM-Dienst konnte nicht auf dem Gerät gestartet werden. |Kritisch | Sollte diese Warnung auftreten, [wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |
|Der VM-Dienst wird nicht auf dem Gerät ausgeführt. |Kritisch |Sollte diese Warnung auftreten, [wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |


## <a name="performance-alerts"></a>Leistungswarnungen

Die folgenden Warnungen weisen auf Leistungsprobleme im Zusammenhang mit dem Speicher oder der CPU-, Arbeitsspeicher- oder Datenträgerauslastung auf einem Azure Stack Edge-Gerät hin.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Die CPU-Auslastung auf Ihrem Gerät hat den Schwellenwert für einen längeren Zeitraum überschritten. |Kritisch |Reduzieren Sie Workloads oder Module, die auf Ihrem Gerät ausgeführt werden. Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Die für die VMs auf Ihrem Gerät reservierte CPU überschreitet den konfigurierten Schwellenwert. |Kritisch |Führen Sie einen der folgenden Schritte aus:<ol><li>Reduzieren Sie die CPU-Reservierung für die VMs, die auf Ihrem Gerät ausgeführt werden.</li><li>Entfernen Sie einige VMs von Ihrem Gerät.</li></ol> |
|Der von den virtuellen Computern auf Ihrem Gerät verwendete Arbeitsspeicher überschreitet den konfigurierten Schwellenwert. |Kritisch |Führen Sie einen der folgenden Schritte aus:<ol><li>Reduzieren Sie den zugeordneten Arbeitsspeicher für die VMs, die auf Ihrem Gerät ausgeführt werden.</li><li>Entfernen Sie einige VMs von Ihrem Gerät.</li></ol> |
|Das Datenvolume auf dem Gerät ist zu {0} % voll. Schreibvorgänge auf das Gerät werden gedrosselt. |Kritisch                    |<ol><li>Verteilen Sie Ihre Datenerfassung auf die Nebenzeiten.</li><li>Dies kann auf ein langsames Netzwerk zurückzuführen sein. Wechseln Sie in der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**, und klicken Sie auf **Diagnosetests ausführen**. Beheben Sie die gemeldeten Probleme.</li></ol>Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Der von den virtuellen Computern auf dem Knoten {0} Ihres Geräts verwendete Arbeitsspeicher überschreitet den konfigurierten Schwellenwert. |Kritisch |Das Gerät versucht, die Last über andere Knoten auszugleichen. Erwägen Sie, einige VM-Workloads auf Ihrem Gerät zu reduzieren. Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Der Speicherplatz auf Ihrem Gerät ist beinahe erschöpft. Wenn ein Datenträger ausfällt, können Sie die Daten auf diesem Gerät möglicherweise nicht wiederherstellen. |Kritisch |Löschen Sie Daten, um auf Ihrem Gerät Kapazität freizugeben. |
|Die CPU-Auslastung auf dem Knoten {0} Ihres Geräts hat den Schwellenwert für einen längeren Zeitraum überschritten.<sup>*</sup> |Warnung |Das Gerät versucht, die Last über andere Knoten auszugleichen. Erwägen Sie, einige VM-Workloads auf Ihrem Gerät zu reduzieren. Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Die CPU-Auslastung auf dem Knoten {0} Ihres Geräts hat den Schwellenwert für einen längeren Zeitraum überschritten.<sup>*</sup> |Warnung |Reduzieren Sie Workloads oder Module, die auf Ihrem Gerät ausgeführt werden. Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Der Knoten {0} auf Ihrem Gerät verwendet mehr Arbeitsspeicher als erwartet. |Warnung |Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Die für die virtuellen Computer auf dem Knoten {0} Ihres Geräts reservierte CPU überschreitet den konfigurierten Schwellenwert. |Warnung |Führen Sie einen der folgenden Schritte aus:<ol><li>Reduzieren Sie die CPU-Reservierung für die VMs, die auf Ihrem Gerät ausgeführt werden.</li><li>Entfernen Sie einige VMs von Ihrem Gerät.</li></ol> |
|Der von den virtuellen Computern auf Ihrem Gerät verwendete Arbeitsspeicher überschreitet den konfigurierten Schwellenwert. |Warnung |Führen Sie einen der folgenden Schritte aus:<ol><li>Reduzieren Sie den zugeordneten Arbeitsspeicher für die VMs, die auf Ihrem Gerät ausgeführt werden.</li><li>Entfernen Sie einige VMs von Ihrem Gerät.</li></ol> |
|Auf dem Knoten {0} Ihres Geräts sind zu viele virtuelle Computer aktiv. |Warnung |Das Gerät versucht, die Last über andere Knoten auszugleichen. Erwägen Sie, einige VM-Workloads auf Ihrem Gerät zu reduzieren. Wenn das Problem weiterhin besteht, [contact Microsoft Support](azure-stack-edge-contact-microsoft-support.md)(in englischer Sprache). |
|Die virtuelle Festplatte {0} erreicht ihre Kapazitätsgrenze. |Warnung |Löschen Sie einige Daten, um Kapazität freizugeben. |

<sup>*</sup> Diese Warnung wird von mehreren Ereignistypen mit unterschiedlichen empfohlenen Aktionen ausgelöst.


## <a name="storage-alerts"></a>Storagewarnungen

Die folgenden Warnungen gelten für Probleme, die beim Zugreifen auf oder Hochladen von Daten in Azure Storage auftreten.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Der Zugriff auf das Volume {0} war nicht möglich.<sup>*</sup> |Kritisch |Dies kann passieren, wenn das Volume offline ist oder zu viele Laufwerke oder Server ausgefallen sind oder getrennt sind. Führen Sie die folgenden Schritte aus:<ol><li>Verbinden Sie fehlende Laufwerke erneut, und starten Sie ausgefallene Server.</li><li>Warten Sie, bis die Synchronisierung abgeschlossen ist.</li><li>Ersetzen Sie fehlerhafte Laufwerke, und stellen Sie verloren gegangene Daten aus der Sicherung wieder her.</li></ol> |
|Der Zugriff auf das Volume {0} war nicht möglich.<sup>*</sup> |Kritisch |Wechseln Sie in der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**, und klicken Sie auf **Diagnosetests ausführen**. Beheben Sie die gemeldeten Probleme.<br>Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Das Volume {0} wurde nicht gefunden.<sup>*</sup> |Kritisch |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|Das Volume {0} wurde nicht gefunden.<sup>*</sup> |Kritisch<br>Warnung |Erweitern Sie das Volume, oder migrieren Sie Workloads zu anderen Volumes. |
|Einige Daten auf diesem Volume {0} sind nicht vollständig resilient. Das Volume ist weiterhin zugänglich. |Informational |Stellen Sie die Resilienz der Daten wieder her. |
|{0} Datei(en) konnte(n) nicht aus der Freigabe {1} hochgeladen werden. |Kritisch |Dies kann einen der folgenden Gründe haben:<ol><li>Verstöße gegen Azure Storage-Benennungs- und -Größenkonventionen. Weitere Informationen finden Sie unter [Namenskonventionen](../azure-resource-manager/management/resource-name-rules.md).</li><li>Die hochgeladenen Dateien wurden in der Cloud von anderen Anwendungen außerhalb des Geräts geändert.<ul><li>{2} in der Freigabe {1} oder</li><li>{3} im Konto {4}.</li></ol></ul> |
|Es konnte keine Verbindung mit dem Speicherkonto „{0}“ hergestellt werden.<sup>*</sup> |Kritisch |Dies kann daran liegen, dass die Speicherkonto-Zugriffsschlüssel erneut generiert wurden. Wenn die Schlüssel erneut generiert wurden, müssen Sie die neuen Schlüssel synchronisieren.<br>Wechseln Sie zur Behebung des Problems im Azure-Portal zu **Freigaben**, wählen Sie die Freigabe aus, und aktualisieren Sie die Speicherschlüssel. |
|Es konnte keine Verbindung mit dem Speicherkonto „{0}“ hergestellt werden.<sup>*</sup> |Kritisch |Dies kann an Internet-Konnektivitätsproblemen liegen. Das Gerät kann nicht mit dem Speicherkontodienst kommunizieren. Wechseln Sie in der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**, und klicken Sie auf **Diagnosetests ausführen**. Beheben Sie die gemeldeten Probleme. |
|Das Gerät verfügt über {0} Dateien. Es werden maximal {1} Dateien unterstützt. |Kritisch |Ziehen Sie in Betracht, einige Dateien vom Gerät zu löschen. |
|Niedriger Durchsatz zu und von Azure Storage erkannt. |Warnung  |Wechseln Sie in der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**, und klicken Sie auf **Diagnosetests ausführen**. Beheben Sie die gemeldeten Probleme. Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |

<sup>*</sup> Diese Warnung wird von mehreren Ereignistypen mit unterschiedlichen empfohlenen Aktionen ausgelöst.


## <a name="security-alerts"></a>Sicherheitswarnungen

Die folgenden Warnungen signalisieren Zugriffsprobleme im Zusammenhang mit Kennwörtern, Zertifikaten oder Schlüsseln oder Melden Zugriffsversuche auf ein Azure Stack Edge-Gerät.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|{0} von {1} läuft in {2} Tagen ab. |Kritisch<br>Warnung |Überprüfen Sie Ihr Zertifikat, und laden Sie vor dem Ablaufdatum ein neues Zertifikat hoch. |
|{0} des Typs {1} ist ungültig. |Kritisch |Überprüfen Sie Ihr Zertifikat. Wenn das Zertifikat ungültig ist, laden Sie ein neues Zertifikat hoch. |
|Interner Fehler bei der Zertifikatrotation |Kritisch |Die internen Zertifikate konnten nicht rotiert werden. Wenn Dienste beeinträchtigt sind, [wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |
|Anmelden von „{0}“ nicht möglich. Anzahl fehlerhafter Versuche: „{1}“. |Kritisch<br>Warnung<br>Informational |Stellen Sie sicher, dass Sie das richtige Kennwort eingegeben haben.<br>Ein autorisierter Benutzer versucht möglicherweise, mit einem falschen Kennwort eine Verbindung mit Ihrem Gerät herzustellen. Stellen Sie sicher, dass diese Versuche von einer legitimen Quelle stammen.<br>Wenn weiterhin fehlgeschlagene Anmeldeversuche zu sehen sind, wenden Sie sich an Ihren Netzwerkadministrator. |
|Rotieren der SED-Schlüsselschutzvorrichtung auf dem Knoten {0} wurde nicht rechtzeitig abgeschlossen. |Warnung |Der Versuch, die SED-Schlüsselschutzvorrichtung auf die neue Standardeinstellung zu rotieren, wurde nicht rechtzeitig abgeschlossen. Überprüfen Sie, ob Knoten und physische Datenträger in einem fehlerfreien Zustand sind. Das System wird es erneut versuchen. |
|Gerätekennwort wurde geändert |Informational |Das Geräteadministratorkennwort wurde geändert. Dies ist eine erforderliche Aktion im Rahmen der erstmaligen Geräteeinrichtung oder der regulären Kennwortzurücksetzung. Es ist keine weitere Aktion erforderlich. |
|Eine Supportsitzung ist aktiviert. |Informational |Dies ist eine Informationswarnung, anhand derer Administratoren sicherstellen können, dass die Supportsitzung rechtmäßig aktiviert werden kann. Es ist keine Aktion erforderlich. |
|Eine Supportsitzung wurde gestartet. |Informational |Dies ist eine Informationswarnung, anhand derer Administratoren sicherstellen können, dass die Supportsitzung rechtmäßig ist. Es ist keine Aktion erforderlich. |


## <a name="key-vault-alerts"></a>Key Vault-Warnungen

Die folgenden Warnungen beziehen sich auf Ihre Azure Key Vault-Konfiguration.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Key Vault ist nicht konfiguriert<sup>*</sup> |Kritisch<br>Warnung |<ol><li>Stellen Sie sicher, dass Key Vault nicht gelöscht wurde.</li><li>Weisen Sie ihrem Gerät die entsprechenden Berechtigungen zum Abrufen und Festlegen der Geheimnisse zu. Ausführliche Schritte finden Sie unter [Voraussetzungen für eine Azure Stack Edge-Ressource](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Wenn Geheimnisse vorläufig gelöscht werden, führen Sie die [hier](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates) beschriebenen Schritte aus, um die Geheimnisse wiederherzustellen.</li><li>Aktualisieren Sie die Key Vault-Details, um die Warnung zu löschen.</li></ol> |
|Key Vault ist nicht konfiguriert<sup>*</sup> |Warnung |Konfigurieren Sie Key Vault für Ihre Azure Stack Edge-Ressource. Ausführliche Schritte finden Sie unter [Erstellen eines Schlüsseltresors](../key-vault/general/quick-create-portal.md). |
|Key Vault wurde gelöscht |Kritisch |Wenn die Key Vault-Instanz gelöscht wird und die Löschschutzphase von 90 Tagen nicht verstrichen ist, führen Sie die Schritte zum [Wiederherstellen der Key Vault-Instanz](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-a-soft-deleted-key-vault) aus. |
|Es konnten keine Geheimnisse aus Key Vault abgerufen werden |Kritisch |<ol><li>Stellen Sie sicher, dass Key Vault nicht gelöscht wurde.</li><li>Weisen Sie ihrem Gerät die entsprechenden Berechtigungen zum Abrufen und Festlegen der Geheimnisse zu. Die erforderlichen Berechtigungen sind [hier](azure-stack-edge-gpu-deploy-prep.md#prerequisites) vorhanden.</li><li>Aktualisieren Sie die Key Vault-Details, um die Warnung zu löschen.</li></ol> |
|Es konnte nicht auf Key Vault zugegriffen werden |Kritisch |<ol><li>Stellen Sie sicher, dass Key Vault nicht gelöscht wurde.</li><li>Weisen Sie ihrem Gerät die entsprechenden Berechtigungen zum Abrufen und Festlegen der Geheimnisse zu. Weitere Informationen finden Sie in den [detaillierten Schritten](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Aktualisieren Sie die Key Vault-Details, um die Warnung zu löschen.</li></ol> |

<sup>*</sup> Diese Warnung wird von mehreren Ereignistypen mit unterschiedlichen empfohlenen Aktionen ausgelöst.


## <a name="hardware-alerts"></a>Hardwarewarnungen

Die folgenden Warnungen weisen auf ein Problem mit einer Hardwarekomponente, z. B. einem physischen Datenträger, einer NIC oder einer Stromversorgungseinheit, auf einem Azure Stack Edge-Gerät hin.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Fehler bei {0} auf {1}. |Kritisch |Dies liegt daran, dass die Stromversorgung nicht ordnungsgemäß verbunden oder ausgefallen ist. Führen Sie die folgenden Schritte aus, um das Problem zu beheben:<ol><li>Stellen Sie die ordnungsgemäße Stromversorgung sicher.</li><li>[Wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md), um eine Ersatzstromversorgungseinheit zu bestellen. |
|{1} konnte nicht erreicht werden. |Kritisch      |Wenn der Controller ausgeschaltet ist, starten Sie den Controller neu.<br>Stellen Sie sicher, dass die Stromversorgung funktionsfähig ist. Informationen zum Überwachen der LEDs der Stromversorgung finden Sie unter https://www.microsoft.com/2.<!--Need new link target. This one goes nowhere--><br>Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md). |
|{0} ist ausgeschaltet. |Warnung |Schließen Sie die Stromversorgungseinheit an eine Stromverteilungseinheit an. |
|Mindestens eine Gerätekomponente funktioniert nicht ordnungsgemäß. |Kritisch |[Wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md) für weitere Schritte. |
|{0} konnte nicht ersetzt werden. |Warnung |[Wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md) für weitere Schritte. |
|Die Ersetzung von {0} wurde gestartet. |Informational |Es ist keine Benutzeraktion erforderlich. |
|{0} erfolgreich ersetzt |Informational |Es ist keine Benutzeraktion erforderlich. |
|{0} ist getrennt. |Warnung |Stellen Sie sicher, dass „{0}“ ordnungsgemäß verkabelt ist und die Netzwerkschnittstelle eingerichtet ist. |
|Fehler bei {0}.<sup>*</sup> |Kritisch |Das Gerät muss ersetzt werden. [Wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md), um das Gerät zu ersetzen. |
|Fehler bei {0}.<sup>*</sup> |Kritisch |Stellen Sie sicher, dass „{0}“ ordnungsgemäß verkabelt ist und die Netzwerkschnittstelle eingerichtet ist.<br>Wechseln Sie in der lokalen Webbenutzeroberfläche des Geräts zu **Problembehandlung** > **Diagnosetests**, und klicken Sie auf **Diagnosetests ausführen**. Beheben Sie die gemeldeten Probleme.<br>Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support unter [https://aka.ms/getazuresupport](azure-stack-edge-contact-microsoft-support.md). |
|Einige Daten auf dem Cachelaufwerk {0} auf Knoten {1} können nicht gelesen und daher nicht auf Kapazitätslaufwerke verschoben werden. |Warnung |Ersetzen Sie den physischen Datenträger. |
|Bei einigen Lese- oder Schreibvorgängen auf dem Cachelaufwerk {0} auf Knoten {1} ist es zu Fehlern gekommen. Zum Schutz Ihrer Daten haben wir sie auf Kapazitätslaufwerke verschoben. |Warnung | Ersetzen Sie den physischen Datenträger. |
|Der physische Datenträger {0} auf Knoten {1} konnte in den letzten Tagen nicht mehrmals gelesen oder geschrieben werden. Wenn dies weiterhin geschieht, hat das Laufwerk möglicherweise eine Fehlfunktion, ist beschädigt oder beginnt zu versagen. |Warnung |Wenn das Problem weiterhin besteht, sollten Sie den physischen Datenträger ersetzen. |
|Der physische Datenträger {0} auf Knoten {1} hat Probleme mit Lese- oder Schreibvorgängen. |Warnung |Wenn das Problem weiterhin besteht, sollten Sie den physischen Datenträger ersetzen. |
|Der physische Datenträger {0} auf Knoten {1} hat 100 % der geschätzten Lebensdauer für Schreibvorgänge erreicht und ist nun schreibgeschützt, d. h., er kann keine weiteren Schreibvorgänge mehr durchführen. |Warnung |Sie sollten den physischen Datenträger ersetzen. |
|Fehler bei physischem Datenträger {0} auf Knoten {1}. |Warnung |Ersetzen Sie den physischen Datenträger. |
|Der physische Datenträger {0} auf Knoten {1} hat Probleme.<sup>*</sup> |Warnung |Auf dem physischen Datenträger sind während der Schreibvorgänge in den letzten Tagen mehrere fehlerhafte Blöcke aufgetreten. Dies kann bedeuten, dass das Laufwerk möglicherweise eine Fehlfunktion hat, beschädigt ist oder zu versagen beginnt.<br>Wenn das Problem weiterhin besteht, sollten Sie den physischen Datenträger ersetzen. |
|Der physische Datenträger {0} auf Knoten {1} hat Probleme.<sup>*</sup> |Warnung |Auf dem physischen Datenträger {0} auf Knoten {1} sind während der Schreibvorgänge in den letzten Tagen mehrere fehlerhafte Blöcke aufgetreten. Dies kann bedeuten, dass das Laufwerk möglicherweise eine Fehlfunktion hat, beschädigt ist oder zu versagen beginnt.<br>Wenn das Problem weiterhin besteht, sollten Sie den physischen Datenträger ersetzen. |
|Der physische Datenträger {0} auf Knoten {1} hat Probleme. |Warnung |Wenn das Problem weiterhin besteht, sollten Sie den physischen Datenträger ersetzen. |
|Der physische Datenträger {0} auf Knoten {1} nutzt sich ab. Er kann schreibgeschützt werden, d. h., er kann keine weiteren Schreibvorgänge mehr ausführen, wenn er 100 % seiner geschätzten Lebensdauer erreicht. |Warnung |Sie sollten den physischen Datenträger ersetzen. |
|Der physische Datenträger {0} auf Knoten {1} wird langsam ausgeführt. |Warnung |Wenn das Problem weiterhin besteht, sollten Sie den physischen Datenträger ersetzen. |
|Es ist keine Verbindung mit dem physischen Datenträger {0} auf Knoten {1} vorhanden. |Warnung |Stellen Sie sicher, dass der physische Datenträger funktioniert und ordnungsgemäß verbunden ist.| 
|{0} ist fehlgeschlagen oder fehlt. |Kritisch |Die Leistung Ihres Geräts ist beeinträchtigt. Das Gerät wird fehlerhaft, wenn bei einem weiteren Datenträger ein Fehler auftritt. [Wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md), um einen Ersatzdatenträger zu bestellen. Austausch des Datenträgers. |
|Der physische Datenträger {0} auf Knoten {1} könnte bald ausfallen. |Warnung |Ersetzen Sie den physischen Datenträger. |
|Ein Datenträgeraustauschvorgang wird ausgeführt. Prozent abgeschlossen = {0}, Datenträger = {2}. |Kritisch |Diese Meldung dient nur zu Informationszwecken. Derzeit ist keine Aktion erforderlich. |
|Fehler bei physischem Datenträger {0} auf Knoten {1}. |Warnung |Ersetzen Sie den physischen Datenträger. |
|Der physische Datenträger {0} auf Knoten {1} reagiert zeitweilig nicht. |Warnung |Ersetzen Sie den physischen Datenträger. |
|Auf dem physischen Datenträger {0} auf Knoten {1} ist keine aktuelle Standard-SED-Schlüsselschutzvorrichtung festgelegt. |Warnung |Das System versucht, die SED-Schlüsselschutzvorrichtung auf den neuesten Stand zu aktualisieren. Wenn das Problem weiterhin besteht, überprüfen Sie, ob sich das Laufwerk in einem fehlerfreien Zustand befindet. |
|Auf dem physischen Datenträger {0} auf Knoten {1} ist die Rotation der SED-Schlüsselschutzvorrichtung fehlgeschlagen. |Warnung |Der Versuch, die SED-Schlüsselschutzvorrichtung auf die neue Standardeinstellung zu rotieren, ist fehlgeschlagen. Überprüfen Sie, ob sich der physische Datenträger in einem fehlerfreien Zustand befindet. Das System wird es erneut versuchen. Wenn das Problem weiterhin besteht, ersetzen Sie das Laufwerk. |
|Der physische Datenträger {0} auf Knoten {1} weist unbekannte Metadaten auf. |Kritisch |Dieser Datenträger kann Daten aus einem unbekannten Speicherpool enthalten. Ersetzen Sie diesen Datenträger durch einen von Microsoft unterstützten Datenträger für Ihr Gerät, der keine Daten enthält. |
|Auf dem physischen Datenträger {0} auf Knoten {1} wird eine nicht unterstützte Firmwareversion ausgeführt. |Warnung |[Wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |
|Der physische Datenträger {0} auf Knoten {1} ist kein unterstützter Datenträger. |Warnung |Ersetzen Sie den physischen Datenträger durch unterstützte Hardware. |
|Der Temperatursensor auf der Hauptplatine des Servers {0} hat eine Warnung ausgelöst. |Warnung |Überprüfen Sie die Knotentemperatur. |

<sup>*</sup> Diese Warnung wird von mehreren Ereignistypen mit unterschiedlichen empfohlenen Aktionen ausgelöst.


## <a name="update-alerts"></a>Updatewarnungen

Die folgenden Warnungen beziehen sich auf Microsoft-Updates und Firmwareupdates für physische Gerätekomponenten.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Die Updates konnten nicht heruntergeladen werden. Fehlermeldung: „{0}“. |Kritisch |{0} |
|Die Updates konnten nicht installiert werden. Fehlermeldung: „{0}“. |Kritisch |Beheben Sie den Fehler: {0} |
|Die Suche nach Updates war nicht möglich. Fehlermeldung: „{0}“. |Kritisch |Beheben Sie den Fehler: {0} |
|{0} Update(s) verfügbar. |Informational |Es wird dringend empfohlen, diese Updates zu installieren. Weitere Informationen finden Sie unter [Installieren von Updates](azure-stack-edge-gpu-install-update.md). |
|Die Datenträgerfirmware konnte nicht aktualisiert werden. |Kritisch |[Wenden Sie sich an den Microsoft Support](azure-stack-edge-contact-microsoft-support.md) für weitere Schritte. |
|Die Firmware auf dem physischen Datenträger {0} auf Knoten {1} konnte nicht aktualisiert werden. |Warnung |[Wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md). |
|Es konnte kein Fortschritt erzielt werden, da ein Firmwarerollout läuft. |Warnung |Stellen Sie sicher, dass alle Speicherplätze fehlerfrei sind und sich derzeit keine Fehlerdomäne im Wartungsmodus befindet. |
|Der Firmwarerollout wurde aufgrund nicht lesbarer oder unerwarteter Firmwareversionsinformationen nach dem Anwenden eines Firmwareupdates abgebrochen. |Warnung |Starten Sie den Firmwarerollout neu, nachdem das Firmwareproblem behoben wurde. |
|Der Firmwarerollout wurde abgebrochen, da das Firmwareupdate auf zu vielen physischen Datenträgern fehlgeschlagen ist. |Warnung |Starten Sie den Firmwarerollout neu, nachdem das Firmwareproblem behoben wurde. |
|Ein Update der Datenträgerfirmware wurde gestartet. |Informational |Es ist keine Benutzeraktion erforderlich. |
|Die Datenträgerfirmware wurde erfolgreich aktualisiert. |Informational |Es ist keine Benutzeraktion erforderlich. |
|Ein Rollout der Firmware für physische Datenträger wird ausgeführt. Prozent abgeschlossen = {0}. |Informational |Diese Meldung dient nur zu Informationszwecken. Derzeit ist keine Aktion erforderlich. |


## <a name="virtual-machine-alerts"></a>VM-Warnungen

Die folgenden Warnungen werden für virtuelle Computer auf einem Azure Stack Edge-Gerät ausgelöst.

|Warnungstext |severity |Beschreibung/Empfohlene Aktion |
|-----------|---------|---------------------------------|
|Der virtuelle Computer {0} ist nicht fehlerfrei. |Warnung  |Informationen zur Problembehandlung für den virtuellen Computer finden Sie unter https://aka.ms/vmtroubleshoot. |
|Der virtuelle Computer {0} funktioniert nicht ordnungsgemäß. |Warnung  |Informationen zur Problembehandlung für den virtuellen Computer finden Sie unter https://aka.ms/vmtroubleshoot. |
|Ihr virtueller Computer {0} wird nicht ausgeführt. |Warnung  |Wenn das Problem weiterhin besteht, löschen Sie den virtuellen Computer, und stellen Sie den virtuellen Computer erneut bereit.|
|Das Gastbetriebssystem auf dem virtuellen Computer {0} ist fehlerhaft. |Warnung  |Informationen zur Problembehandlung für den virtuellen Computer finden Sie unter https://aka.ms/vmtroubleshoot. |
|Ihr virtueller Computer {0} verfügt kaum noch über Arbeitsspeicher. |Warnung  |Reduzieren Sie die Arbeitsspeicherauslastung auf Ihrem virtuellen Computer. |
|Ihr virtueller Computer {0} reagiert nicht auf Hostanforderungen. |Warnung |Informationen zur Problembehandlung für den virtuellen Computer finden Sie unter https://aka.ms/vmtroubleshoot. |


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Aktionsregeln zum Verwalten von Warnungsbenachrichtigungen](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)
- [Verwenden von Metrikdiagrammen](azure-stack-edge-monitor.md#view-metrics)
- [Einrichten von Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md)
