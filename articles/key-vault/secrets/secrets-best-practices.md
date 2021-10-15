---
title: Bewährte Methoden für die Verwaltung von Geheimnissen – Azure Key Vault | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über bewährte Methoden für die Geheimnisverwaltung mit Azure Key Vault.
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 4735fcfd28830c8469ddfbed7b521a507b3ca521
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534680"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Best Practices für die Geheimnisverwaltung in Key Vault

Azure Key Vault ermöglicht es Ihnen, Anmeldeinformationen für Dienste oder Apps, wie zum Beispiel Kennwörter und Zugriffsschlüssel, sicher als Geheimnisse zu speichern. Alle Geheimnisse in Ihrer Key Vault-Instanz werden mit einem Softwareschlüssel verschlüsselt. Wenn Sie Key Vault verwenden, müssen Sie keine Sicherheitsinformationen mehr in Ihren Apps speichern. Wenn Sicherheitsinformationen nicht mehr in Anwendungen gespeichert werden müssen, entfällt die Notwendigkeit, diese Informationen in den Code einzubinden.

Beispiele für Geheimnisse, die in Key Vault gespeichert werden sollen:

- Geheimnisse der Clientanwendung
- Verbindungszeichenfolgen
- Kennwörter
- Zugriffsschlüssel (Redis Cache, Azure Event Hubs, Azure Cosmos DB)
- SSH-Schlüssel

Alle anderen vertraulichen Informationen, wie zum Beispiel IP-Adressen, Namen von Diensten und andere Konfigurationseinstellungen, sollten in [Azure App Configuration](../../azure-app-configuration/overview.md) anstatt in Key Vault gespeichert werden.

Jede einzelne Key Vault-Instanz definiert eine Sicherheitsbegrenzung für Geheimnisse. Für eine einzelne Key Vault-Instanz pro App, pro Region und pro Umgebung wird empfohlen, eine präzise Isolation von Geheimnissen für eine App bereitzustellen.

Weitere Informationen zu bewährten Methoden in Key Vault finden Sie unter: [Bewährte Methoden in Key Vault](../general/best-practices.md).

## <a name="configuration-and-storing"></a>Konfiguration und Speicherung 

Speichern Sie die dynamischen Teile der Anmeldeinformationen, die während der Rotation generiert werden, als Werte. Beispiele hierfür sind Geheimnisse, Kennwörter und Zugriffsschlüssel von Clientanwendungen. Alle dazugehörigen statischen Attribute und Bezeichner, wie zum Beispiel Benutzernamen, App-IDs und Dienst-URLs, sollten als Geheimnistags gespeichert und während der Rotation in die neue Version eines Geheimnisses kopiert werden.

Weitere Informationen zu Geheimnissen finden Sie unter [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md).

## <a name="secrets-rotation"></a>Geheimnisrotation
Geheimnisse werden oft im App-Speicher als Umgebungsvariablen oder Konfigurationseinstellungen für den gesamten App-Lebenszyklus gespeichert, wodurch sie anfällig für unerwünschte Offenlegung werden. Da Geheimnisse anfällig für Datenlecks oder Offenlegung sind, müssen sie häufig rotiert werden, und zwar mindestens alle 60 Tage.

Weitere Informationen zum Geheimnisrotationsprozess finden Sie unter [Automatisieren der Rotation eines Geheimnisses für Ressourcen mit zwei Sätzen von Anmeldeinformationen zur Authentifizierung](tutorial-rotation-dual.md). 

## <a name="access-and-network-isolation"></a>Zugriffs- und Netzwerkisolation

Sie können die Angriffsfläche Ihrer Tresore reduzieren, indem Sie angeben, welche IP-Adressen Zugriff darauf haben. Konfigurieren Sie die Firewall, sodass nur Apps und zugehörige Dienste auf Geheimnisse im Tresor zugreifen können. So verringern Sie die Wahrscheinlichkeit, dass Angreifer auf Geheimnisse zugreifen können.

Weitere Informationen zur Netzwerksicherheit finden Sie unter [Konfigurieren von Azure Key Vault-Netzwerkeinstellungen](../general/how-to-azure-key-vault-network-security.md).

Darüber hinaus sollten Anwendungen die geringsten Zugriffsrechte erhalten, d. h. nur Lesezugriff auf Geheimnisse. Der Zugriff auf Geheimnisse kann entweder durch Zugriffsrichtlinien oder mit der rollenbasierten Zugriffssteuerung von Azure geregelt werden. 

Weitere Informationen zur Zugriffssteuerung in Azure Key Vault finden Sie unter:
- [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung von Azure](../general/rbac-guide.md)
- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>Diensteinschränkungen und Zwischenspeichern
Key Vault wurde ursprünglich mit den unter [Grenzwerte des Azure Key Vault-Diensts](../general/service-limits.md) angegebenen einschränkenden Grenzwerten erstellt. Um Ihre Durchsatzraten zu maximieren, werden diese beiden bewährten Methoden empfohlen:
- Speichern Sie Geheimnisse in Ihrer App mindestens acht Stunden lang zwischen.
- Implementieren Sie eine exponentielle Backoff-Wiederholungslogik, um Szenarios zu verarbeiten, bei denen Grenzwerte von Diensten überschritten werden.

Weitere Informationen zur Einschränkungsunterstützung finden Sie unter [Einschränkungsunterstützung für Azure Key Vault](../general/overview-throttling.md).

## <a name="monitoring"></a>Überwachung
Aktivieren Sie die Protokollierung für Ihre Key Vault-Instanz, um den Zugriff auf Ihre Geheimnisse und deren Lebenszyklus zu überwachen. Verwenden Sie [Azure Monitor](../../azure-monitor/overview.md), um alle Geheimnisaktivitäten in allen Tresoren an einem Ort zu überwachen. Verwenden Sie alternativ [Azure Event Grid](../../event-grid/overview.md), um den Lebenszyklus von Geheimnissen zu überwachen, da diese Instanz über eine einfache Integration in Azure Logic Apps und Azure Functions verfügt.

Weitere Informationen finden Sie unter
- [Azure Key Vault als Event Grid-Quelle](../../event-grid/event-schema-key-vault.md?tabs=event-grid-event-schema.md)
- [Azure Key Vault-Protokollierung](../general/logging.md)
- [Überwachung und Warnungen für Azure Key Vault](../general/alert.md)

## <a name="backup-and-purge-protection"></a>Sicherung und Löschschutz
Aktivieren Sie den [Löschschutz](../general/soft-delete-overview.md#purge-protection), um einer erzwungenen Löschung des Geheimnisses vorzubeugen. Erstellen Sie regelmäßige Sicherungen Ihres Tresors, wenn Sie Geheimnisse in einem Tresor aktualisieren, löschen oder erstellen.

- Weitere Informationen zum Sicherungsbefehl von Azure PowerShell finden Sie unter [Sichern eines Geheimnisses](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret).
- Weitere Informationen zum Sicherungsbefehl der Azure CLI finden Sie unter [Sichern eines Geheimnisses](/cli/azure/keyvault/secret#az_keyvault_secret_backup).

## <a name="learn-more"></a>Erfahren Sie mehr
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
