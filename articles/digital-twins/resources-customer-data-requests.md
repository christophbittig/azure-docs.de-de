---
title: Features für Kundendatenanforderungen für Azure Digital Twins
titleSuffix: Azure Digital Twins
description: In diesem Artikel werden Prozesse zum Exportieren und Löschen personenbezogener Daten in Azure Digital Twins gezeigt.
author: baanders
ms.author: baanders
ms.date: 9/14/2021
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: ef68e552d57df0fc202b9d75ae76065e3119ccff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642793"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Features für Kundendatenanforderungen in Azure Digital Twins

Azure Digital Twins ist eine Entwicklerplattform für die Erstellung sicherer digitaler Darstellungen einer Geschäftsumgebung. Darstellungen basieren auf Livezustandsdaten aus von Benutzern ausgewählten Datenquellen.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Die digitalen Darstellungen werden in Azure Digital Twins als *digitale Zwillinge* bezeichnet. Sie stellen Entitäten in realen Umgebungen dar und sind mit Bezeichnern verknüpft. Microsoft pflegt keine Informationen und hat keinen Zugriff auf Daten, die eine Korrelation zwischen Bezeichnern und Benutzern ermöglichen würden. 

Viele der digitalen Zwillinge in Azure Digital Twins stellen nicht direkt personenbezogene Entitäten dar. In der Regel werden Objekte wie Besprechungsräume oder Produktionsbereiche dargestellt. Benutzer können jedoch bestimmte Entitäten als persönlich identifizierbar betrachten und nach eigenem Ermessen ihre eigenen Ressourcen- oder Bestandsnachverfolgungsmethoden verwenden, die digitale Zwillinge mit Personen verknüpfen. Alle Daten im Zusammenhang mit digitalen Zwillingen werden von Azure Digital Twins wie personenbezogene Daten verwaltet und gespeichert.

Zum Anzeigen, Exportieren und Löschen personenbezogener Daten, auf die möglicherweise im Antrag einer betroffenen Person verwiesen wird, kann ein Azure Digital Twins-Administrator das [Azure-Portal](https://portal.azure.com/) (für Benutzer und Rollen) oder die [Azure Digital Twins-REST-APIs](/rest/api/azure-digitaltwins/) (für digitale Zwillinge) verwenden. Das Azure-Portal und die REST-APIs bieten verschiedene Methoden, mit denen sich Benutzer um solche Anträge betroffener Personen kümmern können.

## <a name="identifying-customer-data"></a>Identifizieren von Kundendaten

Als *personenbezogene Daten* werden in Azure Digital Twins Daten betrachtet, die mit den zugehörigen Administratoren und Benutzern verknüpft sind. 

Von Azure Digital Twins wird die [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)-**Objekt-ID** von Benutzern mit Zugriff auf die Umgebung gespeichert. Im Azure-Portal werden von Azure Digital Twins zwar Benutzer-E-Mail-Adressen angezeigt, diese E-Mail-Adressen werden jedoch nicht in Azure Digital Twins gespeichert. Sie werden dynamisch in Azure Active Directory unter Verwendung der Azure Active Directory-Objekt-ID nachgeschlagen.

## <a name="deleting-customer-data"></a>Löschen von Kundendaten

Azure Digital Twins-Administratoren können benutzerbezogene Daten über das Azure-Portal löschen. Darüber hinaus können über die Azure Digital Twins-REST-APIs auch Löschvorgänge für einzelne digitale Zwillinge ausgeführt werden. Weitere Informationen zu den verfügbaren APIs finden Sie in der [REST-API-Referenz für Azure Digital Twins](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Exportieren von Kundendaten

Von Azure Digital Twins werden Daten im Zusammenhang mit digitalen Zwillingen gespeichert. Benutzer können diese Daten über REST-APIs abrufen und anzeigen und sie durch Kopieren und Einfügen exportieren. 

Kundendaten (einschließlich Benutzerrollen und Rollenzuweisungen) können über das Azure-Portal ausgewählt, kopiert und eingefügt werden. 

## <a name="links-to-additional-documentation"></a>Links zu zusätzlicher Dokumentation

Eine vollständige Liste der APIs des Azure Digital Twins-Diensts finden Sie in der [REST-API-Referenz für Azure Digital Twins](/rest/api/azure-digitaltwins/).