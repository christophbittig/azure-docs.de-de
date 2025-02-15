---
title: Zugreifen auf Protokolle für langsame Abfragen – Azure-Portal – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie die Protokolle für langsame Abfragen in Azure Database for MySQL im Azure-Portal konfigurieren und aus dem Portal auf die Protokolle zugreifen.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 0ac29b34600f03b7270bd717ad9f5147cc41c7cb
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639731"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurieren von und Zugreifen auf Protokolle für langsame Abfragen im Azure-Portal

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Sie können die [Protokolle für langsame Abfragen für Azure Database for MySQL](concepts-server-logs.md) im Azure-Portal konfigurieren und auflisten sowie aus dem Portal herunterladen.

## <a name="prerequisites"></a>Voraussetzungen
Für die Schritte in diesem Artikel ist es erforderlich, dass Sie über [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md) verfügen.

## <a name="configure-logging"></a>Konfigurieren der Protokollierung
Konfigurieren Sie den Zugriff auf das MySQL-Protokoll für langsame Abfragen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie Ihren Server für Azure Database for MySQL aus.

3. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Serverprotokolle** aus. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot der Optionen für Serverprotokolle":::

4. Um die Serverparameter anzuzeigen, wählen Sie **Klicken Sie hier, um Protokolle zu aktivieren und Protokollparameter zu konfigurieren** aus.

5. Aktivieren Sie **slow_query_log** mit **ON**.

6. Wählen Sie mit **log_output** aus, wohin die Protokolle ausgegeben werden sollen. Wenn Protokolle sowohl in den lokalen Speicher als auch in die Azure Monitor-Diagnoseprotokolle gesendet werden sollen, wählen Sie **Datei** aus.

7. Sie sollten die Einstellung „long_query_time“ festlegen. Sie stellt den Schwellenwert für die Abfragezeit für die Abfragen dar, die in der Protokolldatei für langsame Abfragen erfasst werden. Der Mindestwert von „long_query_time“ ist „0“, und der Standardwert lautet „10“.

8. Passen Sie auch andere Parameter an, z. B. „log_slow_admin_statements“ für das Protokollieren von administrativen Anweisungen. Standardmäßig werden administrative Anweisungen nicht protokolliert. Dies gilt auch für Abfragen, bei denen für die Suche keine Indizes verwendet werden. 

9. Wählen Sie **Speichern** aus. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot der Parameter von „Protokoll für langsame Abfrage“ mit „Speichern“.":::

Von der Seite **Serverparameter** können Sie zur Liste der Protokolle zurückkehren, indem Sie die Seite schließen.

## <a name="view-list-and-download-logs"></a>Anzeigen der Liste und Herunterladen von Protokollen
Nachdem die Protokollierung begonnen hat, können Sie eine Liste der verfügbaren Protokolle für langsame Abfragen anzeigen und einzelne Protokolldateien herunterladen.

1. Öffnen Sie das Azure-Portal.

2. Wählen Sie Ihren Server für Azure Database for MySQL aus.

3. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Serverprotokolle** aus. Die Seite zeigt eine Liste der Protokolldateien an.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Screenshot der Seite „Serverprotokolle“ mit hervorgehobener Liste der Protokolle":::

   > [!TIP]
   > Die Namenskonvention des Protokolls ist **mysql-slow-<Name_Ihres_Servers>-jjjjmmtthh.log**. Das im Dateinamen verwendete Datum und die Uhrzeit geben den Zeitpunkt an, zu dem das Protokoll ausgestellt wurde. Die Protokolldateien werden alle 24 Stunden oder bei Erreichen einer Größe von 7,5 GB rotiert. 

4. Verwenden Sie bei Bedarf das Suchfeld, um schnell ein spezifisches Protokoll basierend auf Datum und Uhrzeit einzugrenzen. Die Suche erfolgt anhand des Namens des Protokolls.

5. Um einzelne Protokolldateien herunterzuladen, wählen Sie das Pfeilsymbol nach unten neben den einzelnen Protokolldateien in der Tabellenzeile aus.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Screenshot der Seite „Serverprotokolle“ mit hervorgehobenem Pfeilsymbol nach unten":::

## <a name="set-up-diagnostic-logs"></a>Einrichten von Diagnoseprotokollen

1. Wählen Sie auf der Randleiste im Abschnitt **Überwachung** die Option **Diagnoseeinstellungen** > **Diagnoseeinstellungen hinzufügen** aus.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot der Optionen für Diagnoseeinstellungen":::

2. Geben Sie einen Namen für die Diagnoseeinstellung ein.

3. Geben Sie an, an welche Datensenken die langsamen Abfrageprotokolle gesendet werden sollen (Speicherkonto, Event Hub oder Log Analytics-Arbeitsbereich).

4. Wählen Sie als Protokolltyp **MySqlSlowLogs** aus.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot der Konfigurationsoptionen für Diagnoseeinstellungen":::

5. Nachdem Sie die Datensenken für die langsamen Abfrageprotokolle konfiguriert haben, wählen Sie **Speichern** aus.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot der Konfigurationsoptionen für Diagnoseeinstellungen mit hervorgehobener Option „Speichern“":::

6. Greifen Sie auf die langsamen Abfrageprotokolle zu, indem Sie sie in den von Ihnen konfigurierten Datensenken einsehen. Es kann bis zu 10 Minuten dauern, bis die Protokolle angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie [Konfigurieren von und Zugreifen auf Protokolle für langsame Abfragen mithilfe der Azure CLI](howto-configure-server-logs-in-cli.md), um zu erfahren, wie Protokolle für langsame Abfragen programmgesteuert heruntergeladen werden können.
- Erfahren Sie mehr über [Protokolle für langsame Abfragen in Azure Database for MySQL](concepts-server-logs.md).
- Weitere Informationen zu den Parameterdefinitionen und der MySQL-Protokollierung finden Sie in der MySQL-Dokumentation unter [Protokolle](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).