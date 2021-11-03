---
title: Dienstkonfiguration – QnA Maker
description: Erfahren Sie, wie und wo Ressourcen konfiguriert werden.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f504e1de8c751cb7d97679c8d56c591b0508c808
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071114"
---
# <a name="service-configuration"></a>Dienstkonfiguration

Jede Version von QnA Maker verwendet einen anderen Satz von Azure-Ressourcen (Diensten). In diesem Artikel werden die unterstützten Anpassungen für diese Dienste beschrieben. 

## <a name="app-service"></a>App Service

QnA Maker verwendet den App Service, um die von der [generateAnswer-API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer) verwendete Abfragelaufzeit bereitzustellen.

Diese Einstellungen sind im Azure-Portal für den App Service verfügbar. Die Einstellungen sind durch Auswahl von **Einstellungen** und dann **Konfiguration** verfügbar.

Sie können eine einzelne Einstellung entweder über die Liste der Anwendungseinstellungen festlegen oder mehrere Einstellungen durch Auswahl von **Erweiterte Bearbeitung** ändern.

|Resource|Einstellung|
|--|--|
|AzureSearchAdminKey|Cognitive Search: wird für QnA-Paarspeicher und Bewerter 1 verwendet|
|AzureSearchName|Cognitive Search: wird für QnA-Paarspeicher und Bewerter 1 verwendet|
|DefaultAnswer|Text der Antwort, wenn kein Treffer gefunden wird|
|UserAppInsightsAppId|Chatprotokoll und Telemetrie|
|UserAppInsightsKey|Chatprotokoll und Telemetrie|
|UserAppInsightsName|Chatprotokoll und Telemetrie|
|QNAMAKER_EXTENSION_VERSION|Legen Sie diese Einstellung immer auf _Neueste_ fest. Mit dieser Einstellung wird die QnA Maker-Websiteerweiterung in App Service initialisiert.|

Sie müssen den Dienst über die Seite **Übersicht** des Azure-Portals **neu starten**, sobald Sie mit den Änderungen fertig sind.

## <a name="qna-maker-service"></a>QnA Maker Service

Der QnA Maker-Dienst stellt die Konfiguration für die folgenden Benutzer bereit, die an einem einzelnen QnA Maker-Dienst und all seinen Wissensdatenbanken zusammenarbeiten können.

Erfahren Sie, [wie Sie Projektmitarbeiter](./index.yml) zu Ihrem Dienst hinzufügen können.

## <a name="change-azure-cognitive-search"></a>Ändern von Azure Cognitive Search

Erfahren Sie, [wie Sie den Cognitive Search-Dienst ändern](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource), der mit Ihrem QnA Maker-Dienst verknüpft ist.

## <a name="change-default-answer"></a>Ändern der Standardantwort

Erfahren Sie, [wie Sie den Text Ihrer Standardantworten ändern](How-To/change-default-answer.md) können. 

## <a name="telemetry"></a>Telemetrie

Anwendung Insights wird zur Überwachung der Telemetrie mit QnA Maker GA verwendet. Es gibt keine speziellen Konfigurationseinstellungen für QnA Maker.

## <a name="app-service-plan"></a>App Service-Plan

App Service-Plan verfügt über keine speziellen Konfigurationseinstellungen für QnA Maker.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Formate](reference-document-format-guidelines.md) für Dokumente und URLs, die Sie in eine Wissensdatenbank importieren möchten.
