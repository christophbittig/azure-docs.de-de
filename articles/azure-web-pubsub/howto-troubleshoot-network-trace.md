---
title: Erfassen einer Netzwerkablaufverfolgung
description: Erfahren Sie, wie Sie die Netzwerkablaufverfolgung abrufen, um die Problembehandlung zu unterstützen.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 48209fc5f4876c097d30ce6bcf464ba40ee94a7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997607"
---
# <a name="how-to-collect-a-network-trace"></a>Erfassen einer Netzwerkablaufverfolgung

Wenn ein Problem auftritt, kann eine Netzwerkablaufverfolgung manchmal sehr hilfreiche Informationen liefern. In dieser Schrittanleitung werden die Optionen zum Erfassen einer Netzwerkablaufverfolgung erläutert.

> [!WARNING]
> Eine Netzwerkablaufverfolgung enthält den vollständigen Inhalt jeder Nachricht, die von Ihrer App gesendet wird. Veröffentlichten Sie deshalb **niemals** unbearbeitete Netzwerkablaufverfolgungen von Produktions-Apps in öffentlichen Foren wie GitHub.

## <a name="collect-a-network-trace-with-fiddler"></a>Erfassen einer Netzwerkablaufverfolgung mit Fiddler (bevorzugte Option)

Fiddler ist ein leistungsstarkes Tool zum Erfassen von HTTP-Ablaufverfolgungen. Installieren Sie es von [telerik.com/fiddler](https://www.telerik.com/fiddler), starten Sie es, und führen Sie dann Ihre App aus, und reproduzieren Sie das Problem. Fiddler ist für Windows, macOS und Linux verfügbar. 

Wenn Sie eine Verbindung über HTTPS herstellen, gibt es einige zusätzliche Schritte, um sicherzustellen, dass Fiddler den HTTPS-Datenverkehr entschlüsseln kann. Weitere Informationen finden Sie in der [Fiddler-Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Nachdem Sie die Ablaufverfolgung erfasst haben, können Sie die Ablaufverfolgung exportieren, indem Sie in der Menüleiste **Datei** >  **Speichern** >  **Alle Sitzungen** auswählen.

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Erfassen einer Netzwerkablaufverfolgung mit tcpdump (nur macOS und Linux)

Diese Methode kann für alle Apps angewendet werden.

Sie können unformatierte TCP-Ablaufverfolgungen mithilfe von tcpdump erfassen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen. Möglicherweise müssen Sie dies als `root`-Benutzer tun oder dem Befehl `sudo` als Präfix voranstellen, wenn Sie einen Berechtigungsfehler erhalten:

```console
tcpdump -i [interface] -w trace.pcap
```

Ersetzen `[interface]` Sie durch die Netzwerkschnittstelle, an der Sie die Erfassung durchführen möchten. In der Regel ist dies etwas wie `/dev/eth0` (für Ihre Ethernet-Standardschnittstelle) oder `/dev/lo0` (für localhost-Datenverkehr). Weitere Informationen finden Sie auf der Manpage zu `tcpdump` auf Ihrem Hostsystem.

```console
man tcpdump
```

## <a name="collect-a-network-trace-in-the-browser-browser-based-apps-only"></a>Erfassen einer Netzwerkablaufverfolgung im Browser (nur browserbasierte Apps)

Die meisten Entwicklertools für Browser besitzen eine Registerkarte „Netzwerk“, über die Sie Netzwerkaktivitäten zwischen dem Browser und dem Server erfassen können. 

### <a name="microsoft-edge-chromium"></a>Microsoft Edge (Chromium)

1. Öffnen der [DevTools](/microsoft-edge/devtools-guide-chromium/)
    * `F12` auswählen 
    * Wählen Sie `Ctrl`+`Shift`+`I` \(Windows/Linux\) oder `Command`+`Option`+`I`\(macOS\) aus.
    * Wählen Sie `Settings and more` und dann `More Tools > Developer Tools` aus.  
1. Wählen Sie die Registerkarte `Network` aus.
1. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
1. Wählen Sie in der Symbolleiste `Export HAR...` aus, um die Ablaufverfolgung als HAR-Datei zu exportieren.

    :::image type="content" source="./media/howto-troubleshoot-network-trace/edge-export-network-trace.png" alt-text="Erfassen einer Netzwerkablaufverfolgung mit Microsoft Edge":::

### <a name="google-chrome"></a>Google Chrome

1. Öffnen der [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)
    * `F12` auswählen 
    * Wählen Sie `Ctrl`+`Shift`+`I` \(Windows/Linux\) oder `Command`+`Option`+`I`\(macOS\) aus.  
    * Wählen Sie `Customize and control Google Chrome` und dann `More Tools > Developer Tools` aus.
1. Wählen Sie die Registerkarte `Network` aus.
1. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
1. Wählen Sie in der Symbolleiste `Export HAR...` aus, um die Ablaufverfolgung als HAR-Datei zu exportieren.

    :::image type="content" source="./media/howto-troubleshoot-network-trace/chrome-export-network-trace.png" alt-text="Erfassen einer Netzwerkablaufverfolgung mit Google Chrome":::

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Öffnen der [Firefox-Entwicklertools](https://developer.mozilla.org/en-US/docs/Tools)
    * `F12` auswählen
    * Wählen Sie `Ctrl`+`Shift`+`I` \(Windows/Linux\) oder `Command`+`Option`+`I`\(macOS\) aus. 
    * Wählen Sie `Open menu` und dann `Web Developer > Toggle Tools` aus.
1. Wählen Sie die Registerkarte `Network` aus.
1. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
1. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Liste der Anforderungen, und wählen Sie „Alle als HAR speichern“ aus.

    :::image type="content" source="./media/howto-troubleshoot-network-trace/firefox-export-network-trace.png" alt-text="Erfassen einer Netzwerkablaufverfolgung mit Mozilla Firefox":::

### <a name="safari"></a>Safari

1. Öffnen der [Webentwicklungstools](https://developer.apple.com/safari/tools/)
    * Wählen Sie `Command`+`Option`+`I` aus.
    * Wählen Sie das Menü `Developer` und dann `Show Web Inspector` aus. 
1. Wählen Sie die Registerkarte `Network` aus.
1. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
1. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Liste der Anforderungen, und wählen Sie „Alle als HAR speichern“ aus.