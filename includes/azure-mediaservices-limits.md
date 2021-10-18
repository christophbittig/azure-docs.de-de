---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: 10de63c842eeb60f5e7e725457c88961581370f0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129063781"
---
>[!NOTE]
>Erstellen Sie für Ressourcen ohne festes Limit ein Supportticket, um eine Erhöhung der Kontingente anzufordern. Erstellen Sie keine zusätzlichen Azure Media Services-Konten, um höhere Limits zu erhalten.

| Resource | Begrenzung | 
| --- | --- | 
| Azure Media Services-Konten in einem Einzelabonnement | 25 (feststehend) | 
| Aufträge pro Media Services-Konto | 50.000<sup>2</sup> |
| Verkettete Aufgaben pro Auftrag | 30 (feststehend) |
| Medienobjekte pro Media Services-Konto | 1\.000.000|
| Objekte pro Aufgabe | 50 |
| Objekte pro Auftrag | 100 |
| Eindeutige Locators, die einem Objekt gleichzeitig zugeordnet sind | 5<sup>4</sup> |
| Livekanäle pro Media Services-Konto |5<sup>5</sup>|
| Programme im angehaltenen Zustand pro Kanal |50|
| Programme im ausgeführten Zustand pro Kanal |3|
| Streamingendpunkte (beendet oder ausgeführt) pro Media Services-Konto|2|
| Streamingeinheiten pro Streamingendpunkt |10 |
| Speicherkonten | 100<sup>6</sup> (feststehend) |
| Richtlinien | 1\.000.000<sup>7</sup> |
| Dateigröße| In einigen Szenarien werden für die Verarbeitung in Media Services nur Dateien bis zu einer bestimmten Größe unterstützt.<sup>8</sup> |

<sup>1</sup> Wenn Sie den Typ ändern (etwa von „S2“ in „S1“), werden die maximalen Grenzwerte für reservierte Einheiten zurückgesetzt.

<sup>2</sup> Dieser Wert beinhaltet fertig gestellte, aktive und abgebrochene Aufträge sowie Aufträge in der Warteschlange. Gelöschte Aufträge sind nicht enthalten. Sie können die alten Aufträge mithilfe von **IJob.Delete** oder mithilfe der HTTP-Anforderung **DELETE** löschen.

Seit 1. April 2017 werden alle Auftragsdatensätze in Ihrem Konto, die älter als 90 Tage sind, sowie alle dazugehörigen Aufgabendatensätze automatisch gelöscht. Die automatische Löschung erfolgt auch dann, wenn die Gesamtanzahl von Datensätzen unter dem Kontingentlimit liegt. Wenn Sie die Auftrags- und Aufgabeninformationen archivieren möchten, verwenden Sie den unter [Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md) beschriebenen Code.

<sup>3</sup> Wenn Sie eine Anforderung zum Auflisten von Auftragsentitäten senden, werden pro Anforderung maximal 1.000 Aufträge zurückgegeben. Wenn Sie alle übermittelten Aufträge nachverfolgen möchten, verwenden Sie die unter [OData-Systemabfrageoptionen](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)) beschriebenen Abfragen „$top“ oder „$skip“.

<sup>4</sup> Locators sind nicht für die Verwaltung der benutzerspezifischen Zugriffssteuerung konzipiert. Wenn Sie einzelnen Benutzern unterschiedliche Zugriffsrechte zuweisen möchten, verwenden Sie eine DRM-Lösung (Digital Rights Management). Weitere Informationen finden Sie in der [Übersicht über den Inhaltsschutz](../articles/media-services/latest/drm-content-protection-concept.md).

<sup>5</sup>Abhängig von Ihrem Streaming-Anwendungsfall und dem ausgewählten regionalen Rechenzentrum kann AMS mehr als 5 Livekanälee pro Media Services Konto unterstützen. Stellen Sie eine Supportanfrage, um das Kontingent für Ihr Konto zu erhöhen.

<sup>6</sup>Die Speicherkonten müssen aus demselben Azure-Abonnement stammen.

<sup>7</sup> Für verschiedene Media Services-Richtlinien gilt ein Limit von 1.000.000 Richtlinien. Dies Limit gilt beispielsweise für die Locator-Richtlinie oder für „ContentKeyAuthorizationPolicy“. 

>[!NOTE]
> Falls Sie immer die gleichen Tage und Zugriffsberechtigungen verwenden, empfiehlt es sich, die gleiche Richtlinien-ID zu nutzen. Weitere Informationen und ein Beispiel finden Sie unter [Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>8</sup> In Azure Blob Storage werden derzeit als Größe für ein einzelnes Blob bis zu 5 TB unterstützt. In Media Services gelten abhängig von den vom Dienst verwendeten VM-Größen weitere Grenzwerte. Die Größenbeschränkung gilt sowohl für die von Ihnen hochgeladenen Dateien als auch für Dateien, die in Folge der Verarbeitung durch Media Services (Codierung oder Analyse) generiert werden. Wenn Ihre Quelldatei größer als 260 GB ist, wird Ihr Auftrag wahrscheinlich nicht erfolgreich sein. 

