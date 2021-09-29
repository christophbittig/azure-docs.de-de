---
title: Problembehandlung für die Replikation virtueller Azure-Computer in Azure Site Recovery
description: Hier finden Sie Informationen zum Beheben von Fehlern beim Replizieren von virtuellen Azure-Computern für die Notfallwiederherstellung.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: faee5c872b70f55011c6213a04e14c6f69bcc8df
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730450"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Problembehandlung für Azure-zu-Azure-VM-Replikationsfehler

In diesem Artikel wird beschrieben, wie Sie häufig auftretende Fehler in Azure Site Recovery während der Replikation und Wiederherstellung von virtuellen Azure-Computern (VMs) aus einer Region in eine andere beheben können. Weitere Informationen zu unterstützten Konfigurationen finden Sie unter [Azure Site Recovery support matrix for replicating from Azure to Azure](azure-to-azure-support-matrix.md) (Azure Site Recovery-Supportmatrix zum Replizieren aus Azure in Azure).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Probleme mit dem Azure-Ressourcenkontingent (Fehlercode 150097)

Vergewissern Sie sich, dass es für Ihr Abonnement möglich ist, Azure-VMs in der Zielregion zu erstellen, die Sie als Region für die Notfallwiederherstellung nutzen möchten. Ihr Abonnement benötigt ein ausreichendes Kontingent zum Erstellen von VMs der erforderlichen Größen. Standardmäßig wählt Site Recovery eine Ziel-VM-Größe aus, die der Größe der Quell-VM entspricht. Falls die passende Größe nicht verfügbar ist, wählt Site Recovery automatisch die verfügbare Größe aus, die dieser am nächsten kommt.

Wenn keine Größe vorhanden ist, die die Konfiguration der Quell-VM unterstützt, wird die folgende Meldung angezeigt:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Mögliche Ursachen

- Für Ihre Abonnement-ID ist die Erstellung von VMs am Standort der Zielregion nicht aktiviert.
- Für Ihre Abonnement-ID ist die Erstellung von bestimmten VM-Größen am Standort der Zielregion nicht aktiviert, oder es ist kein ausreichendes Kontingent vorhanden.
- Für die Abonnement-ID wird am Standort der Zielregion keine geeignete Größe der Ziel-VM gefunden, die mit der Anzahl (2) der Netzwerkschnittstellenkarten (NIC) der Quell-VM übereinstimmt.

### <a name="fix-the-problem"></a>Beheben des Problems

Wenden Sie sich an den [Azure-Abrechnungssupport](../azure-portal/supportability/regional-quota-requests.md), um Ihr Abonnement für die Erstellung von VMs der erforderlichen Größen am Zielstandort aktivieren zu lassen. Wiederholen Sie dann den fehlerhaften Vorgang.

Wenn für den Zielstandort eine Kapazitätsbeschränkung gilt, deaktivieren Sie die Replikation an diesen Standort. Dann aktivieren Sie die Replikation an einen anderen Standort, an dem für Ihr Abonnement ein ausreichendes Kontingent zum Erstellen von VMs der erforderlichen Größen zur Verfügung steht.

## <a name="trusted-root-certificates-error-code-151066"></a>Vertrauenswürdige Stammzertifikate (Fehlercode: 151066)

Wenn auf der VM nicht alle aktuellen vertrauenswürdigen Stammzertifikate vorhanden sind, ist Ihr Auftrag zum Aktivieren der Replikation für Azure Site Recovery ggf. nicht erfolgreich. Ohne diese Zertifikate tritt für die Authentifizierung und Autorisierung von Site Recovery-Dienstaufrufen über die VM ein Fehler auf.

Wenn der Auftrag zum Aktivieren der Replikation fehlschlägt, wird die folgende Meldung angezeigt:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Mögliche Ursache

Die für die Autorisierung und Authentifizierung erforderlichen vertrauenswürdigen Stammzertifikate sind auf dem virtuellen Computer nicht vorhanden.

### <a name="fix-the-problem"></a>Beheben des Problems

#### <a name="windows"></a>Windows

Bei einer VM mit Windows-Betriebssystem installieren Sie die aktuellen Windows-Updates, damit alle vertrauenswürdigen Stammzertifikate auf der VM vorhanden sind. Führen Sie den üblichen Prozess Ihrer Organisation für die Verwaltung von Windows-Updates oder Zertifikatupdates durch, um die aktuellen Stammzertifikate und die aktualisierte Zertifikatssperrliste auf den VMs abzurufen.

