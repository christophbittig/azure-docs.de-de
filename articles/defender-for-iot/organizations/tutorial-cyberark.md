---
title: Integrieren von CyberArk in Microsoft Defender für loT
description: In diesem Tutorial erfahren Sie, wie Sie Microsoft Defender für loT in CyberArk integrieren.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbfa178c2e5358503208773109fc53ab3c274be7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278377"
---
# <a name="tutorial-integrate-cyberark-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von CyberArk in Microsoft Defender für loT

In diesem Tutorial lernen Sie, wie Sie CyberArk mit Microsoft Defender for loT integrieren und nutzen.

Defender für IoT ist eine ICS- und IIoT-Cybersicherheitsplattform mit ICS-fähiger Bedrohungsanalyse und maschinellem Lernen.

Bedrohungsakteure verwenden kompromittierte Anmeldeinformationen für den Remotezugriff, um über Remotedesktop- und VPN-Verbindungen auf kritische Infrastrukturnetzwerke zuzugreifen. Durch die Verwendung vertrauenswürdiger Verbindungen können mit diesem Ansatz sämtliche OT-Umgebungssicherheitsmaßnahmen problemlos umgangen werden. Anmeldeinformationen werden in der Regel von privilegierten Benutzern gestohlen, z. B. Steuerungstechnikern und Wartungspersonal von Partnern, die zum Erledigen ihrer täglichen Aufgaben Remotezugriff benötigen.

Die Defender für IoT-Integration mit CyberArk ermöglicht Ihnen Folgendes:

- Reduzieren von OT-Risiken durch nicht autorisierten Remotezugriff

- Bereitstellen einer kontinuierlichen Überwachung und privilegierten Zugriffssicherheit für OT

- Verbessern der Reaktion auf Vorfälle, Bedrohungssuche und Bedrohungsmodellierung

Die Defender für IoT-Appliance ist über einen SPAN-Port (Spiegelport) auf Netzwerkgeräten wie Switches und Routern über eine unidirektionale (eingehende) Verbindung mit den dedizierten Netzwerkschnittstellen der Defender für IoT-Appliance mit dem OT-Netzwerk verbunden.

Zudem wird eine dedizierte Netzwerkschnittstelle für die zentrale Verwaltung und den API-Zugriff in der Defender für IoT-Appliance bereitgestellt. Diese Schnittstelle wird auch für die Kommunikation mit der CyberArk-PSM-Lösung verwendet, die im Rechenzentrum der Organisation bereitgestellt wird, um privilegierte Benutzer zu verwalten und Remotezugriffsverbindungen zu schützen.

:::image type="content" source="media/tutorial-cyberark/architecture.png" alt-text="Bereitstellung der CyberArk-PSM-Lösung":::

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Konfigurieren von PSM in CyberArk
> - Aktivieren der Integration in Defender für IoT
> - Anzeigen und Verwalten von Erkennungen
> - Beenden der Integration

## <a name="prerequisites"></a>Voraussetzungen

- CyberArk Version 2.0.

- Stellen Sie sicher, dass Sie über CLI-Zugriff auf alle Defender für IoT-Appliances Ihres Unternehmens verfügen.

