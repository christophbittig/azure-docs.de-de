---
title: Bewährte Methoden zum Verwenden von Key Vault – Azure Key Vault | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über bewährte Methoden für Azure Key Vault, einschließlich der Steuerung des Zugriffs, der Verwendung separater Schlüsseltresore sowie Sicherungs-, Protokollierungs- und Wiederherstellungsoptionen.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 616376d034fd32fae23d24a3a6e12f329604d376
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858960"
---
# <a name="best-practices-to-use-key-vault"></a>Bewährte Methoden zum Verwenden von Key Vault

## <a name="use-separate-key-vaults"></a>Verwenden separater Key Vault-Instanzen

Es wird empfohlen, einen Schlüsseltresor pro Anwendung und Umgebung (Entwicklung, Präproduktion und Produktion) pro Region zu verwenden. Dadurch können Sie Geheimnisse nicht umgebungs- oder regionsübergreifend freigegeben, und die Bedrohung im Falle einer Sicherheitsverletzung wird ebenfalls verringert.

### <a name="why-we-recommend-separate-key-vaults"></a>Warum wir separate Schlüsseltresore empfehlen

Die Key Vault-Instanz definiert die Sicherheitsgrenze für gespeicherte Geheimnisse. Die Gruppierung von Geheimnissen im selben Tresor erweitert den *Ausbreitungsradius* eines Sicherheitsereignisses, da Angreifer in der Lage sein könnten, übergreifend auf Geheimnisse zuzugreifen. Um dies abzuschwächen, sollten Sie überlegen, auf welche Geheimnisse eine bestimmte Anwendung Zugriff besitzen *sollte*, und dann Ihre Schlüsseltresore auf der Grundlage dieser Abgrenzung trennen. Die Trennung von Schlüsseltresoren nach Anwendung ist die gängigste Grenze, aber die Sicherheitsgrenze kann für große Anwendungen (z. B. pro Gruppe verwandter Dienste) präziser sein.

## <a name="control-access-to-your-vault"></a>Steuern des Zugriffs auf Ihren Schlüsseltresor

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, müssen Sie den Zugriff auf Schlüsseltresore schützen, sodass nur autorisierte Anwendungen und Benutzer zugelassen sind. In diesem [Artikel](security-features.md) finden Sie eine Übersicht des Modells für den Key Vault-Zugriff. Er erläutert Authentifizierung und Autorisierung und beschreibt, wie Sie den Zugriff auf Ihre Schlüsseltresore schützen.

Vorschläge für die Zugriffssteuerung auf Ihren Schlüsseltresor sehen wie folgt aus:
1. Sperren Sie den Zugriff auf Ihr Abonnement, Ihre Ressourcengruppe und Ihre Schlüsseltresore (Azure RBAC).
2. Erstellen Sie Zugriffsrichtlinien für jeden Schlüsseltresor.
3. Verwenden Sie den Prinzipal mit den geringsten Zugriffsrechten, um den Zugriff zu gewähren.
4. Aktivieren Sie die Firewall und [VNET-Dienstendpunkte](overview-vnet-service-endpoints.md).

## <a name="backup"></a>Backup

Stellen Sie sicher, dass Sie regelmäßig Sicherungskopien Ihres Schlüsseltresors beim Aktualisieren/Löschen/Erstellen von Objekten in einem Schlüsseltresor erstellen.

### <a name="azure-powershell-backup-commands"></a>Sicherungsbefehle für Azure PowerShell

* [Sichern eines Zertifikats](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Sichern eines Schlüssels](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Sichern eines Geheimnisses](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Sicherungsbefehle der Azure-Befehlszeilenschnittstelle

* [Sichern eines Zertifikats](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Sichern eines Schlüssels](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Sichern eines Geheimnisses](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Aktivieren der Protokollierung

[Aktivieren Sie die Protokollierung](logging.md) für Ihren Schlüsseltresor. Richten sie auch Warnungen ein.

## <a name="turn-on-recovery-options"></a>Aktivieren von Wiederherstellungsoptionen

1. Aktivieren Sie [Vorläufiges Löschen](soft-delete-overview.md).
2. Aktivieren Sie den Löschschutz, wenn Sie sich auch nach dem Aktivieren des vorläufigen Löschens vor dem erzwungenen Löschen des Geheimnis/Schlüsseltresors schützen möchten.

## <a name="learn-more"></a>Weitere Informationen
- [Best Practices für die Geheimnisverwaltung in Key Vault](../secrets/secrets-best-practices.md)
