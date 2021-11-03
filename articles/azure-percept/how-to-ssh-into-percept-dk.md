---
title: Herstellen einer Verbindung mit Azure Percept DK über SSH
description: Erfahren Sie, wie Sie mithilfe von PuTTY eine SSH-Verbindung mit Ihrem Azure Percept DK herstellen.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: edb45634ee58ada77320374cba4218f3fb00a2c3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006333"
---
# <a name="connect-to-azure-percept-dk-over-ssh"></a>Herstellen einer Verbindung mit Azure Percept DK über SSH

Führen Sie die folgenden Schritte aus, um über OpenSSH oder [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) eine SSH-Verbindung mit Ihrem Azure Percept DK einzurichten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Windows-, Linux- oder OS X-basierter Hostcomputer mit WLAN-Funktion
- Ein SSH-Client (weitere Informationen zur Installation finden Sie im nächsten Abschnitt)
- Ein Azure Percept DK (Development Kit)
- Ein SSH-Konto, das während des [Azure Percept DK-Setups](./quickstart-percept-dk-set-up.md) erstellt wurde

## <a name="install-your-preferred-ssh-client"></a>Installieren Ihres bevorzugten SSH-Clients

Wenn auf dem Hostcomputer Linux oder OS X ausgeführt wird, sind SSH-Dienste in diesen Betriebssystemen enthalten und können ohne separate Clientanwendung ausgeführt werden. Weitere Informationen zum Ausführen von SSH-Diensten finden Sie in der Produktdokumentation des Betriebssystems.

Wenn auf dem Hostcomputer Windows ausgeführt wird, stehen Ihnen möglicherweise zwei SSH-Clientoptionen zur Auswahl: OpenSSH und PuTTY.

### <a name="openssh"></a>OpenSSH

Windows 10 enthält einen integrierten SSH-Client namens OpenSSH, der mit einem einfachen Befehl in einer Eingabeaufforderung ausgeführt werden kann. Sie sollten OpenSSH mit Azure Percept verwenden, sofern für Sie verfügbar. Gehen Sie folgendermaßen vor, um zu überprüfen, ob OpenSSH auf dem Windows-Computer installiert ist:

1. Wechseln Sie zu **Start** -> **Einstellungen**.

1. Klicken Sie auf **Apps**.

1. Wählen Sie unter **Apps & Features** die Option **Optionale Features** aus.

1. Geben Sie **OpenSSH-Client** in die Suchleiste **Installierte Features** ein. Wenn OpenSSH angezeigt wird, ist der Client bereits installiert, und Sie können mit dem nächsten Abschnitt fortfahren. Wenn OpenSSH nicht angezeigt wird, wählen Sie **Feature hinzufügen** aus.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Screenshot der Einstellungen mit OpenSSH-Installationsstatus.":::

1. Wählen Sie **OpenSSH-Client** und dann **Installieren** aus. Sie können jetzt mit dem nächsten Abschnitt fortfahren. Wenn OpenSSH nicht zur Installation auf Ihrem Computer verfügbar ist, führen Sie die folgenden Schritte aus, um PuTTY, einen Drittanbieter-SSH-Client, zu installieren.

### <a name="putty"></a>PuTTY.

Wenn OpenSSH auf Ihrem Windows-Computer nicht vorhanden ist, sollten Sie [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) verwenden. Führen Sie die folgenden Schritte aus, um PuTTY herunterzuladen und zu installieren:

