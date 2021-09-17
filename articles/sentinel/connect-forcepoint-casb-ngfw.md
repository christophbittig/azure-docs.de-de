---
title: Verbinden von Forcepoint-Produkten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Forcepoint-Produkte mit Azure Sentinel verbinden.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: b0b734bcc540651debad35606be496fdc4069b3c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695110"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Verbinden von Forcepoint-Produkten mit Azure Sentinel

> [!IMPORTANT]
> Der Datenconnector für Forcepoint-Produkte in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

In diesem Artikel wird erläutert, wie Sie Ihre Forcepoint-Produkte mit Azure Sentinel verbinden. 

Die Forcepoint-Datenconnectors ermöglichen das Verbinden von Forcepoint Cloud Access Security Broker und Forcepoint Next Generation Firewall-Protokolle mit Azure Sentinel. Auf diese Weise können Sie automatisch benutzerdefinierte Protokolle in Echtzeit in Azure Sentinel exportieren. Der Connector ermöglicht eine angereicherte Ansicht von Benutzeraktivitäten, die von Forcepoint-Produkten aufgezeichnet wurden. Er ermöglicht außerdem eine weitere Korrelation mit Daten aus Azure-Workloads und anderen Feeds und verbessert mit Workbooks in Azure Sentinel die Überwachungsfunktionalität.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Weiterleiten von Protokollen von Forcepoint-Produkten an den Syslog-Agent 

Konfigurieren Sie das Forcepoint-Produkt so, dass Syslog-Nachrichten im CEF-Format über den Syslog-Agent an Ihren Azure-Arbeitsbereich weitergeleitet werden.

1. Richten Sie, wie in den folgenden Installationsanweisungen beschrieben, das Forcepoint-Produkt für die Integration mit Azure Sentinel ein:
 - [Forcepoint CASB und Azure Sentinel – Integrationshandbuch](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW und Azure Sentinel – Integrationshandbuch](https://frcpnt.com/ngfw-sentinel)

2. Suchen Sie nach CommonSecurityLog, um das entsprechende Schema in Log Analytics mit dem Geräteanbieternamen, der „Forcepoint“ enthält, zu verwenden. 

3. Fahren Sie mit dem [Überprüfen der Konnektivität](troubleshooting-cef-syslog.md#validate-cef-connectivity) fort.



## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie Forcepoint-Produkte mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).

- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).

- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.