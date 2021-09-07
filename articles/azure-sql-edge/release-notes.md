---
title: Versionshinweise für Azure SQL Edge
description: Versionshinweise mit Informationen zu Neuerungen oder Änderungen in den Azure SQL Edge-Images
keywords: Versionshinweise für SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: 29ab3c33611b0f525b28fe51245edba377bae0b5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441425"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge: Versionshinweise 

In diesem Artikel erhalten Sie Informationen zu Neuerungen und Änderungen für neue Azure SQL Edge-Builds.

## <a name="azure-sql-edge-104"></a>Azure SQL Edge 1.0.4

SQL-Engine-Build 15.0.2000.1559

### <a name="whats-new"></a>Neuigkeiten

- PREDICT-Unterstützung für ONNX 
    - Verbesserungen bei der Verarbeitung von NULL-Daten in PREDICT für ONNX

## <a name="azure-sql-edge-103"></a>Azure SQL Edge 1.0.3

SQL-Engine-Build 15.0.2000.1557

### <a name="fixes"></a>Fehlerbehebungen

- Es wurde ein Upgrade der ONNX-Runtime auf Version 1.5.3 durchgeführt.
- Aktualisieren auf Microsoft.SqlServer.DACFx Version 150.5084.2
- Verschiedene Fehlerbehebungen  
   
## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL-Engine-Build 15.0.2000.1557

### <a name="fixes"></a>Fehlerbehebungen

- T-SQL-Streaming  
   - Korrektur bei Besitz und Berechtigungen für Streamingobjekte
   - Verbesserungen bei der Protokollierung mit Protokollrotation und Voranstellen eines Präfixes für Protokolle
   - Azure Stream Analytics: Verbesserungen bei der Protokollierung, Verbesserung der Fehlercodes/Fehlermeldungen in Adaptern 

- ONNX
    - Fehlerbehebungen für parallele Abfrageszenarien und Modellbereinigungsfehler
    - Es wurde ein Upgrade der ONNX-Runtime auf Version 1.5.1 durchgeführt.

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL-Engine-Build 15.0.2000.1553

### <a name="whats-new"></a>Neuigkeiten

- In berechneten Spalten definierter Date_Bucket-Ausdruck wird zugelassen.

### <a name="fixes"></a>Fehlerbehebungen

- Korrektur der Aufbewahrungsrichtlinie für das Löschen einer Tabelle mit aktivierter Aufbewahrungsrichtlinie mit unbegrenztem Timeout
- Unterstützung von Streaming- und Aufbewahrungsrichtlinienfeatures durch die DacFx-Bereitstellung 
- Korrektur der DacFx-Bereitstellung zum Aktivieren der Bereitstellung aus einem geschachtelten Ordner in einer SAS-URL 
- PREDICT-Korrektur zur Unterstützung langer Spaltennamen in Fehlermeldungen

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL-Engine-Build 15.0.2000.1552

### <a name="whats-new"></a>Neuigkeiten
- Auf Ubuntu 18.04 basierende Containerimages 
- Unterstützung für `IGNORE NULL`- und `RESPECT NULL`-Syntax mit `LAST_VALUE()`- und `FIRST_VALUE()`-Funktionen 
- Verbesserungen der Zuverlässigkeit für PREDICT mit ONNX
- Unterstützung für auf der Datenaufbewahrungsrichtlinie basierende Bereinigung:
   - Ringpufferunterstützung für die Aufgabe „Bereinigung der Datenaufbewahrung“ zur Problembehandlung
- Unterstützung für neue Funktionen: 
   - Schnelle Wiederherstellung
   - Automatische Optimierung von Abfragen
   - Szenarien mit paralleler Ausführung
