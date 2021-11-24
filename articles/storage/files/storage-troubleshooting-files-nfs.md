---
title: Behandeln von Problemen mit Azure NFS-Dateifreigaben – Azure Files
description: Behandeln von Problemen mit Azure NFS-Dateifreigaben
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: fe9450310e3f8774b31557fd7c045f05ed9b56b1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518354"
---
# <a name="troubleshoot-azure-nfs-file-share-problems"></a>Behandeln von Problemen mit Azure NFS-Dateifreigaben

Dieser Artikel listet einige häufige Probleme und bekannte Probleme im Zusammenhang mit Azure NFS-Dateifreigaben auf. Er enthält mögliche Ursachen und Problemumgehungen, wenn diese Probleme auftreten.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Nein](../media/icons/no-icon.png) | ![Ja](../media/icons/yes-icon.png) |

## <a name="chgrp-filename-failed-invalid-argument-22"></a>Fehler bei chgrp "Dateiname": Ungültiges Argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Ursache 1: idmapping ist nicht deaktiviert.
Azure Files lässt keine alphanumerische UID/GID zu. Daher muss idmapping deaktiviert sein. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Ursache 2: idmapping wurde deaktiviert, wurde jedoch wieder aktiviert, nachdem ein ungültiger Datei-/Verzeichnisname gefunden wurde.
Auch wenn idmapping ordnungsgemäß deaktiviert wurde, werden die Einstellungen zum Deaktivieren von idmapping in einigen Fällen überschrieben. Wenn Azure Files beispielsweise auf einen ungültigen Dateinamen stößt, wird ein Fehler zurückgegeben. Wenn dieser bestimmte Fehlercode erkannt wird, wird idmapping erneut durch den NFS v4.1-Linux-Client aktiviert, und die zukünftigen Anforderungen werden wieder mit alphanumerischer UID/GID gesendet. Eine Liste der bei Azure Files nicht unterstützten Zeichen finden Sie in diesem [Artikel](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Der Doppelpunkt ist eines der nicht unterstützten Zeichen. 

### <a name="workaround"></a>Problemumgehung
Überprüfen Sie, ob idmapping deaktiviert ist und nicht erneut aktiviert wird. Führen Sie dann Folgendes aus:

- Heben Sie die Einbindung der Freigabe auf.
- Deaktivieren Sie id-mapping mit # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping
- Binden Sie die Freigabe wieder ein
- Wenn Sie rsync ausführen, führen Sie rsync mit dem Argument „–numeric-ids“ aus einem Verzeichnis aus, das keinen ungültigen Verzeichnis-/Dateinamen hat.

## <a name="unable-to-create-an-nfs-share"></a>Es kann keine NFS-Freigabe erstellt werden.

### <a name="cause-1-unsupported-storage-account-settings"></a>Ursache 1: Nicht unterstützte Speicherkontoeinstellungen

NFS ist nur für Speicherkonten mit der folgenden Konfiguration verfügbar:

- Ebene – Premium
- Kontoart – FileStorage
- Regionen – [Liste der unterstützten Regionen](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Lösung

Befolgen Sie die Anweisungen in unserem Artikel: [Erstellen einer NFS-Freigabe](storage-files-how-to-create-nfs-shares.md)

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Es ist nicht möglich, eine Verbindung mit einer Azure NFS-Dateifreigabe herzustellen bzw. diese einzubinden.

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Ursache 1: Eine Anforderung stammt von einem Client in einem nicht vertrauenswürdigen Netzwerk/mit einer nicht vertrauenswürdigen IP-Adresse.

NFS unterstützt im Gegensatz zu SMB keine benutzerbasierte Authentifizierung. Die Authentifizierung für eine Freigabe basiert auf der Konfiguration Ihrer Netzwerksicherheitsregeln. Aus diesem Grund müssen Sie entweder den Dienstendpunkt oder private Endpunkte verwenden, um sicherzustellen, dass nur sichere Verbindungen mit der NFS-Freigabe hergestellt werden. Wenn Sie lokal auf Freigaben zugreifen möchten, müssen Sie zusätzlich zu privaten Endpunkten ein VPN oder ExpressRoute einrichten. IP-Adressen, die der Firewall-Positivliste des Speicherkontos hinzugefügt wurden, werden ignoriert. Sie müssen eine der folgenden Methoden verwenden, um den Zugriff auf eine NFS-Freigabe einzurichten:


- [Dienstendpunkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Zugriff über den öffentlichen Endpunkt
    - Nur in derselben Region verfügbar
    - VNET-Peering unterstützt keinen Zugriff auf die Freigabe.
    - Jedes virtuelle Netzwerk oder Subnetz muss der Positivliste einzeln hinzugefügt werden.
    - Für den lokalen Zugriff können Dienstendpunkte mit ExpressRoute, Point-to-Site- und Site-to-Site-VPNs verwendet werden, es wird jedoch empfohlen, einen privaten Endpunkt zu verwenden, da dieser sicherer ist.

Im folgenden Diagramm wird die Konnektivität bei Verwendung öffentlicher Endpunkte dargestellt.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagramm der Verbindungen über öffentliche Endpunkte" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Privater Endpunkt](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Der Zugriff ist sicherer als der über den Dienstendpunkt.
    - Der Zugriff auf die NFS-Freigabe über Private Link ist innerhalb und außerhalb der Azure-Region des Speicherkontos verfügbar (regionsübergreifend, lokal).
    - Das Peering virtueller Netzwerke mit virtuellen Netzwerken, die im privaten Endpunkt gehostet werden, gewährt den Clients in virtuellen Netzwerken mit Peering Zugriff auf NFS-Freigaben.
    - Private Endpunkte können mit ExpressRoute, Point-to-Site- und Site-to-Site-VPNs verwendet werden.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagramm der Verbindungen über private Endpunkte" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Ursache 2: „Sichere Übertragung erforderlich“ ist aktiviert.

Die doppelte Verschlüsselung wird für NFS-Freigaben noch nicht unterstützt. Azure bietet mit MACSec eine Verschlüsselungsschicht für alle Daten, die zwischen Azure-Rechenzentren übertragen werden. Auf NFS-Freigaben kann nur von vertrauenswürdigen virtuellen Netzwerken und über VPN-Tunnel zugegriffen werden. Auf NFS-Freigaben ist keine zusätzliche Verschlüsselung der Transportschicht verfügbar.

#### <a name="solution"></a>Lösung

Deaktivieren Sie auf dem Blatt „Konfiguration“ des Speicherkontos die Option „Sichere Übertragung erforderlich“.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="Screenshot mit dem Blatt „Konfiguration“ des Speicherkontos und der deaktivierten Option „Sichere Übertragung erforderlich“":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Ursache 3: Das Paket „nfs-common“ ist nicht installiert.
Installieren Sie das Paket, bevor Sie den mount-Befehl ausführen, indem Sie den unten angegebenen distributionsspezifischen Befehl ausführen.

Führen Sie `rpm qa | grep nfs-utils` aus, um zu überprüfen, ob das NFS-Paket installiert ist.

#### <a name="solution"></a>Lösung

Wenn das Paket nicht installiert ist, installieren Sie das Paket in Ihrer Distribution.

##### <a name="ubuntu-or-debian"></a>Ubuntu oder Debian

```
sudo apt update
sudo apt install nfs-common
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8+, CentOS 8+

Verwenden Sie den DNF-Paket-Manager `sudo dnf install nfs-utils`.

Verwenden Sie unter älteren Versionen von Red Hat Enterprise Linux und CentOS den YUM-Paket-Manager `sudo yum install nfs-common`.

##### <a name="opensuse"></a>openSUSE

Verwenden Sie den Zypper-Paket-Manager `sudo zypper install-nfscommon`.

### <a name="cause-4-firewall-blocking-port-2049"></a>Ursache 4: Die Firewall blockiert Port 2049.

Das NFS-Protokoll kommuniziert über Port 2049 mit dem Server. Stellen Sie sicher, dass dieser Port für das Speicherkonto (den NFS-Server) geöffnet ist.

#### <a name="solution"></a>Lösung

Überprüfen Sie, ob Port 2049 auf dem Client geöffnet ist, indem Sie den folgenden Befehl ausführen: `telnet <storageaccountnamehere>.file.core.windows.net 2049`. Wenn der Port nicht geöffnet ist, öffnen Sie ihn.

## <a name="ls-hangs-for-large-directory-enumeration-on-some-kernels"></a>ls hängt bei umfangreichen Verzeichnisenumerationen in einigen Kernels

### <a name="cause-a-bug-was-introduced-in-linux-kernel-v511-and-was-fixed-in-v5125"></a>Ursache: Im Linux-Kernel v5.11 wurde ein Fehler eingeführt, der in v5.12.5 behoben wurde.  
Einige Kernelversionen weisen einen Fehler auf, der dazu führt, dass Verzeichnisenumerationen zu endlosen READDIR-Sequenzen führen. Bei sehr kleinen Verzeichnissen, in denen alle Einträge in einem Aufruf übermittelt werden können, tritt das Problem nicht auf.
Der Fehler wurde im Linux-Kernel v5.11 eingeführt und in v5.12.5 behoben. Alle Versionen dazwischen weisen also den Fehler auf. RHEL 8.4 verfügt bekanntermaßen über diese Kernelversion.

#### <a name="workaround-downgrading-or-upgrading-the-kernel"></a>Problemumgehung: Führen Sie ein Downgrade oder Upgrade des Kernels aus.
Durch das Downgrade oder Upgraden des Kernels auf eine beliebige Version außerhalb der betroffenen Kernelversionen wird das Problem behoben.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.
