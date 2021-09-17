---
title: Regionsübergreifendes Migrieren von Azure API Management
description: Erfahren Sie, wie Sie eine API Management-Instanz aus einer Region zu einer anderen migrieren.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 08/20/2021
ms.author: apimpm
ms.custom: subject-moving-resources
ms.openlocfilehash: 4958cc4684cc1ecc8ed43de987246c435b4982eb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698606"
---
# <a name="how-to-move-azure-api-management-across-regions"></a>Regionsübergreifendes Verschieben von Azure API Management

In diesem Artikel wird beschrieben, wie Sie eine API Management-Instanz in eine andere Azure-Region verschieben. Die Verschiebung Ihrer Instanz in eine andere Region kann viele Gründe haben. Zum Beispiel:

* Verschieben Ihrer Instanz näher zu Ihren API-Consumern.
* Bereitstellen von Features, die nur in bestimmten Regionen verfügbar sind
* Erfüllen interner Richtlinien- und Governanceanforderungen

Wenn Sie API Management-Instanzen von einer Azure-Region in eine andere verschieben möchten, können Sie den Wiederherstellungsvorgang [Sicherung und Wiederherstellung](api-management-howto-disaster-recovery-backup-restore.md) des Diensts verwenden. Sie können einen anderen API Management-Instanznamen oder den vorhandenen Namen verwenden. 

> [!NOTE]
> API Management unterstützt auch die [Bereitstellung in mehreren Regionen](api-management-howto-deploy-multi-region.md),wodurch ein einzelner Azure API Management-Dienst auf mehrere Azure-Regionen verteilt wird. Die Bereitstellung in mehreren Regionen trägt dazu bei, die Anforderungslatenz bei geografisch verteilten API-Nutzern zu verringern, und verbessert gleichzeitig die Dienstverfügbarkeit, wenn eine Region offline geht.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="considerations"></a>Weitere Überlegungen

* Wählen Sie in den Quell- und Zielregionen denselben API Management-Tarif aus. 
* Sichern und Wiederherstellen funktioniert nicht bei der Migration zwischen verschiedenen Cloudtypen. In diesem Fall müssen Sie die Ressource [als Vorlage](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) exportieren. Passen Sie dann die exportierte Vorlage für die Azure-Zielregion an, und erstellen Sie die Ressource neu. 

## <a name="prerequisites"></a>Voraussetzungen

* Bewerten Sie die Anforderungen und Einschränkungen der API Management-Dienste [Sichern und Wiederherstellen](api-management-howto-disaster-recovery-backup-restore.md). 
* Mehr Informationen finden Sie unter [Nicht gesicherte Elemente](api-management-howto-disaster-recovery-backup-restore.md#what-is-not-backed-up). Zeichnen Sie Einstellungen und Daten auf, die Sie nach dem Verschieben der Instanz manuell neu erstellen müssen.
* Erstellen Sie ein [Speicherkonto](../storage/common/storage-account-create.md?tabs=azure-portal) in der Quellregion. Sie verwenden dieses Konto, um die Quellinstanz zu sichern. 

## <a name="prepare-and-move"></a>Vorbereiten und Verschieben

### <a name="option-1-use-a-different-api-management-instance-name"></a>Option 1: Verwenden eines anderen API Management-Instanznamens

1. Erstellen Sie in der Zielregion eine neue API Management-Instanz mit demselben Tarif wie die API Management-Quellinstanz. Verwenden Sie einen anderen Namen für die neue Instanz.
1. [Sichern ](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service) Sie die vorhandene API Management-Instanz im Speicherkonto. 
1. [Stellen Sie](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service) die Sicherung der Quellinstanz auf der neuen API Management-Instanz wieder her.
1. Wenn Sie über eine benutzerdefinierte Domäne verfügen, die auf die API Management-Instanz in der Quellregion verweist, aktualisieren Sie den CNAME-Eintrag der benutzerdefinierten Domäne so, dass er auf die neue API Management-Instanz verweist. 

### <a name="option-2-use-the-same-api-management-instance-name"></a>Option 2: Verwenden desselben API Management-Instanznamens

> [!WARNING]
> Diese Option löscht die ursprüngliche API Management-Instanz und führt während der Migration zu Ausfallzeiten. Stellen Sie sicher, dass Sie über eine gültige Sicherung verfügen, bevor Sie die Quellinstanz löschen.

1. [Sichern ](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service) Sie die vorhandene API Management-Instanz im Speicherkonto. 
1. Löschen Sie die API Management-Instanz in der Quellregion. 
1. Erstellen Sie eine neue API Management-Instanz in der Zielregion mit demselben Tarif wie in der Quellregion.
1. [Stellen Sie](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service) die Sicherung der Quellinstanz auf der neuen API Management-Instanz in der Zielregion wieder her.  

## <a name="verify"></a>Überprüfen

1. Stellen Sie sicher, dass der Wiederherstellungsvorgang erfolgreich abgeschlossen wurde, bevor Sie auf Ihre API Management-Instanz in der Zielregion zugreifen.
1. Konfigurieren Sie Einstellungen, die während des Wiederherstellungsvorgangs nicht automatisch verschoben werden. Beispiele: Konfiguration des virtuellen Netzwerks, verwaltete Identitäten, Inhalte im Entwicklerportal sowie benutzerdefinierte Domänen- und benutzerdefinierte Zertifizierungsstellenzertifikate.
1. Greifen Sie auf Ihre API Management-Endpunkte in der Zielregion zu. Testen Sie beispielsweise Ihre APIs, oder greifen Sie auf das Entwicklerportal zu.

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Wenn Sie die API Management-Instanz mit Option 1 verschoben haben, können Sie die Quellinstanz löschen, nachdem Sie die Zielinstanz erfolgreich wiederhergestellt und konfiguriert haben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Funktion „Sichern und Wiederherstellen“ finden Sie unter [So implementieren Sie die Notfallwiederherstellung](api-management-howto-disaster-recovery-backup-restore.md).
* Informationen zur Migration von Azure-Ressourcen finden Sie unter [Anleitung zur regionsübergreifenden Azure-Migration](https://github.com/Azure/Azure-Migration-Guidance).
* [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