- Verbesserungen der Energiesparfunktion bei niedrigem Energiestatus
- Unterstützung für neue Streamingfunktionen: 
   - [Momentaufnahmefenster:](/stream-analytics-query/snapshot-window-azure-stream-analytics) Ein neuer Fenstertyp ermöglicht Ihnen, nach Ereignissen zu gruppieren, die gleichzeitig eintreffen.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) und [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) können als Analysefunktionen aktiviert werden. Sie können Datensätze sortiert nach einer Spalte Ihrer Wahl zurückgeben. Sie müssen nicht Teil eines Fensters sein. 
   - Verbesserungen bei [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Fehlerbehebungen
- Zusätzliche Fehlermeldungen und Details zur Problembehandlung bei T-SQL-Streamingvorgängen 
- Verbesserungen zur Verlängerung der Akkulebensdauer im Leerlaufmodus 
- Korrekturen für die T-SQL-Streaming-Engine: 
   - Bereinigung für beendete Streamingaufträge 
   - Korrekturen für die Lokalisierung 
   - Verbesserte Unicode-Behandlung 
   - Verbessertes Debuggen bei SQL Edge-T-SQL-Streaming, sodass Benutzer Auftragsfehler aus „get_streaming_job“ abfragen können
- Auf der Datenaufbewahrungsrichtlinie basierende Bereinigung: 
    - Korrekturen für die Erstellung von Aufbewahrungsrichtlinien und Bereinigungsszenarien
- Korrekturen für Timertasks im Hintergrund zur Verbesserung der Energieeinsparungen für den Energiesparmodus

### <a name="known-issues"></a>Bekannte Probleme 
- Die T-SQL-Funktion „Date_Bucket“ kann in einer berechneten Spalte nicht verwendet werden.


## <a name="ctp-23"></a>CTP 2.3
SQL-Engine-Build 15.0.2000.1549
### <a name="whats-new"></a>Neuigkeiten
- Unterstützung für benutzerdefinierte Ursprünge in der Date_Bucket()-Funktion 
- Unterstützung für BACPAC-Dateien als Teil der SQL-Bereitstellung
- Unterstützung für auf der Datenaufbewahrungsrichtlinie basierende Bereinigung:      
   - DDL-Unterstützung für das Aktivieren der Aufbewahrungsrichtlinie 
   - Bereinigung gespeicherter Prozeduren und des Bereinigungstasks im Hintergrund
   - Erweiterte Ereignisse zum Überwachen von Bereinigungstasks

### <a name="fixes"></a>Fehlerbehebungen
- Zusätzliche Fehlermeldungen und Details zur Problembehandlung bei T-SQL-Streamingvorgängen 
- Verbesserungen zur Verlängerung der Akkulebensdauer im Leerlaufmodus 
- T-SQL-Streaming-Engine: 
   - Korrektur des hängenden Wasserzeichens mit springendem Substreamfenster 
   - Korrektur der Frameworkausnahmebehandlung, um sicherzustellen, dass die Erfassung als Fehler mit möglicher Benutzeraktion erfolgt


## <a name="ctp-22"></a>CTP 2.2
SQL-Engine-Build 15.0.2000.1546
### <a name="whats-new"></a>Neuigkeiten
- Unterstützung für Container ohne Root-Berechtigung 
- Unterstützung für Verbrauchs- und Diagnosedatensammlung 
- T-SQL-Streamingupdates:
   - Unterstützung für Unicode-Zeichen für Streamobjektnamen

### <a name="fixes"></a>Fehlerbehebungen
- T-SQL-Streamingupdates:
   - Verbesserungen der Prozessbereinigung
   - Verbesserungen bei Protokollierung und Diagnose
- Leistungsverbesserung bei der Datenerfassung

## <a name="ctp-21"></a>CTP 2.1 
SQL-Engine-Build 15.0.2000.1545
### <a name="fixes"></a>Fehlerbehebungen
- Vorhersage (PREDICT) mit ONNX-Modellen zugelassen, um das CPUID-Problem in ARM zu behandeln 
- Verbesserte Behandlung des Fehlerpfads beim Start des T-SQL-Streamings
- Wert der Wasserzeichenverzögerung in Auftragsmetriken korrigiert, wenn keine Daten vorhanden sind
- Problem mit dem Ausgabeadapter behoben, wenn der Adapter über ein variables Schema zwischen Batches verfügt  

## <a name="ctp-20"></a>CTP 2.0 
SQL-Engine-Build 15.0.2000.1401
### <a name="whats-new"></a>Neuigkeiten
-   Produktname in *Azure SQL Edge* geändert
-  Date_Bucket-Funktion:
    - Unterstützung der Typen „Date“, „Time“ und „DateTime“
- Vorhersage (PREDICT) mit ONNX
    - ONNX-Anforderung für den RUNTIME-Parameter  
- Unterstützung von T-SQL-Streaming (eingeschränkte Vorschau) 
 
### <a name="known-issues"></a>Bekannte Probleme

- Problem: Mögliche Fehler beim Anwenden von DACPAC beim Start aufgrund eines Zeitsteuerungsproblems.
- Problemumgehung: Starten Sie SQL Server neu. Andernfalls versucht der Container erneut, das DACPAC anzuwenden.

### <a name="request-support"></a>Anfordern von Support
Support kann auf der [Supportseite](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) angefordert werden. Wählen Sie die unten aufgeführten Felder aus: 
- **Problemtyp**: *Technisch* 
- **Dienst**: *IoT Edge*
- **Problemtyp:** *Mein Problem bezieht sich auf ein IoT Edge-Modul*
- **Problemuntertyp:** *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Screenshot eines Beispielsupporttickets":::

## <a name="ctp-15"></a>CTP 1.5
SQL-Engine-Build 15.0.2000.1331
### <a name="whats-new"></a>Neuigkeiten
- Date_Bucket-Funktion:
    - Unterstützung des Typs „DateTimeOffset“
- Vorhersage (PREDICT) mit ONNX-Modellen:
  - NVARCHAR-Unterstützung
 
## <a name="ctp-14"></a>CTP 1.4
SQL-Engine-Build 15.0.2000.1247
### <a name="whats-new"></a>Neuigkeiten
-   Vorhersage (PREDICT) mit ONNX-Modellen:
    - VARCHAR-Unterstützung
    - Migration zur ONNX-Runtimeversion 1.0 

- Die folgenden Features werden aktiviert:
  - CDC-Unterstützung
  - Verlaufstabelle mit Komprimierung
  - Höherer Skalierungsfaktor für Protokoll-Read-Ahead
  - ES-Filterweitergabe für Batchmodus
  - Read-Ahead-Optimierungen
 
## <a name="ctp-13"></a>CTP 1.3
SQL-Engine-Build 15.0.2000.1147
### <a name="whats-new"></a>Neuigkeiten
- Azure IoT-Portalbereitstellung: 
    - Unterstützung der Bereitstellung von AMD64- und ARM-Images
    - Unterstützung der Streamingauftragserstellung
    - DACPAC-Bereitstellung
- Vorhersage (PREDICT) mit ONNX-Modellen:
    - Unterstützung numerischer Typen
- Die folgenden Features werden aktiviert:
    - Weitergabe von Aggregat an Columnstore-Scan
    - Karussellscans
- Verringerung des Speicher- und Arbeitsspeicherbedarfs