- Ein Azure-Konto. Wenn Sie noch nicht über ein Azure-Konto verfügen, können Sie [noch heute Ihr kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

## <a name="configure-psm-cyberark"></a>Konfigurieren von PSM in CyberArk

CyberArk muss zum Zulassen der Kommunikation mit Defender für IoT konfiguriert werden. Für diese Kommunikation wird PSM konfiguriert.

**So konfigurieren Sie PSM**:

1. Suchen Sie nach der Datei `c:\Program Files\PrivateArk\Server\dbparam.xml`, und öffnen Sie sie.

1. Fügen Sie die folgenden Parameter hinzu:

    `[SYSLOG]` <br>
    `UseLegacySyslogFormat=Yes` <br>
    `SyslogTranslatorFile=Syslog\CyberX.xsl` <br>
    `SyslogServerIP=<CyberX Server IP>` <br>
    `SyslogServerProtocol=UDP` <br>
    `SyslogMessageCodeFilter=319,320,295,378,380` <br>

1. Speichern Sie die Datei, und schließen Sie sie.

1. Platzieren Sie die Syslog-Konfigurationsdatei von Defender für IoT `CyberX.xsl` unter `c:\Program Files\PrivateArk\Server\Syslog\CyberX.xsl`.

1. Öffnen Sie **Server Central Administration** (Zentrale Serververwaltung).

1. Wählen Sie **Stop Traffic Light** (Verkehrsampel beenden) aus, um den Server zu beenden.

    :::image type="content" source="media/tutorial-cyberark/server.png" alt-text="Screenshot der Option „Stop Traffic Light“ (Verkehrsampel beenden) in „Server Central Administration“ (Zentrale Serververwaltung)":::

1. Wählen Sie **Start Traffic Light** (Verkehrsampel starten) aus, um den Server zu starten.

## <a name="enable-the-integration-in-defender-for-iot"></a>Aktivieren der Integration in Defender für IoT

Zum Aktivieren der Integration muss „Syslog-Server“ in der Defender für IoT-Verwaltungskonsole aktiviert werden. Standardmäßig lauscht der Syslog-Server über UDP-Port 514 auf die IP-Adresse des Systems.

**So konfigurieren Sie Defender für IoT**:

1. Navigieren Sie in der Defender für IoT-Verwaltungskonsole zu **Systemeinstellungen**.

1. Schalten Sie „Syslog-Server“ auf **Ein** um.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="Screenshot des aktivierten Syslog-Servers":::

1. (Optional) Ändern Sie den Port, indem Sie sich über die Befehlszeilenschnittstelle (CLI) beim System anmelden, zu `/var/cyberx/properties/syslog.properties` navigieren und `listener: 514/udp` ändern.

## <a name="view-and-manage-detections"></a>Anzeigen und Verwalten von Erkennungen

Die Integration zwischen Microsoft Defender für IoT und CyberArk PSM erfolgt über Syslog-Nachrichten. Diese Nachrichten werden von der PSM-Lösung an Defender für IoT gesendet, um Defender für IoT über Remotesitzungen oder Überprüfungsfehler zu benachrichtigen.

Sobald die Defender für IoT-Plattform diese Nachrichten von PSM empfängt, korreliert sie sie mit den verfügbaren Daten im Netzwerk und überprüft so, ob alle Remotezugriffsverbindungen mit dem Netzwerk von der PSM-Lösung generiert wurden und nicht von einem nicht autorisierten Benutzer.

### <a name="view-alerts"></a>Anzeigen von Warnungen

Wenn die Defender für IoT-Plattform Remotesitzungen identifiziert, die nicht von PSM autorisiert wurden, wird `Unauthorized Remote Session` ausgegeben. Die Warnung enthält auch die IP-Adressen und Namen der Quell- und Zielgeräte, um eine sofortige Untersuchung zu ermöglichen.

**So zeigen Sie Warnungen an**:

1. Melden Sie sich bei der Verwaltungskonsole an.

1. Wählen Sie im linken Bereich die Option **Warnungen** aus.

1. Wählen Sie in der Liste der Warnungen die Warnung **Unauthorized Remote Session** (Nicht autorisierte Remotesitzung) aus.

    :::image type="content" source="media/tutorial-cyberark/unauthorized.png" alt-text="Warnung „Unauthorized Remote Session“ (Nicht autorisierte Remotesitzung)":::

### <a name="event-timeline"></a>Zeitskala der Ereignisse

Wenn PSM eine Remoteverbindung autorisiert, wird diese in Defender für IoT auf der Seite „Ereigniszeitachse“ angezeigt. Auf der Seite „Ereigniszeitachse“ wird eine Zeitachse aller Warnungen und Benachrichtigungen angezeigt.

**So zeigen Sie die Ereigniszeitachse an**:

1. Melden Sie sich am Defender für loT Sensor an.

1. Wählen Sie im linken Bereich die Option **Ereigniszeitachse** aus.

1. Suchen Sie nach einem Ereignis mit der Bezeichnung „PSM Remote Session“ (PSM-Remotesitzung).

    :::image type="content" source="media/tutorial-cyberark/event.png" alt-text="Ansicht des Bildschirms „Ereignisprotokoll“":::

### <a name="auditing--forensics"></a>Überwachung und Forensik

Administratoren können Remotezugriffssitzungen überwachen und untersuchen, indem sie die Defender für IoT-Plattform über die integrierte Data Mining-Schnittstelle abfragen. Anhand dieser Informationen können alle Remotezugriffsverbindungen identifiziert werden, einschließlich forensischer Details wie Quell- oder Zielgeräte, Protokolle (RDP oder SSH), Quell- und Zielbenutzer, Zeitstempel und Angaben dazu, ob die Sitzungen von PSM autorisiert wurden.

**So überwachen und untersuchen Sie Verbindungen**:

1. Melden Sie sich am Defender für loT Sensor an.

1. Wählen Sie im linken Bereich die Option **Data Mining** aus.

1. Wählen Sie **Remotezugriff** aus.

    :::image type="content" source="media/tutorial-cyberark/data-mining.png" alt-text="Ansicht der Data Mining-Schnittstelle":::

## <a name="stop-the-integration"></a>Beenden der Integration

Sie können die Kommunikation der Integration jederzeit beenden.

**So beenden Sie die Integration**:

1. Navigieren Sie in der Defender für IoT-Verwaltungskonsole zum Bildschirm **Systemeinstellungen**.

1. Schalten Sie „Syslog-Server“ auf **Aus** um.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="Ansicht des Serverstatus":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die ersten Schritte bei der Integration von CyberArk kennengelernt. Informieren Sie sich als Nächstes über unsere Forescout-Integration.

> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](./tutorial-forescout.md)
