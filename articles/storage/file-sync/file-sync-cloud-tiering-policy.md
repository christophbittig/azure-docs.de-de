---
title: Cloudtieringrichtlinien der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Hier finden Sie Details dazu, wie die Datumsrichtlinie und die Richtlinie für freien Volumespeicherplatz in verschiedenen Szenarien zusammen verwendet werden.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f5f06f87ece24377aac380e80c308b4e40906255
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259311"
---
# <a name="cloud-tiering-policies"></a>Cloudtieringrichtlinien

Für das Cloudtiering sind zwei Richtlinien verfügbar, die bestimmen, welche Dateien in die Cloud ausgelagert werden: die **Richtlinie für freien Volumespeicherplatz** und die **Datumsrichtlinie**.

Die **Richtlinie für freien Volumespeicherplatz** stellt sicher, dass ein angegebener Prozentsatz des lokalen Volumes, auf dem sich der Serverendpunkt befindet, immer frei bleibt. 

Die **Datumsrichtlinie** lagert Dateien aus, auf die zuletzt vor einer bestimmten Mindestanzahl von Tagen nicht zugegriffen wurde. Die Richtlinie für freien Volumespeicherplatz hat immer Vorrang. Ist auf dem Volume nicht genügend freier Speicherplatz für die gemäß der Datumsrichtlinie zu speichernden Dateien verfügbar, setzt die Azure-Dateisynchronisierung die Datumsrichtlinie außer Kraft und lagert die am längsten ungenutzten Dateien weiter aus, bis der erforderliche Prozentsatz an freiem Speicherplatz auf dem Volume erreicht ist.

## <a name="how-both-policies-work-together"></a>Zusammenspiel der beiden Richtlinien

Wir veranschaulichen anhand eines Beispiels, wie diese Richtlinien funktionieren: Angenommen, Sie haben die Azure-Dateisynchronisierung auf einem lokalen Volume mit 500 GiB konfiguriert, und das Cloudtiering wurde nie aktiviert. Ihre Dateifreigabe enthält die folgenden Dateien:

|Dateiname |Letzter Zugriff  |Dateigröße  |Speicherort |
|----------|------------------|-----------|----------|
|Datei 1    | Vor 2 Tagen  | 10 GiB | Server und Azure-Dateifreigabe
|File 2    | Vor 10 Tagen | 30 GiB | Server und Azure-Dateifreigabe
|Datei 3    | vor 1 Jahr | 200 GiB | Server und Azure-Dateifreigabe
|Datei 4    | Vor 1 Jahr und 2 Tagen | 130 GiB | Server und Azure-Dateifreigabe
|Datei 5    | Vor 2 Jahren und 1 Tag | 140 GiB | Server und Azure-Dateifreigabe

**Änderung 1:** Sie haben das Cloudtiering aktiviert, eine Richtlinie für freien Volumespeicherplatz von 20 % festgelegt und die Datumsrichtlinie deaktiviert. Bei dieser Konfiguration stellt das Cloudtiering sicher, dass 20 % (in diesem Fall 100 GiB) des Speicherplatzes frei bleiben und auf dem lokalen Computer verfügbar sind. Folglich beträgt die Gesamtkapazität des lokalen Cache 400 GiB. Diese 400 GiB werden zum Speichern der Dateien verwendet, auf die zuletzt und am häufigsten auf dem lokalen Volume zugegriffen wurde.

Bei dieser Konfiguration werden nur die Dateien 1 bis 4 im lokalen Cache gespeichert, und die Datei 5 wird ausgelagert. Es können also nur 370 GiB der 400 GiB genutzt werden. Datei 5 ist 140 GiB groß und würde bei lokaler Zwischenspeicherung zu einer Überschreitung des Grenzwerts von 400 GiB führen. 

**Änderung 2:** Angenommen, ein Benutzer greift auf Datei 5 zu. Dadurch wird Datei 5 zu der Datei in der Freigabe, auf die zuletzt zugegriffen wurde. Folglich wird Datei 5 im lokalen Cache gespeichert, und um unter dem Grenzwert von 400 GiB zu bleiben, wird Datei 4 ausgelagert. Die folgende Tabelle zeigt, wo die Dateien gespeichert werden, wenn die folgenden Aktualisierungen vorgenommen werden:

