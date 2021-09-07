---
title: 'Konfigurieren von Serverparametern – Azure-Portal – Azure Database for PostgreSQL: Flexible Server'
description: 'In diesem Artikel erfahren Sie, wie Sie die Postgres-Parameter in Azure Database for PostgreSQL: Flexibler Server im Azure-Portal konfigurieren.'
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 1ca1501f790f914412fa61fce5ceed5ba45130b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355354"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Konfigurieren von Serverparametern in Azure Database for PostgreSQL: Flexible Server über das Azure-Portal 
Sie können Konfigurationsparameter für einen Azure Database for PostgreSQL-Server mit dem Azure-Portal auflisten, anzeigen und aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Azure Database for PostgreSQL: Flexibler Server](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Anzeigen und Bearbeiten von Parametern
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.

3. Wählen Sie im Abschnitt **EINSTELLUNGEN** die Option **Serverparameter**. Auf der Seite werden eine Liste mit Parametern und die dazugehörigen Werte und Beschreibungen angezeigt.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Übersichtsseite für Parameter":::

4. Wählen Sie die **Dropdownschaltfläche** , um die möglichen Werte für Enumerierungsparameter wie „client_min_messages“ anzuzeigen.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Dropdownliste für Enumerierung":::

5. Wählen Sie die Schaltfläche **i** (Informationen), oder zeigen Sie mit der Maus darauf, um den Bereich mit den möglichen Werten für numerische Parameter wie „cpu_index_tuple_cost“ anzuzeigen.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Schaltfläche „Informationen“":::

6. Verwenden Sie bei Bedarf das **Suchfeld**, um nach einem bestimmten Parameter zu suchen. Die Suche wird für den Namen und die Beschreibung der Parameter durchgeführt.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Suchergebnisse":::

7. Ändern Sie die Parameterwerte, die Sie anpassen möchten. Alle in einer Sitzung vorgenommenen Änderungen werden violett hervorgehoben. Nachdem Sie die Werte geändert haben, können Sie **Speichern** wählen. Sie können Ihre Änderungen auch **verwerfen**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Änderungen speichern oder verwerfen":::

8. Wenn Sie neue Werte für die Parameter gespeichert haben, können Sie jederzeit alles zurück auf die Standardwerte setzen, indem Sie die Option **Alle auf Standard zurücksetzen** wählen.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Alle auf Standard zurücksetzen":::

## <a name="working-with-time-zone-parameters"></a>Verwenden von Zeitzonenparametern
Wenn Sie mit Datums- und Uhrzeitdaten in PostgreSQL arbeiten möchten, achten Sie darauf, dass Sie die richtige Zeitzone für Ihren Standort festgelegt haben. Alle zeitzonenspezifischen Datums- und Uhrzeitangaben werden intern in Postgres im UTC-Format gespeichert. Sie werden in die durch den Parameter **TimeZone** angegebene Ortszeit der Zone konvertiert, bevor sie dem Client angezeigt werden.  Dieser Parameter kann wie oben erläutert auf der Seite **Serverparameter** bearbeitet werden. PostgreSQL ermöglicht die Angabe von Zeitzonen in drei verschiedenen Formaten:
1. Ein vollständiger Zeitzonenname (beispielsweise „America/New_York“). Die zulässigen Zeitzonennamen sind in der Ansicht [**pg_timezone_names**](https://www.postgresql.org/docs/9.2/view-pg-timezone-names.html) aufgeführt.  
   Beispiel zum Abfragen dieser Ansicht in psql und zum Abrufen der Liste mit den Zeitzonennamen:
   <pre>select name FROM pg_timezone_names LIMIT 20;</pre>

   Das Resultset sollte in etwa wie folgt aussehen:

   <pre>
            name
        -----------------------
        GMT0
        Iceland
        Factory
        NZ-CHAT
        America/Panama
        America/Fort_Nelson
        America/Pangnirtung
        America/Belem
        America/Coral_Harbour
        America/Guayaquil
        America/Marigot
        America/Barbados
        America/Porto_Velho
        America/Bogota
        America/Menominee
        America/Martinique
        America/Asuncion
        America/Toronto
        America/Tortola
        America/Managua
        (20 rows)
    </pre>
   
2. Eine Zeitzonenabkürzung (beispielsweise PST). Durch eine solche Angabe wird lediglich ein bestimmter UTC-Versatz definiert. Vollständige Zeitzonennamen können dagegen auch eine Reihe von Regeln für das Sommerzeit-Übergangsdatum implizieren. Die zulässigen Abkürzungen sind in der Ansicht [**pg_timezone_abbrevs**](https://www.postgresql.org/docs/9.4/view-pg-timezone-abbrevs.html) aufgeführt. Beispiel zum Abfragen dieser Ansicht in psql und zum Abrufen der Liste mit den Zeitzonenabkürzungen:

   <pre> select abbrev from pg_timezone_abbrevs limit 20;</pre>

    Das Resultset sollte in etwa wie folgt aussehen:

     <pre>
        abbrev|
        ------+
        ACDT  |
        ACSST |
        ACST  |
        ACT   |
        ACWST |
        ADT   |
        AEDT  |
        AESST |
        AEST  |
        AFT   |
        AKDT  |
        AKST  |
        ALMST |
        ALMT  |
        AMST  |
        AMT   |
        ANAST |
        ANAT  |
        ARST  |
        ART   |
    </pre>

3. Neben den Zeitzonennamen und -abkürzungen akzeptiert PostgreSQL POSIX-Zeitzonenangaben im Format „STDoffset“ oder „STDoffsetDST“. Dabei ist „STD“ eine Zonenabkürzung, „offset“ ein numerischer Versatz in Stunden westlich von UTC und „DST“ eine optionale Abkürzung für die Sommerzeitzone (steht für eine Stunde später als der angegebene Versatz). 
   

## <a name="next-steps"></a>Nächste Schritte
Sie erhalten Informationen zu folgenden Themen:
- [Azure Database for PostgreSQL-Server](concepts-server-parameters.md)
- [Anpassen der Serverparameter für Azure Database for PostgreSQL (Flexibler Server) mithilfe der Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md)
  