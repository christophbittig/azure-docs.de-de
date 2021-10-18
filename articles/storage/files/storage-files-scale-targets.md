---
title: Skalierbarkeits- und Leistungsziele für Azure Files
description: Informationen zu den Skalierbarkeits- und Leistungszielen für Azure Files, einschließlich Kapazität, Anforderungsrate sowie Grenzwerte für ein- und ausgehende Bandbreite.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 66ad68711d57767f6f657d941222e4b225c4b20e
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658935"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalierbarkeits- und Leistungsziele für Azure Files
[Azure Files](storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über die SMB- und NFS-Dateisystemprotokolle zugegriffen werden kann. Dieser Artikel behandelt die Skalierbarkeits- und Leistungsziele für Azure Files und Azure-Dateisynchronisierung.

Die hier aufgelisteten Ziele für Skalierbarkeit und Leistung sind hochgesteckt, können jedoch von anderen Variablen in Ihrer Bereitstellung beeinflusst werden. Beispielsweise kann der Durchsatz für eine Datei auch durch die verfügbare Netzwerkbandbreite und nicht nur durch die Server begrenzt sein, die Ihre Azure-Dateifreigaben hosten. Wir empfehlen Ihnen ausdrücklich, Ihr Nutzungsverhalten zu testen, um festzustellen, ob Skalierbarkeit und Leistung von Azure Files Ihren Anforderungen entspricht. Wir verpflichten uns auch, diese Grenzwerte mit der Zeit zu erhöhen. 

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

## <a name="azure-files-scale-targets"></a>Skalierbarkeitsziele für Azure Files
Azure-Dateifreigaben werden in Speicherkonten bereitgestellt, bei denen es sich um Objekte der obersten Ebene handelt, die einen freigegebenen Speicherpool darstellen. Dieser Speicherpool kann verwendet werden, um mehrere Dateifreigaben bereitzustellen. Es gibt also drei Kategorien zu beachten: Speicherkonten, Azure-Dateifreigaben und Dateien.

### <a name="storage-account-scale-targets"></a>Skalierbarkeitsziele für Speicherkonten
Azure unterstützt mehrere Arten von Speicherkonten für unterschiedliche Kundenszenarien, aber es gibt zwei Hauptarten von Speicherkonten für Azure Files. Welche Art von Speicherkonto Sie erstellen müssen, hängt davon ab, ob Sie eine Standard- oder Premium-Dateifreigabe erstellen möchten: 

- **Speicherkonten vom Typ „Universell Version 2 (GPv2)“** : GPv2-Speicherkonten ermöglichen Ihnen die Bereitstellung von Azure-Dateifreigaben auf Standard- bzw. festplattenbasierter Hardware (HDD-basiert). Neben Azure-Dateifreigaben können in GPv2-Speicherkonten auch andere Speicherressourcen wie Blobcontainer, Warteschlangen oder Tabellen gespeichert werden. Dateifreigaben können auf den transaktionsoptimierten (Standardeinstellung), heißen und kalten Ebenen bereitgestellt werden.

- **FileStorage-Speicherkonten**: FileStorage-Speicherkonten ermöglichen Ihnen die Bereitstellung von Azure-Dateifreigaben auf SSD-basierter Hardware (Premium/Solid State Drive). FileStorage-Konten können nur zum Speichern von Azure-Dateifreigaben verwendet werden. In einem FileStorage-Konto können keine anderen Speicherressourcen (Blobcontainer, Warteschlangen, Tabellen usw.) bereitgestellt werden.

| attribute | GPv2-Speicherkonten (Standard) | FileStorage-Speicherkonten (Premium) |
|-|-|-|
| Anzahl von Speicherkonten pro Region und Abonnement | 250 | 250 |
| Maximale Speicherkontokapazität | 5 PiB<sup>1</sup> | 100 TiB (bereitgestellt) |
| Maximale Anzahl der Dateifreigaben | Unbegrenzt | Unbegrenzt, die gesamte bereitgestellte Größe aller Freigaben muss kleiner sein als die maximale Speicherkontokapazität. |
| Rate für maximale Anzahl von gleichzeitigen Anforderungen | 20.000 IOPS<sup>1</sup> | 100.000 IOPS |
| Maximaler Eingang | <ul><li>USA/Europa: 9.536 MiB/Sekunde<sup>1</sup></li><li>Andere Regionen (LRS/ZRS): 9.536 MiB/Sekunde<sup>1</sup></li><li>Andere Regionen (GRS): 4.768 MiB/s<sup>1</sup></li></ul> | 4\.136 MiB/s |
| Maximaler Ausgang | 47.683 MiB/Sekunde<sup>1</sup> | 6\.204 MiB/s |
| Maximale Anzahl von Regeln für virtuelle Netzwerke | 200 | 200 |
| Maximale Anzahl von IP-Adressregeln | 200 | 200 |
| Lesevorgänge für die Verwaltung | 800 pro 5 Minuten | 800 pro 5 Minuten |
| Schreibvorgänge für die Verwaltung | 10 pro Sekunde/1200 pro Stunde | 10 pro Sekunde/1200 pro Stunde |
| Listenvorgänge für die Verwaltung | 100 pro 5 Minuten | 100 pro 5 Minuten |

<sup>1</sup> Speicherkonten vom Typ „Universell, Version 2“ unterstützen höhere Grenzwerte für Kapazität und Eingang auf Anforderung. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/faq/), um eine Erhöhung der Kontogrenzwerte anzufordern.

