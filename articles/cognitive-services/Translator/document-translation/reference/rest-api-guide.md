---
title: Referenzleitfaden zur REST-API für die Dokumentübersetzung
titleSuffix: Azure Cognitive Services
description: Rufen Sie eine Liste mit Links zu den REST-APIs für die Dokumentübersetzung auf.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/21/2021
ms.author: lajanuar
ms.openlocfilehash: 38757efc32a90a74cdf73bc84af28b80f8402948
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112543881"
---
# <a name="document-translation-rest-api-reference-guide"></a>Referenzleitfaden zur REST-API für die Dokumentübersetzung

Die Dokumentübersetzung ist ein cloudbasiertes Feature des Azure-Übersetzerdiensts und ist Teil der Azure Cognitive Service-Familie von REST-APIs. Die Dokumentübersetzungs-API übersetzt Dokumente in und aus alle [unterstützten Sprachen und Dialekte](../../language-support.md), wobei Dokumentstruktur und Datenformat beibehalten werden. Die verfügbaren Methoden werden in der folgenden Tabelle aufgeführt:

| Anforderung| BESCHREIBUNG|
|---------|--------------|
| [**Abrufen unterstützter Dokumentformate**](get-supported-document-formats.md)| Diese Methode gibt eine Liste unterstützter Dokumentformate zurück.|
|[**Abrufen unterstützter Glossarformate**](get-supported-glossary-formats.md)|Diese Methode gibt eine Liste unterstützter Glossarformate zurück.|
|[**Abrufen unterstützter Speicherquellen**](get-supported-storage-sources.md)| Diese Methode gibt eine Liste unterstützter Speicherquellen/-optionen zurück.|
|[**Starten der Übersetzung (POST)** ](start-translation.md)|Diese Methode startet einen Dokumentübersetzungsauftrag. |
|[**Abrufen des Dokumentstatus**](get-documents-status.md)|Diese Methode gibt den Status aller Dokumente in einem Übersetzungsauftrag zurück.|
|[**Abrufen des Dokumentstatus**](get-document-status.md)| Diese Methode gibt den Status für ein bestimmtes Dokument in einem Auftrag zurück. |
|[**Abrufen des Übersetzungsstatus**](get-translations-status.md)| Diese Methode gibt eine Liste aller Übersetzungsanforderungen zurück, die von einem Benutzer übermittelt werden, sowie den Status für jede Anforderung.|
|[**Abrufen des Übersetzungsstatus**](get-translation-status.md) | Diese Methode gibt eine Zusammenfassung des Status für eine bestimmte Dokumentübersetzungsanforderung zurück. Diese enthält den gesamten Anforderungsstatus und den Status für Dokumente, die als Teil dieser Anforderung übersetzt werden.|
|[**Abbrechen der Übersetzung (LÖSCHEN)** ](cancel-translation.md)| Diese Methode bricht eine Dokumentübersetzung ab, die derzeit verarbeitet wird oder in der Warteschlange steht. |

> [!div class="nextstepaction"]
> [Erkunden Sie unsere Clientbibliotheken und SDKs für C# und Python.](../client-sdks.md)