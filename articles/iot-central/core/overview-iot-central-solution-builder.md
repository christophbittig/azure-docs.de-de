---
title: Lösungsentwicklung für Azure IoT Central | Microsoft-Dokumentation
description: Azure IoT Central ist eine IoT-Anwendungsplattform, die die Erstellung von IoT-Lösungen vereinfacht. Dieser Artikel bietet eine Übersicht über das Entwickeln integrierter Lösungen mit IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5ceb6950cb5ed581d2efea9a375fee0bf4008952
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597853"
---
# <a name="iot-central-solution-builder-guide"></a>Leitfaden für IoT Central-Lösungsentwickler

Mit einer IoT Central-Anwendung können Sie Millionen von Geräten während ihres gesamten Lebenszyklus überwachen und verwalten. Dieser Leitfaden ist für Lösungsentwickler bestimmt, die mithilfe von IoT Central integrierte Lösungen erstellen. Mit einer IoT Central-Anwendung können Sie Geräte verwalten sowie Telemetriedaten von Geräten analysieren und in andere Back-End-Dienste integrieren.

Ein Lösungsentwickler führt folgende Aufgaben aus:

- Konfiguriert Dashboards und Ansichten in der IoT Central-Webbenutzeroberfläche.
- Verwendet die integrierten Regeln und Analysetools, um geschäftliche Einblicke aus den verbundenen Geräten abzuleiten.
- Verwendet die Funktionen für Datenexport und Regeln, um IoT Central in andere Back-End-Dienste zu integrieren.

## <a name="configure-dashboards-and-views"></a>Konfigurieren von Dashboards und Ansichten

Eine IoT Central Anwendung kann ein oder mehrere Dashboard(s) enthalten, über die Bediener die Anwendung anzuzeigen und mit ihr interagieren können. Als Lösungsentwickler können Sie das Standarddashboard anpassen und spezialisierte Dashboards erstellen:

- Wenn Sie einige Beispiele für benutzerdefinierte Vorlagen anzeigen möchten, lesen Sie [Branchenorientierte Vorlagen](concepts-app-templates.md#industry-focused-templates).
- Weitere Informationen zu Dashboards finden Sie unter [Erstellen und Verwalten mehrerer Dashboards](howto-manage-dashboards.md) und [Konfigurieren des Anwendungsdashboards](howto-manage-dashboards.md).

Wenn ein Gerät eine Verbindung mit einer IoT Central-Instanz herstellt, wird es einer Gerätevorlage für den Gerätetyp zugeordnet. Eine Gerätevorlage enthält anpassbare Ansichten, mit deren Hilfe ein Bediener einzelne Geräte verwaltet. Als Lösungsentwickler können Sie die verfügbaren Ansichten für einen Gerätetyp erstellen und anpassen. Weitere Informationen finden Sie unter [Hinzufügen von Ansichten](howto-set-up-template.md#views).

## <a name="use-built-in-rules-and-analytics"></a>Verwenden integrierter Regeln und Analysen

Ein Lösungsentwickler kann einer IoT Central-Anwendung Regeln hinzufügen, die anpassbare Aktionen ausführen. Regeln werten Bedingungen auf der Grundlage von Daten aus einem Gerät aus, um zu bestimmen, wann eine Aktion ausgeführt werden soll. Weitere Informationen zu Regeln finden Sie unter:

- [Tutorial: Erstellen einer Regel und Einrichten von Benachrichtigungen in Ihrer Azure IoT Central-Anwendung](tutorial-create-telemetry-rules.md)
- [Konfigurieren von Regeln](howto-configure-rules.md)

IoT Central enthält integrierte Analysefunktionen, mit deren Hilfe ein Bediener die von den verbundenen Geräten übertragenen Daten analysieren kann. Weitere Informationen finden Sie unter [Analysieren von Gerätedaten mithilfe von Analytics](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Integration in andere Dienste

Als Lösungsentwickler können Sie die Funktionen für Datenexport und Regeln verwenden, um sie in andere Dienste zu integrieren. Weitere Informationen finden Sie unter:

- [Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports](howto-export-data.md)
- [Transformieren von Daten für IoT Central](howto-transform-data.md)
- [Integrieren Ihrer Azure IoT Central-Anwendung in andere Clouddienste mithilfe von Workflows](howto-configure-rules-advanced.md)
- [Erweitern von Azure IoT Central mit benutzerdefinierten Regeln mithilfe von Stream Analytics, Azure Functions und SendGrid](howto-create-custom-rules.md)
- [Erweitern von Azure IoT Central mit benutzerdefinierten Analysen mithilfe von Azure Databricks](howto-create-custom-analytics.md)

## <a name="apis"></a>APIs

Mit IoT Central-APIs können Sie tiefe Integrationen mit anderen Diensten in Ihrer IoT-Lösung erstellen. Die verfügbaren APIs werden als *Datenebenen*- oder *Steuerungsebenen*-APIs kategorisiert.

Sie verwenden Datenebenen-APIs für den Zugriff auf die Entitäten in und die Funktionen Ihrer IoT Central-Anwendung. Beispiel: Verwalten von Geräten, Gerätevorlagen, Benutzern und Rollen. Die REST-API-Vorgänge von IoT Central sind Vorgänge auf *Datenebene.* Weitere Informationen finden Sie unter [Verwenden der IoT Central-REST-API zum Verwalten von Benutzern und Rollen](howto-manage-users-roles-with-rest-api.md).

Mithilfe der *Steuerungsebene* verwalten Sie IoT Central-bezogene Ressourcen in Ihrem Azure-Abonnement. Sie können die Vorlagen „Azure CLI“ und „Resource Manager“ für Vorgänge auf Steuerungsebene verwenden. Beispielsweise können Sie mithilfe der Azure-CLI eine IoT Central-Anwendung erstellen. Weitere Informationen finden Sie unter [Verwalten von IoT Central aus Azure CLI](howto-manage-iot-central-from-cli.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mehr zur Verwendung von IoT Central erfahren möchten, sollten Sie als Nächstes die Schnellstarts ausprobieren, beginnend mit dem [Erstellen einer Azure IoT Central-Anwendung](./quick-deploy-iot-central.md).
