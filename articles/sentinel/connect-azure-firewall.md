---
title: Verknüpfen von Azure Firewall-Daten mit Azure Sentinel
description: Hier erfahren Sie, wie Sie Azure Firewall-Daten mit Azure Sentinel verknüpfen.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f4683708f8fdd4eda2f483cc4112fe9fe9939ce6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347177"
---
# <a name="connect-data-from-azure-firewall"></a>Verknüpfen von Daten aus Azure Firewall

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall als ein Dienst mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. 

Sie können Azure Firewall-Protokolle mit Azure Sentinel verbinden. Das ermöglicht es Ihnen, Protokolldaten in Arbeitsmappen anzuzeigen, sie zum Erstellen benutzerdefinierter Warnungen zu verwenden und sie zur Verbesserung Ihrer Untersuchung zu integrieren.

Informieren Sie sich ausführlicher über das [Überwachen von Azure Firewall-Protokollen und -Metriken](../firewall/firewall-diagnostics.md).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

## <a name="connect-to-azure-firewall"></a>Herstellen einer Verbindung mit Azure Firewall
    
1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie im Katalog für Datenconnectors **Azure Firewall** und dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Aktivieren Sie **Diagnoseprotokolle** für alle Firewalls, deren Protokolle Sie verbinden möchten:

    1. Wählen Sie den Link **Open Azure Firewall resource >** (Azure Firewall-Ressource öffnen >) aus.

    1. Wählen Sie im Navigationsmenü für **Firewalls** die Option **Diagnoseeinstellungen** aus.

    1. Wählen Sie am Ende der Liste **+ Diagnoseeinstellung hinzufügen** aus.

    1. Geben Sie auf dem Bildschirm **Diagnoseeinstellungen** einen Namen in das Feld **Name der Diagnoseeinstellungen** ein.
    
    1. Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**. Zwei neue Felder werden darunter angezeigt. Wählen Sie das relevante **Abonnement** und den **Log Analytics-Arbeitsbereich** (in dem sich Azure Sentinel befindet) aus.

    1. Aktivieren Sie die Kontrollkästchen der Regeltypen, deren Protokolle Sie erfassen möchten. Empfohlen werden **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule** und **AzureFirewallDNSProxy**.

    1. Wählen Sie im oberen Bereich des Bildschirms **Speichern** aus.

1. Um in Log Analytics das relevante Schema für die Azure Firewall-Warnungen zu verwenden, suchen Sie nach **AzureDiagnostics**.

> [!NOTE]
>
> Mit diesem speziellen Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten zwei Wochen erfasst wurden. Wenn zwei Wochen ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Azure Firewall-Protokolle mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
