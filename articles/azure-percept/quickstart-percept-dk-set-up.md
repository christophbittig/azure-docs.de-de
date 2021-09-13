---
title: Einrichten des Azure Percept DK-Geräts
description: Einrichten Ihres Azure Percept DK und Herstellen einer Verbindung mit Azure IoT Hub
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 601970978dc69aea2a6f0947a26cc0bebc1d999d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223030"
---
# <a name="set-up-the-azure-percept-dk-device"></a>Einrichten des Azure Percept DK-Geräts

Durchlaufen Sie die Einrichtungserfahrung für das Azure Percept DK, um Ihr Dev Kit zu konfigurieren. Nachdem Sie sich vergewissert haben, dass Ihr Azure-Konto mit Azure Percept kompatibel ist, führen Sie die folgenden Schritte aus:

- Starten des Azure Percept DK-Einrichtungsassistenten
- Herstellen einer WLAN-Verbindung für Ihr Development Kit
- Einrichten einer SSH-Anmeldung für Remotezugriff auf Ihr Development Kit
- Erstellen eines neuen Geräts in Azure IoT Hub

Sollten im Rahmen dieses Prozesses Probleme auftreten, finden Sie im [Leitfaden zur Problembehandlung](./how-to-troubleshoot-setup.md) Informationen zu möglichen Lösungen.

