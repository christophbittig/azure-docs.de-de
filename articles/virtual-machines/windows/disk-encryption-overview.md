---
title: Aktivieren von Azure Disk Encryption für virtuelle Windows-Computer
description: Dieser Artikel enthält eine Anleitung zum Aktivieren von Microsoft Azure Disk Encryption für virtuelle Windows-Computer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1203b44225be723fa453dac8333e2ea6e7f29d33
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318178"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption für virtuelle Windows-Computer

**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Azure Disk Encryption unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen Ihrer Organisation. Der Dienst stellt mithilfe des Windows-Features [BitLocker](https://en.wikipedia.org/wiki/BitLocker) Volumeverschlüsselung für das Betriebssystem und die Datenträger von virtuellen Azure-Computern (virtual machines, VMs) bereit und ist in [Azure Key Vault](../../key-vault/index.yml) integriert, um die Steuerung und Verwaltung von Verschlüsselungsschlüsseln und Geheimnissen für Datenträger zu ermöglichen.

Azure Disk Encryption ist genau wie Virtual Machines zonenresilient. Ausführliche Informationen finden Sie unter [Azure-Dienste mit Unterstützung für Verfügbarkeitszonen](../../availability-zones/az-region.md).

Wenn Sie [Microsoft Defender für Cloud](../../security-center/index.yml) verwenden, werden Sie benachrichtigt, wenn Sie nicht-verschlüsselte virtuelle Computer haben. Die Warnungen werden als „Hoher Schweregrad“ angezeigt. Empfohlen wird in diesem Fall die Verschlüsselung der VMs.

![Microsoft Defender für Cloud: Warnung zur Datenträgerverschlüsselung](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Wenn Sie zuvor Azure Disk Encryption mit Azure AD zum Verschlüsseln eines virtuellen Computers verwendet haben, müssen Sie diese Option auch weiterhin zum Verschlüsseln Ihres virtuellen Computers verwenden. Weitere Informationen finden Sie unter [Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-overview-aad.md). 
> - Einige Empfehlungen führen möglicherweise zu einer erhöhten Daten-, Netzwerk- oder Computeressourcenauslastung, was zusätzliche Lizenz- oder Abonnementkosten nach sich ziehen kann. Sie müssen über ein gültiges aktives Azure-Abonnement verfügen, um in den unterstützten Regionen Ressourcen in Azure zu erstellen.

Anhand der Schnellstartanleitungen [Erstellen und Verschlüsseln eines virtuellen Windows-Computers mit der Azure-Befehlszeilenschnittstelle](disk-encryption-cli-quickstart.md) und [Erstellen und Verschlüsseln eines virtuellen Windows-Computers mit Azure PowerShell](disk-encryption-powershell-quickstart.md) können Sie sich innerhalb weniger Minuten mit den Grundlagen von Azure Disk Encryption für Windows vertraut machen.

## <a name="supported-vms-and-operating-systems"></a>Unterstützte VMs und Betriebssysteme

### <a name="supported-vms"></a>Unterstützte VMs

Windows-VMs sind in [verschiedenen Größen](../sizes-general.md) verfügbar. Azure Disk Encryption wird für virtuelle Computer der 1. und 2. Generation unterstützt. Azure Disk Encryption ist auch für virtuelle Computer mit Storage Premium verfügbar.

Azure Disk Encryption ist nicht verfügbar auf [Basic-VMs der A-Serie](https://azure.microsoft.com/pricing/details/virtual-machines/series/) und auf virtuellen Computern mit weniger als 2 GB Arbeitsspeicher.  Weitere Ausnahmen finden Sie unter [Azure Disk Encryption: Nicht unterstützte Szenarien](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

- Windows-Client: Windows 8 und höher.
- Windows-Server: Windows Server 2008 R2 und höher.
- Windows 10 Enterprise (mehrere Sitzungen)  
 
> [!NOTE]
> Für Windows Server 2008 R2 muss .NET Framework 4.5 für die Verschlüsselung installiert sein. Sie können die Installation über Windows Update mit dem optionalen Update Microsoft .NET Framework 4.5.2 für Windows Server 2008 R2-basierte Systeme (x64) durchführen ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Für Windows Server 2012 R2 Core und Windows Server 2016 Core muss die bdehdcfg-Komponente für die Verschlüsselung auf dem virtuellen Computer installiert sein.


## <a name="networking-requirements"></a>Netzwerkanforderungen
Die VMs müssen die folgenden Konfigurationsanforderungen an den Netzwerkendpunkt erfüllen, damit Azure Disk Encryption aktiviert werden kann:
  - Um ein Token für die Verbindungsherstellung mit Ihrem Schlüsseltresor zu erhalten, muss der virtuelle Windows-Computer eine Verbindung mit dem Azure Active Directory-Endpunkt \[login.microsoftonline.com\] herstellen können.
  - Um die Verschlüsselungsschlüssel in Ihren Schlüsseltresor schreiben zu können, muss der virtuelle Windows-Computer eine Verbindung mit dem Schlüsseltresorendpunkt herstellen können.
  - Die Windows-VM muss eine Verbindung mit dem Azure Storage-Endpunkt herstellen können, an dem das Azure-Erweiterungsrepository gehostet wird, sowie mit einem Azure Storage-Konto, das die VHD-Dateien hostet.
  -  Wenn Ihre Sicherheitsrichtlinie den Zugriff von virtuellen Azure-Computern auf das Internet beschränkt, können Sie den obigen URI auflösen und eine spezielle Regel konfigurieren, um ausgehende Verbindungen mit den IP-Adressen zuzulassen. Weitere Informationen finden Sie unter [Zugreifen auf Azure Key Vault hinter einer Firewall](../../key-vault/general/access-behind-firewall.md).    

## <a name="group-policy-requirements"></a>Gruppenrichtlinienanforderungen

Azure Disk Encryption verwendet für virtuelle Windows-Computer die externe Schlüsselschutzvorrichtung BitLocker. Für VMs, die der Domäne beigetreten sind, sollten Sie keine Gruppenrichtlinien nutzen, mit denen TPM-Schutzvorrichtungen durchgesetzt werden. Informationen zur Gruppenrichtlinie „BitLocker ohne kompatibles TPM zulassen“ finden Sie unter [BitLocker Group Policy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1) (Referenz zur BitLocker-Gruppenrichtlinie).

Die BitLocker-Richtlinie für VMs mit Domänenbeitritt und benutzerdefinierten Gruppenrichtlinien muss die folgende Einstellung enthalten: [Speichern von BitLocker-Wiederherstellungsinformationen durch Benutzer konfigurieren -> 256-Bit-Wiederherstellungsschlüssel zulassen](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Bei Azure Disk Encryption tritt ein Fehler auf, wenn benutzerdefinierte Einstellungen für die Gruppenrichtlinie nicht mit BitLocker kompatibel sind. Auf Computern ohne korrekte Richtlinieneinstellung müssen Sie die neue Richtlinie anwenden und die Aktualisierung der neuen Richtlinie erzwingen (gpupdate.exe /force).  Danach ist möglicherweise ein Neustart erforderlich.

MBAM-Gruppenrichtlinienfeatures (Microsoft Bitlocker Administration and Monitoring) sind nicht mit Azure Disk Encryption kompatibel.

> [!WARNING]
> Azure Disk Encryption **speichert keine Wiederherstellungsschlüssel**. Wenn die Sicherheitseinstellung [Interaktive Anmeldung: Schwellenwert für Computerkontosperrung](/windows/security/threat-protection/security-policy-settings/interactive-logon-machine-account-lockout-threshold) aktiviert ist, können Computer nur wiederhergestellt werden, indem ein Wiederherstellungsschlüssel über die serielle Konsole bereitgestellt wird. Anweisungen, wie Sie sicherstellen, dass die entsprechenden Richtlinien für die Wiederherstellung aktiviert sind, finden Sie im [Leitfaden zur Bitlocker-Wiederherstellung](/windows/security/information-protection/bitlocker/bitlocker-recovery-guide-plan).

Azure Disk Encryption schlägt fehl, wenn die Gruppenrichtlinie auf Domänenebene den von BitLocker verwendeten AES-CBC-Algorithmus blockiert.

## <a name="encryption-key-storage-requirements"></a>Speicheranforderungen für Verschlüsselungsschlüssel  

Azure Disk Encryption erfordert Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung. Ihr Schlüsseltresor und die VMs müssen sich in derselben Azure-Region und im selben Azure-Abonnement befinden.

Ausführliche Informationen finden Sie unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Begriff
In der folgenden Tabelle werden einige der häufig in der Azure Disk Encryption-Dokumentation verwendeten Begriffe beschrieben:

| Begriff | Definition |
| --- | --- |
| Azure-Schlüsseltresor | Key Vault ist ein Dienst zum Verwalten kryptografischer Schlüssel, der auf Hardwaresicherheitsmodulen mit FIPS-Überprüfung (Federal Information Processing Standards) basiert. Diese Standards tragen dazu bei, Ihre kryptografischen Schlüssel und vertraulichen Geheimnisse zu schützen. Weitere Informationen finden Sie in der Dokumentation zu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) und unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) ist für die Verwaltung von Azure-Ressourcen über die Befehlszeile optimiert.|
| BitLocker |Bei [BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) handelt es sich um eine branchenübliche Verschlüsselungstechnologie für Windows-Volumes, die zum Aktivieren der Datenträgerverschlüsselung auf virtuellen Windows-Computern verwendet wird. |
| Schlüsselverschlüsselungsschlüssel (Key encryption key, KEK) | Der asymmetrische Schlüssel (RSA 2048), der zum Schützen oder Umschließen des Geheimnisses verwendet wird. Sie können einen mit Hardwaresicherheitsmodulen geschützten Schlüssel oder einen Schlüssel mit Softwareschutz bereitstellen. Weitere Informationen finden Sie in der Dokumentation zu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) und unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-Cmdlets | Weitere Informationen finden Sie unter [Azure PowerShell-Cmdlets](/powershell/azure/). |

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen und Verschlüsseln eines virtuellen Windows-Computers mit der Azure-Befehlszeilenschnittstelle](disk-encryption-cli-quickstart.md)
- [Schnellstart: Erstellen und Verschlüsseln eines virtuellen Windows-Computers mit Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-Szenarien auf virtuellen Windows-Computern](disk-encryption-windows.md)
- [CLI-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) 
- [PowerShell-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md)
