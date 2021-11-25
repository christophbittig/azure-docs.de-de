---
title: Einrichten kundenseitig verwalteter Schlüssel in Microsoft Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMK) in Microsoft Sentinel einrichten.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0b172d2af5393c71acb9ea25e1b2d3df6091276
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522914"
---
# <a name="set-up-microsoft-sentinel-customer-managed-key"></a>Einrichten kundenseitig verwalteter Schlüssel in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Artikel enthält Hintergrundinformationen und Schritte zum Konfigurieren eines [kundenseitig verwalteten Schlüssels](../azure-monitor/logs/customer-managed-keys.md) (Customer-Managed Key, CMK) für Microsoft Sentinel. Mit dem CMK können Sie allen in Microsoft Sentinel gespeicherten Daten, die bereits von Microsoft in allen relevanten Speicherressourcen verschlüsselt wurden, eine zusätzliche Schutzebene mit einem Verschlüsselungsschlüssel gewähren, der von Ihnen erstellt wurde, sich in Ihrem Besitz befindet und in Ihrer [Azure Key Vault-Instanz](../key-vault/general/overview.md) gespeichert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Die CMK-Fähigkeit erfordert einen dedizierten Log Analytics-Cluster mit einer Verpflichtungsstufe von mindestens 500 GB/Tag. Mehrere Arbeitsbereiche können mit demselben dedizierten Cluster verbunden werden und teilen sich denselben vom Kunden verwalteten Schlüssel.

- Nachdem Sie die Schritte in diesem Leitfaden ausgeführt haben und bevor Sie den Arbeitsbereich verwenden, wenden Sie sich zur Bestätigung des Onboardings an die [Microsoft Sentinel Produktgruppe](mailto:azuresentinelCMK@microsoft.com).

- Weitere Informationen finden Sie unter [Preise für dedizierte Log Analytics-Cluster](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model).

## <a name="considerations"></a>Weitere Überlegungen

- Das Onboarding eines CMK-Arbeitsbereichs in Sentinel wird nur über die REST-API und nicht über das Azure-Portal unterstützt. Azure Resource Manager-Vorlagen (ARM-Vorlagen) werden für das CMK-Onboarding derzeit nicht unterstützt.

- Die CMK-Funktion von Microsoft Sentinel wird nur für *Arbeitsbereiche in dedizierten Log Analytics-Clustern* bereitgestellt, die *noch nicht in Microsoft Sentinel integriert wurden*.

- Die folgenden CMK-bezogenen Änderungen *werden nicht unterstützt*, da sie unwirksam sind (Microsoft Sentinel-Daten werden weiterhin nur mit dem von Microsoft verwalteten Schlüssel und nicht vom CMK verschlüsselt):

  - Aktivieren eines CMK in einem Arbeitsbereich, der bereits in Microsoft Sentinel *integriert ist*
  - Aktivieren eines CMK in einem Cluster, der in Sentinel integrierte Arbeitsbereiche enthält
  - Verknüpfen eines in Sentinel integrierten Nicht-CMK-Arbeitsbereichs mit einem CMK-fähigen Cluster

- Die folgenden CMK-bezogenen Änderungen *werden nicht unterstützt*, da sie zu nicht definierten und problematischen Verhaltensweisen führen können:

  - Deaktivieren eines CMK in einem Arbeitsbereich, der bereits in Microsoft Sentinel integriert ist
  - Festlegen eines in Sentinel integrierten, CMK-fähigen Arbeitsbereichs als Nicht-CMK-Arbeitsbereich, indem die Verknüpfung mit seinem CMK-fähigen dedizierten Cluster aufgehoben wird
  - Deaktivieren des CMK in einem CMK-fähigen dedizierten Log Analytics-Cluster

- Microsoft Sentinel unterstützt vom System zugewiesene Identitäten in der CMK-Konfiguration. Daher sollte die Identität des dedizierten Log Analytics-Clusters vom Typ **systemseitig zugewiesen** sein. Es wird empfohlen, die Identität zu verwenden, die dem Log Analytics-Cluster automatisch beim Erstellen zugewiesen wird.

