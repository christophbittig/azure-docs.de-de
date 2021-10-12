---
title: 'Tutorial: Erste Schritte beim Analysieren von Daten mit dedizierten SQL-Pools'
description: In diesem Tutorial verwenden Sie die NYC Taxi-Beispieldaten, um die Analysefunktionen des SQL-Pools zu erforschen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick, wiassaf
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 09/29/2021
ms.openlocfilehash: cf1aabdef95a58c4bdd5a14e3714da37295ed790
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387732"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analysieren von Daten mit dedizierten SQL-Pools

In diesem Tutorial werden die NYC Taxi-Daten verwendet, um die Analysefunktionen eines dedizierten SQL-Pools zu untersuchen.

## <a name="create-a-dedicated-sql-pool"></a>Erstellen eines dedizierten SQL-Pools

1. Wählen Sie in Synapse Studio im linken Bereich unter **Analytics pools** (Analysepools) die Optionen **Verwalten** > **SQL-Pools** aus.
1. Wählen Sie **Neu** aus.
1. Wählen Sie unter **Dedicated SQL pool name** (Name des dedizierten SQL-Pools) den Namen **SQLPOOL1** aus.
1. Wählen Sie für **Leistungsebene** die Option **DW100C** aus.
1. Wählen Sie **Bewerten + erstellen** > **Erstellen** aus. Ihr dedizierter SQL-Pool steht in wenigen Minuten zur Verfügung. 

Ihr dedizierter SQL-Pool ist einer SQL-Datenbank zugeordnet, die auch als **SQLPOOL1** bezeichnet wird.
1. Navigieren Sie zu **Daten** > **Arbeitsbereich**.
1. Eine Datenbank mit dem Namen **SQLPOOL1** sollte angezeigt werden. Wird sie nicht angezeigt, klicken Sie auf **Aktualisieren**.

Ein dedizierter SQL-Pool nutzt abrechenbare Ressourcen, solange er aktiv ist. Sie können den Pool später anhalten, um die Kosten zu senken.

> [!NOTE] 
> Wenn Sie in Ihrem Arbeitsbereich einen neuen dedizierten SQL-Pool (ehemals SQL DW) erstellen, wird die Bereitstellungsseite für den dedizierten SQL-Pool geöffnet. Die Bereitstellung erfolgt auf dem logischen SQL-Server.

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Laden der NYC Taxi-Daten in SQLPOOL1

1. Navigieren Sie in Synapse Studio zum Hub **Entwickeln**, klicken Sie auf die Schaltfläche **+** , um eine neue Ressource hinzuzufügen, und erstellen Sie dann ein neues SQL-Skript.
1. Wählen Sie in der Dropdownliste **Verbinden mit** oberhalb des Skripts den Pool „SQLPOOL1“ (der Pool wurde in [SCHRITT 1](./get-started-create-workspace.md) dieses Tutorials erstellt) aus.
1. Geben Sie den folgenden Code ein:

    ```sql
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Skript auszuführen.
1. Dieses Skript wird weniger als 60 Sekunden abgeschlossen. Es lädt zwei Millionen Zeilen NYC Taxi-Daten in eine Tabelle mit dem Namen `dbo.NYCTaxiTripSmall`.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Untersuchen der NYC Taxi-Daten im dedizierten SQL-Pool

1. Navigieren Sie in Synapse Studio zum Hub **Daten**.
1. Navigieren Sie zu **SQLPOOL1** > **Tabellen**. 
3. Klicken Sie mit der rechten Maustaste auf die Tabelle **dbo.NYCTaxiTripSmall**, und wählen Sie **Neues SQL-Skript** > **OBERSTE 100 Zeilen auswählen** aus.
4. Warten Sie, während ein neues SQL-Skript erstellt und ausgeführt wird.
5. Beachten Sie, dass am oberen Rand des SQL-Skripts **Verbinden mit** automatisch auf den SQL-Pool mit dem Namen **SQLPOOL1** festgelegt ist.
6. Ersetzen Sie den Text des SQL-Skripts durch diesen Code, und führen Sie ihn aus.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Diese Abfrage zeigt, wie die Gesamtzahl der Fahrtstrecken und die durchschnittliche Fahrtstrecke mit der Anzahl der Fahrgäste in Beziehung stehen.
1. Ändern Sie im Ergebnisfenster des SQL-Skripts die **Ansicht** in **Diagramm**, um eine Visualisierung der Ergebnisse als Liniendiagramm anzuzeigen.
    
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren von Daten in einem Azure Storage-Konto](get-started-analyze-storage.md)
