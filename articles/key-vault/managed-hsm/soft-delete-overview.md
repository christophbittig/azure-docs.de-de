---
title: Vorläufiges Löschen in einem verwalteten Azure Key Vault-HSM | Microsoft-Dokumentation
description: Mit dem vorläufigen Löschen in einem verwalteten HSM können Sie gelöschte HSM-Instanzen und -Schlüssel wiederherstellen. Dieser Artikel bietet eine Übersicht über diese Funktion.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: b832aa066e1d31bfc064f988c722d2503ff96507
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122343139"
---
# <a name="managed-hsm-soft-delete-overview"></a>Übersicht über vorläufiges Löschen von verwaltetem HSM

> [!IMPORTANT]
> Das vorläufige Löschen kann für verwaltete HSM-Ressourcen nicht deaktiviert werden.

> [!IMPORTANT]
> Vorläufig gelöschte verwaltete HSM-Ressourcen werden weiterhin zum vollen Stundensatz abgerechnet, bis sie bereinigt werden.

Das Feature für vorläufiges Löschen von verwaltetem HSM ermöglicht die Wiederherstellung gelöschter HSMs und Schlüssel. Dieses Feature bietet insbesondere die folgenden Sicherheitsmaßnahmen:

- Wurde ein HSM oder Schlüssel gelöscht, kann er während eines konfigurierbaren Zeitraums von 7 bis 90 Kalendertagen wiederhergestellt werden. Sie können die Beibehaltungsdauer festlegen, wenn Sie ein HSM erstellen. Wenn Sie keinen Wert angeben, wird der Standardaufbewahrungszeitraum von 90 Tagen verwendet. Dieser Zeitraum gibt den Benutzern genügend Zeit, eine versehentliche Löschung von Schlüsseln oder HSMs zu bemerken und darauf zu reagieren.
- Um einen Schlüssel dauerhaft zu löschen, müssen Benutzer zwei Aktionen ausführen. Zuerst müssen sie den Schlüssel löschen. Dadurch geht er in den Zustand der vorläufigen Löschung über. Anschließend muss ein Benutzer den Schlüssel im Zustand der vorläufigen Löschung bereinigen. Für den Bereinigungsvorgang ist die Rolle „Managed HSM Crypto Officer“ (Crypto Officer des verwalteten HSM) erforderlich. Diese zusätzlichen Schutzmaßnahmen verringern das Risiko, dass ein Benutzer versehentlich oder böswillig einen Schlüssel oder ein HSM löscht.


## <a name="soft-delete-behavior"></a>Verhalten des vorläufigen Löschens

Vorläufiges Löschen kann für verwaltete HSM-Ressourcen nicht deaktiviert werden.

Als gelöscht markierte Ressourcen werden für einen angegebenen Zeitraum aufbewahrt. Es gibt auch einen Mechanismus zum Wiederherstellen gelöschter HSMs oder Schlüssel, damit Sie Löschungen rückgängig machen können.

Der Standardaufbewahrungszeitraum beträgt 90 Tage. Wenn Sie eine HSM-Ressource erstellen, können Sie das Aufbewahrungsrichtlinienintervall auf einen Wert zwischen 7 und 90 Tagen festlegen. Die Aufbewahrungsrichtlinie des Bereinigungsschutzes verwendet das gleiche Intervall. Nachdem Sie die Aufbewahrungsrichtlinie festgelegt haben, können Sie sie nicht mehr ändern.

Sie können den Namen einer HSM-Ressource, die vorläufig gelöscht wurde, erst wiederverwenden, wenn der Aufbewahrungszeitraum verstrichen ist und die HSM-Ressource bereinigt (endgültig gelöscht) wurde.

## <a name="purge-protection"></a>Bereinigungsschutz

Bereinigungsschutz ist ein optionales Verhalten. Er ist nicht standardmäßig aktiviert. Sie können ihn mit der [Azure CLI](./recovery.md?tabs=azure-cli) oder [PowerShell](./recovery.md?tabs=azure-powershell) aktivieren.

Bei aktiviertem Bereinigungsschutz kann ein HSM oder ein Schlüssel im gelöschten Zustand erst nach Ablauf des Aufbewahrungszeitraums endgültig gelöscht werden. Vorläufig gelöschte HSMs und Schlüssel können immer noch wiederhergestellt werden, wodurch sichergestellt wird, dass die Aufbewahrungsrichtlinie wirksam ist.

Der Standardaufbewahrungszeitraum beträgt 90 Tage. Wenn Sie eine HSM-Ressource erstellen, können Sie das Aufbewahrungsrichtlinienintervall auf einen Wert zwischen 7 und 90 Tagen festlegen. Das Aufbewahrungsrichtlinienintervall kann nur festgelegt werden, wenn Sie ein HSM erstellen. Diese Einstellung kann später nicht mehr geändert werden.

