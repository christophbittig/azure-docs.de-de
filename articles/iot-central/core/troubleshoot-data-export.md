---
title: Problembehandlung bei Datenexporten aus Azure IoT Central | Microsoft-Dokumentation
description: Behandeln von Problemen mit Datenexporten in IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 0fd283c1f3740e3e06f7a41cc66874596159e8f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096153"
---
# <a name="troubleshoot-issues-with-data-exports-from-your-azure-iot-central-application"></a>Behandeln von Problemen mit Datenexporten aus Ihrer Azure IoT Central-Anwendung

Dieses Dokument hilft Ihnen dabei, herauszufinden, warum die Daten, die Ihre IoT Central-Anwendung exportiert, das gewünschte Ziel gar nicht oder nicht im richtigen Format erreichen.

## <a name="managed-identity-issues"></a>Probleme mit der verwalteten Identität

Sie verwenden eine verwaltete Identität zum Autorisieren der Verbindung mit einem Exportziel. Die Daten kommen nicht am Exportziel an.

Führen Sie vor dem Konfigurieren oder Aktivieren des Exportziels unbedingt die folgenden Schritte aus:

- Aktivieren Sie die verwaltete Identität für die Anwendung.
- Konfigurieren Sie die Berechtigungen für die verwaltete Identität.
- Konfigurieren Sie alle virtuellen Netzwerke, privaten Endpunkte und Firewallrichtlinien.

Weitere Informationen finden Sie unter [Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports](howto-export-data.md?tabs=managed-identity).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Hilfe benötigen, können Sie sich über das [MSDN-Azure-Forum oder das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ können Sie ein [Azure-Supportticket](https://portal.azure.com/#create/Microsoft.Support) erstellen.

Weitere Informationen finden Sie unter [Support- und Hilfeoptionen für Azure IoT](../../iot-fundamentals/iot-support-help.md).