### <a name="azure-file-share-scale-targets"></a>Skalierbarkeitsziele für Azure-Dateifreigaben
| attribute | Standard-Dateifreigaben <sup>1</sup> | Premium-Dateifreigaben |
|-|-|-|
| Mindestgröße einer Dateifreigabe | Keine Mindestanforderungen | 100 GiB (bereitgestellt) |
| Einheit für Erhöhung/Verringerung der bereitgestellten Größe | Nicht zutreffend | 1 GiB |
| Maximale Größe einer Dateifreigabe | <ul><li>100 TiB, mit aktiviertem Feature für große Dateifreigaben<sup>2</sup></li><li>5 TiB, Standard</li></ul> | ca. 100 TiB |
| Maximale Anzahl an Dateien in einer Dateifreigabe | Keine Begrenzung | Keine Begrenzung |
| Maximale Anforderungsrate (max. IOPS) | <ul><li>20.000 bei aktiviertem Feature für große Dateifreigaben<sup>2</sup></li><li>1\.000 oder 100 Anforderungen pro 100 ms, Standard</li></ul> | <ul><li>IOPS-Grundwert 400 + 1 IOPS pro GiB, bis zu 100.000</li><li>IOPS-Bursting: Max. (4.000, 3 × IOPS pro GiB), bis zu 100.000</li></ul> |
| Maximaler Eingang für eine einzelne Dateifreigabe | <ul><li>Bis zu 300 MiB/s, mit aktiviertem Feature für große Dateifreigaben<sup>2</sup></li><li>Bis zu 60 MiB/s, Standard</li></ul> | 40 MiB/s + 0,04 * bereitgestelltes GiB |
| Maximaler Ausgang für eine einzelne Dateifreigabe | <ul><li>Bis zu 300 MiB/s, mit aktiviertem Feature für große Dateifreigaben<sup>2</sup></li><li>Bis zu 60 MiB/s, Standard</li></ul> | 60 MiB/s + 0,06 * bereitgestelltes GiB |
| Maximale Anzahl von Freigabemomentaufnahmen | 200 Momentaufnahmen | 200 Momentaufnahmen |
| Maximale Objektnamenlänge (Verzeichnisse und Dateien) | 2\.048 Zeichen | 2\.048 Zeichen |
| Maximale Pfadnamenkomponente (im Pfad \A\B\C\D ist jeder Buchstabe eine Komponente) | 255 Zeichen | 255 Zeichen |
| Grenzwert für feste Links (nur NFS) | – | 178 |
| Maximale Anzahl von SMB Multichannel-Kanälen | N/V | 4 |
| Maximale Anzahl gespeicherter Zugriffsrichtlinien pro Dateifreigabe | 5 | 5 |

<sup>1</sup> Die Grenzwerte für Standarddateifreigaben gelten für alle drei Dienstebenen, die für Standarddateifreigaben verfügbar sind: transaktionsoptimiert, heiß und kalt.

