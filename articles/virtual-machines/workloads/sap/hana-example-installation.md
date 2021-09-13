---
title: Installieren von HANA unter SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie HANA unter SAP HANA in Azure (große Instanzen) installieren.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/4/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e4e7aec0422d3dfe5772748215c350073155a30
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559786"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installieren von HANA unter SAP HANA in Azure (große Instanzen)

In diesem Artikel werden die einzelnen Schritte zum Installieren von HANA unter SAP HANA in Azure (große Instanzen) – auch als BareMetal-Infrastruktur bezeichnet – beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

So installieren Sie HANA unter SAP HANA in Azure (große Instanzen). Zuerst:

- Liefern Sie alle Daten an Microsoft, die auf einer großen SAP HANA-Instanz bereitgestellt werden sollen.
- Sie erhalten die große SAP HANA-Instanz von Microsoft.
- Sie erstellen ein virtuelles Azure-Netzwerk, das mit Ihrem lokalen Netzwerk verbunden ist.
- Sie stellen die ExpressRoute-Verbindung für HANA (große Instanzen) mit demselben virtuellen Azure-Netzwerk her.
- Sie installieren eine Azure-VM, die Sie als Jumpbox für HANA (große Instanzen) verwenden.
- Sie stellen sicher, dass Sie eine Verbindung von der Jumpbox mit HANA (große Instanz) und umgekehrt herstellen können.
- Sie überprüfen, ob alle erforderlichen Pakete und Patches installiert sind.
- Sie lesen die SAP-Hinweise und die Dokumentation zur HANA-Installation für das von Ihnen verwendete Betriebssystem. Stellen Sie sicher, dass das ausgewählte HANA-Release für Ihre Betriebssystemversion unterstützt wird.

## <a name="download-the-sap-hana-installation-bits"></a>Herunterladen der SAP HANA-Installationsdaten

Laden Sie nun die HANA-Installationspakete auf den virtuellen Jumpboxcomputer herunter. In diesem Beispiel ist das Betriebssystem Windows.

Die HANA-Einheiten (große Instanzen) sind nicht direkt mit dem Internet verbunden. Sie können die Installationspakete nicht direkt von SAP auf die HANA-VM (große Instanzen) herunterladen. Stattdessen laden Sie die Pakete auf die Jumpbox-VM herunter.

Sie benötigen einen SAP S-Benutzer oder einen anderen Benutzer, mit dem Sie auf den SAP Marketplace zugreifen können.

1. Melden Sie sich an, und wechseln Sie zum [SAP Service Marketplace](https://support.sap.com/en/index.html). Wählen Sie **Download Software** > **Installations and Upgrade** > **By Alphabetical Index** aus (Software herunterladen -> Installationen und Upgrades -> Nach alphabetischem Index). Wählen Sie dann unter H **SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installation** aus. Laden Sie die im folgenden Screenshot gezeigten Dateien herunter.

   ![Screenshot der herunterzuladenden HANA-Installationsdateien](./media/hana-installation/image16_download_hana.PNG)

2. In diesem Beispiel haben wir SAP HANA 2.0-Installationspakete heruntergeladen. Auf der Azure-Jumpbox-VM erweitern Sie die selbstextrahierenden Archive in das Verzeichnis, wie unten gezeigt.

   ![Screenshot eines selbstextrahierenden Archivs](./media/hana-installation/image17_extract_hana.PNG)

3. Wenn die Archive extrahiert wurden, kopieren Sie das durch die Extraktion erstellte Verzeichnis (in diesem Fall „51052030“). Kopieren Sie das Verzeichnis aus dem Volume „/hana/shared“ der HANA-Einheit (große Instanzen) in das erstellte Verzeichnis.

   > [!Important]
   > Kopieren Sie die Installationspakete nicht in die Stamm- oder Start-LUN. Der Speicherplatz ist begrenzt und muss auch von anderen Prozessen genutzt werden.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installieren von SAP HANA auf der Einheit von HANA (große Instanzen)

1. Für die Installation von SAP HANA müssen Sie sich als Stammbenutzer (root) anmelden. Nur der Stammbenutzer verfügt über ausreichende Berechtigungen zum Installieren von SAP HANA. Legen Sie Berechtigungen für das Verzeichnis fest, das Sie in „/hana/shared“ kopiert haben.

    ```
    chmod –R 744 <Installation bits folder>
    ```
    
    Zum Installieren von SAP HANA über die grafische Benutzeroberfläche muss das Paket „gtk2“ auf HANA (große Instanzen) installiert werden. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob das Paket installiert ist:
    
    ```
    rpm –qa | grep gtk2
    ```

    (In späteren Schritten zeigen wir Ihnen das SAP HANA-Setup mit der grafischen Benutzeroberfläche.)         

2. Wechseln Sie zum Installationsverzeichnis und dann in das Unterverzeichnis „HDB_LCM_LINUX_X86_64“. 

    Starten Sie in diesem Verzeichnis folgende Elemente:
    
    ```
    ./hdblcmgui 
    ```
3. Nun wird eine Reihe von Bildschirmen angezeigt, in denen Sie Daten für die Installation angeben müssen. In diesem Beispiel installieren wir den SAP HANA-Datenbankserver und die SAP HANA-Clientkomponenten. Daher wählen wir **SAP HANA-Datenbank** aus.

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit ausgewählter Option „SAP HANA Database“](./media/hana-installation/image18_hana_selection.PNG)

