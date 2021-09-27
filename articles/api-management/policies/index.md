---
title: Azure API Management-Richtlinienbeispiele | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Richtlinien, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: dlepow
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: d361a7a5d5f0f6b536caf9918dc3e1578b6f364e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128554568"
---
# <a name="api-management-policy-samples"></a>API Management-Richtlinienbeispiele

[Richtlinien](../api-management-howto-policies.md) sind eine leistungsfähige Funktion des Systems, mit der Herausgeber das Verhalten der API über eine Konfiguration ändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Die folgende Tabelle enthält Links zu Beispielen und liefert eine kurze Beschreibung für jedes Beispiel.

| Eingehende Richtlinien | BESCHREIBUNG |
| ---------------- | ----------- |
| [Hinzufügen eines Forwarded-Headers, um der Back-End-API das Erstellen geeigneter URLs zu ermöglichen](./set-header-to-enable-backend-to-construct-urls.md) | Veranschaulicht, wie Sie einen Forwarded-Header in die eingehende Anforderung einfügen, damit die Back-End-API die richtigen URLs erstellen kann.                                                                                                        |
| [Hinzufügen eines Headers mit eine Korrelations-ID](./add-correlation-id.md)                                                             | Veranschaulicht, wie Sie der eingehenden Anforderung einen Header mit einer Korrelations-ID hinzufügen.                                                                                                                                        |
| [Hinzufügen von Funktionen zu einem Back-End-Dienst und Zwischenspeichern der Antwort](./cache-response.md)                                             | Veranschaulicht das Hinzufügen von Funktionen zu einem Back-End-Dienst. Beispielsweise können Sie in einer API zur Wettervorhersage anstelle von Längen- und Breitengrad einen Ortsnamen akzeptieren.                                                                    |
| [Autorisieren des Zugriffs basierend auf JWT-Ansprüchen](./authorize-request-based-on-jwt-claims.md)                                              | Veranschaulicht, wie Sie den Zugriff auf bestimmte HTTP-Methoden in einer API basierend auf JWT-Ansprüchen autorisieren.                                                                                                                                       |
| [Autorisieren von Anforderungen mit einem externen Autorisierer](./authorize-request-using-external-authorizer.md)                                                   | Veranschaulicht, wie Sie einen externen Autorisierer zum Sichern des API-Zugriffs verwenden.                                                                                                                                                               |
| [Autorisieren des Zugriffs mithilfe von Google-OAuth-Token](./use-google-as-oauth-token-provider.md)                                            | Veranschaulicht, wie Sie den Zugriff auf Ihre Endpunkte mithilfe von Google als OAuth-Tokenanbieter autorisieren.                                                                                                                                    |
| [Filtern von IP-Adressen bei Verwendung von Application Gateway](./filter-ip-addresses-when-using-appgw.md) | Zeigt, wie in Richtlinien nach IP-Adressen gefiltert wird, wenn auf die API Management-Instanz über Application Gateway zugegriffen wird.
| [Generieren von Shared Access Signatures und Weiterleiten von Anforderungen an Azure Storage](./generate-shared-access-signature.md)                  | Veranschaulicht, wie Sie [Shared Access Signatures](../../storage/common/storage-sas-overview.md) mithilfe von Ausdrücken generieren und die Anforderung mithilfe der rewrite-uri-Richtlinie an Azure Storage weiterleiten. |
| [Abrufen von OAuth2-Zugriffstoken aus AAD und Weiterleiten des Tokens an das Back-End](./use-oauth2-for-authorization.md)                             | Stellt ein Beispiel zur Verwendung von OAuth2 für die Autorisierung zwischen dem Gateway und einem Back-End bereit. Dieses zeigt, wie Sie ein Zugriffstoken aus AAD abrufen und an das Back-End weiterleiten.                                                    |
| [Abrufen von X-CSRF-Token aus einem SAP-Gateway mit einer Richtlinie zum Senden einer Anforderung](./get-x-csrf-token-from-sap-gateway.md)                           | Veranschaulicht, wie Sie das von vielen APIs verwendete X-CSRF-Muster implementieren. Dieses Beispiel bezieht sich speziell auf das SAP-Gateway.                                                                                                                           |
| [Weiterleiten der Anforderung basierend auf der Größe des Textkörpers](./route-requests-based-on-size.md)                                            | Veranschaulicht, wie Sie Anforderungen basierend auf der Größe des Textkörpers weiterleiten.                                                                                                                                                       |
| [Senden von Anforderungskontextinformationen an den Back-End-Dienst](./send-request-context-info-to-backend-service.md)                    | Veranschaulicht, wie Kontextinformationen zum Zweck der Protokollierung oder Verarbeitung an den Back-End-Dienst gesendet werden.                                                                                                                                |
| [Festlegen der Aufbewahrungsdauer von Antworten im Cache](./set-cache-duration.md)                                                                          | Veranschaulicht, wie Sie die Aufbewahrungsdauer von Antworten im Cache mit dem maxAge-Wert in dem vom Back-End gesendeten Cache-Control-Header festlegen.                                                                                                             |
| **Ausgehende Richtlinien** | **Beschreibung** |
| [Filtern des Antwortinhalts](./filter-response-content.md)                                                                         | Veranschaulicht, wie Sie Datenelemente aus der Antwortnutzlast basierend auf dem der Anforderung zugeordneten Produkt filtern.                                                                                                        |
| **Richtlinien bei Fehler** | **Beschreibung** |
| [Fehler in Stackify protokollieren](./log-errors-to-stackify.md)                                                                           | Veranschaulicht, wie Sie eine Richtlinie für die Protokollierung von Fehlern hinzufügen, um diese Fehler für die Protokollierung an Stackify zu senden.                                                                                                                                            |