<sup>2</sup> Der Standardwert für Standarddateifreigaben ist 5 TiB. Einzelheiten zum Erstellen von Dateifreigaben mit einer Größe von 100 TiB und zum Vergrößern von Standarddateifreigaben auf bis zu 100 TiB finden Sie unter [Erstellen einer Azure-Dateifreigabe](./storage-how-to-create-file-share.md). Um die größeren Skalierungsziele nutzen zu können, müssen Sie Ihr Kontingent so ändern, dass es größer als 5 TiB ist.

### <a name="file-scale-targets"></a>Dateiskalierbarkeitsziele
| attribute | Dateien in Standard-Dateifreigaben  | Dateien in Premium-Dateifreigaben  |
|-|-|-|
| Maximale Dateigröße | 4 TiB | 4 TiB |
| Rate für maximale Anzahl von gleichzeitigen Anforderungen | 1.000IOPS | Bis zu 8.000<sup>1</sup> |
| Maximaler Eingang für eine Datei | 60 MiB/s | 200 MiB/s (bis zu 1 GiB/s bei SMB Multichannel) <sup>2</sup>|
| Maximaler Ausgang für eine Datei | 60 MiB/s | 300 MiB/s (bis zu 1 GiB/s bei SMB Multichannel) <sup>2</sup> |
| Maximale Anzahl gleichzeitiger Handles | 2\.000 Handles | 2\.000 Handles  |