4. Wählen Sie **Neues System installieren** aus.

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit ausgewählter Option „Neues System installieren“](./media/hana-installation/image19_select_new.PNG)

5. Wählen Sie sonstige Komponenten aus, die Sie installieren können.

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit einer Liste zusätzlicher Komponenten](./media/hana-installation/image20_select_components.PNG)

6. Hier wählen wir den SAP HANA-Client und SAP HANA Studio aus. Außerdem installieren wir eine Instanz zum zentralen Hochskalieren. Wählen Sie jetzt **Einzelhostsystem** aus. 

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit ausgewählter Option „Einzelhostsystem“](./media/hana-installation/image21_single_host.PNG)

7. Als Nächstes geben Sie einige Daten an. Als Installationspfad verwenden Sie das Verzeichnis „/hana/shared“.

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit zu definierenden Systemeigenschaftenfeldern](./media/hana-installation/image22_provide_sid.PNG)

    > [!Important]
    > Als HANA-SID (System-ID) müssen Sie die gleiche SID angeben, die Sie für Microsoft bereitgestellt haben, als Sie die HANA-Bereitstellung (große Instanzen) bestellt haben. Wenn Sie eine andere SID wählen, kann die Installation nicht erfolgreich durchgeführt werden, da sich für die verschiedenen Volumes Probleme mit der Zugriffsberechtigung ergeben.

8. Geben Sie die Speicherorte für die HANA-Datendateien und die HANA-Protokolldateien an.

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit Feldern für Daten und Protokollbereich](./media/hana-installation/image23_provide_log.PNG)

    > [!Note]
    > Die SID, die Sie während der Definition der Systemeigenschaften (zwei Bildschirme vorher) angegeben haben, sollte der SID der Mountpunkte entsprechen. Wenn diese Werte nicht übereinstimmen, gehen Sie zwei Bildschirme zurück, und legen Sie die SID auf denselben Wert wie für die Mountpunkte fest.

9. Überprüfen Sie den Hostnamen, und korrigieren Sie ihn bei Bedarf. 

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit dem Hostnamen](./media/hana-installation/image24_review_host_name.PNG)

10. Rufen Sie die Daten ab, die Sie Microsoft geliefert haben, als Sie die Bereitstellung von HANA (große Instanzen) bestellt haben. 

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit zu definierenden Systemadministratorfeldern](./media/hana-installation/image25_provide_guid.PNG)

    > [!Important]
    > Geben Sie die gleiche **Benutzer-ID für den Systemadministrator** und **Benutzergruppen-ID** an, die Sie beim Bestellen der Einheitenbereitstellung bei Microsoft angegeben haben. Andernfalls kann die Installation von SAP HANA auf der HANA-Einheit (große Instanzen) nicht erfolgreich durchgeführt werden.

11. Die beiden nächsten Fenster werden hier nicht gezeigt. Dort können Sie das Kennwort für den SYSTEM-Benutzer der SAP HANA-Datenbank und das Kennwort für den sapadm-Benutzer angeben. Letzteres wird für den SAP-Host-Agent verwendet, der als Teil der SAP HANA-Datenbankinstanz installiert wird.

    Nach dem Definieren des Kennworts wird ein Bestätigungsfenster angezeigt. Überprüfen Sie alle aufgeführten Daten, und fahren Sie mit der Installation fort. Sie gelangen zu einem Statusfenster, auf dem der Installationsfortschritt angezeigt wird:

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit Anzeige des Installationsfortschritts](./media/hana-installation/image27_show_progress.PNG)

12. Wenn die Installation abgeschlossen ist, sollte ein Bildschirm wie dieser angezeigt werden:

    ![Screenshot des Bildschirms „SAP HANA Lifecycle Management“, der auf den Abschluss der Installation hinweist](./media/hana-installation/image28_install_finished.PNG)

    Die SAP HANA-Instanz sollte jetzt ausgeführt werden und betriebsbereit sein. Sie können über SAP HANA Studio eine Verbindung damit herstellen. Stellen Sie sicher, dass Sie nach den neuesten Updates suchen und diese anwenden.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen).

> [!div class="nextstepaction"]
> [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md)
