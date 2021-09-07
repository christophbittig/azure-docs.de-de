---
title: Übersicht über den Defender für IoT-Micro-Agent für Azure RTOS
description: Erfahren Sie mehr über die Unterstützung und Implementierung des Defender für IoT-Micro-Agents für Azure RTOS als Teil von Azure Defender für IoT.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: cf905eb9cff7840eeaac70e731421ba2252193e6
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303650"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos"></a>Übersicht: Defender für IoT-Micro-Agent für Azure RTOS

Das Azure Defender für IoT-Micro-Modul bietet eine umfassende Sicherheitslösung für Geräte, die Azure RTOS verwenden. Es deckt allgemeine Bedrohungen und potenziell schädliche Aktivitäten auf Echtzeitbetriebssystem-Geräten (Real-Time Operating System, RTOS) ab. Azure RTOS wird nun mit integriertem Defender für IoT-Micro-Agent bereitgestellt.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualisierung von Defender für IoT Azure RTOS":::


Micro-Modul für Azure RTOS bietet die folgenden Funktionen:

- Erkennung schädlicher Netzwerkaktivitäten
- Auf benutzerdefinierten Warnungen basierende Basislinienüberwachung für Geräteverhalten
- Verbesserte Gerätesicherheit

## <a name="detect-malicious-network-activities"></a>Erkennung bösartiger Netzwerkaktivitäten

Die eingehenden und ausgehenden Netzwerkaktivitäten jedes Geräts werden überwacht. Die Protokolle TCP, UDP und ICMP auf IPv4 und IPv6 werden unterstützt. Defender für IoT überprüft jede dieser Netzwerkaktivitäten anhand des Microsoft Threat Intelligence-Feeds. Der Feed wird in Echtzeit mit Millionen eindeutiger Bedrohungsindikatoren aktualisiert, die weltweit gesammelt werden.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Auf benutzerdefinierten Warnungen basierende Basislinienüberwachung für Geräteverhalten

Die Basislinienüberwachung ermöglicht das Clustering von Geräten in Sicherheitsgruppen und das Festlegen des erwarteten Verhaltens jeder Gruppe. Da IoT-Geräte in der Regel in gut definierten und begrenzten Szenarios betrieben werden, ist es einfach, eine Basislinie zu erstellen, die das erwartete Verhalten mithilfe eines Satzes von Parametern definiert. Jede Abweichung von der Basislinie löst eine Warnung aus.

## <a name="improve-your-device-security-hygiene"></a>Verbesserung der Gerätesicherheit

Wenn Sie die empfohlene Infrastruktur nutzen, die von Defender für IoT bereitgestellt wird, können Sie Informationen über Ihre Umgebung und Einblicke in die Probleme erhalten, die sich auf die Sicherheit Ihrer Geräte auswirken und diese schädigen. Wenn ein schwacher IoT-Geräte-Sicherheitsstatus unverändert bleibt, kann dies den Erfolg möglicher Angriffe bedeuten. Sicherheit wird immer am schwächsten Kettenglied innerhalb einer Organisation gemessen.

## <a name="get-started-protecting-azure-rtos-devices"></a>Erste Schritte zum Schutz von Azure RTOS-Geräten

Der Defender für IoT-Micro-Agent für Azure RTOS steht als kostenloser Download für Ihre Geräte zur Verfügung. Der Defender für IoT-Clouddienst ist mit einer 30-tägigen Testversion pro Azure-Abonnement verfügbar. Informationen zu den ersten Schritten finden Sie unter [Defender für IoT-Micro-Agent für Azure RTOS](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/device-builders/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über den Defender für IoT-Micro-Agent für Azure RTOS erfahren. Weitere Informationen zum Defender für IoT-Micro-Agent und den ersten Schritten damit finden Sie in den folgenden Artikeln:

- [Konzepte im Zusammenhang mit dem Defender für IoT-Micro-Agent für Azure RTOS](concept-rtos-security-module.md)
- [Schnellstart: Defender für IoT-Micro-Agent für Azure RTOS](quickstart-azure-rtos-security-module.md)