<sup>1 Gilt für E/A-Vorgänge zum Lesen und Schreiben (typischerweise kleinere E/A-Größen, die kleiner oder gleich 64 KiB sind). Metadatenvorgänge (außer Lese-und Schreibvorgängen) können niedriger sein.</sup>
<sup>2 Unterliegen Netzwerkgrenzwerten für Computer, verfügbarer Bandbreite, E/A-Größen, Warteschlangenlänge und anderen Faktoren. Weitere Informationen finden Sie unter [SMB Multichannel-Leistung](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Skalierbarkeitsziele für die Azure-Dateisynchronisierung
Die folgende Tabelle gibt an, welche Grenzen für Tests von Microsoft gelten und welche Ziele feste Grenzwerte sind:

| Resource | Ziel | Harte Grenze |
|----------|--------------|------------|
| Speichersynchronisierungsdienste pro Region | 100 Speichersynchronisierungsdienste | Ja |
| Synchronisierungsgruppen pro Speichersynchronisierungsdienst | 200 Synchronisierungsgruppen | Ja |
| Registrierte Server pro Speichersynchronisierungsdienst | 99 Server | Ja |
| Cloudendpunkte pro Synchronisierungsgruppe | 1 Cloudendpunkt | Ja |
| Serverendpunkte pro Synchronisierungsgruppe | 100 Serverendpunkte | Ja |
| Serverendpunkte pro Server | 30 Serverendpunkte | Ja |
| Dateisystemobjekte (Verzeichnisse und Dateien) pro Synchronisierungsgruppe | 100 Millionen Objekte | Nein |
| Maximale Anzahl von Dateisystemobjekten (Verzeichnisse und Dateien) in einem Verzeichnis **(nicht rekursiv)** | 5 Millionen Objekte | Ja |
| Maximale Sicherheitsbeschreibung des Objekts (Verzeichnisse und Dateien) | 64 KiB | Ja |
| Dateigröße | 100 GB | Nein |
| Minimale Dateigröße für die Unterteilung einer Datei | Basiert auf der Größe des Dateisystemclusters (doppelte Größe des Dateisystemclusters). Wenn die Größe des Dateisystemclusters z. B. 4 KiB beträgt, ist die minimale Dateigröße 8 KiB. | Ja |

> [!Note]  
> Ein Endpunkt für Azure-Dateisynchronisierung kann auf die Größe einer Azure-Dateifreigabe hochskaliert werden. Wenn die maximale Größe der Azure-Dateifreigabe erreicht ist, kann keine Synchronisierung durchgeführt werden.

### <a name="azure-file-sync-performance-metrics"></a>Leistungsmetriken der Azure-Dateisynchronisierung
Da der Azure-Dateisynchronisierungs-Agent auf einem Windows Server-Computer ausgeführt wird, der mit den Azure-Dateifreigaben verbunden wird, hängt die effektive Synchronisierungsleistung von einer Reihe von Faktoren in Ihrer Infrastruktur ab: von Windows Server und der zugrunde liegenden Datenträgerkonfiguration, der Netzwerkbandbreite zwischen dem Server und Azure Storage, der Dateigröße, der gesamten Datasetgröße und der Aktivität im Dataset. Da die Azure-Dateisynchronisierung auf Dateiebene ausgeführt wird, werden die Leistungsmerkmale einer auf der Azure-Dateisynchronisierung basierenden Lösung besser in der Anzahl von Objekten (Dateien und Verzeichnisse) gemessen, die pro Sekunde verarbeitet werden.

Bei der Azure-Dateisynchronisierung ist die Leistung in zwei Phasen entscheidend:

1. **Erste einmalige Bereitstellung**: Informationen zum Optimieren der Leistung bei der ersten Bereitstellung finden Sie unter [Onboarding bei der Azure-Dateisynchronisierung](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync).
2. **Laufende Synchronisierung**: Nachdem zunächst ein Seeding für die Daten in den Azure-Dateifreigaben ausgeführt wird, synchronisiert die Azure-Dateisynchronisierung fortlaufend mehrere Endpunkte.

Wenn Sie die Bereitstellung für jede der Phasen planen, sehen Sie sich im Folgenden die Ergebnisse an, die bei den internen Tests auf einem System mit einer Konfiguration zustande kamen.

| Systemkonfiguration | Details |
|-|-|
| CPU | 64 virtuelle Kerne mit 64-MiB-L3-Cache |
| Arbeitsspeicher | 128 GB |
| Datenträger | SAS-Datenträger mit RAID 10 mit batteriegepuffertem Cache |
| Netzwerk | 1-GBit/s-Netzwerk |
| Workload | Allgemeiner Dateiserver|

| Erste einmalige Bereitstellung  | Details |
|-|-|
| Anzahl der Objekte | 25 Millionen Objekte |
| Datasetgröße| ca. 4,7 TiB |
| Durchschnittliche Dateigröße | ca. 200 KiB (größte Datei: 100 GiB) |
| Anfängliche Enumeration von Cloudänderungen | 20 Objekte pro Sekunde  |
| Uploaddurchsatz | 20 Objekte pro Sekunde pro Synchronisierungsgruppe |
| Durchsatz beim Download von Namespaces | 400 Objekte pro Sekunde |

### <a name="initial-one-time-provisioning"></a>Erste einmalige Bereitstellung

**Anfängliche Enumeration von Cloudänderungen**: Wenn eine neue Synchronisierungsgruppe erstellt wird, ist die anfängliche Enumeration von Cloudänderungen der erste durchgeführte Schritt. In diesem Prozess listet das System alle Elemente in der Azure-Dateifreigabe auf. Während dieses Prozesses findet keine Synchronisierungsaktivität statt. Es werden also keine Elemente vom Cloudendpunkt auf den Serverendpunkt heruntergeladen und keine Elemente vom Serverendpunkt auf den Cloudendpunkt hochgeladen. Die Synchronisierungsaktivität beginnt erst, wenn die anfängliche Enumeration von Cloudänderungen abgeschlossen ist.
Der Durchsatz liegt bei 20 Objekten pro Sekunde. Kunden können die Dauer der anfänglichen Enumeration von Cloudänderungen schätzen, indem sie die Anzahl von Elementen in der Cloudfreigabe bestimmen und den Zeitraum (in Tagen) anhand der folgenden Formel berechnen. 

   **Zeitraum (in Tagen) für die anfängliche Cloudenumeration = (Anzahl von Objekten am Cloudendpunkt) / (20 × 60 × 60 × 24)**

**Erstsynchronisierung von Daten von Windows Server mit der Azure-Dateifreigabe**: Viele Bereitstellungen einer Azure-Dateisynchronisierung beginnen mit einer leeren Azure-Dateifreigabe, weil alle Daten auf dem Windows-Server gespeichert sind. In diesen Fällen ist die anfängliche Enumeration von Cloudänderungen schnell, und der Großteil der Zeit wird für die Synchronisierung von Änderungen vom Windows-Server in die Azure-Dateifreigabe(n) benötigt. 

Obwohl die Synchronisierung Daten in die Azure-Dateifreigabe hochlädt, gibt es auf dem lokalen Dateiserver keine Ausfallzeiten, und Administratoren können [Netzwerklimits einrichten](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits), um die für das Hochladen von Hintergrunddaten beanspruchte Bandbreite einzuschränken.

Die Erstsynchronisierung wird normalerweise durch die anfängliche Uploadrate von 20 Dateien pro Sekunde pro Synchronisierungsgruppe begrenzt. Mithilfe der folgenden Formel zur Berechnung des Zeitraums in Tagen können Kunden schätzen, wie lange es dauert, bis alle ihre Daten in Azure hochgeladen sind:  

   **Zeitraum (in Tagen) zum Hochladen von Dateien in eine Synchronisierungsgruppe = (Anzahl von Objekten am Serverendpunkt)/(20 × 60 × 60 × 24)**

Wenn Sie Ihre Daten in mehrere Serverendpunkte und Synchronisierungsgruppen aufteilen, kann dieser anfängliche Datenupload beschleunigt werden, weil der Upload für mehrere Synchronisierungsgruppen mit einer Rate von jeweils 20 Elementen pro Sekunde parallel durchgeführt werden kann. Zwei Synchronisierungsgruppen würden also mit einer kombinierten Rate von 40 Elementen pro Sekunde ausgeführt. Die Gesamtzeit bis zum Abschluss wäre die geschätzte Zeit für die Synchronisierungsgruppe mit den meisten Dateien, die synchronisiert werden sollen.

**Durchsatz beim Download von Namespaces**: Wenn einer vorhandenen Synchronisierungsgruppe ein neuer Serverendpunkt hinzugefügt wird, lädt der Azure-Dateisynchronisierungs-Agent keine Dateiinhalte vom Cloudendpunkt herunter. Zuerst synchronisiert er den vollständigen Namespace und löst dann im Hintergrund einen Rückruf aus, um die Dateien herunterzuladen, entweder in ihrer Gesamtheit oder bei aktiviertem Cloudtiering in der Cloudtieringrichtliniengruppe für den Serverendpunkt.

| Laufende Synchronisierung  | Details  |
|-|--|
| Anzahl der synchronisierten Objekte| 125.000 Objekte (Änderungsumfang ca. 1 %) |
| Datasetgröße| 50 GiB |
| Durchschnittliche Dateigröße | Ca. 500 KiB |
| Uploaddurchsatz | 20 Objekte pro Sekunde pro Synchronisierungsgruppe |
| Durchsatz bei vollständigen Downloads* | 60 Objekte pro Sekunde |

*Wenn Cloudtiering aktiviert ist, werden Sie wahrscheinlich eine bessere Leistung beobachten, da nur ein Teil der Dateidaten heruntergeladen wird. Die Azure-Dateisynchronisierung lädt die Daten zwischengespeicherter Dateien nur dann herunter, wenn sie auf einem der Endpunkte geändert werden. Bei mehrstufigen oder neu erstellten Dateien lädt der Agent nicht die Dateidaten herunter, sondern synchronisiert lediglich den Namespace auf allen Serverendpunkten. Der Agent unterstützt auch teilweise Downloads von mehrstufigen Dateien, wenn Benutzer auf diese zugreifen. 

> [!Note]  
> Die oben genannten Zahlen sind kein Hinweis auf die Leistung, die bei Ihnen zu erwarten ist. Die tatsächliche Leistung hängt von mehreren Faktoren ab, die am Anfang dieses Abschnitts beschrieben werden.

Als allgemeine Richtlinie für Ihre Bereitstellung sollten Sie einige Dinge berücksichtigen:

- Der Objektdurchsatz wird ungefähr relativ zur Anzahl der Synchronisierungsgruppen auf dem Server skaliert. Das Aufteilen von Daten in mehrere Synchronisierungsgruppen auf einem Server führt zu einem besseren Durchsatz, der zudem durch den Server und Netzwerk begrenzt wird.
- Der Objektdurchsatz ist umgekehrt proportional zum Durchsatz in MiB pro Sekunde. Bei kleineren Dateien tritt ein höherer Durchsatz im Hinblick auf die Anzahl der verarbeiteten Objekte pro Sekunde auf, jedoch einen niedrigeren Durchsatz in MiB pro Sekunde aufweist. Im Gegensatz dazu werden bei größeren Dateien weniger Objekte pro Sekunde verarbeitet, dafür jedoch ein höherer Durchsatz in MiB pro Sekunde erzielt. Der Durchsatz in MiB pro Sekunde wird durch die Azure Files-Skalierungsziele beschränkt.

## <a name="see-also"></a>Weitere Informationen
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md)
