---
title: Datensicherheit in Azure Australien
description: Konfigurieren von Azure in den australischen Regionen, um die spezifischen Anforderungen der Richtlinien, Bestimmungen und Gesetze der australischen Regierung zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: d90dc7dd316e79407069a0c00f9d63659688a640
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "122353631"
---
# <a name="data-security-in-azure-australia"></a>Datensicherheit in Azure Australien

Übergreifende Prinzipien für den Schutz von Kundendaten:

* Schutz von Daten mittels Verschlüsselung
* Verwaltung geheimer Schlüssel
* Einschränken des Datenzugriffs

## <a name="encrypting-your-data"></a>Verschlüsseln Ihrer Daten

Die Verschlüsselung von Daten kann auf Datenträgerebene (ruhende Daten), in Datenbanken (ruhende Daten und während der Übertragung), in Anwendungen (während der Übertragung) und im Netzwerk (während der Übertragung) angewandt werden. Die Verschlüsselung kann in Azure auf mehrere Arten durchgeführt werden:

|Dienst/Funktion|BESCHREIBUNG|
|---|---|
|Speicherdienstverschlüsselung|Azure Storage Service Encryption wird auf der Speicherkontoebene aktiviert, sodass Block- und Seitenblobs beim Schreiben in Azure Storage automatisch verschlüsselt werden. Wenn Sie die Daten aus Azure Storage lesen, werden sie vom Speicherdienst vor der Rückgabe entschlüsselt. Mit der Speicherdienstverschlüsselung (SSE) können Sie Daten schützen, ohne dass Sie in Anwendungen Code ändern oder hinzufügen müssen.|
|Azure Disk Encryption|Mit Azure Disk Encryption können Sie die Betriebssystemdatenträger und andere Datenträger verschlüsseln, die von einem virtuellen Azure-Computer verwendet werden. Die Integration in Azure Key Vault ermöglicht die Steuerung und Verwaltung der Schlüssel für die Datenträgerverschlüsselung.|
|Clientseitige Verschlüsselung auf Anwendungsebene|Die clientseitige Verschlüsselung ist in die Java- und .NET-Speicherclientbibliotheken integriert, in denen Azure Key Vault-APIs verwendet werden können, sodass eine unkomplizierte Implementierung ermöglicht wird. Verwenden Sie Azure Key Vault, um mithilfe von Azure Active Directory auf die Geheimnisse bestimmter Benutzer in Azure Key Vault zuzugreifen.|
|Verschlüsselung während der Übertragung|Die für die Verbindung mit Azure Australia verfügbare Basisverschlüsselung unterstützt das TLS 1.2-Protokoll (Transport Level Security) sowie X.509-Zertifikate. Für Infrastrukturnetzwerkverbindungen zwischen Azure Australia-Rechenzentren werden auch FIPS 140-2-Kryptografiealgorithmen (Federal Information Processing Standard) der Stufe 1 verwendet.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 und Azure-Dateifreigaben können SMB 3.0 für die Verschlüsselung zwischen dem virtuellen Computer und der Dateifreigabe verwenden. Verwenden Sie die clientseitige Verschlüsselung, um die Daten zu verschlüsseln, bevor sie in den Speicher einer Clientanwendung übertragen werden, und sie nach der Übertragung aus dem Speicher zu entschlüsseln.|
|Virtuelle IaaS-Computer|Verwenden Sie Azure Disk Encryption. Aktivieren Sie Storage Service Encryption, um die VHD-Dateien zu verschlüsseln, mit denen die Datenträger in Azure Storage gesichert werden. Dadurch werden jedoch nur neu geschriebene Daten verschlüsselt. Wenn Sie also einen virtuellen Computer erstellen und dann Storage Service Encryption für das Speicherkonto aktivieren, das die VHD-Datei enthält, werden nur die Änderungen verschlüsselt, nicht die ursprüngliche VHD-Datei.|
|Clientseitige Verschlüsselung|Dies ist die sicherste Methode zum Verschlüsseln Ihrer Daten, da die Verschlüsselung vor der Übertragung erfolgt und die Daten als ruhende Daten verschlüsselt werden. Allerdings müssen Sie hierzu Ihren Anwendungen, die den Speicher nutzen, Code hinzuzufügen, was vielleicht nicht unbedingt erwünscht ist. In diesen Fällen können Sie HTTPS für die Daten während der Übertragung verwenden und die Speicherdienstverschlüsselung zum Verschlüsseln der ruhenden Daten. Die clientseitige Verschlüsselung bedeutet auch eine höhere Auslastung des Clients. Dies müssen Sie bei der Skalierbarkeitsplanung berücksichtigen, insbesondere dann, wenn Sie große Datenmengen verschlüsseln und übertragen.|
|