- Die Änderung des kundenseitig verwalteten Schlüssels in einen anderen Schlüsseltyp (mit einer anderen URI) wird *derzeit nicht unterstützt*. Sie sollten den Schlüssel ändern, indem Sie ihn [rotieren](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

- Bevor Sie CMK-Änderungen an einem Produktionsarbeitsbereich oder einem Log Analytics-Cluster vornehmen, wenden Sie sich an die [Microsoft Sentinel-Produktgruppe](mailto:azuresentinelCMK@microsoft.com).

## <a name="how-cmk-works"></a>Funktionsweise von CMK 

Die Microsoft Sentinel-Lösung nutzt eine Reihe von Speicherressourcen für die Protokollerfassung und -features, einschließlich eines dedizierten Log Analytics-Clusters. Im Rahmen der Microsoft Sentinel-CMK-Konfiguration müssen auch die CMK-Einstellungen für die zugehörigen dedizierten Log Analytics-Cluster konfiguriert werden. Daten, die von Microsoft Sentinel in anderen Speicherressourcen als Log Analytics gespeichert werden, werden auch mithilfe des für den dedizierten Log Analytics-Cluster konfigurierten kundenseitig verwalteten Schlüssel verschlüsselt.

Weitere Informationen finden Sie in der folgenden relevanten Dokumentation:
- [Kundenseitig verwaltete Schlüssel (CMK) in Azure Monitor](../azure-monitor/logs/customer-managed-keys.md)
- [Azure Key Vault](../key-vault/general/overview.md).
- [Dedizierte Log Analytics-Cluster](../azure-monitor/logs/logs-dedicated-clusters.md)

> [!NOTE]
> Wenn Sie CMK in Microsoft Sentinel aktivieren, werden Features der öffentlichen Vorschau ohne CMK-Unterstützung nicht aktiviert.

## <a name="enable-cmk"></a>Aktivieren von CMK 

Gehen Sie zum Bereitstellen von CMK wie folgt vor: 

1.  Erstellen Sie eine Azure Key Vault-Instanz, und generieren bzw. importieren Sie einen Schlüssel.

2.  Aktivieren Sie CMK in Ihrem Log Analytics-Arbeitsbereich.

3.  Registrieren Sie sich beim Cosmos DB-Ressourcenanbieter.

4.  Fügen Sie Ihrer Azure Key Vault-Instanz eine Zugriffsrichtlinie hinzu.

5.  Integrieren Sie den Arbeitsbereich über die [Onboarding-API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx) in Microsoft Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-generate-or-import-a-key"></a>Schritt 1: Erstellen einer Azure Key Vault-Instanz und Generieren bzw. Importieren eines Schlüssels

1. [Erstellen Sie eine Azure Key Vault-Ressource](/azure-stack/user/azure-stack-key-vault-manage-portal), und generieren oder importieren Sie einen Schlüssel für die Datenverschlüsselung.

    > [!NOTE]
    >  Azure Key Vault muss als wiederherstellbar konfiguriert werden, um Ihren Schlüssel und den Zugriff zu schützen.

1.  [Aktivieren Sie die Wiederherstellungsoptionen:](../key-vault/general/key-vault-recovery.md)

    -   Stellen Sie sicher, dass [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) aktiviert ist.

    -   Aktivieren Sie den [Bereinigungsschutz](../key-vault/general/soft-delete-overview.md#purge-protection) zum Schutz vor erzwungenem Löschen des Geheimnisses/Tresors (auch nach vorläufigem Löschen).

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>SCHRITT 2: Aktivieren von CMK in Ihrem Log Analytics-Arbeitsbereich

Gehen Sie wie unter [Konfiguration kundenseitig verwalteter Schlüssel in Azure Monitor](../azure-monitor/logs/customer-managed-keys.md) beschrieben vor, um einen CMK-Arbeitsbereich zu erstellen, der in den folgenden Schritten als Microsoft Sentinel-Arbeitsbereich verwendet wird.

### <a name="step-3-register-to-the-cosmos-db-resource-provider"></a>Schritt 3: Durchführen der Registrierung beim Cosmos DB-Ressourcenanbieter

Microsoft Sentinel arbeitet mit Cosmos DB als zusätzliche Speicherressource. Registrieren Sie sich daher unbedingt beim Cosmos DB-Ressourcenanbieter.

Befolgen Sie die Anweisungen für Cosmos DB zum [Registrieren des Azure Cosmos DB-Ressourcenanbieters](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) für Ihr Azure-Abonnement.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>SCHRITT 4: Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault-Instanz

Stellen Sie sicher, dass von Cosmos DB aus auf Ihre Azure Key Vault-Instanz zugegriffen werden kann. [Fügen Sie Ihrer Azure Key Vault-Instanz eine Zugriffsrichtlinie mit Azure Cosmos DB-Prinzipal hinzu](../cosmos-db/how-to-setup-cmk.md#add-access-policy), wie in der Cosmos DB-Anleitung beschrieben.

### <a name="step-5-onboard-the-workspace-to-microsoft-sentinel-via-the-onboarding-api"></a>SCHRITT 5: Integrieren des Arbeitsbereichs über die Onboarding-API in Microsoft Sentinel

Integrieren Sie den Arbeitsbereich über die [Onboarding-API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx) in Microsoft Sentinel.

## <a name="key-encryption-key-revocation-or-deletion"></a>Wiederruf oder Löschung von Schlüsselverschlüsselungsschlüsseln

Wenn ein Benutzer den Schlüsselverschlüsselungsschlüssel (den CMK) widerruft, indem er ihn entweder löscht oder den Zugriff für den dedizierten Cluster und den Cosmos DB-Ressourcenanbieter entfernt, berücksichtigt Microsoft Sentinel diese Änderung binnen einer Stunde und verhält sich so, als wären die Daten nicht mehr verfügbar. Daraufhin werden alle Vorgänge unterbunden, für die beständige Speicherressourcen verwendet werden (beispielsweise Datenerfassung, beständige Konfigurationsänderungen und Incident-Erstellung). Zuvor gespeicherte Daten werden zwar nicht gelöscht, sind aber nicht mehr zugänglich. Daten, auf die nicht zugegriffen werden kann, unterliegen der Datenaufbewahrungsrichtlinie und werden gemäß dieser Richtlinie bereinigt.

Die Kontolöschung ist der einzige Vorgang, der nach dem Widerruf des Verschlüsselungsschlüssels noch möglich ist.

Wird der Zugriff nach dem Widerruf wiederhergestellt, stellt Microsoft Sentinel den Zugriff auf die Daten binnen einer Stunde wieder her.

Der Zugriff auf die Daten kann widerrufen werden, indem der kundenseitig verwaltete Schlüssel im Schlüsseltresor deaktiviert oder die Zugriffsrichtlinie für den Schlüssel sowohl für den dedizierten Log Analytics-Cluster als auch für Cosmos DB gelöscht wird. Das Widerrufen des Zugriffs durch Entfernen des Schlüssels aus dem dedizierten Log Analytics-Cluster oder durch Entfernen der Identität, die dem dedizierten Log Analytics-Cluster zugeordnet ist, wird nicht unterstützt.

Weitere Informationen zur Funktionsweise in Azure Monitor finden Sie unter [CMK-Widerruf (KEK)](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="customer-managed-key-rotation"></a>Rotieren des kundenseitig verwalteten Schlüssels

Die Schlüsselrotation wird von Microsoft Sentinel und Log Analytics unterstützt. Wenn ein Benutzer eine Schlüsselrotation in Key Vault vornimmt, wird der neue Schlüssel binnen einer Stunde von Microsoft Sentinel unterstützt.

In Key Vault können Sie zur Schlüsselrotation eine neue Version des Schlüssels erstellen:

![Schlüsselrotation](./media/customer-managed-keys/key-rotation.png)

Sie können die vorherige Version des Schlüssels nach 24 Stunden deaktivieren oder nachdem in den Azure Key Vault-Überwachungsprotokollen keine Aktivität mit der alten Version mehr vorkommen.

Nach dem Rotieren eines Schlüssels müssen Sie die dedizierte Log Analytics-Clusterressource in Log Analytics explizit mit der neuen Azure Key Vault-Schlüsselversion aktualisieren. Weitere Informationen finden Sie unter [CMK-Rotation (KEK)](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="replacing-a-customer-managed-key"></a>Ersetzen eines kundenseitig verwalteten Schlüssels

Microsoft Sentinel unterstützt das Ersetzen eines kundenseitig verwalteten Schlüssels nicht. Verwenden Sie stattdessen die [Schlüsselrotationsfunktion](#customer-managed-key-rotation).

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie gelernt, wie Sie einen kundenseitig verwalteten Schlüssel in Microsoft Sentinel einrichten. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](./detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