- Wenn Sie sich in einer nicht verbundenen Umgebung befinden, können Sie den Standardprozess für Windows-Updates in Ihrer Organisation durchführen, um die Zertifikate abzurufen.
- Falls die erforderlichen Zertifikate auf der VM nicht vorhanden sind, schlagen die Aufrufe des Site Recovery-Diensts aus Sicherheitsgründen fehl.

Navigieren Sie mit einem Browser auf Ihrer VM zu `login.microsoftonline.com`, um zu überprüfen, ob das Problem behoben wurde.

Weitere Informationen finden Sie unter [Konfigurieren vertrauenswürdiger Stämme und unzulässiger Zertifikate](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Befolgen Sie die Anweisungen, die vom Anbieter Ihrer Linux-Betriebssystemversion bereitgestellt werden, um die aktuellen vertrauenswürdigen Stammzertifikate und die aktuelle Zertifikatssperrliste auf der VM abzurufen.

Führen Sie die folgenden Schritte aus, da SUSE Linux zum Verwalten einer Zertifikatliste symbolische Verknüpfungen (symlinks) verwendet:

1. Melden Sie sich als Benutzer **root** an. Das Rautensymbol (`#`) ist die Standardeingabeaufforderung.

1. Führen Sie den folgenden Befehl aus, um das Verzeichnis zu wechseln:

   `cd /etc/ssl/certs`

1. Überprüfen Sie, ob das Symantec-Zertifikat der Stammzertifizierungsstelle vorhanden ist:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Wenn das Symantec-Zertifikat der Stammzertifizierungsstelle nicht gefunden wurde, führen Sie den folgenden Befehl aus, um die Datei herunterzuladen. Überprüfen Sie, ob Fehler aufgetreten sind, und führen Sie die empfohlenen Maßnahmen für Netzwerkfehler aus.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Überprüfen Sie, ob das Baltimore-Zertifikat der Stammzertifizierungsstelle vorhanden ist:

   `ls Baltimore_CyberTrust_Root.pem`

   - Wurde das Baltimore-Zertifikat der Stammzertifizierungsstelle nicht gefunden, führen Sie den folgenden Befehl aus, um das Zertifikat herunterzuladen:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Überprüfen Sie, ob das Zertifikat „DigiCert_Global_Root_CA“ vorhanden ist:

   `ls DigiCert_Global_Root_CA.pem`

    - Wurde DigiCert_Global_Root_CA nicht gefunden, führen Sie die folgenden Befehle aus, um das Zertifikat herunterzuladen:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Führen Sie das Skript für das erneute Ausführen von Hashvorgängen aus, um die Zertifikatantragsteller-Hashes für die neu heruntergeladenen Zertifikate zu aktualisieren:

   `c_rehash`

1. Führen Sie die folgenden Befehle aus, um zu überprüfen, ob die Antragstellerhashes als Symlinks für die Zertifikate erstellt wurden:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Erstellen Sie eine Kopie der Datei _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ mit dem Dateinamen _b204d74a.0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Erstellen Sie eine Kopie der Datei _Baltimore_CyberTrust_Root.pem_ mit dem Dateinamen _653b494a.0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Erstellen Sie eine Kopie der Datei _DigiCert_Global_Root_CA.pem_ mit dem Dateinamen _3513523f.0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Prüfen Sie, ob die Dateien vorhanden sind:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Ausgehende URLs oder IP-Bereiche (Fehlercode 151037 oder 151072)

Damit die Site Recovery-Replikation funktioniert, ist für die VM ausgehende Konnektivität zu bestimmten URLs erforderlich. Wenn sich Ihr virtueller Computer hinter einer Firewall befindet oder Netzwerksicherheitsgruppen-Regeln (NSG-Regeln) zum Steuern der ausgehenden Konnektivität verwendet werden, wird ggf. eine dieser Fehlermeldungen angezeigt. Der ausgehende Zugriff über URLs wird zwar weiterhin unterstützt, die Verwendung einer Zulassungsliste mit IP-Bereichen jedoch nicht mehr.

#### <a name="possible-causes"></a>Mögliche Ursachen

- Eine Verbindung mit den Site Recovery-Endpunkten kann aufgrund eines Fehlers bei der DNS-Auflösung (Domain Name System) nicht hergestellt werden.
- Dieses Problem tritt häufiger beim erneuten Schützen auf, wenn Sie für die VM ein Failover ausgeführt haben, aber der DNS-Server aus der Region für die Notfallwiederherstellung nicht erreichbar ist.

#### <a name="fix-the-problem"></a>Beheben des Problems

Stellen Sie bei Verwendung von benutzerdefiniertem DNS sicher, dass der DNS-Server in der Notfallwiederherstellungsregion erreichbar ist.

Überprüfen Sie wie folgt, ob für die VM eine Einstellung für das benutzerdefinierte DNS verwendet wird:

1. Öffnen Sie **Virtuelle Computer**, und wählen Sie die VM aus.
1. Navigieren Sie für die VM zu **Einstellungen**, und wählen Sie **Netzwerk** aus.
1. Wählen Sie unter **Virtuelles Netzwerk/Subnetz** den Link zum Öffnen der Ressourcenseite des virtuellen Netzwerks aus.
1. Navigieren Sie zu **Einstellungen**, und wählen Sie **DNS-Server** aus.

Versuchen Sie, über den virtuellen Computer auf den DNS-Server zuzugreifen. Wenn der DNS-Server nicht erreichbar ist, können Sie ein Failover für den DNS-Server ausführen oder eine „Sichtverbindung“ zwischen dem Netzwerk für die Notfallwiederherstellung und dem DNS herstellen.

> [!NOTE]
> Wenn Sie private Endpunkte verwenden, stellen Sie sicher, dass die virtuellen Computer die privaten DNS-Einträge auflösen können.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Fehler bei der Site Recovery-Konfiguration (151196)

#### <a name="possible-cause"></a>Mögliche Ursache

Eine Verbindung mit IP4-Endpunkten für die Authentifizierung und Identität von Microsoft 365 kann nicht hergestellt werden.

#### <a name="fix-the-problem"></a>Beheben des Problems

Azure Site Recovery benötigt zur Authentifizierung Zugriff auf IP-Adressbereiche von Microsoft 365.
Wenn Sie Regeln für Azure-Netzwerksicherheitsgruppen (NSG-Regeln) oder einen Firewallproxy zum Steuern der ausgehenden Netzwerkkonnektivität für die VM verwenden, muss sichergestellt werden, dass Sie zum Zulassen des Zugriffs auf AAD eine NSG-Regel verwenden, die auf dem [AAD-Diensttag (Azure Active Directory)](../virtual-network/network-security-groups-overview.md#service-tags) basiert. Auf IP-Adressen basierende NSG-Regeln werden nicht mehr unterstützt.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Fehler bei der Site Recovery-Konfiguration (151197)

#### <a name="possible-cause"></a>Mögliche Ursache

Eine Verbindung mit Azure Site Recovery-Dienstendpunkten kann nicht hergestellt werden.

#### <a name="fix-the-problem"></a>Beheben des Problems

Wenn Sie zum Steuern der ausgehenden Netzwerkkonnektivität auf dem virtuellen Computer Regeln für Azure-Netzwerksicherheitsgruppen (NSG-Regeln) oder einen Firewallproxy verwenden, müssen Sie Diensttags verwenden. Wir unterstützen für Azure Site Recovery nicht mehr die Verwendung einer Zulassungsliste von IP-Adressen über Netzwerksicherheitsgruppen.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problem 4: Replikation schlägt fehl, wenn der Netzwerkdatenverkehr einen lokalen Proxyserver verwendet (151072)

#### <a name="possible-cause"></a>Mögliche Ursache

Die benutzerdefinierten Proxyeinstellungen sind ungültig, und der Mobilitätsdienst-Agent hat die Proxyeinstellungen von Internet Explorer (IE) nicht automatisch erkannt.

#### <a name="fix-the-problem"></a>Beheben des Problems

1. Der Mobilitätsdienst-Agent erkennt die Proxyeinstellungen von Internet Explorer unter Windows und unter `/etc/environment` für Linux.
1. Wenn nur für den Mobilitätsdienst ein Proxy festgelegt werden soll, können Sie die Proxydetails in _ProxyInfo.conf_ angeben, die sich hier befinden:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ sollte die Proxyeinstellungen im folgenden _INI_-Format enthalten.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Vom Mobilitätsdienst-Agent werden nur **nicht authentifizierte Proxys** unterstützt.

### <a name="more-information"></a>Weitere Informationen

Um die [erforderlichen URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) oder [erforderlichen IP-Adressbereiche](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) anzugeben, befolgen Sie die Anweisungen unter [Netzwerkkonzepte für die Replikation zwischen Azure-Standorten](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Datenträger in VM nicht gefunden (Fehlercode 150039)

Es muss ein neuer Datenträger initialisiert werden, der an die VM angefügt ist. Wenn der Datenträger nicht gefunden wurde, wird die folgende Meldung angezeigt:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Mögliche Ursachen

- Es wurde ein neuer Datenträger an den virtuellen Computer angefügt, aber nicht initialisiert.
- Der Datenträger innerhalb des virtuellen Computers meldet nicht den richtigen LUN-Wert (logische Gerätenummer), mit dem der Datenträger an den virtuellen Computer angefügt wurde.

### <a name="fix-the-problem"></a>Beheben des Problems

Stellen Sie sicher, dass die Datenträger initialisiert wurden, und wiederholen Sie den Vorgang.

- **Windows**: [Anfügen und Initialisieren eines neuen Datenträgers](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux:** [Initialisieren eines neues Datenträgers unter Linux](../virtual-machines/linux/add-disk.md).

Wenden Sie sich an den Support, wenn das Problem weiterhin besteht.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Mehrere Datenträger zum Schutz verfügbar (Fehlercode 153039)

### <a name="possible-causes"></a>Mögliche Ursachen

- Mindestens ein Datenträger wurde dem virtuellen Computer vor Kurzem nach dem Schutz hinzugefügt.
- Mindestens ein Datenträger wurde nach dem Schutz des virtuellen Computers initialisiert.

### <a name="fix-the-problem"></a>Beheben des Problems

Damit der Replikationsstatus des virtuellen Computers wieder fehlerfrei ist, können Sie entweder die Datenträger schützen oder die Warnung ignorieren.

#### <a name="to-protect-the-disks"></a>So schützen Sie die Datenträger

1. Wechseln Sie zu **Replizierte Elemente** > _Name des virtuellen Computers_ > **Datenträger**.
1. Wählen Sie den ungeschützten Datenträger und dann **Replikation aktivieren** aus:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Aktivieren der Replikation auf VM-Datenträgern.":::

#### <a name="to-dismiss-the-warning"></a>So ignorieren Sie die Warnung

1. Wechseln Sie zu **Replizierte Elemente** > _Name des virtuellen Computers_.
1. Wählen Sie die Warnung im Abschnitt **Übersicht** aus, und klicken Sie dann auf **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Ignorieren der Warnung zu neuem Datenträger.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Aus dem Tresor entfernte VM wurde mit Informationen abgeschlossen (Fehlercode 150225)

Beim Schützen des virtuellen Computers erstellt Site Recovery Links auf dem virtuellen Quellcomputer. Wenn Sie den Schutz entfernen oder die Replikation deaktivieren, entfernt Site Recovery diese Links im Rahmen des Bereinigungsauftrags. Wenn für den virtuellen Computer eine Ressourcensperre festgelegt ist, wird der Bereinigungsauftrag mit Informationen abgeschlossen. Die Informationen geben an, dass der virtuelle Computer aus dem Recovery Services-Tresor entfernt wurde, dass aber einige der veralteten Links auf dem Quellcomputer nicht bereinigt werden konnten.

Sie können diese Warnung ignorieren, wenn Sie diesen virtuellen Computer nicht wieder schützen möchten. Wenn Sie diesen virtuellen Computer jedoch später schützen müssen, führen Sie die Schritte in diesem Abschnitt aus, um die Links zu bereinigen.

> [!WARNING]
> Wenn Sie keine Bereinigung ausführen, wirkt sich das wie folgt aus:
>
> - Wenn Sie die Replikation mithilfe des Recovery Services-Tresors aktivieren, wird der virtuelle Computer nicht aufgelistet.
> - Bei dem Versuch, den virtuellen Computer mithilfe von **Virtueller Computer** > **Einstellungen** > **Notfallwiederherstellung** zu schützen, tritt ein Fehler beim Vorgang mit folgender Meldung auf: **Die Replikation kann aufgrund der vorhandenen veralteten Ressourcenlinks für die VM nicht aktiviert werden.**

### <a name="fix-the-problem"></a>Beheben des Problems

> [!NOTE]
> Site Recovery löscht oder beeinträchtigt nicht den virtuellen Quellcomputer, während Sie diese Schritte ausführen.

1. Entfernen Sie die Sperre von der VM oder VM-Ressourcengruppe. In der folgenden Abbildung muss beispielsweise die Ressourcensperre für die VM mit dem Namen `MoveDemo` gelöscht werden:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Entfernen der Sperre von der VM.":::

1. Laden Sie das Skript zum [Entfernen einer veralteten Site Recovery-Konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1) herunter.
1. Führen Sie das Skript _Cleanup-stale-asr-config-Azure-VM.ps1_ aus. Geben Sie **Abonnement-ID**, **VM-Ressourcengruppe** und **Name der VM** als Parameter an.
1. Wenn Sie zur Eingabe von Azure-Anmeldeinformationen aufgefordert werden, geben Sie diese an. Überprüfen Sie dann, ob das Skript ohne Fehler ausgeführt wird.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replikation auf VM mit veralteten Ressourcen nicht aktiviert (Fehlercode 150226)

### <a name="possible-causes"></a>Mögliche Ursachen

Auf dem virtuellen Computer ist eine veraltete Konfiguration von einem früheren Site Recovery-Schutz vorhanden.

Eine veraltete Konfiguration kann auf einem virtuellen Azure-Computer vorhanden sein, wenn Sie die Replikation für den virtuellen Azure-Computer mithilfe von Site Recovery aktiviert haben und dann wie folgt vorgegangen sind:

- Sie haben die Replikation deaktiviert, aber für die Quell-VM war eine Ressourcensperre vorhanden.
- Sie haben den Site Recovery-Tresor gelöscht, ohne die Replikation auf der VM explizit zu deaktivieren.
- Sie haben die Ressourcengruppe gelöscht, die den Site Recovery-Tresor enthält, ohne die Replikation auf der VM explizit zu deaktivieren.

### <a name="fix-the-problem"></a>Beheben des Problems

> [!NOTE]
> Site Recovery löscht oder beeinträchtigt nicht den virtuellen Quellcomputer, während Sie diese Schritte ausführen.

1. Entfernen Sie die Sperre von der VM oder VM-Ressourcengruppe. In der folgenden Abbildung muss beispielsweise die Ressourcensperre für die VM mit dem Namen `MoveDemo` gelöscht werden:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Entfernen der Sperre von der VM.":::

1. Laden Sie das Skript zum [Entfernen einer veralteten Site Recovery-Konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1) herunter.
1. Führen Sie das Skript _Cleanup-stale-asr-config-Azure-VM.ps1_ aus. Geben Sie **Abonnement-ID**, **VM-Ressourcengruppe** und **Name der VM** als Parameter an.
1. Wenn Sie zur Eingabe von Azure-Anmeldeinformationen aufgefordert werden, geben Sie diese an. Überprüfen Sie dann, ob das Skript ohne Fehler ausgeführt wird.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>VM oder Ressourcengruppe kann in Auftrag zum Aktivieren der Replikation nicht ausgewählt werden

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problem 1: Die Ressourcengruppe und die Quell-VM befinden sich an unterschiedlichen Standorten.

Site Recovery erfordert derzeit, dass sich die Quellressourcengruppe für die Region und der virtuelle Computer am selben Standort befinden müssen. Wenn das nicht der Fall ist, können Sie den virtuellen Computer oder die Ressourcengruppe nicht finden, wenn Sie versuchen, einen Schutz anzuwenden.

Als Problemumgehung können Sie die Replikation über den virtuellen Computer statt aus dem Recovery Services-Tresor aktivieren. Navigieren Sie zu **Quell-VM** > **Eigenschaften** > **Notfallwiederherstellung**, und aktivieren Sie die Replikation.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problem 2: Die Ressourcengruppe ist nicht Teil des ausgewählten Abonnements

Möglicherweise können Sie die Ressourcengruppe zum Zeitpunkt des Schutzes nicht finden, wenn sie nicht Teil des ausgewählten Abonnements ist. Stellen Sie sicher, dass die Ressourcengruppe zum verwendeten Abonnement gehört.

### <a name="issue-3-stale-configuration"></a>Problem 3: Veraltete Konfiguration

Möglicherweise wird der virtuelle Computer, für den Sie die Replikation aktivieren möchten, nicht angezeigt, wenn eine veraltete Site Recovery-Konfiguration auf dem virtuellen Azure-Computer vorhanden ist. Dieser Fall kann eintreten, wenn Sie die Replikation für den virtuellen Azure-Computer mithilfe von Site Recovery aktiviert haben und dann wie folgt vorgegangen sind:

- Sie haben den Site Recovery-Tresor gelöscht, ohne die Replikation auf der VM explizit zu deaktivieren.
- Sie haben die Ressourcengruppe gelöscht, die den Site Recovery-Tresor enthält, ohne die Replikation auf der VM explizit zu deaktivieren.
- Sie haben die Replikation deaktiviert, aber für die Quell-VM war eine Ressourcensperre vorhanden.

### <a name="fix-the-problem"></a>Beheben des Problems

> [!NOTE]
> Aktualisieren Sie unbedingt das Modul `AzureRM.Resources`, bevor Sie das in diesem Abschnitt genannte Skript verwenden. Site Recovery löscht oder beeinträchtigt nicht den virtuellen Quellcomputer, während Sie diese Schritte ausführen.

1. Entfernen Sie ggf. die Sperre von der VM oder VM-Ressourcengruppe. In der folgenden Abbildung muss beispielsweise die Ressourcensperre für die VM mit dem Namen `MoveDemo` gelöscht werden:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Entfernen der Sperre von der VM.":::

1. Laden Sie das Skript zum [Entfernen einer veralteten Site Recovery-Konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1) herunter.
1. Führen Sie das Skript _Cleanup-stale-asr-config-Azure-VM.ps1_ aus. Geben Sie **Abonnement-ID**, **VM-Ressourcengruppe** und **Name der VM** als Parameter an.
1. Wenn Sie zur Eingabe von Azure-Anmeldeinformationen aufgefordert werden, geben Sie diese an. Überprüfen Sie dann, ob das Skript ohne Fehler ausgeführt wird.

## <a name="unable-to-select-a-vm-for-protection"></a>Eine VM für den Schutz kann nicht ausgewählt werden.

### <a name="possible-cause"></a>Mögliche Ursache

Auf dem virtuellen Computer ist eine fehlerhafte oder nicht reagierende Erweiterung installiert.

### <a name="fix-the-problem"></a>Beheben des Problems

Wechseln Sie zu **Virtuelle Computer** > **Einstellung** > **Erweiterungen**, und überprüfen Sie, ob es fehlerhafte Erweiterungen gibt. Deinstallieren Sie alle fehlerhaften Erweiterungen, und versuchen Sie dann erneut, den virtuellen Computer zu schützen.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>VM-Bereitstellungsstatus ist ungültig (Fehlercode 150019)

Um die Replikation auf dem virtuellen Computer zu aktivieren, muss dessen Bereitstellungsstatus **Erfolgreich** lauten. Gehen Sie folgendermaßen vor, um den Bereitstellungsstatus zu überprüfen:

1. Wählen Sie im Azure-Portal unter **Alle Dienste** die Option **Ressourcen-Explorer** aus.
1. Erweitern Sie die Liste **Abonnements**, und wählen Sie Ihr Abonnement aus.
1. Erweitern Sie die Liste **Ressourcengruppen**, und wählen Sie die Ressourcengruppe des virtuellen Computers aus.
1. Erweitern Sie die Liste **Ressourcen**, und wählen Sie den virtuellen Computer aus.
1. Überprüfen Sie das Feld **provisioningState** in der Instanzansicht auf der rechten Seite.

### <a name="fix-the-problem"></a>Beheben des Problems

- Wenn **provisioningState** den Status **Fehler** aufweist, wenden Sie sich mit den Informationen zur Problembehandlung an den Support.
- Wenn **provisioningState** den Status **Wird aktualisiert** aufweist, wird möglicherweise gerade eine andere Erweiterung bereitgestellt. Überprüfen Sie, ob gerade Vorgänge für den virtuellen Computer ausgeführt werden, warten Sie, bis diese beendet sind, und wiederholen Sie dann den fehlerhaften Site Recovery-Auftrag zum Aktivieren der Replikation.

## <a name="unable-to-select-target-vm"></a>Ziel-VM kann nicht ausgewählt werden.

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problem 1: Die VM ist mit einem Netzwerk verbunden, das bereits einem Zielnetzwerk zugeordnet ist.

Wenn die Quell-VM während der Konfiguration für die Notfallwiederherstellung Teil eines virtuellen Netzwerks ist und eine andere VM aus demselben virtuellen Netzwerk bereits einem Netzwerk in der Zielressourcengruppe zugeordnet wurde, steht das Dropdown-Listenfeld für die Netzwerkauswahl standardmäßig nicht zur Verfügung (wird abgeblendet angezeigt).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Netzwerkauswahlliste nicht verfügbar.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problem 2: Sie haben die VM zuvor geschützt und dann die Replikation deaktiviert.

Durch das Deaktivieren der Replikation eines virtuellen Computers wird die Netzwerkzuordnung nicht gelöscht. Die Zuordnung muss in dem Recovery Services-Tresor gelöscht werden, in dem der virtuelle Computer geschützt wurde. Wählen Sie den **Recovery Services-Tresor** aus, und wechseln Sie zu **Verwalten** > **Site Recovery-Infrastruktur** > **Für virtuelle Azure-Computer** > **Netzwerkzuordnung**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Löschen der Netzwerkzuordnung.":::

Das während der Notfallwiederherstellungseinrichtung konfigurierte Zielnetzwerk kann nach dem erstmaligen Einrichten und dem Schützen der VM geändert werden. Wählen Sie zum **Ändern der Netzwerkzuordnung** den Netzwerknamen aus:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Ändern der Netzwerkzuordnung.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ oder VSS (Fehlercode 151025)

Wenn der Fehler im Zusammenhang mit COM+ oder dem Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS) auftritt, wird folgende Meldung angezeigt:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Mögliche Ursachen

- Der COM+-Systemanwendungsdienst ist deaktiviert.
- Der Volumeschattenkopie-Dienst ist deaktiviert.

### <a name="fix-the-problem"></a>Beheben des Problems

Legen Sie für die COM+-Systemanwendung und den Volumeschattenkopie-Dienst den automatischen oder manuellen Startmodus fest.

1. Öffnen Sie die Konsole „Dienste“ unter Windows.
1. Vergewissern Sie sich, dass für die COM+-Systemanwendung und den Volumeschattenkopie-Dienst die Option **Starttyp** nicht auf **Deaktiviert** festgelegt ist.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Überprüfen des Starttyps der COM+-Systemanwendung und des Volumeschattenkopie-Diensts.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nicht unterstützte Größe verwalteter Datenträger (Fehlercode 150172)

Wenn dieser Fehler auftritt, wird die folgende Meldung angezeigt:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Mögliche Ursache

Der Datenträger ist kleiner als die unterstützte Größe von 1024 MB.

### <a name="fix-the-problem"></a>Beheben des Problems

Stellen Sie sicher, dass die Datenträgergröße innerhalb des unterstützten Größenbereichs liegt, und wiederholen Sie dann den Vorgang.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Schutz nicht aktiviert, wenn GRUB Gerätename verwendet (Fehlercode 151126)

### <a name="possible-causes"></a>Mögliche Ursachen

Die GRUB-Konfigurationsdateien von Linux (Grand Unified Bootloader, _/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ oder _/etc/default/grub_) geben ggf. die tatsächlichen Gerätenamen anstelle der UUID (universell eindeutiger Bezeichner) für die Parameter `root` und `resume` an. Site Recovery erfordert UUIDs, da sich Gerätenamen ändern können. Beim Neustart wird ein virtueller Computer möglicherweise nicht mit demselben Namen im Failover angezeigt, was zu Problemen führt.

Die folgenden Beispiele sind Zeilen aus GRUB-Dateien, bei denen Gerätenamen anstelle der erforderlichen UUIDs angezeigt werden:

- Datei _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Datei _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Beheben des Problems

Ersetzen Sie jeden Gerätenamen durch die entsprechende UUID:

1. Ermitteln Sie die UUID des Geräts durch Ausführen des Befehls `blkid <device name>`. Beispiel:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ersetzen Sie den Gerätenamen durch die UUID in den Formaten `root=UUID=<UUID>` und `resume=UUID=<UUID>`. Nach der Ersetzung würde z. B. die Zeile in _boot/grub/menu.lst_ wie die folgende Zeile aussehen:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Wiederholen Sie den Schutzvorgang.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>Fehler beim Schutz, weil GRUB-Gerät nicht vorhanden ist (Fehlercode 151124)

### <a name="possible-cause"></a>Mögliche Ursache

Die GRUB-Konfigurationsdateien ( _/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ oder _/etc/default/grub_) enthalten möglicherweise die Parameter `rd.lvm.lv` oder `rd_LVM_LV`. Mit diesen Parametern werden die LVM-Geräte (Logical Volume Manager) angegeben, die zum Startzeitpunkt erkannt werden sollen. Wenn diese LVM-Geräte nicht vorhanden sind, wird das geschützte System selbst nicht gestartet und bleibt im Startprozess hängen. Dasselbe Problem tritt auch bei der Failover-VM auf. Hier sind einige Beispiele angegeben:

- Datei _/boot/grub2/grub.cfg_ für RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Datei _/etc/default/grub_ für RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Datei _/boot/grub/menu.lst_ für RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

Bei jedem Beispiel muss GRUB zwei LVM-Geräte mit den Namen `root` und `swap` in der Volumegruppe `rootvg` erkennen.

### <a name="fix-the-problem"></a>Beheben des Problems

Wenn das LVM-Gerät nicht vorhanden ist, erstellen Sie es, oder entfernen Sie die entsprechenden Parameter aus den GRUB-Konfigurationsdateien. Versuchen Sie dann erneut, den Schutz zu aktivieren.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Update des Mobilitätsdiensts wurde mit Warnungen beendet (Fehlercode 151083)

Der Site Recovery-Mobilitätsdienst hat viele Komponenten, von denen einer der Filtertreiber ist. Der Filtertreiber wird nur bei einem Systemneustart in den Systemspeicher geladen. Wenn ein Update des Mobilitätsdiensts Änderungen des Filtertreibers enthält, wird der Computer aktualisiert, doch wird weiterhin eine Warnung angezeigt, dass für einige Korrekturen ein Neustart erforderlich ist. Die Warnung wird angezeigt, weil die Korrekturen des Filtertreibers nur wirksam werden können, wenn der neue Filtertreiber geladen wird. Dies geschieht nur während eines Neustarts.

> [!NOTE]
> Dies ist nur eine Warnung. Die bestehende Replikation funktioniert auch nach dem neuen Agent-Update. Sie können jederzeit einen Neustart durchführen, um die Vorteile des neuen Filtertreibers zu nutzen. Wenn Sie keinen Neustart durchführen, funktioniert der alte Filtertreiber jedoch weiterhin.
>
> Abgesehen vom Filtertreiber werden die Vorteile aller anderen Erweiterungen und Korrekturen im Update des Mobilitätsdiensts wirksam, ohne dass ein Neustart erforderlich ist.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Schutz nicht aktiviert, wenn verwalteter Replikatdatenträger vorhanden ist

Dieser Fehler tritt auf, wenn der verwaltete Replikatdatenträger (ohne erwartete Tags) in der Zielressourcengruppe bereits vorhanden ist.

### <a name="possible-cause"></a>Mögliche Ursache

Dieses Problem kann auftreten, wenn der virtuelle Computer zuvor geschützt war und der Replikatdatenträger beim Deaktivieren der Replikation nicht entfernt wurde.

### <a name="fix-the-problem"></a>Beheben des Problems

Löschen Sie den in der Fehlermeldung angegebenen Replikatdatenträger, und wiederholen Sie den fehlerhaften Schutzauftrag.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Fehler beim Aktivieren des Schutzes, weil das Installationsprogramm den Stammdatenträger nicht finden kann (Fehlercode 151137)

Dieser Fehler tritt bei Linux-Computern auf, bei denen der Betriebssystemdatenträger mit Azure Disk Encryption (ADE) verschlüsselt ist. Dieses Problem trifft nur auf Agent-Version 9.35 zu.

### <a name="possible-causes"></a>Mögliche Ursachen

Der Stammdatenträger, der das Stammdateisystem hostet, kann vom Installationsprogramm nicht gefunden werden.

### <a name="fix-the-problem"></a>Beheben des Problems

Führen Sie die unten angegebenen Schritte aus, um dieses Problem zu beheben.

1. Suchen Sie auf RHEL- und CentOS-Computern die Agent-Datei unter dem Verzeichnis _/var/lib/waagent_ mit folgendem Befehl: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Erwartete Ausgabe:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Erstellen Sie ein neues Verzeichnis, und ändern Sie das Verzeichnis in dieses neue Verzeichnis.
3. Extrahieren Sie hier die im ersten Schritt gefundene Agent-Datei mit dem folgenden Befehl:

    `tar -xf <Tar Ball File>`

4. Öffnen Sie die Datei _prereq_check_installer.json_, und löschen Sie die folgenden Zeilen. Speichern Sie anschließend die Datei.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Rufen Sie das Installationsprogramm mit folgendem Befehl auf: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Wenn das Installationsprogramm erfolgreich ausgeführt wird, wiederholen Sie den Vorgang zum Aktivieren der Replikation.

## <a name="next-steps"></a>Nächste Schritte

[Replizieren von Azure-VMs in eine andere Azure-Region](azure-to-azure-how-to-enable-replication.md)