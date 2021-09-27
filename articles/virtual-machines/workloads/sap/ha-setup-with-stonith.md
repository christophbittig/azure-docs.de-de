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
ms.openlocfilehash: 0ae2b1ebd0b48b38a0f5b7a27097c89f94205d88
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423680"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith-device"></a>Einrichten von Hochverfügbarkeit unter SUSE mithilfe des STONITH-Geräts

In diesem Artikel werden die Schritte zum Einrichten von Hochverfügbarkeit in HANA (große Instanz) unter dem Betriebssystem SUSE mithilfe des STONITH-Geräts erläutert.

**Haftungsausschluss**: *Dieser Leitfaden ist im Rahmen erfolgreicher Tests der Einrichtung in der Microsoft HANA-Umgebung (große Instanzen) entstanden. Da das Microsoft Service Management-Team für HANA (große Instanzen) kein Betriebssystem unterstützt, müssen Sie sich möglicherweise an SUSE wenden, um weitere Informationen zur Fehlerbehebung oder Klärung auf Betriebssystemebene zu erhalten. Das Microsoft Service Management-Team richtet das STONITH-Gerät ein, bietet Support im vollen Umfang und kann bei der Behandlung von Problemen mit dem STONITH-Gerät zu Rate gezogen werden.*

## <a name="prerequisites"></a>Voraussetzungen

Zum Einrichten von Hochverfügbarkeit (High Availability, HA) mithilfe von SUSE-Clustering müssen die folgenden Voraussetzungen erfüllt sein.

- HANA (große Instanzen) wurde bereitgestellt.
- Das Betriebssystem wurde registriert.
- Es besteht eine Verbindung zwischen HANA-Servern (große Instanzen) und dem SMT-Server zum Abrufen von Patches bzw. Paketen.
- Das Betriebssystem mit den neusten Patches wurde installiert.
- NTP-Zeitserver (Network Time Protocol) wurde eingerichtet.
- Sie haben die aktuelle Version der SUSE-Dokumentation für die Einrichtung von Hochverfügbarkeit gelesen und verstanden.

## <a name="setup-details"></a>Details zur Konfiguration
In diesem Leitfaden wird folgendes Setup verwendet:
- Betriebssystem: SLES 12 SP1 für SAP
- SAP HANA (große Instanzen): 2-mal S192 (vier Sockets, 2 TB)
- HANA-Version: HANA 2.0 SP1
- Servernamen: „sapprdhdb95“ (node1) und „sapprdhdb96“ (node2)
- STONITH-Gerät: iSCSI-basiertes STONITH-Gerät
- NTP-Konfiguration auf einem der HANA-Knoten (große Instanz)

Wenn Sie HANA (große Instanz) mit HANA-Systemreplikation (HSR) einrichten, können Sie beim Microsoft Service Management-Team die Einrichtung des STONITH-Geräts beantragen. Machen Sie dies gleich zum Zeitpunkt der Bereitstellung. 

Auch wenn Sie Bestandskunde mit bereits bereitgestelltem HANA (großen Instanzen) sind, können Sie das STONITH-Gerät dennoch einrichten. Geben Sie dem Microsoft Service Management-Team im Service Request-Formular die folgenden Informationen an. Sie können das Service Request-Formular über den Technical Account Manager oder Ihren Microsoft-Kontakt für das Onboarding von HANA (große Instanzen) anfordern.

- Servername und Server-IP-Adresse (z. B. myhanaserver1, 10.35.0.1)
- Standort (z.B. „USA, Osten“)
- Name des Kunden (z. B. Microsoft)
- SID – HANA-Systembezeichner (z.B. „H11“)

Sobald das STONITH-Gerät konfiguriert ist, erhalten Sie vom Microsoft Service Management-Team den SBD-Namen (STONITH Block Device) und die IP-Adresse des iSCSI-Speichers. Anhand dieser Informationen können Sie die STONITH-Einrichtung konfigurieren. 

Folgen Sie diesen Schritten, um Hochverfügbarkeit mithilfe von STONITH einzurichten:

1.  Identifizieren des SBD-Geräts
2.  Initialisieren des SBD-Geräts
3.  Konfigurieren des Clusters
4.  Einrichten des Softdog-Watchdogs
5.  Hinzufügen des Knotens zum Cluster
6.  Überprüfen Sie den Cluster.
7.  Konfigurieren der Ressourcen für den Cluster
8.  Testen des Failoverprozesses

