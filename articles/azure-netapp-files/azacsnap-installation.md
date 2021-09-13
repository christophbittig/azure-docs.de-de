---
title: Installieren des Tools für anwendungskonsistente Momentaufnahmen in Azure für Azure NetApp Files | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Installationsanleitung für das Tool für anwendungskonsistente Momentaufnahmen in Azure, das Sie mit Azure NetApp Files verwenden können.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/03/2021
ms.author: phjensen
ms.openlocfilehash: 5eae527b288570053e1e899bc776d541ffa9e60b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346115"
---
# <a name="install-azure-application-consistent-snapshot-tool"></a>Installieren des Tools für konsistente Momentaufnahmen

Dieser Artikel enthält eine Installationsanleitung für das Tool für anwendungskonsistente Momentaufnahmen in Azure, das Sie mit Azure NetApp Files oder großen Azure-Instanzen verwenden können.

> [!IMPORTANT]
> Verteilte Installationen sind die einzige Option für **große Azure-Instanzen**, da sie in einem privaten Netzwerk bereitgestellt werden.  Daher müssen AzAcSnap-Installationen auf jedem System durchgeführt werden, um die Konnektivität sicherzustellen.

## <a name="introduction"></a>Einführung

Das herunterladbare selbstständige Installationsprogramm vereinfacht die Einrichtung und Ausführung von Momentaufnahmetools ohne Berechtigungen als root-Benutzer (Beispiel: azacsnap). Das Installationsprogramm richtet den Benutzer ein und fügt die Momentaufnahmetools in das `$HOME/bin`-Unterverzeichnis des Benutzers ein (Standardwert: `/home/azacsnap/bin`).
Das Programm versucht, anhand der Konfiguration des Benutzers, der die Installation durchführt (z. B. des root-Benutzers), die richtigen Einstellungen und Pfade für alle Dateien zu bestimmen. Wenn die erforderlichen Schritte (Aktivieren der Kommunikation mit dem Speicher und SAP HANA) als root-Benutzer ausgeführt wurden, kopiert die Installation den privaten Schlüssel und `hdbuserstore` in den Speicherort des Sicherungsbenutzers. Ein erfahrener Administrator kann die Schritte zum Aktivieren der Kommunikation mit dem Speicher-Back-End und SAP HANA aber auch nach der Installation manuell ausführen.

## <a name="prerequisites-for-installation"></a>Voraussetzungen für die Installation

Befolgen Sie die Richtlinien zum Einrichten und Ausführen der Befehle für Momentaufnahmen und Notfallwiederherstellung. Es empfiehlt sich, die folgenden Schritte als root-Benutzer auszuführen, bevor Sie die Momentaufnahmetools installieren und verwenden.