|Dateiname |Letzter Zugriff  |Dateigröße  |Speicherort |
|----------|------------------|-----------|----------|
|Datei 5    | vor 2 Stunden | 140 GiB | Server und Azure-Dateifreigabe
|Datei 1    | Vor 2 Tagen  | 10 GiB | Server und Azure-Dateifreigabe
|File 2    | Vor 10 Tagen | 30 GiB | Server und Azure-Dateifreigabe
|Datei 3    | vor 1 Jahr | 200 GiB | Server und Azure-Dateifreigabe
|Datei 4    | Vor 1 Jahr und 2 Tagen | 130 GiB | Azure-Dateifreigabe, lokal ausgelagert

**Änderung 3:** Angenommen, Sie haben die Richtlinien so aktualisiert, dass die datumsbasierte Tieringrichtlinie auf 60 Tage festgelegt ist und die Richtlinie für freien Volumespeicherplatz auf 70 %. Jetzt können nur bis zu 150 GiB im lokalen Cache gespeichert werden. Obwohl auf Datei 2 vor weniger als 60 Tagen zugegriffen wurde, setzt die Richtlinie für freien Volumespeicherplatz die Datumsrichtlinie außer Kraft, und Datei 2 wird ausgelagert, um den freien lokalen Speicherplatz von 70 % beizubehalten.

**Änderung 4:** Wenn Sie die Richtlinie für freien Volumespeicherplatz in 20 % ändern und dann mit `Invoke-StorageSyncFileRecall` alle Dateien abrufen, die unter Einhaltung der Cloudtieringrichtlinien auf das lokale Laufwerk passen, sieht die Tabelle wie folgt aus:

|Dateiname |Letzter Zugriff  |Dateigröße  |Speicherort |
|----------|------------------|-----------|----------|
|Datei 5    | vor 1 Stunde  | 140 GiB | Server und Azure-Dateifreigabe
|Datei 1    | Vor 2 Tagen  | 10 GiB | Server und Azure-Dateifreigabe
|File 2    | Vor 10 Tagen | 30 GiB | Server und Azure-Dateifreigabe
|Datei 3    | vor 1 Jahr | 200 GiB | Azure-Dateifreigabe, lokal ausgelagert
|Datei 4    | Vor 1 Jahr und 2 Tagen | 130 GiB | Azure-Dateifreigabe, lokal ausgelagert

In diesem Fall werden die Dateien 1, 2 und 5 lokal zwischengespeichert und die Dateien 3 und 4 ausgelagert. Aufgrund der Datumsrichtlinie von 60 Tagen werden die Dateien 3 und 4 ausgelagert, obwohl die Richtlinie für freien Volumespeicherplatz die lokale Speicherung von bis zu 400 GiB zulässt.

> [!NOTE] 
> Dateien werden nicht automatisch abgerufen, wenn Kunden die Richtlinie für freien Volumespeicherplatz in einen niedrigeren Wert ändern (z. B. von 20 % in 10 %) oder die Datumsrichtlinie in einen höheren Wert ändern (z. B. von 20 Tagen in 50 Tage).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Mehrere Serverendpunkte auf einem lokalen Volume

Das Cloudtiering kann für mehrere Serverendpunkte auf einem einzelnen lokalen Volume aktiviert werden. Für diese Konfiguration sollten Sie den freien Volumespeicherplatz für alle Serverendpunkte auf demselben Volume auf den gleichen Wert festlegen. Wenn Sie für mehrere Serverendpunkte auf demselben Volume unterschiedliche Richtlinien für freien Volumespeicherplatz festlegen (d. h. verschiedene Prozentsätze), hat der höchste Prozentsatz Vorrang. Dies wird als **effektive Richtlinie für freien Volumespeicherplatz** bezeichnet. Wenn Sie beispielsweise drei Serverendpunkte auf demselben lokalen Volume haben, von denen einer auf 15 % festgelegt ist, einer auf 20 % und einer auf 30 %, beginnen alle mit der Auslagerung der am längsten ungenutzten Dateien, wenn weniger als 30 % freier Speicherplatz verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von Cloudtiering](file-sync-monitor-cloud-tiering.md)
