---
title: Verknüpfen von Daten des F5-ASM mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem ASM-Datenconnector von F5 F5-ASM-Protokolle in Azure Sentinel pullen können. Zeigen Sie F5-ASM-Daten in Arbeitsmappen an, Erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: db79aaa5f949d60d79e2500d0d282c2f5909bf32
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694025"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Verbinden des F5-ASM mit Azure Sentinel

In diesem Artikel wird beschrieben, wie Sie mit dem ASM-Datenconnector von F5 problemlos F5-ASM-Protokolle in Azure Sentinel abrufen. Dies ermöglicht es Ihnen, F5-ASM-Daten in Arbeitsmappen anzuzeigen, sie zum Erstellen benutzerdefinierter Warnungen zu verwenden und zur Verbesserung der Untersuchung zu integrieren. Mit den Daten des F5-ASM in Azure Sentinel erhalten Sie mehr Einblicke in die Webanwendungssicherheit Ihrer Organisation und können die Sicherheitsfunktionen verbessern. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Konfigurieren des F5-ASM zum Senden von CEF-Nachrichten

1. Befolgen Sie die Anleitung im F5-Artikel [Konfigurieren der Ereignisprotokollierung für die Anwendungssicherheit](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) zum Einrichten der Remoteprotokollierung anhand der folgenden Richtlinien:
   - Legen Sie **Remote storage type** auf **CEF** fest.
   - Legen Sie das **Protokoll** auf **TCP** fest.
   - Legen Sie **IP address** auf die IP-Adresse des Syslog-Servers fest.
   - Legen Sie **port number** auf **514** oder den Port fest, den Sie für Ihren Agent angegeben haben.
   - Sie können die maximale Größe von Abfragezeichenfolgen (**Maximum Query String Size**) auf die Größe festlegen, die Sie in Ihrem Agent angegeben haben.

1. Um das relevante Schema in Log Analytics für CEF-Ereignisse zu verwenden, suchen Sie nach `CommonSecurityLog`.

1. Fahren Sie mit dem[Überprüfen der Konnektivität](troubleshooting-cef-syslog.md#validate-cef-connectivity) fort.


## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie den F5-ASM mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.