Informationen finden Sie unter [Verwenden des vorläufigen Löschens von verwalteten HSMs mit der Befehlszeilenschnittstelle](./recovery.md?tabs=azure-cli#managed-hsms-cli) oder [Verwenden des vorläufigen Löschens von verwalteten HSMs mit PowerShell](./recovery.md?tabs=azure-powershell#managed-hsms-powershell).

## <a name="managed-hsm-recovery"></a>Wiederherstellung von verwalteten HSMs

Wenn Sie ein HSM löschen, erstellt der Dienst eine Proxyressource im Abonnement und fügt genügend Metadaten hinzu, um eine Wiederherstellung zu ermöglichen. Die Proxyressource ist ein gespeichertes Objekt. Es ist am gleichen Speicherort verfügbar wie das gelöschte HSM. 

## <a name="key-recovery"></a>Schlüsselwiederherstellung

Beim Löschen eines Schlüssels versetzt der Dienst ihn in einen gelöschten Zustand, sodass keine Vorgänge mehr darauf zugreifen können. In diesem Zustand können die Schlüssel aufgelistet, wiederhergestellt oder bereinigt werden. Verwenden Sie zum Anzeigen der Objekte den Azure CLI-Befehl `az keyvault key list-deleted` (unter [Vorläufiges Löschen und Löschschutz für verwaltete HSMs mit der CLI](./recovery.md?tabs=azure-cli#keys-cli) beschrieben) oder den Azure PowerShell-Parameter `-InRemovedState` (unter [Vorläufiges Löschen und Löschschutz für verwaltete HSMs mit PowerShell](./recovery.md?tabs=azure-powershell#keys-powershell) beschrieben).  

Wenn Sie den Schlüssel löschen, plant das verwaltete HSM die Löschung der zugrunde liegenden Daten, die dem gelöschten HSM oder Schlüssel entsprechen, nach einem vorgegebenen Aufbewahrungsintervall. Der dem HSM entsprechende DNS-Eintrag wird während des Aufbewahrungsintervalls ebenfalls beibehalten.

## <a name="soft-delete-retention-period"></a>Aufbewahrungszeitraum für vorläufig gelöschte Ressourcen

Vorläufig gelöschte Ressourcen werden für einen festgelegten Zeitraum von 90 Tagen beibehalten. Während des Aufbewahrungsintervalls für vorläufig gelöschte Ressourcen gelten die folgenden Bedingungen:

- Sie können alle HSMs und Schlüssel auflisten, die sich für Ihr Abonnement im vorläufig gelöschten Zustand befinden. Sie können auch auf Lösch- und Wiederherstellungsinformationen zu diesen zugreifen.
- Nur Benutzer mit der Rolle „Mitwirkender für verwaltetes HSM“ können gelöschte HSMs auflisten. Es wird empfohlen, eine benutzerdefinierte Rolle mit diesen speziellen Berechtigungen für den Umgang mit gelöschten Tresoren zu erstellen.
- Die Namen verwalteter HSMs müssen an einem bestimmten Speicherort eindeutig sein. Wenn Sie einen Schlüssel erstellen, können Sie keinen Namen verwenden, wenn das HSM einen Schlüssel mit diesem Namen in einem gelöschten Zustand enthält.
- Nur Benutzer mit der Rolle „Mitwirkender für verwaltetes HSM“ können verwaltete HSMs auflisten, anzeigen, wiederherstellen und bereinigen.
- Nur Benutzer mit der Rolle „Managed HSM Crypto Officer“ (Kryptoverantwortlicher für verwaltete HSMs) können Schlüssel auflisten, anzeigen, wiederherstellen und endgültig löschen.
  
Wenn ein verwaltetes HSM oder ein Schlüssel nicht wiederhergestellt wird, wird das vorläufig gelöschte HSM bzw. der vorläufig gelöschte Schlüssel vom Dienst am Ende des Aufbewahrungsintervalls endgültig gelöscht. Sie können das Löschen von Ressourcen nicht neu planen.

### <a name="billing-implications"></a>Hinweise zur Gebührenberechnung

Verwaltetes HSM ist ein Einzelmandantendienst. Wenn Sie ein verwaltetes HSM erstellen, reserviert der Dienst zugrunde liegende Ressourcen, die Ihrem HSM zugeordnet sind. Diese Ressourcen bleiben auch dann zugeordnet, wenn sich das HSM in einem gelöschten Zustand befindet. Ihnen wird das HSM in Rechnung gestellt, während es sich in einem gelöschten Zustand befindet.

## <a name="next-steps"></a>Nächste Schritte

In diesen Artikeln werden die wichtigsten Szenarien für die Verwendung von vorläufigem Löschen beschrieben:

- [Verwenden des vorläufigen Löschens von verwalteten HSMs mit PowerShell](./recovery.md?tabs=azure-powershell) 
- [Verwenden des vorläufigen Löschens von verwalteten HSMs mit der Azure CLI](./recovery.md?tabs=azure-cli)
