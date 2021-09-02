---
title: Herstellen einer Verbindung mit Azure Percept DK über Ethernet
description: Dieser Leitfaden beschreibt, wie Sie eine Verbindung mit dem Azure Percept DK-Einrichtungsassistenten herstellen, wenn Sie über eine Ethernet-Verbindung verbunden sind.
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ffc2a2d55bdad670c8323dfc57efad17dec1930
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227093"
---
# <a name="connect-to-azure-percept-dk-over-ethernet"></a>Herstellen einer Verbindung mit Azure Percept DK über Ethernet

In dieser Anleitung erfahren Sie, wie Sie den Einrichtungsassistenten für Azure Percept DK über eine Ethernetverbindung starten. Sie ist eine Begleitanleitung für [Schnellstart: Einrichten Ihres Azure Percept DK und Bereitstellen Ihres ersten KI-Modells zur Anwendung](./quickstart-percept-dk-set-up.md). Sehen Sie sich die unten beschriebenen Optionen an und wählen Sie die für Ihre Umgebung am besten geeignete.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Percept DK ([hier bestellen](https://go.microsoft.com/fwlink/?linkid=2155270))
- Ein Windows-, Linux- oder OS X-basierter Hostcomputer mit WLAN oder Ethernet und einem Webbrowser
- Ein Netzwerkkabel

## <a name="identify-your-dev-kits-ip-address"></a>Identifizieren der IP-Adresse Ihres Devkits

Um den Azure Percept DK-Einrichtungsassistenten über eine Ethernetverbindung auszuführen, müssen Sie die IP-Adresse Ihres Devkits herausfinden. Dieser Artikel beschreibt drei Möglichkeiten dazu:
1. Von Ihrem Netzwerkrouter
1. Über SSH
1. Mit dem Nmap-Tool

### <a name="from-your-network-router"></a>Von Ihrem Netzwerkrouter
Am schnellsten finden Sie die IP-Adresse Ihres Devkits heraus, indem Sie sie im Netzwerkrouter ausfindig machen.
1. Schließen Sie das Ethernetkabel an das Devkit und das andere Ende an den Router an.
1. Aktivieren Sie Ihr Azure Percept DK.
1. Suchen Sie auf dem Router nach einem Schild mit Zugriffsanweisungen.

    **Beispiele für Router-Schilder**

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-01.png" alt-text="Beispielhaftes Schild eines Netzwerkrouters":::

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-02.png" alt-text="Ein weiteres Beispiel-Schild eines Netzwerkrouters":::

1. Öffnen Sie auf Ihrem über Ethernet oder WLAN verbundenen Computer einen Webbrowser.
1. Geben Sie die auf dem Schild angegebene Konfigurationsadresse des Routers ein.
1. Wenn Sie dazu aufgefordert werden, geben Sie den Namen und das Kennwort für den Router ein, wie sie auf dem Schild angegeben sind.
1. Wählen Sie im Routermenü „Meine Geräte“ (oder ähnliches, je nach Router) aus.
1. Suchen Sie das Azure Percept-Devkit in der Liste der Geräte.
1. Kopieren Sie die IP-Adresse des Azure Percept-Devkits.

### <a name="via-ssh"></a>Über SSH
Sie können die IP-Adresse Ihres Devkits über eine SSH-Verbindung herausfinden.

> [!NOTE]
> Um die IP-Adresse Ihres Devkits mit der SSH-Methode zu identifizieren, müssen Sie eine Verbindung mit dem WLAN-Access Point Ihres Devkits herstellen können. Werwenden Sie eine der anderen Methoden, wenn Ihnen dies nicht möglich ist.

1. Schließen Sie das Ethernetkabel an das Devkit und das andere Ende an den Router an.
1. Schalten Sie Ihr Azure Percept-Devkit ein.
1. Stellen Sie eine SSH-Verbindung mit Ihrem Devkit her. Lesen Sie dazu [Herstellen einer Verbindung mit Ihrem Azure Percept DK über SSH](./how-to-ssh-into-percept-dk.md).
1. Geben Sie den nachstehenden Befehl in Ihr SSH-Terminalfenster ein, um die im lokalen Netzwerk gültige IP-Adresse anzuzeigen:

    ```bash
    ip a | grep eth1
    ```

    :::image type="content" source="media/how-to-connect-over-ethernet/ssh-local-network-address.png" alt-text="Beispiel für das Identifizieren der im lokalen Netzwerk gültigen IP-Adresse per SSH-Terminal":::


1. Die IP-Adresse des Dev Kits wird nach „inet“ angezeigt. Kopieren Sie die IP-Adresse.

### <a name="using-the-nmap-tool"></a>Mit dem Nmap-Tool
Sie können auch kostenlose Tools aus dem Web verwenden, um die IP-Adresse Ihres Devkits herauszufinden. In diesen Anweisungen behandeln wir ein Tool namens Nmap.
1. Schließen Sie das Ethernetkabel an das Devkit und das andere Ende an den Router an.
1. Schalten Sie Ihr Azure Percept-Devkit ein.
1. Laden Sie den kostenlosen [Nmap-Sicherheitsscanner](https://nmap.org/download.html) auf Ihren Computer (Windows/Mac/Linux) herunter und installieren Sie ihn.
1. Ermitteln Sie das Standardgateway Ihres Computers: [Ermitteln Ihres Standardgateways](https://www.noip.com/support/knowledgebase/finding-your-default-gateway/)
1. Starten Sie die Nmap-Anwendung. 
1. Geben Sie Ihr Standardgateway in das *Target* (Ziel) ein, und fügen Sie **/24** am Ende an. Ändern Sie *Profile* (Profil) zu **Quick scan** (Schnellscan) und wählen Sie die Schaltfläche **Scan** (Scannen) aus.
    
    :::image type="content" source="media/how-to-connect-over-ethernet/nmap-tool.png" alt-text="Beispiel für die Einstellung des Nmap-Tools":::
 
1. Suchen Sie in der Ergebnis-Geräteliste den Namen des Azure Percept-Devkits – ähnlich wie **apd-xxxxxxxx**.
1. Kopieren Sie die IP-Adresse des Azure Percept-Devkits. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Starten des Azure Percept DK-Einrichtungsassistenten
1. Schließen Sie das Ethernetkabel an das Devkit und das andere Ende an den Router an.
1. Schalten Sie Ihr Azure Percept-Devkit ein.
1. Öffnen Sie einen Webbrowser und fügen Sie die IP-Adresse des Devkits ein. Der Einrichtungsassistent sollte im Browser gestartet werden.

## <a name="next-steps"></a>Nächste Schritte
- [Abschließen der Einrichtungsassistenten](./quickstart-percept-dk-set-up.md)