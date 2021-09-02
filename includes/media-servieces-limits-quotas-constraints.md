---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 95143e78947cd44faec6ed48bea1fb44cc0011e2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867908"
---
> [!NOTE]
> Erstellen Sie für Ressourcen ohne festes Limit ein Supportticket, um eine Erhöhung der Kontingente anzufordern. Erstellen Sie keine zusätzlichen Azure Media Services-Konten, um höhere Limits zu erhalten.

### <a name="account-limits"></a>Kontolimits

| Resource | Standardlimit |
| --- | --- |
| Media Services-Konten in einem einzelnen Abonnement | 100 (feststehend) |

### <a name="asset-limits"></a>Medienobjektlimits

| Resource | Standardlimit |
| --- | --- |
| Medienobjekte pro Media Services-Konto | 1\.000.000|

### <a name="storage-media-limits"></a>Speicherlimits (Medien)

| Resource | Standardlimit |
| --- | --- |
| Dateigröße| In einigen Szenarien werden für die Verarbeitung in Media Services nur Dateien bis zu einer bestimmten Größe unterstützt. <sup>(1)</sup> |
| Speicherkonten | 100<sup>(2)</sup> (feststehend) |

<sup>1</sup> In Azure Blob Storage werden derzeit als Größe für ein einzelnes Blob bis zu 5 TB unterstützt. In Media Services gelten abhängig von den vom Dienst verwendeten VM-Größen weitere Grenzwerte. Die Größenbeschränkung gilt sowohl für die von Ihnen hochgeladenen Dateien als auch für Dateien, die in Folge der Verarbeitung durch Media Services (Codierung oder Analyse) generiert werden. Wenn Ihre Quelldatei größer als 260 GB ist, wird Ihr Auftrag wahrscheinlich nicht erfolgreich sein.

<sup>2</sup> Die Speicherkonten müssen aus demselben Azure-Abonnement stammen.

### <a name="jobs-encoding--analyzing-limits"></a>Auftragslimits (Codierung und Analyse)

| Resource | Standardlimit |
| --- | --- |
| Aufträge pro Media Services-Konto | 500.000 <sup>(3)</sup> (feststehend)|
| Auftragseingaben pro Auftrag | 50  (feststehend)|
| Auftragsausgaben pro Auftrag | 20 (feststehend) |
| Transformationen pro Media Services-Konto | 100  (feststehend)|
| Transformationsausgaben in einer Transformation | 20 (feststehend) |
| Dateien pro Auftragseingabe|10 (feststehend)|

<sup>3</sup> Diese Zahl umfasst fertiggestellte, aktive und abgebrochene Aufträge sowie Aufträge in der Warteschlange. Gelöschte Aufträge sind nicht enthalten. 

Alle Auftragsdatensätze in Ihrem Konto, die älter als 90 Tage sind, werden automatisch gelöscht, selbst wenn die Gesamtanzahl von Datensätzen unterhalb des maximalen Kontingents liegt. 

### <a name="live-streaming-limits"></a>Livestreaminglimits

| Resource | Standardlimit |
| --- | --- |
| Liveereignisse <sup>(4)</sup> pro Media Services-Konto |5|
| Live-Ausgaben pro Liveereignis |3 <sup>(5)</sup> |
| Max. Dauer für Liveausgabe | [Größe des DVR-Fensters](../articles/media-services/latest/live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> Ausführliche Informationen zu den Einschränkungen bei Liveereignissen finden Sie unter [Liveereignistypen – Vergleich und Einschränkungen](../articles/media-services/latest/live-event-types-comparison-reference.md).

<sup>5</sup> Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet.

### <a name="packaging--delivery-limits"></a>Limits für Verpackung und Bereitstellung

| Resource | Standardlimit |
| --- | --- |
| Streamingendpunkte (angehalten oder ausgeführt) pro Media Services-Konto| 2 |
| Dynamische Manifestfilter|100|
| Streamingrichtlinien | 100 <sup>(6)</sup> |
| Eindeutige Streaminglocator, die einem Medienobjekt gleichzeitig zugeordnet sind | 100<sup>(7)</sup> (feststehend) |

<sup>6</sup> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. Sie sollten nicht für jeden Streaminglocator eine neue Streamingrichtlinie erstellen.

<sup>7</sup> Streaminglocators sind nicht für die Verwaltung der benutzerbezogenen Zugriffssteuerung konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM).

### <a name="protection-limits"></a>Schutzlimits

| Resource | Standardlimit |
| --- | --- |
| Optionen pro Richtlinie für den Inhaltsschlüssel | 30 |
| Lizenzen pro Monat für jeden der DRM-Typen für den Media Services-Schlüsselbereitstellungsdienst pro Konto|1\.000.000|

### <a name="support-ticket"></a>Supportticket

Für Ressourcen ohne feste Vorgabe können Sie die Erhöhung von Kontingenten anfordern, indem Sie ein [Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) öffnen. Fügen Sie in die Anforderung ausführliche Informationen zu den gewünschten Kontingentänderungen, den Verwendungsfallszenarien und den benötigten Regionen ein. <br/>Erstellen Sie **keine** zusätzlichen Azure Media Services-Konten, um zu versuchen, höhere Grenzwerte zu erzielen.