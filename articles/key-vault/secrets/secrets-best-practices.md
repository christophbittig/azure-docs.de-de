---
title: Bewährte Methoden für die Verwaltung von Geheimnissen – Azure Key Vault | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über bewährte Methoden für die Verwaltung von Geheimnissen in Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: ae4d2bd17dc1a233be71f48ebd106f1db2a31de6
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094460"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Best Practices für die Geheimnisverwaltung in Key Vault

Azure Key Vault ermöglicht es Ihnen, Dienst- oder Anwendungsanmeldeinformationen wie Kennwörter und Zugriffsschlüssel sicher als Geheimnisse zu speichern. Alle Geheimnisse in Ihrer Key Vault-Instanz werden mit einem Softwareschlüssel verschlüsselt gespeichert. Dank Key Vault müssen Anwendungsentwickler Sicherheitsinformationen nicht mehr in der Anwendung speichern. Wenn Sicherheitsinformationen nicht mehr in Anwendungen gespeichert werden müssen, entfällt die Notwendigkeit, diese Informationen in den Code einzubinden. 

Beispiele für Geheimnisse, die in Key Vault gespeichert werden sollen:

- Geheimnisse der Clientanwendung
- Verbindungszeichenfolgen
- Kennwörter
- Zugriffsschlüssel (Redis Cache, Event Hub, Cosmos DB)
- SSH-Schlüssel

Alle anderen vertraulichen Informationen wie IP-Adressen, Dienstnamen und andere Konfigurationseinstellungen sollten stattdessen in [Azure App Configuration](../../azure-app-configuration/overview.md) gespeichert werden.

Jeder einzelne Schlüsseltresor definiert eine Sicherheitsgrenze für Geheimnisse. Es wird ein einzelner Schlüsseltresor pro Anwendung, pro Region und pro Umgebung empfohlen, um eine präzise Isolation von Anwendungsgeheimnissen bereitzustellen. 

Weitere Informationen zu bewährten Methoden für Schlüsseltresore finden Sie unter:
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)

## <a name="configuration-and-storing"></a>Konfiguration und Speicherung 

Es wird empfohlen, die dynamische Bestandteile von Anmeldeinformationen zu speichern, die während der Rotation generiert werden, z. B. Clientanwendungsgeheimnisse, Kennwörter und Zugriffsschlüssel geheim als Wert. Alle zugehörigen statischen Attribute und Bezeichner wie Benutzernamen, App-IDs und Dienst-URLs sollten als Geheimnistags gespeichert und während der Rotation in eine neue Version eines Geheimnisses kopiert werden.

Weitere Informationen finden Sie unter:
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)

## <a name="secrets-rotation"></a>Geheimnisrotation
Geheimnisse werden im Anwendungsspeicher häufig als Umgebungsvariablen oder Konfigurationseinstellungen für den gesamten Anwendungslebenszyklus gespeichert, wodurch sie anfällig für unerwünschte Gefährdungen sind. Da Geheimnisse anfällig für Datenlecks oder Gefährdungen sind, müssen sie häufig neu rotiert werden, und zwar mindestens alle 60 Tage. 

Weitere Informationen zur Geheimnisrotation finden Sie unter:
- [Automatisieren der Geheimnisrotation für Ressourcen, die über zwei Sätze mit Anmeldeinformationen für die Authentifizierung verfügen](tutorial-rotation-dual.md) 

## <a name="access-and-network-isolation"></a>Zugriffs- und Netzwerkisolation

Sie können die Angriffsfläche Ihrer Tresore reduzieren, indem Sie angeben, welche IP-Adressen Zugriff darauf haben. Konfigurieren Sie die Firewall so, dass nur die Anwendung und zugehörige Dienste auf Geheimnisse im Tresor zugreifen dürfen. So verringern Sie die Wahrscheinlichkeit, dass Angreifer Zugriff auf Geheimnisse erlangen. 

Weitere Informationen zur Netzwerksicherheit finden Sie unter:
- [Konfigurieren von Azure Key Vault-Netzwerkeinstellungen](../general/how-to-azure-key-vault-network-security.md)

Darüber hinaus sollten Anwendungen die geringsten Zugriffsrechte erhalten, d. h. nur Lesezugriff auf Geheimnisse. Der Zugriff auf Geheimnisse kann entweder mit Zugriffsrichtlinien oder mit der rollenbasierten Zugriffssteuerung von Azure gesteuert werden. 

Weitere Informationen zur Zugriffssteuerung in Azure Key Vault finden Sie unter:
- [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](../general/rbac-guide.md)
- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>Diensteinschränkungen und Zwischenspeichern
Key Vault wurde ursprünglich mit den unter [Grenzwerte des Azure Key Vault-Diensts](../general/service-limits.md) angegebenen Grenzwerten erstellt. Zur Maximierung der Key Vault-Durchsatzraten werden hier einige empfohlene Richtlinien und bewährte Methoden genannt:
- Zwischenspeichern von Geheimnissen in der Anwendung für mindestens 8 Stunden
- Implementieren einer exponentiellen Backoffwiederholungslogik, um Szenarios zu beheben, in denen Dienstgrenzwerte überschritten werden.

Weitere Informationen zur Drosselung finden Sie unter:
- [Anleitung zur Drosselung von Azure Key Vault](../general/overview-throttling.md)

## <a name="monitoring"></a>Überwachung
Aktivieren Sie die Protokollierung für Ihre Key Vault-Instanz, um den Zugriff auf Ihre Geheimnisse und deren Lebenszyklus zu überwachen. Mit [Azure Monitor](https://docs.microsoft.com//azure/azure-monitor/overview) können Sie alle Geheimnisaktivitäten in allen Ihren Tresoren an einem einzigen Ort überwachen. [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) ermöglicht es Ihnen, den Lebenszyklus von Geheimnissen mit einer einfachen Integration in Logic Apps und Azure Functions zu überwachen.

Weitere Informationen finden Sie unter
- [Azure Key Vault als Event Grid-Quelle](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault?tabs=event-grid-event-schema)
- [Azure Key Vault-Protokollierung](../general/logging.md)
- [Überwachung und Warnungen für Azure Key Vault](../general/alert.md)

## <a name="backup-and-purge-protection"></a>Sicherung und Löschschutz
Aktivieren Sie den [Löschschutz](../general/soft-delete-overview.md#purge-protection), um sich vor erzwungene Löschungen von Geheimnissen/Tresoren zu schützen. Lassen Sie bei Aktualisierungen/Löschungen/Erstellungen von Geheimnissen in einer Key Vault-Instanz regelmäßig Sicherungen Ihres Schlüsseltresors erstellen.

### <a name="azure-powershell-backup-commands"></a>Sicherungsbefehle für Azure PowerShell

* [Sichern eines Geheimnisses](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Sicherungsbefehle der Azure-Befehlszeilenschnittstelle

* [Sichern eines Geheimnisses](/cli/azure/keyvault/secret#az_keyvault_secret_backup)

## <a name="learn-more"></a>Weitere Informationen
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
