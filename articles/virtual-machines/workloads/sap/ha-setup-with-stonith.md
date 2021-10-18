---
title: Hochverfügbarkeitskonfiguration mit STONITH für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie für SAP HANA in Azure (große Instanzen) unter SUSE mithilfe des STONITH-Geräts für Hochverfügbarkeit sorgen.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5866fbb227477b0079f5f2ac314357ca8e67a364
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710843"
---
# <a name="high-availability-setup-in-suse-using-the-stonith-device"></a>Einrichten von Hochverfügbarkeit unter SUSE mithilfe des STONITH-Geräts

In diesem Artikel werden die Schritte zum Einrichten von Hochverfügbarkeit in HANA (große Instanzen) unter dem Betriebssystem SUSE mithilfe des STONITH-Geräts erläutert.

> [!NOTE]
> Dieser Leitfaden ist im Rahmen von erfolgreichen Tests der Konfiguration in der Microsoft HANA-Umgebung (große Instanzen) entstanden. Das Microsoft Service Management-Team für HANA (große Instanzen) unterstützt das Betriebssystem nicht. Wenden Sie sich an SUSE, um Informationen zur Problembehandlung des Betriebssystems oder Erläuterungen zur Betriebssystemebene zu erhalten. 
>
> Das Microsoft Service Management-Team richtet das STONITH-Gerät ein und unterstützt es vollständig. Es kann bei der Behandlung von STONITH-Geräteproblemen helfen.

## <a name="prerequisites"></a>Voraussetzungen

Um Hochverfügbarkeit mithilfe von SUSE-Clustering einzurichten, ist Folgendes erforderlich:

- Bereitstellen von HANA (große Instanzen).
- Installieren und Registrieren des Betriebssystems mit den neuesten Patches.
- Verbinden von HANA-Servern (große Instanzen) mit dem SMT-Server, um Patches und Pakete zu erhalten.
- Einrichten von Network Time Protocol (NTP-Zeitserver).
- Lesen und Verstehen der aktuellen Version der SUSE-Dokumentation zum Einrichten von Hochverfügbarkeit.

## <a name="setup-details"></a>Details zur Konfiguration

In diesem Leitfaden wird folgendes Setup verwendet:

- Betriebssystem: SLES 12 SP1 für SAP
- Große HANA-Instanzen: 2 x S192 (vier Sockets, 2TB)
- HANA-Version: HANA 2.0 SP1
- Servernamen: „sapprdhdb95“ (node1) und „sapprdhdb96“ (node2)
- STONITH-Gerät: iSCSI-basiert
- NTP auf einem der HANA-Knoten (große Instanzen)

Wenn Sie HANA (große Instanzen) mit HANA-Systemreplikation einrichten, können Sie beim Microsoft Service Management-Team die Einrichtung des STONITH-Geräts beantragen. Führen Sie dies zum Zeitpunkt der Bereitstellung aus. 

Auch wenn Sie Bestandskunde mit bereits bereitgestelltem HANA (großen Instanzen) sind, können Sie das STONITH-Gerät dennoch einrichten. Geben Sie dem Microsoft Service Management-Team im Service Request-Formular die folgenden Informationen an. Sie können das Service Request-Formular über den Technical Account Manager oder Ihren Microsoft-Kontakt für das Onboarding von HANA (große Instanzen) erhalten.

- Servername und Server-IP-Adresse (z. B. myhanaserver1, 10.35.0.1)
- Standort (z.B. „USA, Osten“)
- Name des Kunden (z. B. Microsoft)
- HANA-Systembezeichner (SID) (z.B. „H11“)

Nachdem das STONITH-Gerät konfiguriert wurde, erhalten Sie vom Microsoft Service Management-Team den SBD-Namen (STONITH Block Device) und die IP-Adresse des iSCSI-Speichers. Anhand dieser Informationen können Sie die STONITH-Einrichtung konfigurieren. 