1. Wechseln Sie zur [PuTTY-Downloadseite](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Wählen Sie unter **Paketdateien** die 32-Bit- oder 64-Bit-MSI-Datei aus, um das Installationsprogramm herunterzuladen. Wenn Sie nicht sicher sind, welche Version ausgewählt werden soll, lesen Sie die [häufig gestellten Fragen](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Wählen Sie das Installationsprogramm aus, um die Installation zu starten. Befolgen Sie die Anweisungen.

1. Herzlichen Glückwunsch! Der PuTTY-SSH-Client wurde erfolgreich installiert.

## <a name="initiate-the-ssh-connection"></a>Initiieren der SSH-Verbindung

1. Aktivieren Sie Ihr Azure Percept DK.

1. Wenn Ihr DevKit Kit bereits über Ethernet oder WLAN mit einem Netzwerk verbunden ist, fahren Sie mit dem nächsten Schritt fort. Stellen Sie andernfalls eine direkte Verbindung Ihres Hostcomputers mit dem WLAN-Zugriffspunkt des Development Kits her. Öffnen Sie dazu genau wie bei anderen WLAN-Verbindungen die Netzwerk- und Interneteinstellungen auf Ihrem Computer, wählen Sie das folgende Netzwerk aus, und geben Sie das Netzwerkkennwort ein, wenn Sie dazu aufgefordert werden:

    - **Netzwerkname:** Der Name des WLAN-Zugriffspunkts lautet abhängig von der Betriebssystemversion Ihres Development Kits entweder **scz-xxxx** oder **apd-xxxx** (wobei „xxxx“ für die letzten vier Ziffern der MAC-Adresse des Dev Kits steht).
    - **Kennwort:** Das Kennwort finden Sie auf der Begrüßungskarte, die im Lieferumfang des Development Kits enthalten war.

    > [!WARNING]
    > Während Sie eine Verbindung mit dem WLAN-Zugriffspunkt des Azure Percept DK herstellen, verliert Ihr Hostcomputer vorübergehend seine Internetverbindung. Aktive Videokonferenzanrufe, Webstreaming oder andere netzwerkbasierte Erfahrungen werden unterbrochen.

1. Vervollständigen Sie den SSH-Verbindungsprozess gemäß Ihrem SSH-Client.

### <a name="using-openssh"></a>Verwenden von OpenSSH

1. Öffnen Sie eine Eingabeaufforderung (**Start** -> **Eingabeaufforderung**).

1. Geben Sie an der Eingabeaufforderung Folgendes ein:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Wenn Ihr Computer mit dem WLAN-Zugangspunkt des Dev Kits verbunden ist, lautet die IP-Adresse 10.1.1.1. Wenn Ihr DevKit über Ethernet verbunden ist, verwenden Sie die lokale IP-Adresse des Geräts, die Sie auf dem Ethernet-Router oder-Hub finden. Wenn Ihr Dev Kit über WLAN verbunden ist, müssen Sie die IP-Adresse verwenden, die während des [Einrichtens Ihres Azure Percept DK und Bereitstellens Ihres ersten KI-Modells](./quickstart-percept-dk-set-up.md) bereitgestellt wurde.

    > [!TIP]
    > Wenn Ihr Dev Kit mit einem Wi-Fi Netzwerk verbunden ist, Sie aber seine IP-Adresse nicht kennen, besuchen Sie Azure Percept Studio, und [öffnen Sie den Videostream Ihres Geräts](./how-to-view-video-stream.md). In der Adressleiste auf der Videodatenstrombrowser-Registerkarte wird die IP-Adresse Ihres Geräts angezeigt.

1. Geben Sie Ihr SSH-Kennwort ein, wenn Sie dazu aufgefordert werden.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Screenshot der OpenSSH-Eingabeaufforderung zur Anmeldung.":::

1. Wenn Sie zum ersten Mal eine Verbindung mit dem Dev Kit über OpenSSH herstellen, werden Sie möglicherweise auch aufgefordert, den Schlüssel des Hosts zu akzeptieren. Geben Sie **Ja** ein, um den Schlüssel zu akzeptieren.

1. Herzlichen Glückwunsch! Sie haben erfolgreich über SSH eine Verbindung mit Ihrem Dev Kit hergestellt.

### <a name="using-putty"></a>Verwenden von PuTTY

1. Öffnen Sie PuTTY. Geben Sie im Fenster der **PuTTY-Konfiguration** Folgendes ein, und wählen Sie **Öffnen** aus, um per SSH eine Verbindung mit Ihrem Dev Kit herzustellen:

    1. Hostname: [IP-Adresse]
    1. Port: 22
    1. Verbindungstyp: SSH

    Der **Hostname** ist die IP-Adresse Ihres Dev Kits. Wenn Ihr Computer mit dem WLAN-Zugangspunkt des Dev Kits verbunden ist, lautet die IP-Adresse 10.1.1.1. Wenn Ihr DevKit über Ethernet verbunden ist, verwenden Sie die lokale IP-Adresse des Geräts, die Sie auf dem Ethernet-Router oder-Hub finden. Wenn Ihr Dev Kit über WLAN verbunden ist, müssen Sie die IP-Adresse verwenden, die während des [Einrichtens Ihres Azure Percept DK und Bereitstellens Ihres ersten KI-Modells](./quickstart-percept-dk-set-up.md) bereitgestellt wurde.

    > [!TIP]
    > Wenn Ihr Dev Kit mit einem Wi-Fi Netzwerk verbunden ist, Sie aber seine IP-Adresse nicht kennen, besuchen Sie Azure Percept Studio, und [öffnen Sie den Videostream Ihres Geräts](./how-to-view-video-stream.md). In der Adressleiste auf der Videodatenstrombrowser-Registerkarte wird die IP-Adresse Ihres Geräts angezeigt.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Screenshot des Fensters der „PuTTY-Konfiguration“.":::

1. Ein PuTTY-Terminal wird geöffnet. Wenn Sie dazu aufgefordert werden, geben Sie den SSH-Benutzernamen und das Kennwort im Terminal ein.

1. Herzlichen Glückwunsch! Sie haben erfolgreich über SSH eine Verbindung mit Ihrem Dev Kit hergestellt.

## <a name="next-steps"></a>Nächste Schritte

Nach der Verbindung mit Ihrem Azure Percept DK über SSH können Sie eine Reihe von Aufgaben durchführen, einschließlich [Fehlerbehebung bei Geräten](./troubleshoot-dev-kit.md) und [USB-Updates](./how-to-update-via-usb.md).
