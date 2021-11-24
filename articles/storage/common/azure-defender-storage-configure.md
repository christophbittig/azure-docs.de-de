---
title: Konfigurieren von Microsoft Defender für Storage (Speicher)
titleSuffix: Azure Storage
description: Konfigurieren Sie Azure Defender für Storage, um Anomalien in Bezug auf die Kontoaktivität zu erkennen und um Benachrichtigungen über potenziell schädliche Zugriffsversuche auf Ihr Konto zu erhalten.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 17cc82bd448910cc92eec42db889605c48b65fb5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297010"
---
# <a name="configure-microsoft-defender-for-storage"></a>Konfigurieren von Microsoft Defender für Storage (Speicher)

Microsoft Defender für Storage stellt eine zusätzliche Ebene der Sicherheitsanalyse bereit, die ungewöhnliche und potenziell bedrohliche Versuche erkennt, auf Speicherkonten zuzugreifen oder diese unbefugt zu nutzen. Aufgrund dieser Schutzebene können Sie Bedrohungen begegnen, ohne ein Sicherheitsexperte zu sein oder Systeme für die Überwachung der Sicherheit verwalten zu müssen.

Bei Anomalien im Rahmen von Aktivitäten werden Sicherheitswarnungen ausgelöst. Diese Sicherheitswarnungen sind in [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/) integriert und werden mit Informationen zu verdächtigen Aktivitäten und Empfehlungen zur Untersuchung und Beseitigung dieser Bedrohungen auch per E-Mail an Abonnementadministratoren gesendet.