> [!NOTE]
> Die Einrichtungserfahrung des Webdiensts wird nach 30-minütiger Inaktivität automatisch heruntergefahren. Wenn Sie keine Verbindung mit dem Dev Kit herstellen können oder dessen WLAN-Zugriffspunkt nicht angezeigt wird, starten Sie das Gerät neu.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Percept DK (Development Kit).
- Ein Windows-, Linux- oder OS X-basierter Hostcomputer mit WLAN und einem Webbrowser.
- Ein aktives Azure-Abonnement. [Kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Benutzer müssen innerhalb des Abonnements über die Rolle **Besitzer** oder **Mitwirkender** verfügen. Führen Sie die im Anschluss angegebenen Schritte aus, um die Rolle Ihres Azure-Kontos zu prüfen. Weitere Informationen zu Azure-Rollendefinitionen finden Sie in der [Dokumentation zur rollenbasierten Zugriffssteuerung in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

    > [!CAUTION]
    > Schließen Sie alle Browserfenster, und melden Sie sich über das [Azure-Portal](https://portal.azure.com/) bei Ihrem Abonnement an, bevor Sie mit der Einrichtungserfahrung beginnen. Weitere Informationen dazu, wie Sie sicherstellen können, dass Sie mit dem richtigen Konto angemeldet sind, finden Sie im [Leitfaden zur Problembehandlung](./how-to-troubleshoot-setup.md).

### <a name="check-your-azure-account-role"></a>Überprüfen der Rolle Ihres Azure-Kontos

Gehen Sie wie folgt vor, um sich zu vergewissern, dass Ihr Azure-Konto innerhalb des Abonnements über die Rolle „Besitzer“ oder „Mitwirkender“ verfügt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit dem Azure-Konto an, das Sie mit Azure Percept verwenden möchten.

1. Klicken Sie auf das Symbol **Abonnements** (gelber Schlüssel).

1. Wählen Sie Ihr Abonnement in der Liste aus. Sollte Ihr Abonnement nicht angezeigt werden, vergewissern Sie sich, dass Sie mit dem richtigen Azure-Konto angemeldet sind. Wenn Sie ein neues Abonnement erstellen möchten, befolgen Sie [diese Schritte](../cost-management-billing/manage/create-subscription.md).

1. Wählen Sie im Menü „Abonnement“ die Option **Zugriffssteuerung (IAM)** aus.
1. Klicken Sie auf **View my access**.
1. Überprüfen Sie die Rolle:
    - Wenn Ihre Rolle **Leser** lautet oder eine Meldung mit dem Hinweis angezeigt wird, dass Sie nicht zum Anzeigen von Rollen berechtigt sind, muss die Rolle Ihres Kontos erhöht werden. Verwenden Sie hierzu den in Ihrer Organisation vorgesehenen Prozess.
    - Wenn Ihre Rolle **Besitzer** oder **Mitwirkender** lautet, kann Ihr Konto mit Azure Percept verwendet werden, und Sie können mit der Einrichtungserfahrung fortfahren.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Starten der Azure Percept DK-Einrichtungserfahrung

1. Verbinden Sie Ihren Hostcomputer mit dem WLAN-Zugriffspunkt des Dev Kits. Wählen Sie das folgende Netzwerk aus, und geben Sie das WLAN-Kennwort ein, wenn Sie dazu aufgefordert werden:

    - **Netzwerkname**: **scz-xxxx** oder **apd-xxxx** (wobei **xxxx** die letzten vier Ziffern der MAC-Adressen des DevKits sind)
    - **Kennwort**: Zu finden auf der Begrüßungskarte, die im Lieferumfang des Dev Kits enthalten war.

    > [!WARNING]
    > Während Sie eine Verbindung mit dem WLAN-Zugriffspunkt des Azure Percept DK herstellen, verliert Ihr Hostcomputer vorübergehend seine Internetverbindung. Aktive Videokonferenzanrufe, Webstreaming oder andere netzwerkbasierte Erfahrungen werden unterbrochen.

1. Nachdem die Verbindung mit dem WLAN-Zugriffspunkt des Development Kits hergestellt wurde, startet der Hostcomputer die Einrichtungserfahrung automatisch in einem neuen Browserfenster mit **your.new.device/** in der Adressleiste. Wenn die Registerkarte nicht automatisch geöffnet wird, starten Sie die Einrichtung, indem Sie in einem Webbrowser zu [http://10.1.1.1](http://10.1.1.1) navigieren. Vergewissern Sie sich, dass Ihr Browser mit den Azure-Kontoanmeldeinformationen angemeldet ist, die Sie mit Azure Percept verwenden möchten.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-welcome.png" alt-text="Begrüßungsseite.":::

    > [!NOTE]
    > **Mac-Benutzer**: Wenn Sie die Einrichtungserfahrung auf einem Mac durchlaufen, wird zunächst ein Captive Portal-Fenster geöffnet, in dem sich die Einrichtungserfahrung nicht abschließen lässt. Schließen Sie dieses Fenster, und öffnen Sie einen Webbrowser mit der Adresse https://10.1.1.1, was es ihnen erlaubt, die Einrichtungserfahrung abzuschließen.

## <a name="connect-your-dev-kit-to-a-wi-fi-network"></a>Herstellen einer WLAN-Verbindung für Ihr Development Kit

1. Klicken Sie auf dem Bildschirm **Willkommen** auf **Weiter**.

1. Klicken Sie auf der Seite **Netzwerkverbindung** auf **Verbindung mit neuem WLAN herstellen**.

    Falls Sie Ihr Development Kit bereits mit Ihrem WLAN verbunden haben, klicken Sie auf **Überspringen**.

1. Wählen Sie in der Liste mit den verfügbaren Netzwerken Ihr WLAN aus, und klicken Sie auf **Verbinden**. Geben Sie Ihr Netzwerkkennwort ein, wenn Sie dazu aufgefordert werden.

    > [!NOTE]
    > Es wird empfohlen, dieses Netzwerk als „Bevorzugtes Netzwerk“ (Mac) festlegen oder das Kontrollkästchen „Automatisch verbinden“ (Windows) aktivieren.  Dadurch kann der Hostcomputer erneut eine Verbindung mit dem WLAN-Zugriffspunkt des Dev Kits herstellen, wenn die Verbindung während dieses Vorgangs unterbrochen wird.

1. Wenn das Dev Kit erfolgreich eine Verbindung hergestellt hat, wird auf der Seite die IPv4-Adresse angezeigt, die Ihrem Dev Kit zugewiesen ist. **Notieren Sie sich die auf der Seite angezeigte IPv4-Adresse.** Die IP-Adresse wird benötigt, wenn Sie eine SSH-Verbindung mit Ihrem Development Kit herstellen möchten, um Probleme zu behandeln oder Geräteupdates durchzuführen.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-success-wi-fi.png" alt-text="Kopieren der IP-Adresse.":::

    > [!NOTE]
    > Die IP-Adresse kann sich nach jedem Gerätestart ändern.

1. Lesen Sie den Lizenzvertrag (Sie müssen bis zum Ende des Vertrags scrollen), und wählen Sie **Ich habe den Lizenzvertrag gelesen und stimme ihm zu** aus. Wählen Sie anschließend **Weiter** aus.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-eula.png" alt-text="Akzeptieren des Lizenzvertrags.":::

## <a name="set-up-an-ssh-login-for-remote-access-to-your-dev-kit"></a>Einrichten einer SSH-Anmeldung für Remotezugriff auf Ihr Development Kit

1. Erstellen Sie einen SSH-Kontonamen und einen öffentlichen Schlüssel bzw. ein Kennwort, und wählen Sie **Weiter** aus.

    Wenn Sie bereits ein SSH-Konto erstellt haben, können Sie diesen Schritt überspringen.

    **Notieren Sie sich Ihre Anmeldeinformationen für die spätere Verwendung**.

    > [!NOTE]
    > SSH (Secure Shell) ist ein Netzwerkprotokoll, das es Ihnen ermöglicht, über einen Hostcomputer eine Remoteverbindung mit dem Development Kit herzustellen.

##  <a name="create-a-new-device-in-azure-iot-hub"></a>Erstellen eines neuen Geräts in Azure IoT Hub

1. Wählen Sie **Als neues Gerät einrichten** aus, um in Ihrem Azure-Konto ein neues Gerät zu erstellen.

    Jetzt wird ein Gerätecode von Azure abgerufen.

1. Wählen Sie **Kopieren** aus.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-copy-code.png" alt-text="Kopieren des Gerätecodes.":::

    > [!NOTE]
    > Wenn Sie bei der Verwendung Ihres Gerätecodes in den nächsten Schritten eine Fehlermeldung erhalten, oder wenn der Gerätecode nicht angezeigt wird, finden Sie weitere Informationen in unseren [Problembehandlungsschritten](./how-to-troubleshoot-setup.md). 

1. Wählen Sie **Bei Azure anmelden** aus.

1. Daraufhin wird eine neue Browserregisterkarte mit einem Fenster für die **Codeeingabe** geöffnet. Fügen Sie den Code in das Fenster ein, und klicken Sie auf **Weiter**. Lassen Sie die Registerkarte **Willkommen** mit der Einrichtungserfahrung GEÖFFNET.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-enter-code.png" alt-text="Eingeben des Gerätecodes.":::

1. Melden Sie sich unter Verwendung der Azure-Kontoanmeldeinformationen für Ihr Development Kit bei Azure Percept an. Lassen Sie die Browserregisterkarte nach Abschluss des Vorgangs geöffnet.

    > [!CAUTION]
    > Es kann sein, dass Ihr Browser andere Anmeldeinformationen automatisch zwischenspeichert. Stellen Sie sicher, dass Sie sich mit dem richtigen Konto anmelden.

    Wählen Sie nach erfolgreicher Anmeldung bei Azure Percent auf dem Gerät **Zulassen** aus. 
    
    Kehren Sie zur Registerkarte **Willkommen** zurück, um die Einrichtungserfahrung fortzusetzen.

1. Sollte auf der Registerkarte **Willkommen** die Seite **Gerät Ihrer Azure IoT Hub-Instanz zuweisen** angezeigt werden, führen Sie eine der folgenden Aktionen aus:

    - Wechseln Sie zu **Ihren Azure IoT Hub auswählen**, wenn Ihr IoT Hub auf dieser Seite aufgeführt ist.
    - Falls Sie über keine IoT Hub-Instanz verfügen oder eine neue Instanz erstellen möchten, klicken Sie auf **Neue Azure IoT Hub-Instanz erstellen**.

    > [!IMPORTANT]
    > Falls Sie über eine IoT Hub-Instanz verfügen, diese aber nicht in der Liste aufgeführt ist, haben Sie sich möglicherweise mit den falschen Anmeldeinformationen bei Azure Percept angemeldet. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./how-to-troubleshoot-setup.md).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-iot-hub-select.png" alt-text="Auswählen eines IoT Hubs.":::

1. So erstellen Sie einen neuen IoT Hub

    - Wählen Sie das Azure-Abonnement aus, das Sie mit Azure Percept verwenden möchten.
    - Wählen Sie eine vorhandene Ressourcengruppe aus. Sollte keine vorhanden sein, klicken Sie auf **Neu erstellen**, und folgen Sie den Anweisungen.
    - Wählen Sie die Azure-Region aus, die Ihrem physischen Standort am nächsten ist.
    - Benennen Sie Ihre neue IoT Hub-Instanz.
    - Wählen Sie den Tarif „S1“ (Standard) aus.

    > [!NOTE]
    > Es kann einige Minuten dauern, bis ihre IoT Hub-Bereitstellung abgeschlossen ist. Wenn Sie für Ihre Edge-KI-Anwendungen einen höheren [Nachrichtendurchsatz](../iot-hub/iot-hub-scaling.md#message-throughput) benötigen, können Sie [Ihren IoT Hub im Azure-Portal jederzeit auf einen höheren Standard-Tarif upgraden](../iot-hub/iot-hub-upgrade.md). In den Tarifen B und F wird Azure Percept NICHT unterstützt.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Registrieren**.

1. Auswählen Ihres Azure IoT Hubs

1. Geben Sie einen Gerätenamen für Ihr Development Kit ein, und klicken Sie auf **Weiter**.

1. Die Gerätemodule werden nun auf Ihrem Gerät bereitgestellt. Dies kann einige Minuten dauern.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-finalize.png" alt-text="Abschließen der Einrichtung.":::

1. **Geräteeinrichtung abgeschlossen.** Ihr Dev Kit wurde erfolgreich mit Ihrem IoT Hub verknüpft, und es wurden alle Module bereitgestellt.

    > [!NOTE]
    > Nach Abschluss des Vorgangs wird die Verbindung des WLAN-Zugriffspunkts des Dev Kits automatisch getrennt, und die Einrichtungserfahrung des Webdiensts wird beendet, was zu zwei Benachrichtigungen führt.

    > [!NOTE]
    > Für die IoT Edge-Container, die bei diesem Setupprozess konfiguriert werden, werden Zertifikate genutzt, die nach 90 Tagen ablaufen. Die Zertifikate können automatisch erneut generiert werden, indem IoT Edge neu gestartet wird. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten auf einem IoT Edge-Gerät](../iot-edge/how-to-manage-device-certificates.md).

1. Verbinden Sie Ihren Hostcomputer mit dem WLAN, mit dem Ihr Dev Kit verbunden ist.

1. Klicken Sie auf **Zum Azure-Portal wechseln**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-Azure-portal-continue.png" alt-text="Wechseln zu Azure Percept Studio.":::

### <a name="video-walk-through"></a>Exemplarische Vorgehensweise im Video 
Im folgenden Video finden Sie eine visuelle exemplarische Vorgehensweise zu den oben beschriebenen Schritten.
> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Ihr Dev Kit nun eingerichtet ist, ist es an der Zeit, Vision-KI in Aktion zu sehen.
- [Anzeigen Ihres Dev Kit-Videostreams](./how-to-view-video-stream.md)
- [Bereitstellen eines Vision-KI-Modells für Ihr Dev Kit](./how-to-deploy-model.md)