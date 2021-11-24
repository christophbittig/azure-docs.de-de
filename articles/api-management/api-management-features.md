---
title: Featurebasierter Vergleich der Azure API Management-Tarife | Microsoft-Dokumentation
description: Vergleichen von API Management-Tarifen anhand der angebotenen Features. Ihnen wird eine Tabelle mit einer Zusammenfassung der wichtigsten Features, die in den einzelnen Tarifen verfügbar sind, gezeigt.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/15/2021
ms.author: danlep
ms.openlocfilehash: 6159b5c5d677dd66dda12573feeb3aad9faa9764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556532"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Featurebasierter Vergleich der Azure API Management-Tarife

Jeder API Management-[Tarif](https://aka.ms/apimpricing) bietet einen bestimmten Satz von Features und [Kapazität pro Einheit](api-management-capacity.md). In der folgenden Tabelle werden die wichtigsten Features, die in den einzelnen Tarifen verfügbar sind, zusammengefasst. Einige Features funktionieren je nach Tarif möglicherweise anders oder weisen unterschiedliche Funktionen auf. In solchen Fällen werden die Unterschiede in den Dokumentationsartikel zu diesen einzelnen Features hervorgehoben.

> [!IMPORTANT]
> Beachten Sie, dass der Tarif „Developer“ nicht für Anwendungsfälle und Auswertungen in Produktionsumgebungen vorgesehen ist. Es wird keine SLA angeboten.

| Funktion                                                                                      | Nutzung | Entwickler | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-Integration<sup>1</sup>                                                             | Nein          | Ja       | Nein    | Ja      | Ja     |
| Unterstützung von virtuellen Netzwerken (VNETs)                                                               | Nein          | Ja       | Nein    | Nein       | Ja     |
| Bereitstellung in mehreren Regionen                                                                      | Nein          | Nein        | Nein    | Nein       | Ja     |
| Verfügbarkeitszonen                                                                           | Nein          | Nein        | Nein    | Nein       | Ja     |
| Mehrere benutzerdefinierte Domänennamen                                                                 | Nein          | Ja        | Nein    | Nein       | Ja     |
| Entwicklerportal<sup>2</sup>                                                                 | Nein          | Ja       | Ja   | Ja      | Ja     |
| Integrierter Cache                                                                               | Nein          | Ja       | Ja   | Ja      | Ja     |
| Integrierte Analysen                                                                           | Nein          | Ja       | Ja   | Ja      | Ja     |
| [Selbstgehostetes Gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nein          | Ja       | Nein    | Nein       | Ja     |
| [TLS-Einstellungen](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Externer Cache](./api-management-howto-cache-external.md)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Clientzertifikatsauthentifizierung](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Sichern und Wiederherstellen](api-management-howto-disaster-recovery-backup-restore.md)               | Nein          | Ja       | Ja   | Ja      | Ja     |
| [Verwaltung über Git](api-management-configuration-repository-git.md)                        | Nein          | Ja       | Ja   | Ja      | Ja     |
| Direkte Verwaltungs-API                                                                        | Nein          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor-Protokolle und -Metriken                                                               | Nein          | Ja       | Ja   | Ja      | Ja     |
| Statische IP                                                                                    | Nein          | Ja       | Ja   | Ja      | Ja     |
| [WebSocket-APIs](websocket-api.md)                                                                                    | Nein          | Ja       | Ja   | Ja      | Ja     |
| [GraphQL-APIs (Vorschau)](graphql-api.md)                                                                               | Ja          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Ermöglicht die Verwendung von Azure AD (und Azure AD B2C) als Identitätsanbieter für die Benutzeranmeldung im Entwicklerportal.<br/>
<sup>2</sup> Einschließlich zugehöriger Funktionen, z.B. Benutzer, Gruppen, Probleme, Anwendungs- und E-Mail-Vorlagen und Benachrichtigungen.<br/>
<sup>3</sup> Auf der Entwicklerebene sind selbstgehostete Gateways auf einen einzelnen Gatewayknoten beschränkt.<br/>
