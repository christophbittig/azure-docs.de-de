---
title: Vergleich von Azure Data Lake Storage Gen1 und Blob Storage
description: Hier finden Sie Informationen zu den Unterschieden zwischen Azure Data Lake Storage Gen1 und Azure Blob Storage hinsichtlich wichtiger Aspekte der Big Data-Verarbeitung.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: 33e7ab0acdba7511561c141b08e67b4646c975e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676027"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Vergleich von Azure Data Lake Storage Gen1 und Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

In der Tabelle in diesem Artikel werden die Unterschiede zwischen Azure Data Lake Storage Gen1 und Azure Blob Storage zusammengefasst und einige wichtige Aspekte der Big Data-Verarbeitung aufgezeigt. Azure Blob Storage ist ein universell einsetzbarer und skalierbarer Objektspeicher, der auf eine Vielzahl von Speicherszenarien ausgelegt ist. Azure Data Lake Storage Gen1 ist ein hochgradig skalierbares Repository, das für Big Data-Analyseworkloads optimiert ist.

| Category | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| -------- | ---------------------------- | ------------------ |
| Zweck |Optimierter Speicher für Big Data-Analyseworkloads |Universell einsetzbarer Objektspeicher für eine Vielzahl von Speicherszenarien, wie z.B. Big Data-Analysen |
| Anwendungsfälle |Batch-, interaktive Streaming Analytics- und Machine Learning-Daten wie beispielsweise Protokolldateien, IoT-Daten, Clickstreams, große Datasets |Jede Art von Text- oder Binärdaten, beispielsweise Daten des Anwendungs-Back-Ends, Sicherungsdaten, Medienspeicher für Streaming und universelle Daten Zusätzlich vollständige Unterstützung für Analyseworkloads; Batch-, interaktive Streaming Analytics- und Machine Learning-Daten wie beispielsweise Protokolldateien, IoT-Daten, Clickstreams, große Datasets |
| Wichtige Konzepte |Das Data Lake Storage Gen1-Konto enthält Ordner, die wiederum als Dateien gespeicherte Daten enthalten. |Das Speicherkonto enthält Container, die wiederum Daten in Form von Blobs enthalten. |
| Struktur |Hierarchisches Dateisystem |Objektspeicher mit flachem Namespace |
| API |REST-API über HTTPS |REST-API über HTTP/HTTPS |
| Serverseitige API |[WebHDFS-kompatible REST-API](/rest/api/datalakestore/) |[Azure Blob Storage-REST-API](/rest/api/storageservices/Blob-Service-REST-API) |
| Hadoop-Dateisystemclient |Ja |Ja |
| Datenvorgänge – Authentifizierung |Basierend auf [Azure Active Directory-Identitäten](../active-directory/develop/authentication-vs-authorization.md) |Basierend auf gemeinsamen Geheimnissen – [Kontozugriffsschlüssel](../storage/common/storage-account-keys-manage.md) und [Shared Access Signature-Schlüssel](../storage/common/storage-sas-overview.md). |
| Datenvorgänge – Authentifizierungsprotokoll |[OpenID Connect](https://openid.net/connect/). Aufrufe müssen ein gültiges, über Azure Active Directory ausgestelltes JWT (JSON-Webtoken) enthalten.|Hash-based Message Authentication Code (HMAC). Aufrufe müssen einen Base64-codierten SHA-256-Hash über einen Teil der HTTP-Anforderung enthalten. |
| Datenvorgänge – Autorisierung |POSIX-Zugriffssteuerungslisten (ACLs).  Auf Azure Active Directory-Identitäten basierende ACLs können auf Datei- und Ordnerebene festgelegt werden. |Für die Autorisierung auf Kontoebene – Verwenden Sie [Zugriffsschlüssel](../storage/common/storage-account-keys-manage.md)<br>Für Konto-, Container- oder Blobautorisierung – Verwenden Sie [Shared Access Signature-Schlüssel](../storage/common/storage-sas-overview.md) |
| Datenvorgänge – Überwachung |Verfügbar. Weitere Informationen finden Sie [hier](data-lake-store-diagnostic-logs.md) . |Verfügbar |
| Verschlüsselung für ruhende Daten |<ul><li>Transparent, serverseitig</li> <ul><li>Mit dienstverwalteten Schlüsseln</li><li>Mit kundenverwalteten Schlüsseln in Azure Key Vault</li></ul></ul> |<ul><li>Transparent, serverseitig</li> <ul><li>Mit dienstverwalteten Schlüsseln</li><li>Mit kundenverwalteten Schlüsseln in Azure Key Vault (Vorschau)</li></ul><li>Clientseitige Verschlüsselung</li></ul> |
| Verwaltungsvorgänge (z. B. Kontoerstellung) |[Rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) zur Kontoverwaltung |[Rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) zur Kontoverwaltung |
| Entwickler-SDKs |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Leistung von Analyseworkloads |Optimierte Leistung für parallele Analyseworkloads. Hoher Durchsatz, hohe IOPS. |Optimierte Leistung für parallele Analyseworkloads. |
| Größenbeschränkungen |Keine Beschränkungen für Kontogrößen, Dateigrößen oder die Anzahl von Dateien. |Informationen zu bestimmten Grenzwerten finden Sie unter [Skalierbarkeitsziele für Standardspeicherkonten](../storage/common/scalability-targets-standard-account.md) und [Skalierbarkeits- und Leistungsziele für Blob Storage](../storage/blobs/scalability-targets.md). Für größere Kontolimits wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/faq/). |
| Georedundanz |Lokal redundant (mehrere Kopien von Daten in einer Azure-Region) |Lokal redundant (LRS), zonenredundant (ZRS) global redundant (GRS), global redundant mit Lesezugriff (RA-GRS). Weitere Informationen finden Sie [hier](../storage/common/storage-redundancy.md) . |
| Dienstzustand |Allgemein verfügbar |Allgemein verfügbar |
| Regionale Verfügbarkeit |Siehe [hier](https://azure.microsoft.com/regions/#services) |In allen Azure-Regionen verfügbar |
| Preis |Siehe [Preise](https://azure.microsoft.com/pricing/details/data-lake-store/) |Siehe [Preise](https://azure.microsoft.com/pricing/details/storage/) |