---
title: Aktivieren von privaten Verbindungen – Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den privaten Zugriff für Azure Time Series Insights Gen2-Lösungen mit Private Link über das Azure-Portal aktivieren.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 21a6c529aaa086fa75aef9e665cbbdce882baf5a
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056661"
---
# <a name="enable-private-access-for-tsi-with-private-link-preview"></a>Privaten Zugang für TSI mit Private Link aktivieren (Vorschau)

Dieser Artikel beschreibt das [Aktivieren von Private Link mit einem privaten Endpunkt für eine Azure Time Series Insights Gen2-Umgebung](concepts-private-links.md) (derzeit in der Vorschau). Die Konfiguration eines privaten Endpunkts für Ihre Azure Time Series Insights Gen2-Umgebung ermöglicht es Ihnen, Ihre Azure Time Series Insights-Umgebung zu sichern und die öffentliche Exposition zu beseitigen sowie Datenexfiltration aus Ihrem [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) zu vermeiden.

In diesem Artikel wird die Vorgehensweise mit dem [**Azure-Portal**](https://portal.azure.com) beschrieben.

Die folgenden Schritte werden in diesem Artikel behandelt: 
1. Einschalten der Private Link-Verbindung und Konfigurieren eines privaten Endpunkts für eine Time Series Insights Gen2-Umgebung.
1. Deaktivieren oder aktivieren Sie die Flags für den Zugang zum öffentlichen Netz, um den Zugang nur auf Private Link-Verbindungen zu beschränken.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen privaten Endpunkt einrichten können, benötigen Sie eine [**Azure Virtual Network-Instanz (virtuelles Netzwerk)**](../virtual-network/virtual-networks-overview.md), in dem der Endpunkt bereitgestellt werden kann. Wenn Sie noch nicht über ein virtuelles Netzwerk verfügen, können Sie einen [Schnellstart](../virtual-network/quick-create-portal.md) für Azure Virtual Network durchlaufen, um eines einzurichten.

## <a name="add-a-private-endpoint-for-an-azure-time-series-insights-gen2-environment"></a>Hinzufügen eines privaten Endpunkts für eine Azure Time Series Insights Gen2-Umgebung 

Bei Verwendung des [Azure-Portals](https://portal.azure.com) können Sie die Private Link-Verbindung mit einem privaten Endpunkt für eine Azure Time Series Insights Gen2-Umgebung als Teil der Ersteinrichtung der Umgebung aktivieren oder sie später für eine bereits vorhandene Umgebung aktivieren. 

Beide Erstellungsmethoden bieten die gleichen Konfigurationsoptionen und das gleiche Endergebnis für Ihre Umgebung. In diesem Abschnitt werden beide Vorgehensweisen beschrieben.

>[!TIP]
> Sie können einen Private Link-Endpunkt auch über den Private Link-Dienst einrichten, anstatt über Ihre Azure Time Series Insights Gen2-Umgebung. Dies bietet Ihnen dieselben Konfigurationsoptionen und dasselbe Endergebnis.
>
> Weitere Informationen zum Einrichten von Private Link-Ressourcen finden Sie in der Private Link-Dokumentation zum [Azure-Portal](../private-link/create-private-endpoint-portal.md), zur [Azure CLI](../private-link/create-private-endpoint-cli.md), zu [ARM](../private-link/create-private-endpoint-template.md) oder [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-environment-creation"></a>Hinzufügen eines privaten Endpunkts während der Umgebungserstellung

In diesem Abschnitt werden Sie Private Link mit einem privaten Endpunkt in einer Azure Time Series Insights Gen2-Umgebung aktivieren, die gerade erstellt wird. Dieser Abschnitt fokussiert sich auf den Netzwerkschritt des Erstellungsprozesses; eine vollständige Anleitung zur Erstellung einer neuen Azure Time Series Insights Gen2-Umgebung finden Sie unter [*Vorgehensweise: Einrichten einer Umgebung*](tutorial-set-up-environment.md).

Die Optionen für die Private Link-Verbindung befinden sich auf der Registerkarte **Networking** der Umgebungseinrichtung.

Auf dieser Registerkarte können Sie private Endpunkte aktivieren, indem Sie die Option **Privater Endpunkt** als **Verbindungsmethode** auswählen.

Dadurch wird ein Abschnitt namens **Private Endpunktverbindungen** hinzugefügt, in dem Sie die Details Ihres privaten Endpunkts konfigurieren können. Klicken Sie auf **+ Hinzufügen**, um fortzufahren.

:::image type="content" source="media/private-links/create-instance-networking.png" alt-text="Screenshot von dem Azure-Portal mit der Registerkarte &quot;Netzwerk&quot; des Dialogfelds &quot;Ressource erstellen&quot; für Time Series Insights Gen2. Es gibt eine Hervorhebung um den Registerkartennamen, die Option „Privater Endpunkt für die Konnektivitätsmethode“ und die Schaltfläche „+ Hinzufügen, um eine neue Verbindung mit einem privaten Endpunkt zu erstellen“." lightbox="media/private-links/create-instance-networking.png":::

Daraufhin wird eine Seite geöffnet, auf der Sie die Details des neuen privaten Endpunkts eingeben können.

:::image type="content" source="media/private-links/create-private-endpoint.png" alt-text="Screenshot: Azure-Portal mit der Seite „Privaten Endpunkt erstellen“ mit den im Folgenden beschriebenen Feldern":::

1. Wählen Sie Ihr **Abonnement** und Ihre **Ressourcengruppe** aus. Legen Sie denselben **Standort** fest, den Ihr virtuelles Netzwerk aufweist. Wählen Sie einen **Namen** für den Endpunkt, und für **Ziel-Subressourcen** wählen Sie *Umgebung* oder *tsiExplorer*.

1. Wählen Sie als Nächstes das **virtuelle Netzwerk** und **Subnetz** aus, die Sie zum Bereitstellen des Endpunkts verwenden möchten.

1. Entscheiden Sie zuletzt, ob Sie die **Integration mit einer privaten DNS-Zone** verwenden möchten. Sie können die Standardoption **Ja** verwenden, oder dem Link im Portal verwenden, um [mehr über die private DNS-Integration zu erfahren](../private-link/private-endpoint-overview.md#dns-configuration).

Nachdem Sie die Konfigurationsoptionen ausgewählt haben, klicken Sie auf **OK**.

Daraufhin kehren Sie zur Registerkarte **Netzwerk** der Azure Time Series Insights Gen2-Umgebung zurück, wo Ihr neuer Endpunkt unter **Private Endpunktverbindungen** sichtbar sein sollte.

Sie können dann die unteren Navigationsschaltflächen verwenden, um mit dem Rest der Umgebungseinrichtung fortzufahren.

### <a name="add-a-private-endpoint-to-an-existing-environment"></a>Hinzufügen eines privaten Endpunkts zu einer bestehenden Umgebung

In diesem Abschnitt aktivieren Sie die private Verbindung mit einem privaten Endpunkt für eine bereits vorhandene Azure Time Series Insights Gen2-Umgebung.

1. Öffnen Sie zunächst das [Azure-Portal](https://portal.azure.com) in einem Browser. Rufen Sie Ihre Azure Time Series Insights Gen2-Umgebung auf, indem Sie in der Suchleiste des Portals nach ihrem Namen suchen.

1. Klicken Sie im linken Menü auf **Netzwerk (Vorschau)** .

1. Wechseln Sie zur Registerkarte **Privater Endpunktverbindungen**.

1. Klicken Sie auf **+ Privater Endpunkt**, um das Dialogfeld **Privaten Endpunkt erstellen** zu öffnen.

    :::image type="content" source="media/private-links/add-private-endpoint.png" alt-text="Screenshot von dem Azure-Portal mit der Seite „Netzwerk (Vorschau)“ für eine Azure Time Series Insights Gen2-Umgebung. Die Registerkarte „Private Endpunktverbindungen“ ist hervorgehoben, und die Schaltfläche „+ Privater Endpunkt“ ist ebenfalls hervorgehoben." lightbox="media/private-links/add-private-endpoint.png":::

1. Geben Sie auf der Registerkarte  **Grundlagen** das **Abonnement** und die **Ressourcengruppe** Ihres Projekts sowie einen **Namen** und eine **Region** für Ihren Endpunkt an. Die Region muss mit der Region des verwendeten virtuellen Netzwerks übereinstimmen.

    :::image type="content" source="media/private-links/create-private-endpoint-2.png" alt-text="Screenshot: Azure-Portal mit der Registerkarte „Grundlagen“ des Dialogfelds „Privaten Endpunkt erstellen“ und den oben beschriebenen Feldern":::

    Wenn Sie fertig sind, klicken Sie auf **Weiter: Ressource >** , um zur nächsten Registerkarte zu gelangen.

1. Geben Sie auf der Registerkarte **Ressource** die folgenden Informationen an: 
    * **Verbindungsmethode**:Wählen Sie **Mit einer Azure-Ressource in meinem Verzeichnis verbinden** aus, um nach Ihrer Azure Time Series Insights Gen2-Umgebung zu suchen.
    * **Abonnement**: Geben Sie Ihr Abonnement an.
    * **Ressourcentyp**: Wählen Sie **Microsoft.TimeSeriesInsights/environments** aus
    * **Ressource**: Wählen Sie den Namen Ihrer Azure Time Series Insights Gen2-Umgebung aus.
    * **Zielunterressource**: Wählen Sie **Umgebung** oder **tsiExplorer** aus.

    :::image type="content" source="media/private-links/create-private-endpoint-3.png" alt-text="Screenshot: Azure-Portal mit der zweiten Registerkarte „Ressource“ des Dialogfelds „Privaten Endpunkt erstellen“ und den oben beschriebenen Feldern":::

    Wenn Sie fertig sind, klicken Sie auf **Weiter: Konfiguration >** , um zur nächsten Registerkarte zu gelangen.    

1. Geben Sie die folgenden Informationen auf der Registerkarte  **Konfiguration** an:
    * **Virtuelles Netzwerk:** Wählen Sie Ihr virtuelles Netzwerk aus.
    * **Subnetz**: Wählen Sie ein Subnetz Ihres virtuellen Netzwerks aus.
    * **Integration in eine private DNS-Zone**: Entscheiden Sie, ob Sie die **Integration mit einer privaten DNS-Zone** verwenden möchten. Sie können die Standardoption **Ja** verwenden, oder dem Link im Portal verwenden, um [mehr über die private DNS-Integration zu erfahren](../private-link/private-endpoint-overview.md#dns-configuration).
    Wenn Sie **Ja** auswählen möchten, können Sie die Standardoption beibehalten.

    :::image type="content" source="media/private-links/create-private-endpoint-4.png" alt-text="Screenshot: Azure-Portal mit der dritte Registerkarte „Konfiguration“ des Dialogfelds „Privaten Endpunkt erstellen“ und den oben beschriebenen Feldern":::

    Wenn Sie fertig sind, können Sie auf **Überprüfen und erstellen** klicken, um das Setup abzuschließen. 

1. Überprüfen Sie auf der Registerkarte **Überprüfen und erstellen** Ihre Angaben, und klicken Sie dann auf  **Erstellen**. 

Wenn die Bereitstellung des Endpunkts abgeschlossen ist, sollte er in den privaten Endpunktverbindungen für Ihre Azure Time Series Insights Gen2-Umgebung angezeigt werden.

>[!TIP]
> Der Endpunkt kann auch über Private Link Center im Azure-Portal angezeigt werden.

## <a name="disable--enable-public-network-access-flags"></a>Deaktivieren und Aktivieren von Zugriffsflags für das öffentliche Netzwerk

Sie können Ihre Azure Time Series Insights Gen2-Umgebung so konfigurieren, dass alle öffentlichen Verbindungen verweigert und nur Verbindungen über private Endpunkte zugelassen werden, um die Sicherheit des Netzes zu erhöhen. Diese Aktion erfolgt mithilfe eines **Zugriffsflags für das öffentliche Netzwerk**. 

Mit dieser Richtlinie können Sie den Zugriff nur auf Private Link-Verbindungen beschränken. Wenn das Zugriffsflag für das öffentliche Netzwerk auf *deaktiviert* gesetzt ist, kommen alle REST-API-Aufrufe an die Datenebene der Azure Time Series Insights Gen2-Umgebung aus der öffentlichen Cloud zurück`403, Unauthorized`. Wenn die Richtlinie auf *deaktiviert* festgelegt ist und eine Anforderung über einen privaten Endpunkt erfolgt, ist der API-Aufruf erfolgreich.

Um den Zugriff auf öffentliche Netze im [Azure-Portal](https://portal.azure.com) zu deaktivieren oder zu aktivieren, öffnen Sie das Portal und navigieren Sie zu Ihrer Azure Time Series Insights Gen2-Umgebung.

1. Klicken Sie im linken Menü auf **Netzwerk (Vorschau)** .

1. Legen Sie auf der Registerkarte **Öffentlicher Zugriff** für **Allow public network access to** (Öffentlichen Netzwerkzugriff zulassen auf) entweder **Deaktiviert** oder **Alle Netzwerke** fest.

    :::row:::
        :::column:::
            :::image type="content" source="media/private-links/network-flag-portal.png" alt-text="Screenshot von dem Azure-Portal mit der Seite &quot;Netzwerk (Vorschau)“ für eine Azure Time Series Insights Gen2-Umgebung. Die Registerkarte „Öffentlicher Zugriff“ ist hervorgehoben, und die Option zum Auswählen, ob der Öffentliche Netzwerkzugriff zugelassen werden soll, ist ebenfalls hervorgehoben." lightbox="media/private-links/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr über Private Link für Azure: 
* [*Was ist der Azure Private Link-Dienst?*](../private-link/private-link-service-overview.md)