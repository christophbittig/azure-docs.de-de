---
title: Voraussetzungen für Azure Disk Encryption mit Azure AD-App (vorheriges Release)
description: Dieser Artikel ergänzt Azure Disk Encryption für Linux-VMs um zusätzliche Anforderungen und Voraussetzungen für Azure Disk Encryption mit Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 742e96d07a059c933e6293912c364a230b0ddb27
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695471"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption mit Azure Active Directory (AD) (vorherige Version)

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Beim neuen Release von Azure Disk Encryption muss kein Azure AD-Anwendungsparameter (Azure Active Directory) mehr angegeben werden, um die VM-Datenträgerverschlüsselung zu aktivieren. Sie müssen beim neuen Release während des Schritts zum Aktivieren der Verschlüsselung keine Azure AD-Anmeldeinformationen mehr angeben. Alle neuen virtuellen Computer müssen mit dem neuen Release und ohne die Azure AD-Anwendungsparameter verschlüsselt werden. Eine Anleitung zum Aktivieren der VM-Datenträgerverschlüsselung mit dem neuen Release finden Sie unter [Azure Disk Encryption für Linux-VMs](disk-encryption-overview.md). Virtuelle Computer, die bereits mit Azure AD-Anwendungsparametern verschlüsselt wurden, werden weiterhin unterstützt und sollten weiterhin mit der AAD-Syntax gepflegt werden.

Dieser Artikel ergänzt [Azure Disk Encryption für Linux-VMs](disk-encryption-overview.md) um zusätzliche Anforderungen und Voraussetzungen für Azure Disk Encryption mit Azure AD (vorheriges Release).

Die Angaben in folgenden Abschnitten bleiben unverändert:

- [Unterstützte VMs und Betriebssysteme](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Weitere VM-Anforderungen](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Netzwerk und Gruppenrichtlinie

Virtuelle IaaS-Computer (Infrastructure-as-a-Service) müssen die folgenden Anforderungen für die Netzwerkendpunktkonfiguration erfüllen, damit das Azure Disk Encryption-Feature mit der älteren AAD-Parametersyntax aktiviert werden kann: 
  - Um ein Token für die Verbindungsherstellung mit Ihrem Schlüsseltresor zu erhalten, muss der virtuelle IaaS-Computer eine Verbindung mit dem Azure AD-Endpunkt \[login.microsoftonline.com\] herstellen können.
  - Um die Verschlüsselungsschlüssel in Ihren Schlüsseltresor schreiben zu können, muss der virtuelle IaaS-Computer eine Verbindung mit dem Schlüsseltresorendpunkt herstellen können.
  - Der virtuelle IaaS-Computer muss eine Verbindung mit dem Azure-Speicherendpunkt herstellen können, an dem das Azure-Erweiterungsrepository gehostet wird, sowie mit einem Azure Storage-Konto, das die VHD-Dateien hostet.
  -  Wenn Ihre Sicherheitsrichtlinie den Zugriff von virtuellen Azure-Computern auf das Internet beschränkt, können Sie den obigen URI auflösen und eine spezielle Regel konfigurieren, um ausgehende Verbindungen mit den IP-Adressen zuzulassen. Weitere Informationen finden Sie unter [Zugreifen auf Azure Key Vault hinter einer Firewall](../../key-vault/general/access-behind-firewall.md).
  - Wenn TLS 1.0 unter Windows explizit deaktiviert und die .NET-Version nicht mindestens auf 4.6 aktualisiert wurde, sorgt die folgende Registrierungsänderung dafür, dass Azure Disk Encryption die neuere TLS-Version verwenden kann:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Gruppenrichtlinie
 - Die Azure Disk Encryption-Lösung verwendet für virtuelle Windows-IaaS-Computer die externe BitLocker-Schlüsselschutzvorrichtung. Pushen Sie für in eine Domäne eingebundene virtuelle Computer keine Gruppenrichtlinien, die TPM-Schutzvorrichtungen erzwingen. Informationen zur Gruppenrichtlinie für die Option **BitLocker ohne kompatibles TPM zulassen** finden Sie in der [Referenz zur BitLocker-Gruppenrichtlinie](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Die BitLocker-Richtlinie für in eine Domäne eingebundene virtuelle Computer mit benutzerdefinierter Gruppenrichtlinie muss die folgende Einstellung enthalten: [Speichern von BitLocker-Wiederherstellungsinformationen durch Benutzer konfigurieren -> 256-Bit-Wiederherstellungsschlüssel zulassen](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Wenn Einstellungen für die benutzerdefinierte Gruppenrichtlinie nicht mit BitLocker kompatibel sind, tritt für Azure Disk Encryption ein Fehler auf. Auf Computern ohne korrekte Richtlinieneinstellung müssen Sie die neue Richtlinie anwenden und die Aktualisierung der neuen Richtlinie erzwingen (gpupdate.exe /force). Danach ist möglicherweise ein Neustart erforderlich. 

## <a name="encryption-key-storage-requirements"></a>Speicheranforderungen für Verschlüsselungsschlüssel 

Azure Disk Encryption benötigt Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung. Ihr Schlüsseltresor und die VMs müssen sich in derselben Azure-Region und im selben Azure-Abonnement befinden.

Weitere Informationen finden Sie unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-key-vault-aad.md)
- [Aktivieren von Azure Disk Encryption mit Azure AD auf Linux-VMs (vorheriges Release)](disk-encryption-linux-aad.md)
- [CLI-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
