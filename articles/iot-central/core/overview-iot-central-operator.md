---
title: Leitfaden für Azure IoT Central-Operatoren
description: Azure IoT Central ist eine IoT-Anwendungsplattform, die die Erstellung von IoT-Lösungen vereinfacht. Dieser Artikel bietet einen Überblick über die Operatorrolle in IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 86a5d327e00ee7c9595cc586aa7e63023a386bba
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586543"
---
# <a name="iot-central-operator-guide"></a>Leitfaden für IoT Central-Operatoren

Mit einer IoT Central-Anwendung können Sie Millionen von Geräten während ihres gesamten Lebenszyklus überwachen und verwalten. Dieser Leitfaden richtet sich an Operatoren, die eine IoT Central-Anwendung für die Verwaltung von IoT-Geräten verwenden.

Aufgaben eines Operators:

- Überwachen und Verwalten der Geräte, die mit der Anwendung verbunden sind.
- Troubleshooting und Behebung von Problemen mit Geräten.
- Bereitstellen neuer Geräte.

## <a name="monitor-and-manage-devices"></a>Überwachen und Verwalten von Geräten

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Screenshot mit einer Geräteansicht":::

Zum Überwachen von Geräten kann ein Operator die vom Lösungsentwickler definierten Geräteansichten als Teil der Gerätevorlage verwenden. In diesen Ansichten können Gerätetelemetrie- und Eigenschaftswerte angezeigt werden. Ein Beispiel ist die Ansicht **Übersicht** auf dem Screenshot weiter oben.

Um ausführlichere Informationen zu erhalten, kann ein Operator Gerätegruppen und die integrierten Analysefunktionen verwenden. Weitere Informationen finden Sie unter [Analysieren von Gerätedaten mithilfe von Analytics](howto-create-analytics.md).

Zum Verwalten einzelner Geräte kann ein Operator Geräteansichten verwenden, um Geräte- und Cloudeigenschaften festzulegen und Gerätebefehle aufzurufen. Beispiele hierfür sind die Ansichten **Gerät verwalten** und **Befehle** im vorherigen Screenshot.

Zur Verwaltung vieler Geräte kann ein Operator Aufträge erstellen und planen. Aufträge können auf mehreren Geräten Eigenschaften aktualisieren und Befehle ausführen. Weitere Informationen finden Sie unter [Erstellen und Ausführen von Aufträgen in Ihrer Azure IoT Central-Anwendung](howto-manage-devices-in-bulk.md).

## <a name="troubleshoot-and-remediate-issues"></a>Troubleshooting und Beheben von Geräteproblemen

Der Operator ist für die Integrität der Anwendung und der zugehörigen Geräte verantwortlich. Der [Leitfaden zur Problembehandlung](troubleshoot-connection.md) unterstützt Operatoren bei der Diagnose und Behebung häufiger Probleme. Auf der Seite **Geräte** kann ein Operator Geräte blockieren, die anscheinend nicht ordnungsgemäß funktionieren, bis das Problem behoben ist.

## <a name="add-and-remove-devices"></a>Hinzufügen und Entfernen von Geräten

Der Operator kann Geräte entweder einzeln oder in einem Massenvorgang zu der IoT Central-Anwendung hinzufügen und daraus entfernen. Weitere Informationen finden Sie unter [Verwalten von Geräten in Ihrer Azure IoT Central-Anwendung](howto-manage-devices-individually.md).

## <a name="personalize"></a>Personalize

Operatoren können persönliche Dashboards in einer IoT Central-Anwendung erstellen, die Links zu den Ressourcen enthält, die sie am häufigsten verwenden. Weitere Informationen finden Sie unter [Verwalten von Dashboards](howto-manage-dashboards.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mehr zur Verwendung von IoT Central erfahren möchten, sollten Sie als Nächstes die Schnellstarts ausprobieren, beginnend mit dem [Erstellen einer Azure IoT Central-Anwendung](./quick-deploy-iot-central.md).
