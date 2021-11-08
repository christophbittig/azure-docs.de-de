---
title: 'Microsoft Defender für Resource Manager: Vorteile und Features'
description: Hier erfahren Sie etwas über die Vorteile und Features von Microsoft Defender für Resource Manager.
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ea1f2259d34389498bcdf144a463973baeb3919d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453172"
---
# <a name="introduction-to-microsoft-defender-for-resource-manager"></a>Einführung in Microsoft Defender für Resource Manager

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Resource Manager](../azure-resource-manager/management/overview.md) ist der Bereitstellungs- und Verwaltungsdienst für Azure. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Konto ermöglicht. Mithilfe von Verwaltungsfeatures wie Zugriffssteuerung, Sperren und Tags können Sie Ihre Ressourcen nach der Bereitstellung schützen und organisieren.

Die Cloudverwaltungsebene ist ein wichtiger Dienst, der mit allen Ihren Cloudressourcen verbunden ist. Dies macht ihn allerdings auch zu einem potenziellen Ziel für Angreifer. Daher sollte die Ressourcenverwaltungsebene sehr genau von Sicherheitsteams überwacht werden. 

Microsoft Defender für Resource Manager überwacht automatisch die Ressourcenverwaltungsvorgänge in Ihrer Organisation. Dabei spielt es keine Rolle, ob diese über das Azure-Portal, Azure-REST-APIs, die Azure CLI oder andere programmgesteuerte Azure-Clients ausgeführt werden. Defender für Cloud führt erweiterte Sicherheitsanalysen durch, um Bedrohungen zu erkennen und Sie auf verdächtige Aktivitäten aufmerksam zu machen.

>[!NOTE]
> Einige dieser Analysen basieren auf [Microsoft Defender für Cloud-Apps (früher als „Microsoft Cloud App Security“ bezeichnet)](/cloud-app-security/what-is-cloud-app-security). Um von diesen Analysen zu profitieren, müssen Sie eine Cloud App Security-Lizenz aktivieren. Wenn Sie über eine Cloud App Security-Lizenz verfügen, sind diese Warnungen standardmäßig aktiviert. So deaktivieren Sie die Warnungen:
>
> 1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.
> 1. Wählen Sie das Abonnement aus, das Sie ändern möchten.
> 1. Wählen Sie **Integrationen** aus.
> 1. Deaktivieren Sie **Microsoft Defender für Cloud-Apps den Zugriff auf meine Daten erlauben**, und wählen Sie **Speichern** aus.


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Microsoft Defender für Resource Manager** wird gemäß den Angaben in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-resource-manager"></a>Welche Vorteile bietet Microsoft Defender für Resource Manager?

Microsoft Defender für Resource Manager schützt unter anderem vor folgenden Problemen:

- **Verdächtige Ressourcenverwaltungsvorgänge**, beispielsweise Vorgänge von schädlichen IP-Adressen, Deaktivierung von Antischadsoftware oder die Ausführung verdächtiger Skripts in VM-Erweiterungen
- **Verwendung von Exploit-Toolkits** wie Microburst oder PowerZure
- **Lateral Movement** von der Azure-Verwaltungsebene zur Azure-Ressourcendatenebene

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager: Übersichtsdiagramm":::

Eine vollständige Liste der Warnungen, die von Microsoft Defender für Resource Manager bereitgestellt werden, finden Sie auf der [Referenzseite zu Warnungen](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-microsoft-defender-for-resource-manager"></a>Untersuchen von Warnungen von Microsoft Defender für Resource Manager

Sicherheitswarnungen von Microsoft Defender für Resource Manager basieren auf Bedrohungen, die durch die Überwachung von Azure Resource Manager-Vorgängen erkannt werden. Defender für Cloud nutzt interne Protokollquellen von Azure Resource Manager sowie das Azure-Aktivitätsprotokoll. Dies ist ein Plattformprotokoll in Azure, das Einblicke in Ereignisse auf Abonnementebene bietet.

Weitere Informationen zum Azure-Aktivitätsprotokoll finden Sie [hier](../azure-monitor/essentials/activity-log.md).

So untersuchen Sie Sicherheitswarnungen von Microsoft Defender für Resource Manager:

1. Öffnen Sie das Azure-Aktivitätsprotokoll.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Öffnen des Azure-Aktivitätsprotokolls":::

1. Filtern Sie die Ereignisse nach Folgendem:
    - In der Warnung erwähntes Abonnement
    - Zeitrahmen der erkannten Aktivität
    - Zugehöriges Benutzerkonto (sofern relevant)

1. Suchen Sie nach verdächtigen Aktivitäten.

> [!TIP]
> Streamen Sie Ihre Azure-Aktivitätsprotokolle, wie unter [Verknüpfen von Daten aus dem Azure-Aktivitätsprotokoll](../sentinel/data-connectors-reference.md#azure-activity) beschrieben, an Microsoft Sentinel, um sie noch besser und umfassender analysieren zu können.



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Microsoft Defender für Resource Manager erfahren. 

> [!div class="nextstepaction"]
> [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md)

Verwandte Informationen finden Sie im folgenden Artikel: 

- Sicherheitswarnungen können von Defender für Cloud generiert oder von Defender für Cloud aus verschiedenen Sicherheitsprodukten empfangen werden. Führen Sie die Anleitungen unter [Exportieren von Warnungen in ein SIEM-System](continuous-export.md) aus, um alle diese Warnungen in Microsoft Sentinel, ein beliebiges Drittanbieter-SIEM oder ein beliebiges anderes externes Tool zu exportieren.