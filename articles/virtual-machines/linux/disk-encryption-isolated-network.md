---
title: Azure Disk Encryption in einem isolierten Netzwerk
description: In diesem Artikel finden Sie Tipps zur Problembehandlung für Microsoft Azure Disk Encryption auf Linux VMs.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 05/15/2021
ms.custom: seodec18
ms.openlocfilehash: fac7da74ee31f1bd775daa5446f4dec9b9bc2b79
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695657"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption in einem isolierten Netzwerk

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn die Konnektivität durch eine Firewall, eine Proxyanforderung oder Einstellungen für Netzwerksicherheitsgruppen (NSGs) eingeschränkt ist, kann die Fähigkeit der Erweiterung zur Durchführung von erforderlichen Aufgaben beeinträchtigt sein. Dies kann zu Statusmeldungen der Art „Erweiterungsstatus auf der VM nicht verfügbar“.

## <a name="package-management"></a>Paketverwaltung

Azure Disk Encryption hängt von einer Reihe von Komponenten ab, die in der Regel als Teil der ADE-Aktivierung installiert werden, sofern sie nicht bereits vorhanden sind. Wenn die Ausführung hinter einer Firewall erfolgt oder anderweitig vom Internet isoliert ist, müssen diese Pakete vorab installiert werden oder lokal verfügbar sein.

Dies sind die Pakete, die für die einzelnen Distributionen erforderlich sind. Eine vollständige Liste der unterstützten Distributionen und Volumetypen finden Sie unter [Unterstützte VMs und Betriebssysteme](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps, grub-pc-bin
- **CentOS 7.2 bis 7.9, 8.1, 8.2**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 bis 7.9, 8.1, 8.2**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Wenn unter Red Hat ein Proxy erforderlich ist, müssen Sie sicherstellen, dass die richtige Einrichtung von subscription-manager und yum sichergestellt ist. Weitere Informationen finden Sie unter [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Beheben von Problemen mit subscription-manager und yum).  

Wenn Pakete manuell installiert werden, müssen sie auch manuell aktualisiert werden, wenn neue Versionen veröffentlicht werden.

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen
Für alle angewendeten Einstellungen von Netzwerksicherheitsgruppen muss es ermöglicht werden, dass der Endpunkt die dokumentierten Voraussetzungen für die Netzwerkkonfiguration in Bezug auf die Datenträgerverschlüsselung erfüllt.  Weitere Informationen finden Sie unter [Azure Disk Encryption: Netzwerkanforderungen](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption mit Azure AD (vorherige Version)

Wenn [Azure Disk Encryption mit Azure AD (vorherige Version)](disk-encryption-overview-aad.md) verwendet wird, muss die [Azure Active Directory-Bibliothek](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) für alle Distributionen manuell installiert werden (zusätzlich zu den Paketen, die für die Distribution geeignet sind, wie [oben aufgelistet](#package-management)).

Wenn die Verschlüsselung mit [Azure AD-Anmeldeinformationen](disk-encryption-linux-aad.md) aktiviert wird, muss der virtuelle Zielcomputer die Konnektivität sowohl mit Azure Active Directory-Endpunkten als auch mit Schlüsseltresor-Endpunkten zulassen. Aktuelle Azure Active Directory-Authentifizierungsendpunkte werden in den Abschnitten 56 und 59 der Dokumentation zu [URLs und IP-Adressbereichen in Microsoft 365](/microsoft-365/enterprise/urls-and-ip-address-ranges) verwaltet. Anweisungen zu Schlüsseltresoren werden in der Dokumentation [Zugreifen auf Azure Key Vault hinter einer Firewall](../../key-vault/general/access-behind-firewall.md) bereitgestellt.

### <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst 

Der virtuelle Computer muss Zugriff auf den [Azure-Instanzmetadatendienst](instance-metadata-service.md)-Endpunkt haben, der eine bekannte nicht routingfähige IP-Adresse (`169.254.169.254`) verwendet, auf die nur vom virtuellen Computer aus zugegriffen werden kann.  Proxykonfigurationen, die den lokalen HTTP-Datenverkehr an diese Adresse ändern (z. B. durch Hinzufügen eines X-Forwarded-For-Headers), werden nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Schritte finden Sie unter [Azure Disk Encryption-Problembehandlung](disk-encryption-troubleshooting.md)
- [Datenverschlüsselung ruhender Azure-Daten](../../security/fundamentals/encryption-atrest.md)
