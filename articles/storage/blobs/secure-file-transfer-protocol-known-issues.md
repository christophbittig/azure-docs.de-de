---
title: Bekannte Probleme mit SFTP in Azure Blob Storage (Vorschau) | Microsoft-Dokumentation
description: Erfahren Sie mehr über Einschränkungen und bekannte Probleme bei der Unterstützung von SFTP (Secure File Transfer Protocol) in Azure Blob Storage.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: c9c7a1395006aa7613aa40ff9ea7b402f69bbf5d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557308"
---
# <a name="known-issues-with-secure-file-transfer-protocol-sftp-support-in-azure-blob-storage-preview"></a>Bekannte Probleme bei der Unterstützung von SFTP (Secure File Transfer Protocol) in Azure Blob Storage (Vorschau)

In diesem Artikel werden Einschränkungen und bekannte Probleme bei der SFTP-Unterstützung in Azure Blob Storage beschrieben.

> [!IMPORTANT]
> SFTP-Unterstützung befindet sich derzeit in der Vorschauversion und ist in [diesen Regionen](secure-file-transfer-protocol-support.md#regional-availability) verfügbar.
> 
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
> Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/r/gZguN0j65Y).

## <a name="data-redundancy-options"></a>Datenredundanzoptionen

- Georedundanter Speicher (GRS) und geozonenredundanter Speicher (GZRS) wird in Konten, für die SFTP aktiviert ist, nicht unterstützt.

## <a name="authorization"></a>Autorisierung

- Lokale Benutzer sind die einzige Form der Identitätsverwaltung, die derzeit für den SFTP-Endpunkt unterstützt wird.

- Azure Active Directory (Azure AD), SAS (Shared Access Signature) und Kontoschlüsselautorisierung werden für den SFTP-Endpunkt nicht unterstützt.

- POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs) werden für den SFTP-Endpunkt nicht unterstützt.

  > [!NOTE]
  > Nachdem Ihre Daten in Azure Storage erfasst wurden, können Sie die gesamte Bandbreite der Azure-Speichersicherheitseinstellungen verwenden. Autorisierungsmechanismen wie rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) und Zugriffssteuerungslisten werden zwar nicht als Mittel zum Autorisieren eines sich verbindenden SFTP-Clients unterstützt, können jedoch zum Autorisieren des Zugriffs über Azure-Tools (z. B. Azure-Portal, Azure CLI, Azure PowerShell-Befehle und AzCopy) sowie Azure SDKs und Azure-REST-APIs verwendet werden. 

- Vorgänge auf Kontoebene wie das Auflisten, Festlegen/Abrufen, Erstellen/Löschen von Containern werden nicht unterstützt.
 
## <a name="networking"></a>Netzwerk

- Partitionierte DNS-Endpunkte werden nicht unterstützt.

- Um über SFTP auf das Speicherkonto zuzugreifen, muss Ihr Netzwerk Datenverkehr an Port 22 zulassen.

- Wenn eine Firewall konfiguriert ist, werden Verbindungen von nicht zulässigen IP-Adressen nicht wie erwartet abgelehnt. Wenn jedoch eine erfolgreiche Verbindung für einen authentifizierten Benutzer besteht, werden alle Vorgänge der Datenebene abgelehnt.

## <a name="supported-algorithms"></a>Unterstützte Algorithmen

| Hostschlüssel | Schlüsselaustausch | Verschlüsselungsverfahren/Verschlüsselung | Integrität/MAC | Öffentlicher Schlüssel |
|----------|--------------|--------------------|---------------|------------|
| rsa-sha2-256 | ecdh-sha2-nistp384 | aes128-gcm@openssh.com | hmac-sha2-256 | ssh-rsa |
| rsa-sha2-512 | ecdh-sha2-nistp256 | aes256-gcm@openssh.com | hmac-sha2-512 | ecdsa-sha2-nistp256 |
| ecdsa-sha2-nistp256 | diffie-hellman-group14-sha256 | aes128-cbc| | ecdsa-sha2-nistp384 |
| ecdsa-sha2-nistp384| diffie-hellman-group16-sha512 | aes256-cbc |  | 
||| aes192-cbc ||

