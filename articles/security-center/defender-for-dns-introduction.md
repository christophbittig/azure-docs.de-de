---
title: 'Microsoft Azure Defender für DNS: Vorteile und Features'
description: Hier erfahren Sie etwas über die Vorteile und Features von Microsoft Defender für DNS.
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 00f783b82b9a5b1aa8e6152e513fa91ab3213929
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055861"
---
# <a name="introduction-to-microsoft-defender-for-dns"></a>Einführung in Microsoft Defender für DNS

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Azure Defender für DNS bietet eine zusätzliche Schutzebene für Ressourcen, die die Azure DNS-Funktion [Von Azure bereitgestellte Namensauflösung](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) nutzen. 

In Azure DNS überwacht Defender für DNS die Abfragen von diesen Ressourcen und erkennt verdächtige Aktivitäten, ohne dass zusätzliche Agents für Ihre Ressourcen erforderlich sind.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Microsoft Defender für DNS** wird gemäß den Angaben in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-dns"></a>Welche Vorteile bietet Microsoft Defender für DNS?

Microsoft Defender für DNS erkennt beispielsweise folgende verdächtige und anomale Aktivitäten:

- **Datenexfiltration** aus Ihren Azure-Ressourcen mittels DNS-Tunneling
- **Schadsoftware**, die mit Command-and-Control-Servern kommuniziert
- **DNS-Angriffe** (Kommunikation mit schädlichen DNS-Resolvern) 
- **Kommunikation mit Domänen, die für schädliche Aktivitäten verwendet werden** (etwa Phishing und Crypto Mining)

Eine vollständige Liste der Warnungen, die von Microsoft Defender für DNS bereitgestellt werden, finden Sie auf der [Referenzseite zu Warnungen](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Abhängigkeiten

Microsoft Defender für DNS verwendet keine Agents. 

Aktivieren Sie Microsoft Defender für DNS zum Schutz Ihrer DNS-Schicht für jedes Ihrer Abonnements, wie unter [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md) beschrieben wird.


## <a name="respond-to-microsoft-defender-for-dns-alerts"></a>Reagieren auf Microsoft Defender für DNS-Warnungen

Wenn Sie eine Warnung von Microsoft Defender für DNS erhalten, wird empfohlen, dass Sie die Warnung wie unten beschrieben untersuchen und darauf reagieren. Microsoft Defender für DNS schützt alle verbundenen Ressourcen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.  


### <a name="step-1-contact"></a>Schritt 1: Contact

1. Wenden Sie sich an den Ressourcenbesitzer, um zu bestimmen, ob das Verhalten erwartet oder beabsichtigt war.
1. Wenn die Aktivität erwartet wird, schließen Sie die Warnung.
1. Wenn die Aktivität unerwartet ist, behandeln Sie die Ressource als potenziell kompromittiert und mindern Sie das Risiko, wie im nächsten Schritt beschrieben.

### <a name="step-2-immediate-mitigation"></a>Schritt 2: Sofortige Risikominderung 

1. Isolieren Sie die Ressource vom Netzwerk, um Lateral Movement zu verhindern.
1. Führen Sie eine vollständige Antischadsoftwareüberprüfung für die Ressource aus, und befolgen Sie dabei alle resultierenden Korrekturanweisungen.
1. Überprüfen Sie die installierte und laufende Software auf der Ressource, und entfernen Sie alle unbekannten oder unerwünschten Pakete.
1. Versetzen Sie den Computer in einen bekannten gültigen Zustand zurück, installieren Sie ggf. das Betriebssystem neu, und stellen Sie die Software aus einer verifizierten schadsoftwarefreien Quelle wieder her.
1. Lösen Sie alle Microsoft Defender für Cloud-Empfehlungen für den Computer, und beheben Sie dabei hervorgehobene Sicherheitsprobleme, um zukünftige Sicherheitsverletzungen zu verhindern.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Microsoft Defender für DNS erfahren. 

> [!div class="nextstepaction"]
> [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md)

Verwandte Informationen finden Sie im folgenden Artikel: 

- Sicherheitswarnungen können von Defender für Cloud generiert oder aus anderen Sicherheitsprodukten empfangen werden. Führen Sie die Anleitungen unter [Exportieren von Warnungen in ein SIEM-System](continuous-export.md) aus, um alle diese Warnungen in Microsoft Sentinel, ein beliebiges Drittanbieter-SIEM oder ein beliebiges anderes externes Tool zu exportieren.