Führen Sie die Schritte in den folgenden Abschnitten aus, um Hochverfügbarkeit mit STONITH einzurichten.

## <a name="identify-the-sbd-device"></a>Identifizieren des SBD-Geräts

> [!NOTE]
> Dieser Abschnitt gilt nur für Bestandskunden. Wenn Sie Neukunde sind, nennt Ihnen das Microsoft Service Management-Team den Namen des SBD-Geräts. Überspringen Sie daher diesen Abschnitt.

1. Ändern Sie */etc/iscsi/initiatorname.isci* wie folgt: 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Diese Zeichenfolge wird vom Microsoft Service Management-Team bereitgestellt. Ändern Sie die Datei auf *beiden* Knoten. Die Knotennummer ist jedoch auf jedem Knoten unterschiedlich.
    
    ![Screenshot: initiatorname-Datei mit InitiatorName-Werten für einen Knoten.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. Ändern Sie */etc/iscsi/iscsid.conf*, indem Sie `node.session.timeo.replacement_timeout=5` und `node.startup = automatic` festlegen. Ändern Sie die Datei auf *beiden* Knoten.

3. Führen Sie den folgenden Ermittlungsbefehl auf *beiden* Knoten aus.

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    Die Ergebnisse zeigen vier Sitzungen an.
    
    ![Screenshot: Konsolenfenster mit den Ergebnissen des Ermittlungsbefehls.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. Führen Sie den folgenden Befehl auf *beiden* Knoten aus, um sich beim iSCSI-Gerät anzumelden. 

    ```
    iscsiadm -m node -l
    ```
    
    Die Ergebnisse zeigen vier Sitzungen an.
    
    ![Screenshot: Konsolenfenster mit den Ergebnissen des node-Befehls.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. Verwenden Sie den folgenden Befehl, um das Skript *rescan-scsi-bus.sh* zum erneuten Überprüfen auszuführen. Dieses Skript zeigt die neuen für Sie erstellten Datenträger an.  Führen Sie ihn auf *beiden* Knoten aus.

    ```
    rescan-scsi-bus.sh
    ```
    
    Die Ergebnisse sollten eine LUN-Nummer größer als 0 anzeigen (z. B. 1, 2usw.).
     
    ![Screenshot: Konsolenfenster mit den Ergebnissen des Skripts.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. Führen Sie den folgenden Befehl auf *beiden* Knoten aus, um den Gerätenamen abzurufen. 

    ```
      fdisk –l
    ```
    
    Wählen Sie in den Ergebnissen das Gerät mit der Größe 178 MiB aus.
    
    ![Screenshot: Konsolenfenster mit den Ergebnissen des fdisk-Befehls.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>Initialisieren des SBD-Geräts

1. Verwenden Sie den folgenden Befehl, um das SBD-Gerät auf *beiden* Knoten zu initialisieren.

    ```
    sbd -d <SBD Device Name> create
    ```
    ![Screenshot: Konsolenfenster mit dem Ergebnis des sbd create-Befehls.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. Verwenden Sie den folgenden Befehl auf *beiden* Knoten, um zu überprüfen, was auf das Gerät geschrieben wurde.

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-suse-ha-cluster"></a>Konfigurieren des SUSE-Hochverfügbarkeitsclusters

1. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob die ha_sles- und SAPHanaSR-doc-Muster auf *beiden* Knoten installiert sind. Wenn sie nicht installiert sein sollten, installieren Sie sie.

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![Screenshot: Konsolenfenster mit dem Ergebnis des pattern-Befehls.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    
    ![Screenshot: Konsolenfenster mit dem Ergebnis des SAPHanaSR-doc-Befehls.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. Richten Sie den Cluster entweder mit dem `ha-cluster-init`-Befehl oder dem yast2-Assistenten ein. In diesem Beispiel wird der yast2-Assistent verwendet. Führen Sie diesen Schritt nur auf dem *primären Knoten* aus.

    1. Navigieren Sie zu **yast2** > **High Availability** > **Cluster** (yast2 > Hochverfügbarkeit > Cluster).
    
        ![Screenshot: YaST Control Center und den ausgewählten Optionen „Hochverfügbarkeit“ und „Cluster“.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)        
                
    1. Wählen Sie im Dialogfeld, das zur hawk-Paketinstallation angezeigt wird, **Cancel** (Abbrechen) aus, weil das Paket „halk2“ bereits installiert ist.
    
        ![Screenshot: Dialogfeld mit den Optionen „Installieren“ und „Abbrechen“.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
                    
    1. Wählen Sie im Dialogfeld, das zum Fortsetzen angezeigt wird, **Continue** (Fortsetzen) aus.
    
        ![Screenshot: Meldung zum Fortsetzen ohne Installation erforderlicher Pakete.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)        
        
    1. Der erwartete Wert entspricht der Anzahl der bereitgestellten Knoten (in diesem Fall 2). Klicken Sie auf **Weiter**.  

     
        
    1. Fügen Sie Knotennamen hinzu, und wählen Sie dann **Add suggested files** (Vorgeschlagene Dateien hinzufügen) aus.

        ![Screenshot: Fenster zur Clusterkonfiguration mit den Listen der zu synchronisierenden Hosts und Dateien.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
    1. Wählen Sie **Turn csync2 ON** (csync2 aktivieren) aus.
    
    1. Wählen Sie **Generate Pre-Shared-Keys** (Vorinstallierte Schlüssel generieren). 
    
    1. Wählen Sie in der angezeigten Popupmeldung **OK** aus.
    
        ![Screenshot: Meldung, dass der Schlüssel generiert wurde.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. Die Authentifizierung erfolgt über die IP-Adressen und vorinstallierten Schlüssel in Csync2. Die Schlüsseldatei wird mit `csync2 -k /etc/csync2/key_hagroup` generiert. 
    
        Kopieren Sie die Datei *key_hagroup* nach der Erstellung manuell auf alle Mitglieder des Clusters. Achten Sie darauf, die Datei von node1 auf node2 zu kopieren. Wählen Sie **Weiter** aus.
        
        ![Screenshot: Dialogfeld zur Clusterkonfiguration mit den erforderlichen Optionen zum Kopieren des Schlüssels auf alle Mitglieder des Clusters.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. Bei der Standardoption war **Booting** (Starten) **deaktiviert**. Ändern Sie sie in die Option in **On** (Ein), damit der Pacemaker-Dienst beim Start gestartet wird. Sie können die Auswahl gemäß Ihren Konfigurationsanforderungen treffen.

        ![Screenshot: Fenster „Clusterdienst“ mit aktivierter Startkonfiguration.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
    
    1. Nach Auswählen von **Weiter** ist die Clusterkonfiguration abgeschlossen.

## <a name="set-up-the-softdog-watchdog"></a>Einrichten des Softdog-Watchdogs

1. Fügen Sie */etc/init.d/boot.local* auf *beiden* Knoten die folgende Zeile hinzu.
    
    ```
    modprobe softdog
    ```
    ![Screenshot: Bootdatei mit hinzugefügter Softdog-Zeile.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. Verwenden Sie den folgenden Befehl, um die Datei */etc/sysconfig/sbd* auf *beiden* Knoten zu aktualisieren.
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![Screenshot: sbd-Datei mit hinzugefügtem Wert SBD_DEVICE.](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. Laden Sie das Kernelmodul auf *beiden* Knoten, indem Sie den folgenden Befehl ausführen.
    
    ```
    modprobe softdog
    ```
    ![Screenshot: Teil eines Konsolenfensters mit dem Befehl „modprobe softdog“.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. Verwenden Sie den folgenden Befehl, um sicherzustellen, dass Softdog auf *beiden* Knoten ausgeführt wird.
    
    ```
    lsmod | grep dog
    ```
    ![Screenshot: Teil eines Konsolenfensters mit dem Ausführungsergebnis des lsmod-Befehls.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. Verwenden Sie den folgenden Befehl, um das SBD-Gerät auf *beiden* Knoten zu starten.

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![Screenshot: Teil eines Konsolenfensters mit dem start-Befehl.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. Verwenden Sie den folgenden Befehl, um den SBD-Daemon auf *beiden* Knoten zu starten. 
    
    ```
    sbd -d <SBD Device Name> list
    ```
    Die Ergebnisse zeigen zwei Einträge nach der Konfiguration auf beiden Knoten an.    
    
    ![Screenshot: Teil eines Konsolenfensters, das zwei Einträge anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. Senden Sie die folgende Testnachricht an *einen* der Knoten.

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    
8. Verwenden Sie auf dem *zweiten* Knoten (node2) den folgenden Befehl, um den Nachrichtenstatus zu überprüfen.
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![Screenshot: Teil eines Konsolenfensters, in dem eins der Mitglieder einen Testwert für das andere Mitglied anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. Um die SBD-Konfiguration zu übernehmen, aktualisieren Sie die Datei */etc/sysconfig/sbd* wie folgt auf *beiden* Knoten.

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. Verwenden Die den folgenden Befehl, um den Pacemaker-Dienst auf dem *primären Knoten* (node1) zu starten.

    ```
    systemctl start pacemaker
    ```
    ![Screenshot: Konsolenfenster, das den Status nach dem Starten von Pacemaker zeigt.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Wenn der Pacemaker-Dienst fehlschlägt, lesen Sie den Abschnitt [Szenario 5: Pacemaker-Dienst schlägtfehl](#scenario-5-pacemaker-service-fails) weiter unten in diesem Artikel.

## <a name="join-the-node-to-the-cluster"></a>Beitreten des Knotens zum Cluster

Führen Sie den folgenden Befehl auf *node2* aus, damit dieser Knoten dem Cluster beitritt.

```
ha-cluster-join
```

Wenn beim Beitritt zum Cluster ein Fehler auftritt, lesen Sie den Abschnitt [Szenario 6: Node2 kann dem Cluster nicht beitreten](#scenario-6-node2-cant-join-the-cluster) weiter unten in diesem Artikel.

## <a name="validate-the-cluster"></a>Überprüfen des Clusters

1. Verwenden Sie die folgenden Befehle, um den Cluster auf *beiden* Knoten zu überprüfen und optional zu starten.
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![Screenshot: Konsolenfenster mit dem Status von Pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. Vergewissern Sie sich mithilfe des folgenden Befehls, dass *beide* Knoten online sind. Sie können ihn auf *beiden Knoten* des Clusters ausführen.
    
    ```
    crm_mon
    ```
    ![Screenshot: Konsolenfenster mit dem Ergebnis des crm_mon-Befehls.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    Sie können sich auch bei hawk anmelden, um den Clusterstatus zu überprüfen: `https://\<node IP>:7630`. Der Standardbenutzer lautet **hacluster** und das Kennwort **linux**. Wenn erforderlich, können Sie das Kennwort mit dem Befehl `passwd` ändern.
    
## <a name="configure-cluster-properties-and-resources"></a>Konfigurieren von Clustereigenschaften und -ressourcen

In diesem Abschnitt werden die Schritte zum Konfigurieren von Clusterressourcen beschrieben.
In diesem Beispiel richten Sie die folgenden Ressourcen ein. Sie können den Rest (bei Bedarf) konfigurieren, indem Sie den SUSE-Leitfaden für Hochverfügbarkeit verwenden.

- Cluster-Bootstrap
- STONITH-Gerät
- Virtuelle IP-Adresse

Nehmen Sie die Konfiguration nur auf dem *primären Knoten* vor.

1. Erstellen Sie die Bootstrapdatei des Clusters, und konfigurieren Sie sie, indem Sie den folgenden Text hinzufügen.
    
    ```
    sapprdhdb95:~ # vi crm-bs.txt
    # enter the following to crm-bs.txt
    property $id="cib-bootstrap-options" \
    no-quorum-policy="ignore" \
    stonith-enabled="true" \
    stonith-action="reboot" \
    stonith-timeout="150s"
    rsc_defaults $id="rsc-options" \
    resource-stickiness="1000" \
    migration-threshold="5000"
    op_defaults $id="op-options" \
    timeout="600"
    ```

2. Verwenden Sie den folgenden Befehl, um die Konfiguration dem Cluster hinzuzufügen.

    ```
    crm configure load update crm-bs.txt
    ```
    ![Screenshot: Teil eines Konsolenfensters, in dem der crm-Befehl ausgeführt wird.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. Konfigurieren Sie das STONITH-Gerät, indem Sie die Ressource hinzufügen, die Datei erstellen und Text wie folgt hinzufügen.

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
    Verwenden Sie den folgenden Befehl, um die Konfiguration dem Cluster hinzuzufügen.
        
    ```
    crm configure load update crm-sbd.txt
    ```
        
4. Fügen Sie die virtuelle IP-Adresse für die Ressource hinzu, indem Sie die Datei erstellen und den folgenden Text hinzufügen.

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    
    Verwenden Sie den folgenden Befehl, um die Konfiguration dem Cluster hinzuzufügen.
    
    ```
    crm configure load update crm-vip.txt
    ```
        
5. Verwenden Sie den `crm_mon`-Befehl, um die Ressourcen zu überprüfen. 

    Die Ergebnisse zeigen die beiden Ressourcen.

    ![Screenshot: Konsolenfenster mit zwei Ressourcen.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    Sie können den Status auch unter *https://\<node IP address>:7630/cib/live/state* überprüfen.
    
    ![Screenshot: Status der beiden Ressourcen.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>Testen des Failoverprozesses

1. Verwenden Sie zum Testen des Failoverprozesses den folgenden Befehl, um den Pacemaker-Dienst auf node1 zu beenden.

    ```
    Service pacemaker stop
    ```
    
    Für die Ressourcen wird ein Failover auf node2 ausgeführt.

2. Beenden Sie den Pacemaker-Dienst auf node2. Für die Ressourcen wird ein Failover auf node1 ausgeführt.

    Dies ist der Status vor dem Failover:  
    ![Screenshot: Status der beiden Ressourcen vor dem Failover.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    Hier sehen Sie den Status nach dem Failover:  
    ![Screenshot: Status der beiden Ressourcen nach dem Failover.](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![Screenshot: Konsolenfenster mit dem Status der Ressourcen nach dem Failover.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>Problembehandlung

In diesem Abschnitt werden Fehlerszenarien beschrieben, die während des Setups auftreten können.

### <a name="scenario-1-cluster-node-not-online"></a>Szenario 1: Der Clusterknoten ist nicht online.

Wenn der Knoten im Cluster-Manager nicht als „online“ angezeigt wird, können Sie Folgendes versuchen, um ihn online zu schalten.

1. Verwenden Sie den folgenden Befehl zum Starten des iSCSI-Diensts.

    ```
    service iscsid start
    ```
    
2. Verwenden Sie den folgenden Befehl, um sich bei diesem iSCSI-Knoten anzumelden.

    ```
    iscsiadm -m node -l
    ```
    
    Die erwartete Ausgabe sieht wie folgt aus:

    ```
    sapprdhdb45:~ # iscsiadm -m node -l
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
    ```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>Szenario 2: yast2 zeigt keine grafische Ansicht an

In diesem Artikel wurde der grafische yast2-Bildschirm zum Konfigurieren des Hochverfügbarkeitsclusters verwendet. Wenn yast2 nicht wie gezeigt mit dem grafischen Fenster geöffnet und stattdessen ein Qt-Fehler ausgelöst wird, führen Sie die folgenden Schritte aus, um die erforderlichen Pakete zu installieren. Wenn es mit dem grafischen Fenster geöffnet wird, können Sie die Schritte überspringen.

Hier sehen Sie ein Beispiel für den Qt-Fehler:

![Screenshot: Teil eines Konsolenfensters mit einer Fehlermeldung.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

Dies ist ein Beispiel für die erwartete Ausgabe:

![Screenshot: YaST Control Center mit hervorgehobenen Optionen „High Availability“ (Hochverfügbarkeit) und „Cluster“.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

1. Stellen Sie sicher, dass Sie als „root“-Benutzer angemeldet sind und SMT für den Download bzw. die Installation der Pakete konfiguriert haben.

2. Navigieren Sie zu **yast** > **Software** > **Software Management** > **Dependencies** (Abhängigkeiten), und wählen Sie dann **Install recommended packages** (Empfohlene Pakete installieren) aus. 

    >[!NOTE]
    >Führen Sie die Schritte auf *beiden* Knoten aus, damit Sie über beide Knoten auf die grafische Ansicht von yast2 zugreifen können.
    
    Der folgende Screenshot zeigt den erwarteten Bildschirm an.
    
    ![Screenshot: Konsolenfenster, in dem das YaST Control Center angezeigt wird.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. Wählen Sie unter **Dependencies** (Abhängigkeiten) die Option **Install Recommended Packages** (Empfohlene Pakete installieren) aus.

    ![Screenshot: Konsolenfenster mit ausgewählter Option „Empfohlene Pakete installieren“.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. Überprüfen Sie die Änderungen, und wählen Sie **OK** aus.

    ![Screenshot: Konsolenfenster mit einer Liste von Paketen, die für die Installation ausgewählt wurden.](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    Die Paketinstallation wird fortgesetzt.

    ![Screenshot: Konsolenfenster mit dem Status der Installation.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. Klicken Sie auf **Weiter**.    

6. Wenn der Bildschirm **Installation Successfully Finished** (Installation erfolgreich abgeschlossen) angezeigt wird, wählen Sie **Finish** (Fertig stellen) aus.

    ![Screenshot: Konsolenfenster mit einer Erfolgsmeldung.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

7. Verwenden Sie die folgenden Befehle, um die Pakete libqt4 und libyui-qt zu installieren.
    
    ```
    zypper -n install libqt4
    ```
    ![Screenshot: Konsolenfenster, in dem das erste Paket installiert wird.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![Screenshot: Konsolenfenster, in dem das zweite Paket installiert wird.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![Screenshot: Konsolenfenster, in dem das zweite Paket installiert wird (Fortsetzung).](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    Yast2 kann die grafische Ansicht nun öffnen.

    ![Screenshot: YaST Control Center mit den ausgewählten Optionen „Software“ und „Onlineupdate“.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>Szenario 3: Yast2 zeigt die Hochverfügbarkeitsoption nicht an

Damit die Hochverfügbarkeitsoption im Control Center von yast2 angezeigt wird, müssen Sie die anderen Pakete installieren.

1. Navigieren Sie zu **Yast2** > **Software** > **Software Management**. Wählen Sie dann **Software** > **Online Update** aus.  

    ![Screenshot: YaST Control Center mit den ausgewählten Optionen „Software“ und „Onlineupdate“.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. Wählen Sie Muster für die folgenden Elemente aus. Wählen Sie dann **Accept** (Akzeptieren) aus.

    - SAP HANA-Serverbasis
    - C/C++-Compiler und -Tools
    - Hochverfügbarkeit
    - SAP-Anwendungsserverbasis

    ![Screenshot: Auswählen des ersten Musters im Menüpunkt für Compiler und Tools.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![Screenshot: Auswählen des zweiten Musters im Menüpunkt für Compiler und Tools.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

4. Wählen Sie in der Liste der Pakete, die geändert wurden, um Abhängigkeiten aufzulösen, **Weiter** aus.

    ![Screenshot: Dialogfeld „Geänderte Pakete“ mit geänderten Paketen zum Auflösen von Abhängigkeiten.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. Wählen Sie **Next** (Weiter) auf der Statusseite **Performing Installation** (Installation wird ausgeführt) aus.

    ![Screenshot: Statusseite „Performing Installation“ (Installation wird ausgeführt).](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. Nach Abschluss der Installation wird ein Installationsbericht angezeigt. Wählen Sie **Fertig stellen** aus.

    ![Screenshot: Installationsbericht.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Szenario 4: Bei der HANA-Installation tritt ein GCC-Assemblyfehler auf.

Wenn bei der HANA-Installation ein Fehler auftritt, erhalten Sie möglicherweise die folgende Fehlermeldung.

![Screenshot: Fehlermeldung, dass das Betriebssystem nicht bereit ist, gcc5-Assemblys auszuführen.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Installieren Sie die Bibliotheken libgcc_sl und libstdc++6 wie im folgenden Screenshot gezeigt, um das Problem zu beheben.

![Screenshot: Konsolenfenster, in dem erforderliche Bibliotheken installiert werden.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Szenario 5: Beim Pacemaker-Dienst tritt ein Fehler auf.

Die folgenden Informationen werden angezeigt, wenn der Pacemaker-Dienst nicht gestartet werden kann.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Um dieses Problem zu beheben, löschen Sie die folgende Zeile aus der Datei */usr/lib/systemd/system/fstrim.timer*:

```
Persistent=true
```
    
![Screenshot: fstrim-Datei mit dem zu löschenden Wert „Persistent=true“.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node2-cant-join-the-cluster"></a>Szenario 6: Node2 kann dem Cluster nicht beitreten

Der folgende Fehler wird angezeigt, wenn beim Beitritt von node2 zum vorhandenen Cluster über den Befehl *ha-cluster-join* ein Problem auftritt.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Screenshot: Konsolenfenster mit einer Fehlermeldung, die besagt, dass SSH-Schlüssel nicht von einer bestimmten IP-Adresse abgerufen werden können.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Problemlösung:

1. Führen Sie die folgenden Befehle auf *beiden Knoten* aus.

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![Screenshot: Teil eines Konsolenfensters, in dem der Befehl auf dem ersten Knoten ausgeführt wird.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![Screenshot: Teil eines Konsolenfensters, in dem der Befehl auf dem zweiten Knoten ausgeführt wird.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. Bestätigen Sie, dass node2 dem Cluster hinzugefügt wurde.

    ![Screenshot: Konsolenfenster mit einem erfolgreichen join-Befehl.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Hochverfügbarkeitskonfiguration unter SUSE finden Sie in folgenden Artikeln: 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/support/kb/doc/?id=000019450) (Leistungsoptimiertes SAP HANA-SR-Szenario) (SUSE-Website)
- [Fencing and STONITH](https://documentation.suse.com/sle-ha/15-SP1/html/SLE-HA-all/cha-ha-fencing.html) (SUSE-Website)
- [Be Prepared for Using Pacemaker Cluster for SAP HANA – Part 1: Basics](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/) (Vorbereiten auf die Verwendung des Pacemaker-Clusters für SAP HANA – Teil 1: Grundlagen) (SAP-Blog)
- [Be Prepared for Using Pacemaker Cluster for SAP HANA – Part 2: Failure of Both Nodes](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/) (Vorbereiten auf die Verwendung des Pacemaker-Clusters für SAP HANA – Teil 2: Fehler beider Knoten) (SAP-Blog)

Erfahren Sie, wie Sie eine Sicherung und Wiederherstellung auf Dateiebene des Betriebssystems durchführen:

> [!div class="nextstepaction"]
> [Betriebssystemsicherung und -wiederherstellung](large-instance-os-backup.md)