## <a name="security"></a>Sicherheit

- Hostschlüssel werden [hier](secure-file-transfer-protocol-host-keys.md) veröffentlicht. Während der öffentlichen Vorschau werden Hostschlüssel bis zu ein Mal pro Monat rotiert.

- Es gibt verschiedene Gründe für die Warnung „remote host identification has changed“ (Remotehostidentifikation hat sich geändert):

  - Der Remotehostschlüssel wurde aktualisiert (Hostschlüssel werden in regelmäßigen Abständen rotiert).
  
  - Der Client hat einen anderen Hostschlüsselalgorithmus als den in der lokalen SSH-Datei „known_hosts“ gespeicherten Algorithmus ausgewählt. OpenSSH verwendet einen bereits vertrauenswürdigen Schlüssel, wenn der Host (account.blob.core.windows.net) übereinstimmt, auch wenn der Algorithmus nicht unbedingt übereinstimmt.
  
  - Für das Speicherkonto wurde ein Failover in eine andere Region ausgeführt.
  
  - Der Remotehost (account.blob.core.windows.net) wird gefälscht.

## <a name="integrations"></a>Integrationen

- Der Änderungsfeed wird nicht unterstützt.

- Kontometriken wie Transaktionen und Kapazität sind verfügbar. Filtern Sie Protokolle nach Vorgängen, um die SFTP-Aktivität anzuzeigen.

- NFS 3.0 (Network File System) und SFTP können nicht für dasselbe Speicherkonto aktiviert werden.

## <a name="performance"></a>Leistung

- Die Uploadleistung mit Standardeinstellungen kann für einige Clients langsam sein. Einiges davon ist zu erwarten, da SFTP ein gesprächiges Protokoll ist und kleine Nachrichtenanforderungen sendet. Wenn Sie die Puffergröße erhöhen und mehrere gleichzeitige Verbindungen verwenden, kann die Geschwindigkeit erheblich verbessert werden. 

  - Für WinSCP können Sie maximal 9 gleichzeitige Verbindungen verwenden, um mehrere Dateien hochzuladen. 

  - Für OpenSSH unter Windows können Sie die Puffergröße auf 100.000 erhöhen: sftp -B 100000 testaccount.user1@testaccount.blob.core.windows.net 

  - Für OpenSSH unter Linux können Sie die Puffergröße auf 262.000 erhöhen: sftp -B 262000 -R 32 testaccount.user1@testaccount.blob.core.windows.net 

- Es tritt ein Timeout von 4 Minuten für Verbindungen im Leerlauf oder inaktive Verbindungen auf. OpenSSH scheint nicht mehr zu reagieren und trennt die Verbindung dann. Einige Clients stellen die Verbindung automatisch erneut her. 

- Der maximale Dateigröße für den Upload ist durch die Clientnachrichtengröße beschränkt. Hier sind einige Beispiele: 

  - 32k-Nachricht (OpenSSH Standard) * 50.000 Blöcke = 1,52 GB 

  - 100k-Nachricht (OpenSSH Windows max.) * 50.000 Blöcke = 4,77 GB 

  - 256k-Nachricht (OpenSSH Linux max.) * 50.000 Blöcke = 12,20 GB 

## <a name="other"></a>Sonstiges

- Symbolische Verknüpfungen werden nicht unterstützt.

- PowerShell und die Azure CLI und werden nicht unterstützt. Sie können das Portal und ARM-Vorlagen für die Public Preview nutzen.

## <a name="see-also"></a>Siehe auch

- [Unterstützung des SFTP-Protokolls (Secure File Transfer) in Azure Blob Storage](secure-file-transfer-protocol-support.md)