Weitere Informationen zu den Verschlüsselungsoptionen in Azure finden Sie im [Azure Storage-Sicherheitsleitfaden](../storage/blobs/security-recommendations.md).

## <a name="protecting-data-by-managing-secrets"></a>Schützen von Daten durch Verwalten von Geheimnissen

Eine sichere Schlüsselverwaltung ist essenziell, um Daten in der Cloud zu schützen. Für Kunden empfiehlt es sich, die Schlüsselverwaltung möglichst einfach zu gestalten und die Kontrolle über die Schlüssel zu behalten, die von Cloudanwendungen und -diensten verwendet werden, um Daten zu verschlüsseln.

### <a name="managing-secrets"></a>Verwaltung geheimer Schlüssel

* Verwenden Sie Key Vault, um das Risiko zu minimieren, dass geheime Schlüssel durch hartcodierte Konfigurationsdateien, durch Skripts oder im Quellcode offengelegt werden. Azure Key Vault verschlüsselt Schlüssel (etwa die Verschlüsselungsschlüssel für Azure Disk Encryption) und geheime Schlüssel (beispielsweise Kennwörter) mittels Speicherung in FIPS 140-2-Hardwaresicherheitsmodulen (HSMs) der Stufe 2. Zur Steigerung der Sicherheit können Sie Schlüssel in diese HSMs importieren oder in diesen generieren.
* Der Anwendungscode und Vorlagen dürfen nur URI-Verweise auf die Geheimnisse enthalten, sodass die tatsächlichen Geheimnisse nicht im Code, in der Konfiguration oder in Quellcoderepositorys enthalten sind. Dies verhindert umfassende Phishing-Angriffe auf interne oder externe Repositorys, z. B. "harvest-bots" auf GitHub.
* Verwenden Sie innerhalb von Key Vault eine sichere Azure RBAC-Kontrolle. Ein vertrauenswürdiger Mitarbeiter, der aus dem Unternehmen ausscheidet oder innerhalb des Unternehmens in eine andere Gruppe wechselt, darf keinen Zugriff mehr auf die geheimen Schlüssel haben.  

Weitere Informationen finden Sie unter [Azure Key Vault](azure-key-vault.md).

## <a name="isolation-to-restrict-data-access"></a>Isolierung zur Einschränkung des Datenzugriffs

Bei der Isolierung geht es um die Einrichtung von Grenzen, Segmentierungen und Containern, um den Datenzugriff auf autorisierte Benutzer, Dienste und Anwendungen zu beschränken. So ist beispielsweise die Trennung von Mandanten eine unverzichtbare Sicherheitsmaßnahme für mehrinstanzenfähige Cloudplattformen wie Microsoft Azure. Eine logische Trennung trägt dazu bei, Konflikte zwischen Mandanten zu verhindern.

#### <a name="per-customer-isolation"></a>Kundenspezifische Isolierung

Azure implementiert die Netzwerkzugriffssteuerung und Trennung mittels VLAN-Isolierung in Schicht 2, Zugriffssteuerungslisten, Lastenausgleichsmodulen und IP-Filtern.

Kunden können ihre Ressourcen für Abonnements, Ressourcengruppen, virtuelle Netzwerke und Subnetze noch weiter isolieren.

Weitere Informationen zur Isolierung in Microsoft Azure finden Sie unter [Isolation in der öffentlichen Azure-Cloud](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Artikel zu [Azure VPN Gateway](vpn-gateway.md).