Der Dienst erfasst Ressourcenprotokolle von an Blob Storage und Azure Files gesendete Lese-, Schreib- und Löschanforderungen, um Bedrohungen zu erkennen. Wenn Sie Warnungen von Microsoft Defender für Cloud untersuchen möchten, können Sie mithilfe von Storage Analytics Logging (Speicheranalyseprotokoll) die entsprechende Speicheraktivitäten anzeigen. Weitere Informationen finden Sie unter **Konfigurieren der Protokollierung** in [Überwachen eines Speicherkontos im Azure-Portal](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Verfügbarkeit

Microsoft Defender für Storage ist derzeit für Blob Storage (Blob-Speicher), Azure Files und den Azure-Datenbankspeicher (Data Lake Storage) Gen2 verfügbar. Zu den Kontotypen, die Microsoft Defender für Storage unterstützen, gehören Konten vom Typ „Allgemein v2“ sowie Blockblob- und Blob-Speicher-Konten. Microsoft Defender für Storage ist in allen öffentlichen Clouds und in Clouds der US-Regierungsbehörden verfügbar, nicht aber in anderen souveränen oder anderen Azure-Government-Cloudregionen.

Konten mit für Data Lake Storage aktivierten hierarchischen Namespaces unterstützen Transaktionen, die sowohl die Azure Blob Storage-APIs als auch die Data Lake Storage-APIs verwenden. Azure-Dateifreigaben unterstützen Transaktionen über SMB.

Informationen zum Preis, einschließlich einer kostenlosen 30-Tage-Testversion, finden Sie auf der [Seite mit der Preisübersicht zu Microsoft Defender für Cloud](https://azure.microsoft.com/pricing/details/security-center/).

Die folgende Liste biete eine Übersicht über die Verfügbarkeit von Microsoft Defender für Storage:

- Status des Release:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (allgemeine Verfügbarkeit)
  - [Azure Files](../files/storage-files-introduction.md) (allgemeine Verfügbarkeit)
  - Azure Data Lake Storage Gen2 (allgemeine Verfügbarkeit)
- Clouds: ✔ Kommerzielle Clouds<br>
    ✔ Azure Government<br>
    ✘ Azure China 21Vianet

## <a name="set-up-microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud einrichten

Sie können Microsoft Defender für Storage auf die jeweils verschiedenen Arten konfigurieren, die in den folgenden Abschnitten beschrieben werden.

### <a name="microsoft-defender-for-cloud"></a>[Microsoft Defender für Cloud](#tab/azure-security-center)

Microsoft Defender für Storage ist in Microsoft Defender für Cloud integriert. Wenn Sie die erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud in Ihrem Abonnement aktivieren, wird Microsoft Defender für Storage automatisch für alle Ihre Speicherkonten aktiviert. So aktivieren oder deaktivieren Sie Defender für Storage für einzelne Speicherkonten in einem bestimmten Abonnement:

1. Starten Sie im [Azure-Portal](https://portal.azure.com) den Dienst **Microsoft Defender für Cloud**.
1. Wählen Sie im Hauptmenü von Defender für Cloud die Option **Umgebungseinstellungen** aus.
1. Wählen Sie das Abonnement aus, für das Sie Microsoft Defender für Cloud aktivieren bzw. deaktivieren möchten.
1. Wählen Sie **Alle Microsoft Defender-Pläne aktivieren** aus, um Microsoft Defender für Cloud im Abonnement zu aktivieren.
1. Suchen Sie unter **Azure Defender-Pläne nach Ressourcentyp auswählen** nach der Zeile **Speicher** und wählen Sie in der Spalte **Plan** die Option **Aktiviert** aus.
1. Speichern Sie die Änderungen.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Screenshot: Aktivieren von Microsoft Defender für Storage.":::

Microsoft Defender für Storage ist nun für alle Speicherkonten in diesem Abonnement aktiviert.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Starten Sie das [Azure-Portal](https://portal.azure.com/).
1. Navigieren Sie zum Speicherkonto. Wählen Sie unter **Einstellungen** die Option **Erweiterte Sicherheit** aus.
1. Wählen Sie **Microsoft Defender für Storage aktivieren** aus.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Screenshot: Aktivieren eines Kontos für Microsoft Defender für Storage.":::

Microsoft Defender für Speicher ist jetzt für dieses Speicherkonto aktiviert.​

### <a name="template"></a>[Vorlage](#tab/template)

Verwenden Sie zur Bereitstellung eines Azure Storage-Kontos, bei dem Microsoft Defender für Storage aktiviert ist, eine Azure Ressourcen-Manager-Vorlage. Weitere Informationen finden Sie unter [Storage-Konto mit Advanced Threat Protection](https://azure.microsoft.com/resources/templates/storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Verwenden Sie eine Azure-Richtlinie (Azure Policy) zum Aktivieren von Microsoft Defender für Cloud bei Speicherkonten in einem bestimmten Abonnement oder einer bestimmten Ressourcengruppe.

1. Starten Sie die Azure-Seite **Richtlinie – Definitionen**.
1. Suchen Sie nach der Richtlinie **Microsoft Defender-für-Storage-Konten bereitstellen**.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Anwenden einer Richtlinie zum Aktivieren von Microsoft Defender-für-Storage-Konten":::

1. Wählen Sie ein Azure-Abonnement oder eine Ressourcengruppe aus.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Auswählen eines Abonnements oder einer Ressourcengruppe für den Umfang der Richtlinie":::

1. Weisen Sie die Richtlinie zu.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Zuweisen einer Richtlinie zum Aktivieren von Microsoft Defender-für-Storage-Konten":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um Microsoft Defender für Storage für ein Speicherkonto mit PowerShell zu aktivieren, stellen Sie zunächst sicher, dass Sie das Modul [Az.Security](https://www.powershellgallery.com/packages/Az.Security) installiert haben. Rufen Sie dann den Befehl [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) auf. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

Um die Einstellung von Microsoft Defender für Storage für ein Speicherkonto mit PowerShell zu überprüfen, rufen Sie den Befehl [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) auf. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um Microsoft Defender für Storage für ein Speicherkonto mit Azure CLI zu aktivieren, rufen Sie den Befehl [az security atp storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update) auf. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Um die Einstellung von Microsoft Defender für Storage für ein Speicherkonto mit Azure CLI zu überprüfen, rufen Sie den Befehl [az security atp storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) auf. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Untersuchen von sicherheitsrelevanten Anomalien

Wenn für die Speicheraktivität Anomalien auftreten, erhalten Sie eine E-Mail-Benachrichtigung mit Informationen zum verdächtigen Sicherheitsereignis. Einzelheiten des Ereignisses sind:

- Art der Anomalie
- Speicherkontoname
- Ereigniszeit
- Speichertyp
- Mögliche Ursachen
- Untersuchungsschritte
- Schritte zur Bereinigung

Die E-Mail enthält auch Details zu möglichen Ursachen und empfohlenen Aktionen zur Untersuchung und Eindämmung der potenziellen Bedrohung.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="E-Mail mit einer Warnung in Microsoft Defender für Storage":::

Sie können Ihre aktuellen Sicherheitswarnungen über die [Kachel Sicherheitswarnungen](../../security-center/security-center-managing-and-responding-alerts.md) von Microsoft Defender für Cloud anzeigen und verwalten. Durch Klicken auf eine Warnung werden Informationen und Aktionen zum Untersuchen der aktuellen Bedrohung und zum Begegnen zukünftiger Bedrohungen bereitgestellt.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Warnung in Microsoft Defender für Speicher":::

## <a name="security-alerts"></a>Sicherheitswarnungen

Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploit-Vorgängen für Speicherkonten generiert. Eine Liste mit Warnungen für Azure Storage finden Sie unter [Warnungen für Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Microsoft Defender für Storage](../../security-center/defender-for-storage-introduction.md)
- [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md)
- [Protokolle in Azure Storage-Konten](/rest/api/storageservices/About-Storage-Analytics-Logging)