1. **Das Betriebssystem ist gepatcht**: Informationen zum Patchen und SMT-Setup finden Sie unter [Installieren und Konfigurieren von SAP HANA in Azure (große Instanzen)](../virtual-machines/workloads/sap/hana-installation.md#operating-system).
1. **Zeitsynchronisierung ist eingerichtet**: Der Kunde muss einen mit NTP kompatiblen Zeitserver bereitstellen und das Betriebssystem entsprechend konfigurieren.
1. **HANA ist installiert**: Anweisungen zum Installieren von HANA finden Sie unter [SAP NetWeaver-Installation in einer HANA-Datenbank](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Aktivieren der Kommunikation mit dem Speicher](#enable-communication-with-storage)** (Details dazu finden Sie im entsprechenden Abschnitt): Der Kunde muss SSH mit einem Paar aus privatem/öffentlichem Schlüssel einrichten und dem Microsoft-Betriebsteam den öffentlichen Schlüssel für jeden Knoten zur Verfügung stellen, auf dem die Ausführung der Momentaufnahmetools geplant ist, damit das Team die Schlüssel im Speicher-Back-End einrichten kann.
   1. **Für Azure NetApp Files (Details dazu finden Sie im entsprechenden Abschnitt)** : Der Kunde muss die Authentifizierungsdatei für den Dienstprinzipal generieren.
      
      > [!IMPORTANT]
      > Bei der Validierung der Kommunikation mit Azure NetApp Files kann bei der Kommunikation ein Fehler oder ein Timeout auftreten. Stellen Sie sicher, dass die Firewallregeln den ausgehenden Datenverkehr vom System, auf dem AzAcSnap läuft, zu den folgenden Adressen und TCP/IP-Ports nicht blockieren:
      > - (https://)management.azure.com:443
      > - (https://)login.microsoftonline.com:443
      
   1. **Für große Azure-Instanzen (Details dazu finden Sie im entsprechenden Abschnitt)** : Der Kunde muss SSH mit einem Paar aus privatem/öffentlichem Schlüssel einrichten und dem Microsoft-Betriebsteam den öffentlichen Schlüssel für jeden Knoten zur Verfügung stellen, auf dem die Ausführung der Momentaufnahmetools geplant ist, damit das Team die Schlüssel im Speicher-Back-End einrichten kann.

      Testen Sie das Setup, indem Sie über SSH eine Verbindung mit einem der Knoten herstellen (z. B. `ssh -l <Storage UserName> <Storage IP Address>`).
      Geben Sie `exit` ein, um sich von der Eingabeaufforderung des Speichers abzumelden.

      Das Microsoft-Betriebsteam stellt den Benutzer und die IP-Adresse für den Speicher zum Zeitpunkt der Bereitstellung zur Verfügung.
  
1. **[Aktivieren der Kommunikation mit SAP HANA](#enable-communication-with-sap-hana)** (Details dazu finden Sie im entsprechenden Abschnitt): Der Kunde muss einen geeigneten SAP HANA-Benutzer mit den für die Ausführung der Momentaufnahme erforderlichen Berechtigungen einrichten.
   1. Dieses Setup kann über die Befehlszeile mit dem `grey` formatierten Text getestet werden:
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Die obigen Beispiele gelten für die Nicht-SSL-Kommunikation mit SAP HANA.

## <a name="enable-communication-with-storage"></a>Aktivieren der Kommunikation mit dem Speicher

In diesem Abschnitt wird erläutert, wie Sie die Kommunikation mit dem Speicher aktivieren.  

Befolgen Sie die Anweisungen zum Konfigurieren des Speichers für Ihre Konfiguration:
1. [Azure NetApp Files (mit Virtual Machine)](#azure-netapp-files-with-virtual-machine) 
1. [Azure (große Instanz) (Bare-Metal)](#azure-large-instance-bare-metal)

### <a name="azure-netapp-files-with-virtual-machine"></a>Azure NetApp Files (mit Virtual Machine)

Erstellen eines RBAC-Dienstprinzipals

1. Stellen Sie in einer Azure Cloud Shell-Sitzung sicher, dass Sie bei dem Abonnement angemeldet sind, in dem Sie standardmäßig dem Dienstprinzipal zugeordnet werden möchten:

    ```azurecli-interactive
    az account show
    ```

1. Falls das Abonnement nicht korrekt ist, führen Sie diesen Befehl aus:

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Erstellen Sie über die Azure CLI einen Dienstprinzipal, wie im folgenden Beispiel gezeigt:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Dadurch eine Ausgabe ähnlich der folgenden generiert werden:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Dieser Befehl weist dem Dienstprinzipal automatisch die Rolle als RBAC-Mitwirkender auf Abonnementebene zu. Sie können den Gültigkeitsbereich auf die Ressourcengruppe eingrenzen, in dem Ihre Tests Ressourcen erstellen werden.

1. Schneiden Sie den Inhalt der Ausgabe aus, und fügen Sie ihn in eine Datei namens `azureauth.json` ein, die im selben System wie der Befehl `azacsnap` gespeichert ist. Sichern Sie die Datei mit geeigneten Systemberechtigungen.

### <a name="azure-large-instance-bare-metal"></a>Azure (große Instanz) (Bare-Metal)

Die Kommunikation mit dem Speicher-Back-End erfolgt über einen verschlüsselten SSH-Kanal. Die folgenden Beispielschritte dienen als Anleitung für die Einrichtung von SSH für diese Kommunikation.

1. Ändern Sie die Datei `/etc/ssh/ssh_config`.

    Beachten Sie die folgende Ausgabe, in der die Zeile `MACs hmac-sha1` hinzugefügt wurde:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Erstellen eines Paars aus privatem/öffentlichem Schlüssel

    Verwenden Sie den folgenden Beispielbefehl, um das Schlüsselpaar zu generieren. Geben Sie beim Generieren eines Schlüssels kein Kennwort ein.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Senden Sie den öffentlichen Schlüssel an das Microsoft-Betriebsteam.

    Senden Sie die Ausgabe des Befehls `cat /root/.ssh/id_rsa.pub` (siehe Beispiel unten) an das Microsoft-Betriebsteam, um Kommunikation zwischen den Momentaufnahmetools und dem Speichersubsystem zu aktivieren.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Aktivieren der Kommunikation mit SAP HANA

Die Momentaufnahmetools kommunizieren mit SAP HANA und benötigen einen Benutzer mit geeigneten Berechtigungen zum Initiieren und Freigeben eines Sicherungspunkts für Datenbanken. Das folgende Beispiel zeigt, wie Sie den SAP HANA v2-Benutzer und `hdbuserstore` für die Kommunikation mit der SAP HANA-Datenbank einrichten.

Die folgenden Beispielbefehle richten einen Benutzer (AZACSNAP) in der SYSTEMDB in SAP HANA 2 ein.
Ändern Sie die IP-Adresse, Benutzernamen und Kennwörter entsprechend Ihren Anforderungen:

1. Stellen Sie eine Verbindung mit der SYSTEMDB her, um den Benutzer zu erstellen.

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Erstellen Sie den Benutzer.

    Dieses Beispiel erstellt den Benutzer AZACSNAP in der SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Erteilen Sie dem Benutzer Berechtigungen.

    Dieses Beispiel legt die Berechtigung für den Benutzer AZACSNAP fest, um die Erstellung einer datenbankkonsistenten Speichermomentaufnahme zuzulassen.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *OPTIONAL*: Verhindern Sie, dass das Kennwort des Benutzers abläuft.

    > [!NOTE]
    > Informieren Sie sich in der entsprechenden Unternehmensrichtlinie, bevor Sie diese Änderung vornehmen.

   Dieses Beispiel deaktiviert den Kennwortablauf für den Benutzer AZACSNAP. Ohne diese Änderung würde das Kennwort des Benutzers ablaufen, und Momentaufnahmen könnten nicht mehr ordnungsgemäß erstellt werden.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Richten Sie den sicheren Benutzerspeicher von SAP HANA ein (ändern Sie das Kennwort). Dieses Beispiel verwendet den Befehl `hdbuserstore` aus der Linux-Shell, um den sicheren SAP HANA-Benutzerspeicher einzurichten.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Überprüfen Sie, ob der sichere SAP HANA-Benutzerspeicher ordnungsgemäß eingerichtet wurde, indem Sie den Befehl `hdbuserstore` verwenden, der eine Ausgabe ähnlich dem folgenden Beispiel auflistet. Weitere Informationen zur Verwendung von `hdbuserstore` finden Sie auf der SAP-Website.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Verwenden von SSL für die Kommunikation mit SAP HANA

Das `azacsnap`-Tool verwendet den SAP HANA-Befehl `hdbsql` für die Kommunikation mit SAP HANA. Beim Verschlüsseln der Kommunikation mit SAP HANA werden SSL-Optionen verwendet. `azacsnap` verwendet die SSL-Optionen des `hdbsql`-Befehls wie folgt.

Mit der Option `azacsnap --ssl` werden immer folgende Optionen verwendet:

- `-e`: Aktiviert die TLS- bzw. SSL-Verschlüsselung. Der Server wählt die höchste verfügbare Verschlüsselung aus.
- `-ssltrustcert`: Gibt an, ob das Serverzertifikat überprüft werden soll.
- `-sslhostnameincert "*"`: Gibt den Hostnamen an, der zur Überprüfung der Serveridentität verwendet werden soll. Wenn `"*"` als Hostname angegeben ist, wird der Hostname des Servers nicht überprüft.

Für die SSL-Kommunikation sind auch Schlüsselspeicher- und Vertrauensspeicherdateien erforderlich.  Es ist zwar möglich, diese Dateien in einer Linux-Installation in Standardspeicherorten zu speichern, aber um sicherzustellen, dass das richtige Schlüsselmaterial für die verschiedenen SAP HANA-Systeme verwendet wird (also in Fällen, in denen für die SAP HANA-Systeme unterschiedliche Schlüsselspeicher- und Vertrauensspeicherdateien verwendet werden), erwartet `azacsnap` die Dateien für Schlüssel- und Vertrauensspeicher im Speicherort `securityPath`, wie in der Konfigurationsdatei `azacsnap` angegeben.

#### <a name="key-store-files"></a>Schlüsselspeicherdateien

- Wenn Sie mehrere SIDs mit demselben Schlüsselmaterial verwenden, ist es einfacher, Links zum securityPath-Speicherort zu erstellen, wie in der Konfigurationsdatei `azacsnap` definiert.  Stellen Sie sicher, dass diese Werte für jede SID vorhanden sind, die SSL verwendet.
  - Für openssl:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Für commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Wenn Sie mehrere SIDs mit unterschiedlichem Schlüsselmaterial für jede SID verwenden, kopieren Sie die Dateien in den securityPath-Speicherort, wie in der Konfigurationsdatei `azacsnap` der SID definiert (oder verschieben Sie die Dateien, und benennen Sie sie um).
  - Für openssl:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Für commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Beim Aufruf von `hdbsql` fügt `azacsnap` der Befehlszeile `-sslkeystore=<securityPath>/<SID>_keystore` hinzu.

#### <a name="trust-store-files"></a>Vertrauensspeicherdateien

- Wenn Sie mehrere SIDs mit demselben Schlüsselmaterial verwenden, erstellen Sie hartcodierte Links zum securityPath-Speicherort, wie in der Konfigurationsdatei `azacsnap` definiert.  Stellen Sie sicher, dass diese Werte für jede SID vorhanden sind, die SSL verwendet.
  - Für openssl:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Für commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Wenn Sie mehrere SIDs mit unterschiedlichem Schlüsselmaterial für jede SID verwenden, kopieren Sie die Dateien in den securityPath-Speicherort, wie in der Konfigurationsdatei `azacsnap` der SID definiert (oder verschieben Sie die Dateien, und benennen Sie sie um).
  - Für openssl:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Für commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> Der Bestandteil `<SID>` der Dateinamen muss der SAP HANA-Systembezeichner in Großbuchstaben sein (z. B. `H80`, `PR1` usw.).

Beim Aufruf von `hdbsql` fügt `azacsnap` der Befehlszeile `-ssltruststore=<securityPath>/<SID>_truststore` hinzu.

Daher führt die Befehlszeile `azacsnap -c test --test hana --ssl openssl`, wenn die `SID` in der Konfigurationsdatei `H80` lautet, die `hdbsql`-Verbindungen wie folgt aus:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> Das Zeichen `\` ist ein Zeilenumbruchzeichen, mit dem Sie die verschiedenen Parameter, die an der Befehlszeile übergeben werden, besser unterscheiden können.

## <a name="installing-the-snapshot-tools"></a>Installieren der Momentaufnahmetools

Das herunterladbare selbstständige Installationsprogramm vereinfacht die Einrichtung und Ausführung von Momentaufnahmetools ohne Berechtigungen als root-Benutzer (Beispiel: azacsnap). Das Installationsprogramm richtet den Benutzer ein und fügt die Momentaufnahmetools in das `$HOME/bin`-Unterverzeichnis des Benutzers ein (Standardwert: `/home/azacsnap/bin`).

Das Programm versucht, anhand der Konfiguration des Benutzers, der die Installation durchführt (z. B. des root-Benutzers), die richtigen Einstellungen und Pfade für alle Dateien zu bestimmen. Wenn die vorherigen Einrichtungsschritte (Aktivieren der Kommunikation mit dem Speicher und SAP HANA) als root-Benutzer ausgeführt wurden, kopiert die Installation den privaten Schlüssel und `hdbuserstore` in den Speicherort des Sicherungsbenutzers. Ein erfahrener Administrator kann die Schritte zum Aktivieren der Kommunikation mit dem Speicher-Back-End und SAP HANA aber auch nach der Installation manuell ausführen.

> [!NOTE]
> In früheren Installationen von SAP HANA auf großen Azure-Instanzen waren lautete das Verzeichnis von vorinstallierten Momentaufnahmetools `/hana/shared/<SID>/exe/linuxx86_64/hdb`.

Wenn die [erforderlichen Schritte](#prerequisites-for-installation) abgeschlossen sind, können Sie jetzt wie folgt die Momentaufnahmetools mithilfe des selbstständigen Installationsprogramms installieren:

1. Kopieren Sie das heruntergeladene selbstständige Installationsprogramm auf das Zielsystem.
1. Führen Sie das Programm als `root`-Benutzer aus, wie im folgenden Beispiel gezeigt. Legen Sie die Datei bei Bedarf mithilfe des Befehls `chmod +x *.run` als ausführbar fest.

Die Ausführung des selbstständigen Installationsprogramms ohne Argumente zeigt Hilfe zur Verwendung des Programms zur Installation der Momentaufnahmetools an:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Das selbstständige Installationsprogramm enthält eine Option zum Extrahieren (-X) der Momentaufnahmetools aus dem Paket, ohne dass ein Benutzer erstellt und eingerichtet werden muss. So können erfahrene Administratoren die Einrichtungsschritte manuell ausführen oder die Befehle kopieren, um ein Upgrade einer vorhandenen Installation auszuführen.

### <a name="easy-installation-of-snapshot-tools-default"></a>Einfache Installation der Momentaufnahmetools (Standard)

Das Installationsprogramm dient dazu, die Momentaufnahmetools für SAP HANA in Azure schnell zu installieren. Standardmäßig führt das Installationsprogramm die folgenden Schritte aus, wenn es nur mit der Option „-I“ ausgeführt wird:

1. Der Momentaufnahmebenutzer „azacsnap“ und das Basisverzeichnis werden erstellt, und die Gruppenmitgliedschaft wird festgelegt.
1. Das `~/.profile` für die Anmeldung des Benutzers „azacsnap“ wird konfiguriert.
1. Das Dateisystem wird nach Verzeichnissen durchsucht, die dem `$PATH` von „azacsnap“ hinzugefügt werden sollen. Dies sind in der Regel die Pfade zu den SAP HANA-Tools, z. B. `hdbsql` und `hdbuserstore`.
1. Das Dateisystem wird nach Verzeichnissen durchsucht, die dem `$LD_LIBRARY_PATH` von „azacsnap“ hinzugefügt werden sollen. Viele Befehle erfordern für die korrekte Ausführung einen Bibliothekspfad. Dieser wird für den installierten Benutzer konfiguriert.
1. Die SSH-Schlüssel für den Back-End-Speicher für „azacsnap“ werden vom root-Benutzer (dem Benutzer, der die Installation ausgeführt hat) kopiert. Dabei wird vorausgesetzt, dass der root-Benutzer die Verbindung mit dem Speicher bereits konfiguriert hat.
    - Siehe Abschnitt [Aktivieren der Kommunikation mit dem Speicher](#enable-communication-with-storage).
1. Der sichere Benutzerspeicher für die SAP HANA-Verbindung wird für den Zielbenutzer „azacsnap“ kopiert. Dabei wird vorausgesetzt, dass der root-Benutzer den sicheren Benutzerspeicher bereits konfiguriert hat – siehe Abschnitt „Aktivieren der Kommunikation mit SAP HANA“.
1. Die Momentaufnahmetools werden in `/home/azacsnap/bin/` extrahiert.
1. Die Berechtigungen für die Befehle in `/home/azacsnap/bin/` werden festgelegt (Besitz, ausführbares Bit usw.).

Das folgende Beispiel zeigt die korrekte Ausgabe des Installationsprogramms bei Ausführung mit der Standardinstallationsoption.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Deinstallieren der Momentaufnahmetools

Wenn die Momentaufnahmetools mit den Standardeinstellungen installiert wurden, muss zur Deinstallation nur der Benutzer entfernt werden, für den die Befehle installiert wurden (Standardwert: azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Manuelle Installation der Momentaufnahmetools

In einigen Fällen ist es notwendig, die Tools manuell zu installieren. Es wird aber empfohlen, die Standardoption des Installationsprogramms zu verwenden, um das Verfahren zu vereinfachen.

Jede mit einem `#`-Zeichen beginnende Zeile zeigt, dass die Beispielbefehle nach dem Zeichen durch den root-Benutzer ausgeführt werden. Das Zeichen `\` am Ende einer Zeile ist das standardmäßige Zeilenfortsetzungszeichen für einen Shellbefehl.

Als root-Superuser kann eine manuelle Installation wie folgt durchgeführt werden:

1. Rufen Sie die Gruppen-ID für „sapsys“ ab, in diesem Fall „1010“.

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Erstellen Sie den Momentaufnahmebenutzer „azacsnap“ und das Basisverzeichnis, und legen Sie die Gruppenmitgliedschaft mithilfe der Gruppen-ID aus Schritt 1 fest.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Stellen Sie sicher, dass das `.profile` der Benutzeranmeldung für „azacsnap“ vorhanden ist.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Durchsuchen Sie das Dateisystem nach Verzeichnissen, die dem $PATH von „azacsnap“ hinzugefügt werden sollen. Dies sind in der Regel die Pfade zu den SAP HANA-Tools, z. B. `hdbsql` und `hdbuserstore`.

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Fügen Sie den aktualisierten PATH zum Benutzerprofil hinzu.

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Durchsuchen Sie das Dateisystem nach Verzeichnissen, die dem $LD_LIBRARY_PATH von „azacsnap“ hinzugefügt werden sollen.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Fügen Sie den aktualisierten Bibliothekspfad zum Benutzerprofil hinzu.

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Gehen Sie in großen Azure-Instanzen wie folgt vor:
    1. Die SSH-Schlüssel für den Back-End-Speicher für „azacsnap“ werden vom root-Benutzer (dem Benutzer, der die Installation ausgeführt hat) kopiert. Dabei wird vorausgesetzt, dass der root-Benutzer die Verbindung mit dem Speicher bereits konfiguriert hat.
       > Siehe Abschnitt [Aktivieren der Kommunikation mit dem Speicher](#enable-communication-with-storage).

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Legen Sie die Benutzerberechtigungen für die SSH-Dateien ordnungsgemäß fest.

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Gehen Sie in Azure NetApp Files wie folgt vor:
    1. Konfigurieren Sie den `DOTNET_BUNDLE_EXTRACT_BASE_DIR`-Pfad des Benutzers gemäß der Anleitung zum Extrahieren einzelner .NET Core-Dateien.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Der sichere Benutzerspeicher für die SAP HANA-Verbindung wird für den Zielbenutzer „azacsnap“ kopiert. Dabei wird vorausgesetzt, dass der root-Benutzer den sicheren Benutzerspeicher bereits konfiguriert hat.
    > Siehe Abschnitt [Aktivieren der Kommunikation mit SAP HANA](#enable-communication-with-sap-hana).

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Legen Sie die Benutzerberechtigungen für die `hdbuserstore`-Dateien ordnungsgemäß fest.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extrahieren Sie die Momentaufnahmetools in diesen Pfad: /home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Legen Sie die Befehle als ausführbar fest.

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Stellen Sie sicher, dass im Basisverzeichnis des Benutzers die richtigen Besitzberechtigungen festgelegt sind.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Abschließen der Einrichtung der Momentaufnahmetools

Das Installationsprogramm bietet Schritte zum Abschluss des Prozesses an, nachdem die Installation der Momentaufnahmetools erfolgt ist.
Führen Sie diese Schritte aus, um die Momentaufnahmetools zu konfigurieren und zu testen.  Nach dem erfolgreichen Test erstellen Sie die erste datenbankkonsistente Speichermomentaufnahme.

Die folgende Ausgabe zeigt die Schritte zum Abschließen des Prozesses nach Ausführung des Installationsprogramms mit den Standardinstallationsoptionen:

1. Wechseln Sie zum Konto des Momentaufnahmebenutzers.
    1. `su - azacsnap`
1. Richten Sie den sicheren Benutzerspeicher für HANA ein.
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Wechseln Sie zum Speicherort der Befehle.
   1. `cd /home/azacsnap/bin/`
1. Konfigurieren Sie die Datei mit Kundendetails.
   1. `azacsnap -c configure –-configuration new`
1. Testen Sie die Verbindung mit dem Speicher.
   1. `azacsnap -c test –-test storage`
1. Testen Sie die Verbindung mit HANA.
    1. Ohne SSL:
       1. `azacsnap -c test –-test hana`
    1. Mit SSL, Sie müssen die richtige SSL-Option auswählen:
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Führen Sie die erste Momentaufnahmesicherung durch.
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Schritt 2 ist erforderlich, wenn der Schritt [Aktivieren der Kommunikation mit SAP HANA](#enable-communication-with-sap-hana) vor der Installation nicht ausgeführt wurde.

> [!NOTE]
> Die Testbefehle sollten ordnungsgemäß ausgeführt werden. Andernfalls werden die Befehle möglicherweise nicht erfolgreich ausgeführt.

## <a name="configuring-the-database"></a>Konfigurieren der Datenbank

In diesem Abschnitt wird erläutert, wie Sie die Datenbank konfigurieren.

### <a name="sap-hana-configuration"></a>SAP HANA-Konfiguration

Hier finden Sie einige empfohlene Änderungen an SAP HANA, um den Schutz von Protokollsicherungen und Katalog sicherzustellen. Standardmäßig geben `basepath_logbackup` und `basepath_catalogbackup` ihre Dateien in das Verzeichnis `$(DIR_INSTANCE)/backup/log` aus. Es ist unwahrscheinlich, dass dieser Pfad sich in einem Volume befindet, in dem `azacsnap` für die Erstellung von Momentaufnahmen dieser Dateien konfiguriert ist und die Speichermomentaufnahmen nicht geschützt werden.

Die folgenden Beispiele für den `hdbsql`-Befehl sollen veranschaulichen, wie die Protokoll- und Katalogpfade auf Speicherorte in Speichervolumes festgelegt werden, für die durch `azacsnap` eine Momentaufnahme erstellt werden kann. Vergewissern Sie sich, dass die Werte in der Befehlszeile mit der lokalen SAP HANA-Konfiguration übereinstimmen.

### <a name="configure-log-backup-location"></a>Konfigurieren des Speicherorts für Protokollsicherungen

In diesem Beispiel wird der Parameter `basepath_logbackup` geändert.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Konfigurieren des Speicherorts für Katalogsicherungen

In diesem Beispiel wird der Parameter `basepath_catalogbackup` geändert. Überprüfen Sie zunächst, ob der Pfad `basepath_catalogbackup` im Dateisystem vorhanden ist. Wenn er nicht vorhanden ist, erstellen Sie den Pfad mit denselben Besitzeinstellungen, die auch für das Verzeichnis gelten.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Das folgende Beispiel erstellt den Pfad und legt die richtigen Werte für Besitz und Berechtigungen fest. Diese Befehle müssen als root-Benutzer ausgeführt werden.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Das folgende Beispiel ändert die SAP HANA-Einstellung.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Überprüfen der Speicherorte für die Protokoll- und Katalogsicherungen

Nachdem Sie die oben erläuterten Änderungen vorgenommen haben, überprüfen Sie mit dem folgenden Befehl, ob die Einstellungen korrekt sind.
In diesem Beispiel werden die Einstellungen, die gemäß der Anleitung oben festgelegt wurden, als Einstellungen für „SYSTEM“ angezeigt.

> Die Abfrage gibt zu Vergleichszwecken auch die Einstellungen für „STANDARD“ zurück.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Konfigurieren des Timeouts für Protokollsicherungen

Mit der Standardeinstellung führt SAP HANA alle 900 Sekunden (15 Minuten) eine Protokollsicherung aus. Es wird empfohlen, diesen Wert auf 300 Sekunden (5 Minuten) zu senken.  Dann ist es möglich, regelmäßige Sicherungen (beispielsweise alle 10 Minuten) auszuführen, indem Sie das Volume „log_backups“ zum Volumeabschnitt „OTHER“ der Konfigurationsdatei hinzufügen.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Überprüfen des Timeouts für Protokollsicherungen

Nachdem Sie den Timeoutwert für Protokollsicherungen geändert haben, überprüfen Sie folgendermaßen, ob die Änderung korrekt durchgeführt wurde.
In diesem Beispiel werden die Einstellungen als Einstellungen für „SYSTEM“ angezeigt, aber die Abfrage gibt zu Vergleichszwecken auch die Einstellungen für „STANDARD“ zurück.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des Tools für anwendungskonsistente Momentaufnahmen in Azure](azacsnap-cmd-ref-configure.md)
