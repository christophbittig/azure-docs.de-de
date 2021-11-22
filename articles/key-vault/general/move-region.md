---
title: Verschieben eines Schlüsseltresors in eine andere Region – Azure Key Vault| Microsoft-Dokumentation
description: Dieser Artikel bietet eine Anleitung zum Verschieben eines Schlüsseltresors in eine andere Region.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2021
ms.author: mbaldwin
ms.custom: subject-moving-resources
ms.openlocfilehash: b8364ae34c8fcef3b65b83d5f2e1851920634edd
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370137"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Regionsübergreifendes Verschieben eines Azure Key Vault

Azure Key Vault gestattet es Ihnen nicht, einen Schlüsseltresor aus einer Region in eine andere zu verschieben. Sie können jedoch einen Schlüsseltresor in der neuen Region erstellen, jeden einzelnen Schlüssel, jedes Geheimnis oder jedes Zertifikat manuell aus Ihrem vorhandenen Schlüsseltresor in den neuen Schlüsseltresor kopieren und dann den ursprünglichen Schlüsseltresor entfernen.

## <a name="prerequisites"></a>Voraussetzungen

Es ist wichtig, die Auswirkungen dieser Problemumgehung zu verstehen, bevor Sie versuchen, sie in einer Produktionsumgebung anzuwenden.

## <a name="prepare"></a>Vorbereiten

Zunächst müssen Sie einen neuen Schlüsseltresor in der Region erstellen, in die Sie verschieben möchten. Hierzu können Sie das [Azure-Portal](quick-create-portal.md), die [Azure CLI](quick-create-cli.md) oder [Azure PowerShell](quick-create-powershell.md) verwenden.

Denken Sie an Folgendes:

* Schlüsseltresornamen sind global eindeutig. Sie können einen Tresornamen nicht wiederverwenden.
* Sie müssen Ihre Zugriffsrichtlinien und Netzwerkkonfigurationseinstellungen in dem neuen Schlüsseltresor erneut konfigurieren.
* Sie müssen vorläufiges Löschen und Bereinigungsschutz in dem neuen Schlüsseltresor erneut konfigurieren.
* Der Sicherungs- und Wiederherstellungsvorgang erhält Ihre Einstellungen für die automatische Rotation nicht. Sie müssen die Einstellungen eventuell neu konfigurieren.

## <a name="move"></a>Move

Exportieren Sie Ihre Schlüssel, Geheimnisse oder Zertifikate aus Ihrem alten Schlüsseltresor, und importieren Sie sie dann in Ihren neuen Tresor. 

Mit dem Sicherungsbefehl können Sie die einzelnen Geheimnisse, Schlüssel und Zertifikate in Ihrem Tresor sichern. Ihre Geheimnisse werden als verschlüsseltes Blob heruntergeladen.  Eine Schritt-für-Schritt-Anleitung finden Sie unter [Azure Key Vault: Sichern und Wiederherstellen](backup.md).

Alternativ können Sie bestimmte Geheimnistypen manuell herunterladen. Beispielsweise können Sie Zertifikate als PFX-Datei herunterladen. Diese Option beseitigt die geografischen Einschränkungen für einige Geheimnistypen wie Zertifikate. Sie können die PFX-Dateien in beliebige Schlüsseltresore in einer beliebigen Region hochladen. Die Geheimnisse werden in einem nicht kennwortgeschützten Format heruntergeladen. Sie sind für den Schutz Ihrer Geheimnisse während des Verschiebens verantwortlich.

Nachdem Sie Ihre Schlüssel, Geheimnisse oder Zertifikate heruntergeladen haben, stellen Sie sie in Ihrem neuen Schlüsseltresor wieder her. 

Die Verwendung der Befehle Sichern und Wiederherstellen weist zwei Einschränkungen auf:

* Es ist nicht möglich, einen Schlüsseltresor in einer geografischen Region zu sichern und ihn in einer anderen geografischen Region wiederherzustellen. Weitere Informationen finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).

* Der Sicherungsbefehl sichert alle Versionen der einzelnen Geheimnisse. Wenn Sie ein Geheimnis mit einer großen Anzahl von Vorgängerversionen (mehr als 10) verwenden, kann die Anforderung die maximal zulässige Anforderungsgröße überschreiten, und der Vorgang schlägt eventuell fehl.

## <a name="verify"></a>Überprüfung

Überprüfen Sie vor dem Löschen Ihres alten Schlüsseltresors, ob der neue Tresor alle erforderlichen Schlüssel, Geheimnisse und Zertifikate enthält. 


## <a name="next-steps"></a>Nächste Schritte

- [Azure Key Vault: Sichern und Wiederherstellen](backup.md)
- [Verschieben einer Azure Key Vault-Instanz zwischen Ressourcengruppen](move-resourcegroup.md)
- [Verschieben einer Azure Key Vault-Instanz in ein anderes Abonnement](move-subscription.md)