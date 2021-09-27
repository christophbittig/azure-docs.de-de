---
title: 'Azure Communication Services: Aktivieren der und Zugreifen auf die Anrufzusammenfassung und Anrufdiagnoseprotokolle'
titleSuffix: An Azure Communication Services concept document
description: Zugreifen auf die Anrufzusammenfassung und Anrufdiagnoseprotokolle in Azure Monitor
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: b5c2da9842ba21b63c7b36d5b7377f74a25a1e90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672271"
---
# <a name="enable-and-access-call-summary-and-call-diagnostic-logs"></a>Aktivieren von und Zugreifen auf die Aufrufzusammenfassung und Anrufdiagnoseprotokolle

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Führen Sie die hier angegebenen Schritte aus, um auf die Telemetriedaten für Sprach- und Videoressourcen von Azure Communication Services zuzugreifen.

## <a name="enable-logging"></a>Aktivieren der Protokollierung
1. Zunächst müssen Sie ein Speicherkonto für Ihre Protokolle erstellen. Eine Anleitung zu diesem Schritt finden Sie unter [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md?tabs=azure-portal). Weitere Informationen zu den Typen und Features der unterschiedlichen Speicheroptionen finden Sie auch unter [Speicherkontoübersicht](../../storage/common/storage-account-overview.md). Fahren Sie mit Schritt 2 fort, falls Sie bereits über ein Azure-Speicherkonto verfügen.
 
1. Nach der Erstellung Ihres Speicherkontos müssen Sie als Nächstes die Protokollierung aktivieren, indem Sie die Anleitung unter [Aktivieren von Diagnoseprotokollen in der Ressource](./logging-and-diagnostics.md#enable-diagnostic-logs-in-your-resource) befolgen. Sie aktivieren die Kontrollkästchen für die Protokolle „CallSummaryPRIVATEPREVIEW“ und „CallDiagnosticPRIVATEPREVIEW“. 

1. Aktivieren Sie als Nächstes das Feld „In ein Speicherkonto archivieren“, und wählen Sie dann unten im Dropdownmenü das Speicherkonto für Ihre Protokolle aus. Die Option „An Analytics-Arbeitsbereich senden“ ist derzeit für die private Vorschauversion dieses Features nicht verfügbar. Sie wird bereitgestellt, wenn das Feature öffentlich verfügbar gemacht wird.

:::image type="content" source="media\call-logs-images\call-logs-access-diagnostic-setting.png" alt-text="Azure Monitor-Diagnoseeinstellung":::



## <a name="access-your-logs"></a>Zugreifen auf Ihre Protokolle

Navigieren Sie zum Zugreifen auf Ihre Protokolle zu dem Speicherkonto, das Sie oben in Schritt 3 angegeben haben, indem Sie im Azure-Portal auf [Speicherkonten](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) zugreifen. 

:::image type="content" source="media\call-logs-images\call-logs-access-storage.png" alt-text="Azure-Portal: Speicher":::

Dort können Sie alle oder nur bestimmte Protokolle herunterladen.

:::image type="content" source="media\call-logs-images\call-logs-access-storage-resource.png" alt-text="Azure-Portal: Speicherdownload":::

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Protokollierung und Diagnose](./logging-and-diagnostics.md).