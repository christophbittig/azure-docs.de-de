---
title: Verknüpfen von Windows Defender Firewall-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Aktivieren Sie den Windows Firewall-Connector in Azure Sentinel, um auf einfache Weise Firewallereignisse von Windows-Computern mit installierten Log Analytics-Agents zu streamen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 3af0977880adf900171dad6c518f0267a1d9d2a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346284"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Verknüpfen von Windows Defender Firewall über erweiterte Sicherheit mit Azure Sentinel

Der Connector [Windows Defender Firewall mit erweiterter Sicherheit](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) erlaubt es Azure Sentinel, Windows Defender Firewall mit erweiterten Sicherheitsprotokollen von allen Windows-Computern in Ihrem Arbeitsbereich einfach zu erfassen. Durch diese Verbindung können Sie Windows-Firewallereignisse in Ihren Arbeitsmappen anzeigen und analysieren, sie bei der Erstellung benutzerdefinierter Warnmeldungen verwenden und in Ihre Sicherheitsuntersuchungen einbeziehen. Hierdurch können Sie zusätzliche Erkenntnisse über das Netzwerk Ihrer Organisation erlangen und Ihre Möglichkeiten für Sicherheitsvorgänge ausbauen. 

Die Lösung sammelt Windows-Firewallereignisse von den Windows-Computern, auf denen ein Log Analytics-Agent installiert ist. 

> [!NOTE]
> - Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.
>
> - Wenn Azure Defender-Benachrichtigungen aus dem Azure Security Center bereits im Azure Sentinel-Arbeitsbereich gesammelt werden, ist es nicht erforderlich, die Windows-Firewall-Lösung über diesen Connector zu aktivieren. Wenn Sie es trotzdem aktiviert haben, führt dies nicht zu doppelt vorhandenen Daten.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen Lese- und Schreibberechtigungen für den Arbeitsbereich haben, an den die zu überwachenden Computer angeschlossen sind.

- Zusätzlich zu allen **Azure Sentinel**-Rollen muss Ihnen die Rolle **Log Analytics-Mitwirkender** für die SecurityInsights-Lösung in diesem Arbeitsbereich zugewiesen sein. [Weitere Informationen](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Aktivieren des Connectors 

1. Wählen Sie im Navigationsmenü des Azure Sentinel-Portals **Datenconnectors** aus.

1. Wählen Sie aus dem Connectorkatalog die Option **Windows-Firewall** aus, und klicken Sie auf **Connectorseite öffnen**.

### <a name="instructions-tab"></a>Registerkarte „Anweisungen“

- **Wenn sich Ihre Windows-Computer in Azure befinden:**

    1. Wählen Sie **Agent auf virtuellem Windows-Computer (Azure) installieren** aus.

    1. Klicken Sie auf den angezeigten Link **Agent für virtuelle Windows-Computer (Azure) herunterladen und installieren**.

    1. Wählen Sie in der Liste **Virtual machines** (Virtuelle Computer) den Windows-Computer aus, für den Sie an Azure Sentinel streamen möchten. (Sie können **Windows** im Betriebssystem-Spaltenfilter auswählen, um sicherzustellen, dass nur virtuelle Windows-Computer angezeigt werden.)

    1. Klicken Sie in dem Fenster, das für diesen virtuellen Computer wird geöffnet, auf **Verbinden**.

    1. Kehren Sie zum Bereich **Virtuelle Computer** zurück, und wiederholen Sie die vorherigen beiden Schritte für alle anderen VMs, die Sie verbinden möchten. Wenn Sie fertig sind, kehren Sie zum Bereich **Windows-Firewall** zurück.

- **Wenn Ihr Windows-Computer kein virtueller Azure-Computer ist:**

    1. Wählen Sie **Agent auf einem Windows-Computer (kein Azure) installieren** aus.

    1. Klicken Sie auf den angezeigten Link **Agent für Windows-Computer (kein Azure) herunterladen und installieren**.

    1. Wählen Sie im Bereich **Agent-Verwaltung** je nach Bedarf entweder **Windows-Agent herunterladen (64 Bit)** oder **Windows-Agent herunterladen (32 Bit)** aus.

    1. Kopieren Sie die Zeichenfolgen **Arbeitsbereichs-ID**, **Primärschlüssel** und **Sekundärschlüssel** in eine Textdatei. Kopieren Sie diese Datei und die heruntergeladene Installationsdatei auf Ihren Windows-Computer. Führen Sie die Installationsdatei aus, und geben Sie bei Aufforderung während der Installation die ID- und Schlüsselzeichenfolgen in die Textdatei ein.

    1. Kehren Sie zum Bereich **Windows-Firewall** zurück.

1. Klicken Sie auf **Lösung installieren**.

### <a name="next-steps-tab"></a>Registerkarte „Nächste Schritte“

- Sehen Sie sich die verfügbaren empfohlenen Arbeitsmappen und Abfragebeispiele an, die mit dem Datenconnector der **Windows-Firewall** gebündelt sind, um einen Einblick in Ihre Windows-Firewallprotokolldaten zu erhalten.

- Wenn Sie die Windows-Firewalldaten in **Protokolle** abfragen möchten, geben Sie im Abfragefenster **WindowsFirewall** ein.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität
 
Da die Protokolle der Windows-Firewall erst dann an Azure Sentinel gesendet werden, wenn die lokale Protokolldatei ihre Kapazität erreicht hat, führt das Beibehalten der Standardgröße des Protokolls von 4096 KB höchstwahrscheinlich zu einer hohen Sammlungslatenz. Sie können die Latenz verringern, indem Sie die Größe der Protokolldatei verringern. Weitere Informationen finden Sie unter [Konfigurieren des Windows-Firewall-Protokolls](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log).

> [!NOTE]
>
> - Kleinere Protokollgrößen verringern zwar die Sammlungslatenz, können sich aber auch negativ auf die Leistung des lokalen Computers auswirken.
> 
> - Die Konfiguration der Datensammlung erfordert mindestens 1.000 neue Zeilen in der Protokolldatei, bevor Daten gesammelt werden. Daher sollte die Protokolldateigröße auf nicht weniger als 100 KB festgelegt werden, da dadurch die Ansammlung von 1.000 Zeilen sichergestellt wird.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die Windows-Firewall mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
