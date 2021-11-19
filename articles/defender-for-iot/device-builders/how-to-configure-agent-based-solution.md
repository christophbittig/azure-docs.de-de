---
title: Konfigurieren einer Agent-basierten Lösung für Microsoft Defender für IoT
description: Hier erfahren Sie, wie Sie die Datensammlung in einer auf dem Microsoft Defender für IoT-Agent basierenden Lösung konfigurieren.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 65e3febe4a1715ee1a55b377a5c54cbb4f3f88c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306204"
---
# <a name="configure-microsoft-defender-for-iot-agent-based-solution"></a>Konfigurieren einer Agent-basierten Lösung für Microsoft Defender für IoT  

In diesem Artikel wird beschrieben, wie Sie die Datensammlung in einer auf dem Microsoft Defender für IoT-Agent basierenden Lösung konfigurieren.

## <a name="configure-data-collection"></a>Konfigurieren der Datensammlung

So konfigurieren Sie die Datensammlung in einer auf dem Microsoft Defender für IoT-Agent basierenden Lösung 

1. Navigieren Sie zum Azure-Portal, und wählen Sie die IoT Hub-Instanz aus, an die der Defender für IoT angefügt ist. 

1. Wählen Sie im Menü  **Sicherheit**  die Option **Einstellungen** aus. 

1. Wählen Sie  **Datensammlung** aus. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Wählen Sie im Menü „Sicherheit“ in „Einstellungen“ die Option „Datensammlung“ aus.":::

## <a name="geolocation-and-ip-address-handling"></a>Geolocation und Verarbeitung von IP-Adressen 

Um Ihre IoT-Lösung abzusichern, werden standardmäßig die IP-Adressen der ein- und ausgehenden Verbindungen Ihrer IoT-Geräte, IoT Edge- und IoT Hub-Instanzen gesammelt und gespeichert. Diese Informationen sind unverzichtbar und dienen zur Erkennung anormaler Verbindungen mit verdächtigen Quellen von IP-Adressen. So zum Beispiel, wenn Versuche unternommen werden, Verbindungen von einer IP-Adressenquelle eines bekannten Botnets oder von einer IP-Adressenquelle außerhalb Ihrer geografischen Region herzustellen. Mit dem Dienst „Defender für IoT“ kann die Sammlung von IP-Adressdaten jederzeit flexibel aktiviert oder deaktiviert werden. 

So aktivieren oder deaktivieren Sie die Sammlung von IP-Adressdaten 

1. Öffnen Sie Ihre IoT Hub-Instanz, und wählen Sie im Menü  **Sicherheit**  die Option  **Einstellungen**  aus. 

1. Wählen Sie den Bildschirm **Datensammlung**  aus, und ändern Sie die Einstellungen für Geolocation und Behandlung von IP-Adressen gemäß Ihren Anforderungen. 

## <a name="log-analytics-creation"></a>Log Analytics-Erstellung 

Defender für IoT ermöglicht das Speichern von Sicherheitswarnungen, Empfehlungen und Sicherheitsrohdaten in Ihrem Log Analytics-Arbeitsbereich. Die Log Analytics-Erfassung in IoT Hub ist in der Defender für IoT-Lösung standardmäßig **deaktiviert**. Es ist möglich, Defender für IoT an einen Log Analytics-Arbeitsbereich anzufügen und die Sicherheitsdaten auch in diesem zu speichern. 

Es gibt zwei Arten von Informationen, die standardmäßig in Ihrem Log Analytics-Arbeitsbereich von Defender für IoT gespeichert werden:
 
- Sicherheitswarnungen.

- Empfehlungen. 

Sie können noch einen weiteren Informationstyp hinzufügen, nämlich `raw events`. 

> [!Note] 
> Für die Speicherung von  `raw events`  in Log Analytics fallen zusätzliche Speicherkosten an. 

So aktivieren Sie Log Analytics für die Zusammenarbeit mit Micro-Agent 

1. Navigieren Sie zu **Arbeitsbereichskonfiguration** > **Datensammlung**, und ändern Sie den Umschalter in  **Ein**. 

1. Erstellen Sie einen neuen Log Analytics-Arbeitsbereich, oder fügen Sie einen bereits vorhandenen an. 

1. Stellen Sie sicher, dass die Option  **Access raw security data**  (Auf Sicherheitsrohdaten zugreifen) ausgewählt ist.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Stellen Sie sicher, dass die Option „Access raw security data“ (Auf Sicherheitsrohdaten zugreifen) ausgewählt ist.":::

1. Wählen Sie  **Speichern** aus.

Jeden Monat sind die ersten 5 Gigabyte Daten, die pro Kunde im Azure Log Analytics-Dienst erfasst werden, kostenlos. Jedes GB an Daten, das in Ihrem Azure Log Analytics-Arbeitsbereich erfasst wird, wird die ersten 31 Tage kostenlos aufbewahrt. Weitere Informationen zu Preisen finden Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/monitor/). 

So ändern Sie die Arbeitsbereichskonfiguration von Log Analytics: 

1. Wählen Sie in Ihrer IoT Hub-Instanz im Menü **Sicherheit** die Option  **Einstellungen** aus. 

1. Wählen Sie den Bildschirm  **Datensammlung**  aus, und ändern Sie die Arbeitsbereichskonfiguration der Log Analytics-Einstellungen entsprechend Ihren Anforderungen. 

So greifen Sie nach der Konfiguration auf Warnungen in Ihrem Log Analytics-Arbeitsbereich zu

1. Wählen Sie in Defender für IoT eine Warnung aus.

1. Wählen Sie **Investigate alerts in Log Analytics workspace** (Warnungen im Log Analytics-Arbeitsbereich untersuchen) aus.

So greifen Sie nach der Konfiguration auf Warnungen in Ihrem Log Analytics-Arbeitsbereich zu

1. Wählen Sie eine Empfehlung in Defender für IoT aus.

1. Wählen Sie **Investigate recommendations in Log Analytics workspace** (Empfehlungen im Log Analytics-Arbeitsbereich untersuchen) aus. 
 
Weitere Informationen zum Abfragen von Daten aus Log Analytics finden Sie unter  [Erste Schritte mit Protokollabfragen in Azure Monitor](../../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>So deaktivieren Sie Defender für IoT 

So aktivieren oder deaktivieren Sie den Dienst „Defender für IoT“ für eine bestimmte IoT Hub-Instanz 

1. Wählen Sie in Ihrer IoT Hub-Instanz im Menü **Sicherheit** die Option  **Einstellungen** aus.

1. Wählen Sie den Bildschirm  **Datensammlung**  aus, und ändern Sie die Arbeitsbereichskonfiguration der Log Analytics-Einstellungen entsprechend Ihren Anforderungen.

## <a name="next-steps"></a>Nächste Schritte 

Im nächsten Artikel erfahren Sie, wie Sie [Ihre Lösung konfigurieren](quickstart-configure-your-solution.md).
