---
title: Einrichten eines SMT-Servers für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen SMT-Server für SAP HANA in Azure (große Instanzen) einrichten.
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
ms.date: 06/25/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b8793e5d8badc6ef19462651e1e039cdae1e8bac
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981374"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Einrichten eines SMT-Servers für SUSE Linux

In diesem Artikel werden die einzelnen Schritte zum Einrichten eines SMT-Servers für SAP HANA in Azure (große Instanzen) – auch als BareMetal-Infrastruktur bezeichnet – beschrieben.

SAP HANA-Einheiten (große Instanzen) haben keine direkte Verbindung mit dem Internet. Daher sind das Registrieren einer solchen Einheit beim Betriebssystemanbieter sowie das Herunterladen und Anwenden von Updates nicht ganz einfach. Eine mögliche Lösung für SUSE Linux ist die Einrichtung eines SMT-Servers auf einer Azure-VM. Hosten Sie die VM in einem virtuellen Azure-Netzwerk, das mit der Instanz von SAP HANA (große Instanzen) – (HANA Large Instance, HLI) – verbunden ist. Mit einem solchen SMT-Server kann die Instanz von SAP HANA (große Instanzen) Updates registrieren und herunterladen. 

Weitere Informationen zu SUSE finden Sie in der Dokumentation zum [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

## <a name="prerequisites"></a>Voraussetzungen

Zum Installieren eines SMT-Servers für SAP HANA (große Instanzen) benötigen Sie zunächst Folgendes:

- Ein virtuelles Azure-Netzwerk, das per ExpressRoute-Leitung mit der Instanz von SAP HANA (große Instanzen) verbunden ist
- Ein SUSE-Konto, das einer Organisation zugeordnet ist Die Organisation muss über ein gültiges SUSE-Abonnement verfügen.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installieren des SMT-Servers auf einem virtuellen Azure-Computer

1. Melden Sie sich beim [SUSE Customer Center](https://scc.suse.com/) an. Wechseln Sie zu to **Organization** > **Organization Credentials** (Organisation -> >Anmeldeinformationen für die Organisation). In diesem Abschnitt finden Sie die Anmeldeinformationen, die zum Einrichten des SMT-Servers erforderlich sind.

2. Installieren Sie eine SUSE Linux-VM im virtuellen Azure-Netzwerk. Verwenden Sie zum Bereitstellen des virtuellen Computers ein SLES 12 SP2-Katalogimage von Azure (wählen Sie das BYOS SUSE-Image aus). Definieren Sie während des Bereitstellungsprozesses keinen DNS-Namen, und verwenden Sie keine statischen IP-Adressen.

    ![Screenshot der VM-Bereitstellung für den SMT-Server](./media/hana-installation/image3_vm_deployment.png)

    Die bereitgestellte VM hat im virtuellen Azure-Netzwerk die interne IP-Adresse 10.34.1.4. Der Name des virtuellen Computers lautet *smtserver*. Überprüfen Sie nach der Installation die Konnektivität mit der Instanz von SAP HANA (große Instanzen). Je nachdem, wie Sie die Namensauflösung organisiert haben, müssen Sie u. U. die Auflösung der Instanz von SAP HANA (große Instanzen) auf der Azure-VM unter „etc/hosts“ konfigurieren. 

3. Fügen Sie einen Datenträger an den virtuellen Computer an. Sie benötigen diesen Datenträger zum Speichern der Updates. Der Startdatenträger ist dafür möglicherweise zu klein. Hier wird der Datenträger wie im folgenden Screenshot gezeigt unter „/srv/www/htdocs“ bereitgestellt. Ein Datenträger mit 100 GB sollte hierfür ausreichen.

    ![Screenshot: Hinzugefügter Datenträger im PuTTY-Fenster](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

4. Melden Sie sich bei der Instanz von SAP HANA (große Instanzen) an, und verwalten Sie „/etc/hosts“. Überprüfen Sie, ob Sie eine Verbindung mit der Azure-VM herstellen können, auf der der SMT-Server über das Netzwerk ausgeführt werden soll.

5. Melden Sie sich bei der Azure-VM an, auf der der SMT-Server ausgeführt werden soll. Wenn Sie PuTTY für die Anmeldung bei der VM verwenden, führen Sie diese Befehlssequenz in Ihrem Bash-Fenster aus:

    ```
    cd ~
    echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
    ```

6. Starten Sie Bash neu, um die Einstellungen zu aktivieren. Starten Sie anschließend YAST.

7. Verbinden Sie Ihren virtuellen Computer („smtserver“) mit der SUSE-Website.

    ```
    smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
    Registered SLES_SAP 12.2 x86_64
    To server: https://scc.suse.com
    Using E-Mail: email address
    Successfully registered system.
    ```
    
8. Wenn die Verbindung des virtuellen Computers mit der SUSE-Website hergestellt ist, installieren Sie die SMT-Pakete. Verwenden Sie den folgenden putty-Befehl, um die SMT-Pakete zu installieren.

    ```
    smtserver:~ # zypper in smt
    Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
    Loading repository data...
    Reading installed packages...
    Resolving package dependencies...
    ```
    
    Sie können auch das YAST-Tool verwenden, um die SMT-Pakete zu installieren. Navigieren Sie in YAST zu **Software Maintenance**, und suchen Sie nach „smt“. Wählen Sie **smt** aus. Es erfolgt ein automatischer Wechsel zu „yast2-smt“.

    ![Screenshot von SMT in YAST](./media/hana-installation/image5_smt_in_yast.PNG)

    Übernehmen Sie die Auswahl für die Installation von smtserver. 


9. Wechseln Sie nach Abschluss der Installation zur Konfiguration des SMT-Servers. Geben Sie die Anmeldeinformationen für die Organisation aus dem SUSE Customer Center ein, die Sie weiter oben abgerufen haben. Geben Sie außerdem Ihren Azure-VM-Hostnamen als SMT-Server-URL ein. In diesem Beispiel lautet die URL „https:\//smtserver“.

    ![Screenshot zur Konfiguration des SMT-Servers](./media/hana-installation/image6_configuration_of_smtserver1.png)

10. Testen Sie jetzt, ob die Verbindung mit dem SUSE Customer Center funktioniert. Wie im folgenden Screenshot zu sehen ist, wurde die Verbindung in diesem Beispiel erfolgreich hergestellt.

    ![Screenshot zum Test der Verbindung mit dem SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

11. Nach dem Start des SMT-Setups müssen Sie ein Datenbankkennwort angeben. Da es sich um eine neue Installation handelt, sollten Sie das Kennwort wie im folgenden Screenshot gezeigt definieren.

    ![Screenshot zum Definieren eines Kennworts für die Datenbank](./media/hana-installation/image8_define_db_passwd.PNG)

12. Erstellen eines Zertifikats.

    ![Screenshot zum Erstellen eines Zertifikats für den SMT-Server](./media/hana-installation/image9_certificate_creation.PNG)

    Am Schluss der Konfiguration kann es einige Minuten dauern, bis die Synchronisierungsprüfung ausgeführt wurde. Nach der Installation und Konfiguration des SMT-Servers finden Sie das Verzeichnisrepository im Bereitstellungspunkt „/srv/www/htdocs/“. Im Repository befinden sich auch einige Unterverzeichnisse. 

13. Starten Sie den SMT-Server und die dazugehörigen Dienste mit diesen Befehlen neu.

    ```
    rcsmt restart
    systemctl restart smt.service
    systemctl restart apache2
    ```

## <a name="download-packages-onto-the-smt-server"></a>Herunterladen von Paketen auf den SMT-Server

1. Wählen Sie nach dem Neustart aller Dienste mithilfe von YAST die entsprechenden Pakete unter „SMT Management“ aus. Die Paketauswahl richtet sich nach dem Betriebssystemimage des HANA-Servers (große Instanzen). Die Paketauswahl richtet sich nicht nach dem SLES-Release oder der Version des virtuellen Computers, auf dem der SMT-Server ausgeführt wird. Der folgende Screenshot zeigt ein Beispiel des Auswahlbildschirms.

    ![Screenshot zum Auswählen der Pakete](./media/hana-installation/image10_select_packages.PNG)

2. Starten Sie den Download der ersten Kopie der ausgewählten Pakete auf den eingerichteten SMT-Server. Dieser Kopiervorgang wird in der Shell mit dem Befehl „smt-mirror“ ausgelöst.

    ![Screenshot: Herunterladen der Pakete auf den SMT-Server](./media/hana-installation/image11_download_packages.PNG)

    Diese Pakete sollten in die Verzeichnisse kopiert werden, die unter dem Bereitstellungspunkt „/srv/www/htdocs“ erstellt wurden. Dieser Prozess kann eine Stunde oder länger dauern, je nachdem, wie viele Pakete Sie ausgewählt haben. Nachdem dieser Vorgang abgeschlossen ist, geht es mit dem Einrichten des SMT-Clients weiter. 

## <a name="set-up-the-smt-client-on-hana-large-instances"></a>Einrichten des SMT-Clients auf der Instanz von SAP HANA (große Instanzen)

Der bzw. die Clients sind in diesem Fall Instanzen von SAP HANA (große Instanzen). Beim Einrichten des SMT-Servers wurde das Skript „clientSetup4SMT.sh“ auf den virtuellen Azure-Computer kopiert. 

Kopieren Sie dieses Skript auf die Instanz von SAP HANA (große Instanzen), die Sie mit Ihrem SMT-Server verbinden möchten. Starten Sie das Skript mit der Option „-h“, und geben Sie den Namen Ihres SMT-Servers als Parameter an. In diesem Beispiel lautet der Name *smtserver*.

![Screenshot zum Konfigurieren des SMT-Clients](./media/hana-installation/image12_configure_client.PNG)

Es ist möglich, dass der Client das Zertifikat erfolgreich vom Server laden kann. Wie im folgenden Screenshot gezeigt, schlägt die Registrierung in diesem Beispiel jedoch fehl.

![Screenshot des Fehlers bei der Clientregistrierung](./media/hana-installation/image13_registration_failed.PNG)

Falls bei der Registrierung ein Fehler auftritt, lesen Sie dieses [SUSE-Supportdokument](https://www.suse.com/de-de/support/kb/doc/?id=7006024) (in englischer Sprache), und führen Sie die dort beschriebenen Schritte aus.

> [!IMPORTANT] 
> Geben Sie als Servernamen den Namen des virtuellen Computers (hier *smtserver*) ohne den vollqualifizierten Domänennamen an. 
    
Wenn diese Schritte abgeschlossen sind, führen Sie den folgenden Befehl auf der Instanz von SAP HANA (große Instanzen) aus:
    
```
SUSEConnect –cleanup
```

> [!Note] 
> Warten Sie nach diesem Schritt einige Minuten. Wenn Sie „clientSetup4SMT.sh“ sofort ausführen, erhalten Sie möglicherweise einen Fehler.

Falls ein Problem auftritt, das Sie gemäß den Schritten im SUSE-Artikel beheben müssen, starten Sie „clientSetup4SMT.sh“ auf der Instanz von SAP HANA (große Instanzen) neu. Der Vorgang sollte jetzt erfolgreich abgeschlossen werden.

![Screenshot zur erfolgreichen Clientregistrierung](./media/hana-installation/image14_finish_client_config.PNG)

Sie haben den SMT-Client der HLI zum Herstellen einer Verbindung mit dem auf der Azure-VM installierten SMT-Server konfiguriert. Verwenden Sie nun „zypper up“ oder „zypper in“, um Betriebssystemupdates für SAP HANA (große Instanzen) oder andere Pakete zu installieren. Sie können nur Updates abrufen, die Sie zuvor auf den SMT-Server heruntergeladen haben.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über die Migration von SAP HANA in Azure (große Instanzen) zu Azure-VMs.

> [!div class="nextstepaction"]
> [SAP HANA in Azure (große Instanzen) – Migration zu Azure Virtual Machines](hana-large-instance-virtual-machine-migration.md)