## <a name="identify-the-sbd-device"></a>Identifizieren des SBD-Geräts

> [!NOTE]
> Dieser Abschnitt gilt nur für Bestandskunden. Wenn Sie Neukunde sind, nennt Ihnen das Microsoft Service Management-Team den Namen des SBD-Geräts. Überspringen Sie daher diesen Abschnitt.

Nur für Bestandskunden: Nachdem das Microsoft Service Management-Team das STONITH-Gerät konfiguriert hat, müssen Sie das SBD-Gerät für Ihr Setup bestimmen.

1. Ändern Sie */etc/iscsi/initiatorname.isci* wie folgt: 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Diese Zeichenfolge wird vom Microsoft Service Management-Team bereitgestellt. Ändern Sie die Datei auf **beiden** Knoten, wobei die Knotennummer auf jedem Knoten unterschiedlich ist.
    
    ![Der Screenshot zeigt eine initiatorname-Datei mit InitiatorName-Werten für einen Knoten.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. Ändern Sie */etc/iscsi/iscsid.conf*: Legen Sie *node.session.timeo.replacement_timeout=5* und *node.startup = automatic* fest. Ändern Sie die Datei auf **beiden** Knoten.

3. Führen Sie den Ermittlungsbefehl aus. Es werden vier Sitzungen angezeigt. Führen Sie ihn auf beiden Knoten aus.

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    ![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des isciadm-Ermittlungsbefehls.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. Führen Sie den Befehl zum Anmelden beim iSCSI-Gerät aus. Es werden vier Sitzungen angezeigt. Führen Sie ihn auf **beiden** Knoten aus.

    ```
    iscsiadm -m node -l
    ```
    ![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des iscsiadm-Knotenbefehls](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. Führen Sie das Skript zur erneuten Überprüfung aus: *rescan-scsi-bus.sh*. Dieses Skript zeigt die neuen Datenträger an, die für Sie erstellt wurden.  Führen Sie es auf beiden Knoten aus. Eine LUN-Nummer größer als 0 sollte angezeigt werden (z. B. 1 oder 2).

    ```
    rescan-scsi-bus.sh
    ```
    ![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des Skripts.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. Um den Gerätenamen abzurufen, führen Sie den Befehl *fdisk – l* aus. Führen Sie ihn auf beiden Knoten aus. Wählen Sie das Gerät mit der Größe **178 MiB** aus.

    ```
      fdisk –l
    ```
    
    ![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des fdisk-Befehls.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>Initialisieren des SBD-Geräts

Dieser Abschnitt und die folgenden Abschnitte gelten sowohl für neue als auch für Bestandskunden.

1. Initialisieren Sie das SBD-Gerät auf **beiden** Knoten.

    ```
    sbd -d <SBD Device Name> create
    ```
    ![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des sbd create-Befehls.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. Überprüfen Sie, welche Daten auf das Gerät geschrieben wurden. Machen Sie dies auf **beiden** Knoten.

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-cluster"></a>Konfigurieren des Clusters

In diesem Abschnitt werden die Schritte zum Konfigurieren des SUSE-Hochverfügbarkeitsclusters beschrieben.

1. Installieren Sie zunächst das Paket. Überprüfen Sie, ob die Muster „ha_sles“ und „SAPHanaSR-doc“ installiert sind. Wenn sie nicht installiert sein sollten, installieren Sie sie. Installieren Sie sie auf **beiden** Knoten.

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![Der Screenshot zeigt ein Konsolenfenster mit dem Ergebnis des Musterbefehls.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    ![Der Screenshot zeigt ein Konsolenfenster mit dem Ergebnis des Befehls „SAPHanaSR-doc“.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. Richten Sie nun den Cluster ein. Sie können den Cluster entweder mit dem Befehl *ha-cluster-init* oder dem Assistenten „yast2“ konfigurieren. In diesem Beispiel haben wir den yast2-Assistenten verwendet. Führen Sie diesen Schritt **nur auf dem primären Knoten** durch.

    1. Folgen Sie **yast2** > **Hochverfügbarkeit** > **Cluster**.
    
        ![Screenshot mit dem YaST Control Center und den ausgewählten Optionen „Hochverfügbarkeit“ und „Cluster“.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
        
        ![Screenshot: Dialogfeld mit den Optionen „Installieren“ und „Abbrechen“](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
        
    1. Wählen Sie **Abbrechen** aus, da das Paket „halk2“ bereits installiert ist.
    
        ![Der Screenshot zeigt eine Meldung zu Ihrer Abbrechen-Option.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)
    
    1. Wählen Sie **Weiter**.
    
        Der erwartete Wert entspricht der Anzahl der bereitgestellten Knoten (in diesem Fall 2).
        
        ![Screenshot: Clustersicherheit mit Kontrollkästchen „Enable Security Auth“ (Sicherheitsauthentifizierung aktivieren)](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
        
    1. Klicken Sie auf **Weiter**.
    
        ![Screenshot: Fenster zur Clusterkonfiguration mit den Listen der Synchronisierungshosts und der zu synchronisierenden Dateien](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
        Fügen Sie Knotennamen hinzu, und wählen Sie dann „Add suggested files“ (Vorgeschlagene Dateien hinzufügen) aus.
        
    1. Wählen Sie **Turn csync2 ON** (csync2 aktivieren) aus.
    
    1. Wählen Sie **Generate Pre-Shared-Keys** (Vorinstallierte Schlüssel generieren) aus. Daraufhin wird das unten dargestellte Popupfenster angezeigt.
    
        ![Der Screenshot zeigt eine Meldung, dass Ihr Schlüssel generiert wurde.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. Klicken Sie auf **OK**.
    
        Die Authentifizierung erfolgt über die IP-Adressen und vorinstallierten Schlüssel in csync2. Die Schlüsseldatei wird mit „csync2 -k /etc/csync2/key_hagroup“ erzeugt. Die Datei „key_hagroup“ sollte nach der Erstellung manuell in alle Clustermitglieder kopiert. **Achten Sie darauf, die Datei von node1 auf node2 zu kopieren**.
        
        ![Der Screenshot zeigt ein Dialogfeld zur Clusterkonfiguration mit den erforderlichen Optionen zum Kopieren des Schlüssels auf alle Mitglieder des Clusters.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. Klicken Sie auf **Weiter**.
        ![Screenshot mit dem Fenster „Clusterdienst“.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
        
        Bei der Standardoption war „Starten“ deaktiviert. Ändern Sie sie in „Ein“, damit Pacemaker beim Start gestartet wird. Sie können die Auswahl gemäß Ihren Konfigurationsanforderungen treffen.
    
    1. Nach Auswählen von **Weiter** ist die Clusterkonfiguration abgeschlossen.

## <a name="set-up-the-softdog-watchdog"></a>Einrichten des Softdog-Watchdogs

In diesem Abschnitt wird die Konfiguration des Watchdogs (Softdog) beschrieben.

1. Fügen Sie */etc/init.d/boot.local* auf **beiden** Knoten die folgende Zeile hinzu.
    
    ```
    modprobe softdog
    ```
    ![Der Screenshot zeigt eine Bootdatei mit hinzugefügter Softdog-Zeile.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. Aktualisieren Sie die Datei */etc/sysconfig/sbd* wie folgt auf **beiden** Knoten:
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![Der Screenshot zeigt die sbd-Datei mit hinzugefügtem Wert SBD_DEVICE.](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. Laden Sie das Kernelmodul auf **beiden** Knoten, indem Sie den folgenden Befehl ausführen:
    
    ```
    modprobe softdog
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters mit dem Befehl „modprobe softdog“.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. Stellen Sie sicher, dass Softdog wie folgt auf **beiden** Knoten ausgeführt wird:
    
    ```
    lsmod | grep dog
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters mit dem Ausführungsergebnis des lsmod-Befehls.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. Starten Sie das SBD-Gerät auf **beiden** Knoten:

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters mit dem Startbefehl.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. Testen Sie den SBD-Deamon auf **beiden** Knoten. Nach der Konfiguration auf beiden Knoten werden zwei Einträge angezeigt.
    
    ```
    sbd -d <SBD Device Name> list
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters, das zwei Einträge anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. Senden Sie eine Testnachricht an **einen** der Knoten.

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters, das zwei Einträge anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
8. Auf dem **zweiten** Knoten (node2) können Sie den Nachrichtenstatus überprüfen.
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters, in dem eins der Mitglieder einen Testwert für das andere Mitglied anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. Um die SBD-Konfiguration zu übernehmen, aktualisieren Sie die Datei */etc/sysconfig/sbd* wie folgt. Ändern Sie die Datei auf **beiden** Knoten.

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. Starten Sie den Pacemaker-Dienst auf dem **primären Knoten** (node1).

    ```
    systemctl start pacemaker
    ```
    ![Der Screenshot zeigt ein Konsolenfenster, das den Status nach dem Starten von pacemaker zeigt.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Wenn beim Pacemaker-Dienst ein *Fehler* auftritt, lesen Sie *Szenario 5: Beim Pacemaker-Dienst tritt ein Fehler auf*.

## <a name="join-the-cluster"></a>Beitreten zum Cluster

Nun fügen Sie den Knotens dem Cluster hinzu.

- Fügen Sie den Knoten hinzu. Führen Sie den folgenden Befehl auf **node2** aus, damit node2 dem Cluster beitritt.

    ```
    ha-cluster-join
    ```
    Wenn beim Beitritt zum Cluster ein *Fehler* auftritt, lesen Sie *Szenario 6: Beim Betritt von node2 zum Cluster tritt ein Fehler auf*.

## <a name="validate-the-cluster"></a>Überprüfen des Clusters

1. Starten Sie den Clusterdienst. Überprüfen und starten Sie den Cluster optional zum ersten Mal auf **beiden** Knoten:
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![Der Screenshot zeigt ein Konsolenfenster mit dem Status von pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. Überwachen Sie den Status. Führen Sie den Befehl *crm_mon* aus, um sicherzustellen, dass **beide** Knoten online sind. Sie können ihn auf **beiden Knoten** des Clusters ausführen.
    
    ```
    crm_mon
    ```
    ![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen von „crm_mon“.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    Sie können sich auch bei hawk anmelden, um den Clusterstatus zu überprüfen: *https://\<node IP>:7630*. Der Standardbenutzer lautet „hacluster“ und das Kennwort „linux“. Bei Bedarf können Sie das Kennwort mit dem Befehl *passwd* ändern.
    
## <a name="configure-cluster-properties-and-resources"></a>Konfigurieren von Clustereigenschaften und -ressourcen

In diesem Abschnitt werden die Schritte zum Konfigurieren von Clusterressourcen beschrieben.
Richten Sie bei diesem Beispiel die folgende Ressourcen ein. Der Rest kann (bei Bedarf) anhand des Hochverfügbarkeitsleitfadens für SUSE konfiguriert werden.

- Cluster-Bootstrap
- STONITH-Gerät
- Die virtuelle IP-Adresse

Führen Sie die Konfiguration nur auf **einem der Knoten** aus. Führen Sie sie auf dem primären Knoten aus.

1. Konfigurieren des Clusterbootstraps und mehr: Fügen Sie den Cluster-Bootstrap hinzu. Erstellen Sie die Datei, und fügen Sie den Text wie folgt hinzu:
    
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
2. Fügen Sie dem Cluster die Konfiguration hinzu.

    ```
    crm configure load update crm-bs.txt
    ```
    ![Der Screenshot zeigt einen Teil eines Konsolenfensters, während der crm-Befehl ausgeführt wird.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. Konfigurieren des STONITH-Geräts: Fügen Sie die Ressource STONITH hinzu, erstellen Sie die Datei, und fügen Sie Text wie folgt hinzu.

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
      - Fügen Sie die Konfiguration zum Cluster hinzu.
        
          ```
          crm configure load update crm-sbd.txt
          ```
        
4. Konfigurieren der virtuellen IP-Adresse: Fügen Sie die virtuelle IP-Adresse der Ressource hinzu. Erstellen Sie die Datei, und fügen Sie den Text wie folgt hinzu.

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    - Fügen Sie dem Cluster die Konfiguration hinzu.
    
        ```
        crm configure load update crm-vip.txt
        ```
        
5. Überprüfen Sie die Ressourcen nun. Beim Ausführen des Befehls *crm_mon* werden die beiden Ressourcen angezeigt.

    ![Der Screenshot zeigt ein Konsolenfenster mit zwei Ressourcen.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    - Darüber hinaus können Sie den Status unter *https://\<node IP address>:7630/cib/live/state* einsehen.
    
        ![Der Screenshot zeigt den Status der beiden Ressourcen.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>Testen des Failoverprozesses

1. Um den Failoverprozess zu testen, beenden Sie den Pacemaker-Dienst auf node1 und das Ressourcenfailover auf node2.

    ```
    Service pacemaker stop
    ```
2. Beenden Sie nun den Pacemaker-Dienst auf **node2** und die Ressourcen, für die ein Failover auf **node1** ausgeführt wurde.

    **Vor dem Failover**  
    ![Der Screenshot zeigt den Status der beiden Ressourcen vor dem Failover.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    **Nach dem Failover**  
    ![Der Screenshot zeigt den Status der beiden Ressourcen nach dem Failover.](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![Der Screenshot zeigt ein Konsolenfenster mit dem Status der Ressourcen nach dem Failover.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>Problembehandlung
In diesem Abschnitt werden einige Fehlerszenarien beschrieben, die bei der Einrichtung auftreten können. Diese Probleme müssen nicht zwingend bei Ihnen auftreten.

### <a name="scenario-1-cluster-node-not-online"></a>Szenario 1: Der Clusterknoten ist nicht online.

Wenn der Knoten im Cluster-Manager nicht als „online“ angezeigt wird, können Sie Folgendes versuchen, um ihn wieder online zu schalten.

1. Starten Sie den iSCSI-Dienst:

    ```
    service iscsid start
    ```
    
2. Melden Sie sich nun bei diesem iSCSI-Knoten an.

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
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>Szenario 2: yast2 zeigt keine grafische Ansicht

In diesem Dokument wurde der grafische yast2-Bildschirm zum Konfigurieren des Hochverfügbarkeitsclusters verwendet. Wenn yast2 nicht wie gezeigt mit dem grafischen Fenster geöffnet und stattdessen ein Qt-Fehler ausgelöst wird, führen Sie die folgenden Schritte aus. Wenn es mit dem grafischen Fenster geöffnet wird, können Sie die Schritte überspringen.

**Fehler**

![Der Screenshot zeigt einen Teil eines Konsolenfensters mit einer Fehlermeldung.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Erwartete Ausgabe**

![Der Screenshot zeigt das YaST Control Center mit hervorgehobenen Optionen „High Availability“ und „Cluster“.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Wenn yast2 nicht mit der grafischen Ansicht geöffnet wird, führen Sie die folgenden Schritte aus:

1. Installieren Sie die erforderlichen Pakete. Sie müssen als „root“-Benutzer angemeldet sein und SMT für den Download bzw. die Installation der Pakete konfiguriert haben.

2. Navigieren Sie zum Installieren der Pakete zur Option „Empfohlene Pakete installieren...“ unter „yast“ > „Software“ > „Softwareverwaltung“ > „Abhängigkeiten“. Der folgende Screenshot zeigt die erwarteten Bildschirme.

    >[!NOTE]
    >Damit Sie über beide Knoten auf die grafische Ansicht von yast2 zugreifen können, müssen Sie die Schritte auf beiden Knoten durchführen.
    
    ![Der Screenshot zeigt ein Konsolenfenster, in dem das YaST Control Center angezeigt wird.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. Wählen Sie unter „Abhängigkeiten“ die Option **Empfohlene Pakete installieren** aus.

    ![Screenshot zeigt ein Konsolenfenster mit ausgewählter Option „Empfohlene Pakete installieren“.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. Überprüfen Sie die Änderungen, und wählen Sie **OK** aus.

    ![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    Die Paketinstallation wird fortgesetzt.

    ![Der Screenshot zeigt ein Konsolenfenster mit dem Status der Installation.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. Klicken Sie auf **Weiter**.

    ![Der Screenshot zeigt ein Konsolenfenster mit einer Erfolgsmeldung.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

6. Wählen Sie **Fertig stellen** aus.

7. Sie müssen auch die Pakete „libqt4“ und „libyui“ installieren.
    
    ```
    zypper -n install libqt4
    ```
    ![Der Screenshot zeigt ein Konsolenfenster zum Installieren des libqt4-Pakets.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![Screenshot mit einem Konsolenfenster zum Installieren des libyui-qt-Pakets.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![Screenshot mit einem Konsolenfenster zum Installieren des libyui-qt-Pakets (Fortsetzung).](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    Yast2 kann nun die grafische Ansicht wie hier gezeigt öffnen.

    ![Der Screenshot zeigt das YaST Control Center mit den ausgewählten Optionen „Software“ und „Online-Update“.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>Szenario 3: yast2 zeigt nicht die Hochverfügbarkeitsoption

Damit die Hochverfügbarkeitsoption im Control Center von yast2 angezeigt wird, müssen Sie die anderen Pakete installieren.

1. Wählen Sie unter „Yast2“ > „Software“ > „Softwareverwaltung“ folgende Muster aus:

    - SAP HANA-Serverbasis
    - C/C++-Compiler und -Tools
    - Hochverfügbarkeit
    - SAP-Anwendungsserverbasis

    Auf dem folgenden Bildschirm werden die Schritte zum Installieren der Muster angezeigt.

    Diese finden Sie unter „yast2“ > „Software“ > „Softwareverwaltung“.

    ![Der Screenshot zeigt das YaST Control Center mit den ausgewählten Optionen „Software“ und „Online-Update“, um die Installation zu beginnen.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. Wählen Sie die Muster aus.

    ![Screenshot des Auswählens des ersten Musters im Menüpunkt „C/C++-Compiler und -Tools“.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![Screenshot des Auswählens des zweiten Musters im Menüpunkt „C/C++-Compiler und -Tools“.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

3. Wählen Sie **Akzeptieren** aus.

    ![Der Screenshot zeigt das Dialogfeld „Geänderte Pakete“ mit geänderten Paketen zum Auflösen von Abhängigkeiten.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. Wählen Sie **Weiter**.

    ![Der Screenshot zeigt die Statusseite „Performing Installation“ (Installation wird durchgeführt).](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. Wählen Sie nach Abschluss der Installation **Weiter** aus.

    ![Der Screenshot zeigt den Installationsbericht.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Szenario 4: Bei der HANA-Installation tritt ein GCC-Assemblyfehler auf.
Bei der HANA-Installation tritt folgender Fehler auf.

![Screenshot mit der Fehlermeldung, dass das Betriebssystem nicht bereit ist, gcc5-Assemblys auszuführen.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

- Um das Problem zu beheben, müssen Sie Bibliotheken (libgcc_sl und libstdc++6) wie im folgenden Screenshot gezeigt installieren.

    ![Der Screenshot zeigt ein Konsolenfenster von der Installation der erforderlichen Bibliotheken.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Szenario 5: Beim Pacemaker-Dienst tritt ein Fehler auf.

Das folgende Problem tritt beim Start des Pacemaker-Diensts auf.

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

- Um dies zu beheben, löschen Sie die folgende Zeile aus der Datei */usr/lib/systemd/system/fstrim.timer*.

    ```
    Persistent=true
    ```
    
    ![Der Screenshot zeigt die fstrim-Datei mit dem zu löschenden Wert „Persistent=true“.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Szenario 6: Node 2 kann nicht dem Cluster beitreten.

Wenn node2 über den Befehl *ha-cluster-join* dem vorhandenen Cluster beitritt, tritt der folgende Fehler auf.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Der Screenshot zeigt ein Konsolenfenster mit der Fehlermeldung „Die SSH-Schlüssel können nicht von einer IP-Adresse abgerufen werden“.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

1. Um das Problem zu beheben, führen Sie Folgendes auf beiden Knoten aus:

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![Der Screenshot zeigt einen Teil eines Konsolenfensters während der Ausführung des Befehls auf dem ersten Knoten.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![Der Screenshot zeigt einen Teil eines Konsolenfensters während der Ausführung des Befehls auf dem zweiten Knoten.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. Nach der vorherigen Fehlerbehebung muss node2 dem Cluster hinzugefügt werden.

    ![Der Screenshot zeigt ein Konsolenfenster mit einem erfolgreichen ha-cluster-join-Befehl.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Hochverfügbarkeitskonfiguration unter SUSE finden Sie in folgenden Artikeln: 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/support/kb/doc/?id=000019450 ) (Leistungsoptimiertes SAP HANA-SR-Szenario)
- [Storage based fencing](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html) (Speicherbasierte Umgrenzung)
- [Blog – Using Pacemaker Cluster for SAP HANA- Part 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/) (Blog: Verwenden eines Pacemaker-Clusters für SAP Hana – Teil 1)
- [Blog – Using Pacemaker Cluster for SAP HANA- Part 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/) (Blog: Verwenden eines Pacemaker-Clusters für SAP Hana – Teil 2)

Erfahren Sie, wie Sie eine Sicherung und Wiederherstellung auf Dateiebene des Betriebssystems durchführen.

> [!div class="nextstepaction"]
> [Betriebssystemsicherung und -wiederherstellung](large-instance-os-backup.md)
