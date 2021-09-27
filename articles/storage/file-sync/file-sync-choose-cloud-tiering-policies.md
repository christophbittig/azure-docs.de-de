---
title: Auswählen von Cloudtieringrichtlinien der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Es wird beschrieben, was Sie beim Auswählen von Cloudtieringrichtlinien für die Azure-Dateisynchronisierung beachten müssen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8cd8b51531fa28e3d698de47358c3b69e4f88ec2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665832"
---
# <a name="choose-cloud-tiering-policies"></a>Auswählen von Cloudtieringrichtlinien

Dieser Artikel enthält eine Anleitung für Benutzer, die ihre Cloudtieringrichtlinien auswählen und anpassen möchten. Machen Sie sich vor dem Lesen dieses Artikels mit der Funktionsweise des Cloudtierings vertraut. Informationen zu den Grundlagen des Cloudtierings finden Sie in der [Übersicht über das Cloudtiering](file-sync-cloud-tiering-overview.md). Eine ausführliche Beschreibung der Cloudtieringrichtlinien mit Beispielen finden Sie unter [Cloudtieringrichtlinien](file-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Einschränkungen

- Das Cloudtiering wird auf dem Systemvolume von Windows nicht unterstützt.

- Sie können das Cloudtiering trotzdem aktivieren, falls Sie über ein FSRM-Kontingent auf Volumeebene verfügen. Nachdem ein FSRM-Kontingent festgelegt wurde, melden die APIs für die Abfrage des freien Speicherplatzes, die automatisch aufgerufen werden, den freien Speicherplatz auf dem Volume gemäß der Kontingenteinstellung.

### <a name="minimum-file-size-for-a-file-to-tier"></a>Mindestdateigröße für Dateien, für die das Tiering durchgeführt werden soll

Die Mindestdateigröße für Dateien, für die ein Tiering durchgeführt werden soll, basiert ab den Agent-Versionen 9.x auf der Größe des Dateisystemclusters. Die Mindestdateigröße für Cloudtiering wird durch Verdopplung der Clustergröße berechnet und muss mindestens 8 KiB betragen. In der folgenden Tabelle sind die Mindestdateigrößen auf Grundlage der Volumeclustergröße aufgeführt, für die ein Tiering möglich ist:

|Volumeclustergröße  |Mindestdateigröße für Tiering  |
|----------------------------|---------|
|4 KiB oder kleiner (4096 Bytes)      | 8 KiB    |
|8 KiB (8192 Bytes)                 | 16 KiB   |
|16 KiB (16384 Bytes)               | 32 KiB   |
|32 KiB (32768 Bytes)               | 64 KiB   |
|64 KiB (65536 Bytes)    | 128 KiB  |
|128 KiB (131072 Bytes) | 256 KiB |
|256 KiB (262144 Bytes) | 512 KiB |
|512 KiB (524288 Bytes) | 1 MiB |
|1 MiB (1048576 Bytes) | 2 MiB |
|2 MiB (2097152 Bytes) | 4 MiB |

Clustergrößen bis zu 2 MB werden mit dem Azure-Dateisynchronisierungs-Agent, Version 12, unterstützt, aber bei größeren Größen funktioniert das Cloudtiering nicht.

Alle von Windows verwendeten Dateisysteme organisieren Ihre Festplatte auf Grundlage der Clustergröße (auch als Größe der Zuordnungseinheiten bezeichnet). Die Clustergröße stellt die kleinste Menge an Speicherplatz dar, die zum Speichern einer Datei verwendet werden kann. Wenn Dateigrößen kein gerades Vielfaches der Clustergröße ergeben, muss zum Speichern der Datei zusätzlicher Speicherplatz verwendet werden – bis zum nächsten Vielfachen der Clustergröße.

Die Azure-Dateisynchronisierung wird auf NTFS-Volumes mit Windows Server 2012 R2 und höher unterstützt. In der folgenden Tabelle sind die Standardclustergrößen für das Erstellen eines neuen NTFS-Volumes mit Windows Server 2019 aufgeführt.

|Volumegröße    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MiB – 16 TiB   | 4 KiB                |
|16 TiB – 32 TiB   | 8 KiB                |
|32 TiB – 64 TiB   | 16 KiB               |

Möglicherweise haben Sie das Volume bei der Erstellung manuell mit einer anderen Clustergröße formatiert. Wenn das Volume von einer älteren Version von Windows stammt, können die Standardclustergrößen ebenfalls abweichen. [In diesem Artikel finden Sie weitere Informationen zu den Standardclustergrößen.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Auch wenn Sie eine Clustergröße unter 4 KiB auswählen, gilt weiterhin der Grenzwert von 8 KiB als kleinste Dateigröße, für die ein Tiering durchgeführt werden kann. (Auch wenn technisch gesehen das Doppelte der Clustergröße weniger als 8 KiB wäre.)

Der Grund für diesen absoluten Minimalwert findet sich in der Art und Weise, in der NTFS sehr kleine Dateien speichert, also mit Dateigrößen von 1 bis 4 KiB. Je nach den Parametern des Volumes ist es möglich, dass kleine Dateien überhaupt nicht in einem Cluster oder auf einem Datenträger gespeichert werden. Möglicherweise ist es effizienter, solche Dateien direkt im Datensatz der Masterdateitabelle (Master File Table, MFT) des Volumes zu speichern. Der Analysepunkt für das Cloudtiering wird immer auf dem Datenträger gespeichert und belegt genau einen Cluster. Bei so kleinen Dateien spart ein Tiering möglicherweise keinen Speicherplatz. In extremen Fällen könnte es sogar vorkommen, dass ein aktiviertes Tiering zu einer höheren Speicherplatzbelegung führt. Zum Schutz vor solchen Situationen ist die kleinste Dateigröße, für die ein Cloudtiering ausgeführt werden kann, 8 KiB in einem Cluster mit 4 KiB oder weniger.

## <a name="selecting-your-initial-policies"></a>Auswählen Ihrer anfänglichen Richtlinien

Im Allgemeinen sollten Sie beim Aktivieren des Cloudtierings auf einem Serverendpunkt ein lokales virtuelles Laufwerk für jeden Serverendpunkt erstellen. Wenn Sie die Serverendpunkte isolieren, können Sie sich leichter damit vertraut machen, wie das Cloudtiering funktioniert, und Ihre Richtlinien entsprechend anpassen. Die Azure-Dateisynchronisierung funktioniert aber auch, wenn Sie mehrere Serverendpunkte auf demselben Laufwerk verwenden. Ausführlichere Informationen hierzu finden Sie im Abschnitt [Mehrere Serverendpunkte auf einem lokalen Volume](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume). Darüber hinaus empfehlen wir Ihnen, beim erstmaligen Aktivieren des Cloudtierings die Datumsrichtlinie deaktiviert zu lassen und die Richtlinie für den freien Volumespeicherplatz auf 10 bis 20 % festzulegen. Für die meisten Volumes von Dateiservern ist ein freier Speicherplatz von 20 % normalerweise die beste Option.

Der Einfachheit halber und zum besseren Verständnis des Tierings von Elementen empfehlen wir Ihnen, vor allem Ihre Richtlinie für den freien Speicherplatz auf Volumes anzupassen und die Datumsrichtlinie deaktiviert zu lassen, sofern Sie diese nicht zwingend benötigen. Der Grund für diese Empfehlung ist, dass es für die meisten Kunden hilfreich ist, den lokalen Cache mit so vielen „heißen“ Dateien wie möglich zu füllen und für die restlichen Dateien das Tiering in der Cloud durchzuführen. Die Datumsrichtlinie kann aber nützlich sein, wenn Sie Speicherplatz auf dem lokalen Datenträger proaktiv freigeben möchten und wissen, dass Dateien auf diesem Serverendpunkt, auf die nach der in der Datumsrichtlinie angegebenen Anzahl von Tagen zugegriffen wird, nicht lokal vorgehalten werden müssen. Durch das Festlegen der Datumsrichtlinie wird wertvolle lokale Festplattenkapazität für andere Endpunkte auf demselben Volume freigegeben, damit mehr Dateien zwischengespeichert werden können.

Nachdem Sie Ihre Richtlinien festgelegt haben, sollten Sie den ausgehenden Datenverkehr überwachen und die beiden Richtlinien entsprechend anpassen. Wir empfehlen Ihnen, in Azure Monitor vor allem auf die Metriken für die **Cloudtiering-Abrufgröße** und **Cloudtiering-Abrufgröße nach Anwendung** zu achten. Informationen zur Überwachung des ausgehenden Datenverkehrs finden Sie unter [Überwachen des Cloudtierings](file-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Anpassen Ihrer Richtlinien

Falls die Menge an ständig aus Azure abgerufenen Dateien größer als gewünscht ist, verfügen Sie ggf. über mehr heiße Dateien, als auf dem lokalen Servervolume gespeichert werden können. Erhöhen Sie nach Möglichkeit die Größe Ihres lokalen Volumes, bzw. verringern Sie in kleinen Schritten den Prozentsatz in der Richtlinie für den freien Volumespeicherplatz. Wenn Sie den Prozentsatz für den freien Volumespeicherplatz zu stark verringern, kann dies auch negative Auswirkungen haben. Je höher die Datenänderungsrate in Ihrem Dataset ist, desto mehr freier Speicherplatz wird benötigt – für neue Dateien und das Abrufen von „kalten“ Dateien. Bis zum Beginn des Tieringvorgangs kann es zu einer Verzögerung von bis zu einer Stunde kommen, und dann fällt eine gewisse Verarbeitungsdauer an. Aus diesem Grund sollten Sie auf Ihrem Volume immer über ausreichend freien Speicherplatz verfügen.

Wenn Sie mehr Daten lokal speichern, fallen geringere Kosten für ausgehenden Datenverkehr an, da weniger Dateien aus Azure zurückgerufen werden. Sie müssen aber eine größere Menge von lokalem Speicher vorhalten, und dies ist ebenfalls mit Kosten verbunden.

Beim Anpassen Ihrer Richtlinie für den freien Volumespeicherplatz wird die Menge an Daten, die Sie lokal vorhalten sollten, anhand der folgenden Faktoren bestimmt: Ihrer Bandbreite, dem Zugriffsmuster des Datasets und dem Budget. Bei einer Verbindung mit geringer Bandbreite kann es unter Umständen hilfreich sein, mehr Daten lokal zu speichern, um die Verzögerung für Benutzer möglichst gering zu halten. Andernfalls können Sie als Baseline die Änderungsrate in einem bestimmten Zeitraum verwenden. Wenn Sie beispielsweise wissen, dass sich jeden Monat 10 % Ihres 1-TiB-Datasets ändern bzw. hierfür aktive Zugriffe erfolgen, sollten Sie 100 GiB lokal speichern, damit nicht zu häufig Dateien abgerufen werden müssen. Wenn das Volume 2 TiB groß ist, sollten Sie 5 % (100 GiB) lokal speichern. Dies bedeutet, dass die verbleibenden 95 % der Anteil des freien Speicherplatzes auf dem Volume sind. Es ist aber ratsam, einen Puffer für Zeiträume mit höheren Datenänderungsraten einzubauen. Anders ausgedrückt: Sie sollten mit einem höheren Anteil an freiem Speicherplatz auf dem Volume beginnen und diesen später dann je nach Bedarf anpassen.

## <a name="standard-operating-procedures"></a>Standardverfahren für den Betrieb

- Bei der ersten Migration zu Azure Files über die Azure-Dateisynchronisierung hängt das Cloudtiering vom anfänglichen Upload ab.
- Beim Cloudtiering wird die Konformität für den freien Volumespeicherplatz und die Datumsrichtlinien alle sechzig Minuten überprüft.
- Indem Sie den Schalter „/LFSM“ beim Migrieren von Dateien in Robocopy verwenden, können Dateien synchronisiert werden, und per Cloudtiering kann während des anfänglichen Uploads Speicherplatz bereitgestellt werden.
- Wenn das Tiering durchgeführt wird, bevor ein Wärmebild erstellt wird, wird für die Dateien der Zeitstempel der letzten Änderung zugrunde gelegt.

## <a name="next-steps"></a>Nächste Schritte

- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](file-sync-planning.md)
