---
title: Verwenden eines kostenlosen Azure-Kontos, um Azure Database for MySQL – Flexible Server kostenlos zu testen
description: Anleitung für die kostenlose Bereitstellung von Azure Database for MySQL – Flexible Server unter Verwendung eines kostenlosen Azure-Kontos.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.topic: how-to
ms.date: 08/16/2021
ms.custom: template-how-to
ms.openlocfilehash: 619c65cb2f86c946d0e85573a967a23d718b38c9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501436"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-mysql---flexible-server-for-free"></a>Verwenden eines kostenlosen Azure-Kontos, um Azure Database for MySQL – Flexible Server kostenlos zu testen

Die Azure Datenbank für MySQL-flexible Server ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können.  Mit einem kostenlosen Azure-Konto können Sie Flexible Server **12 Monate kostenlos** mit folgenden **monatlichen Grenzwerten** verwenden:
- **750 Stunden** **burstfähige B1MS-Instanz** – genügend Stunden, um eine Datenbankinstanz jeden Monat kontinuierlich auszuführen.
- **32 GB Speicher und 32 GB Sicherungsspeicher.** 

In diesem Artikel erfahren Sie, wie Sie mit einem [kostenlosen Azure-Konto](https://azure.microsoft.com/free/) einen flexiblen Server kostenlos erstellen und verwenden. 


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- ein kostenloses Azure-Konto. Wenn Sie keines haben, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen. 


## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Erstellen einer Instanz von Azure Database for MySQL – Flexible Server

In diesem Artikel verwenden Sie das Azure-Portal, um eine Flexible Server-Instanz mit einer Verbindungsmethode mit öffentlichem Zugriff zu erstellen. Alternativ dazu können Sie die entsprechenden Schnellstarts zum Erstellen einer Flexible Server-Instanz mit der [Azure CLI](./quickstart-create-server-cli.md) oder einer [ARM-Vorlage](./quickstart-create-arm-template.md) oder [in einem VNET](./quickstart-create-connect-server-vnet.md) verwenden.


1. Melden Sie sich mit Ihrem kostenlosen Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an. 
    
    Die Standardansicht ist Ihr Dienstdashboard. 

1. Um eine MySQL-Datenbank (Flexible Server) zu erstellen, suchen Sie nach **Azure Database for MySQL-Server**, und wählen Sie diesen Eintrag aus:
    
    :::image type="content" source="./media/how-to-deploy-on-azure-free-account/search-select-azure-database-for-mysql.png" alt-text="Screenshot: Suchen und Auswählen von Azure Database for MySQL im Azure-Portal.":::


    Alternativ können Sie nach **Kostenlose Dienste** suchen und zu diesen Diensten wechseln, um dort die Kachel **Azure Database for MySQL** aus der Liste auszuwählen:
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/all-free-services.png" alt-text="Screenshot: Liste aller kostenlosen Dienste im Azure-Portal.":::

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie auf der Seite **Bereitstellungsoption für die Azure Datenbank für MySQL auswählen** die Option **Flexible Server** aus. 

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-deployment-option.png" alt-text="Screenshot: Zu wählende Flexible Server-Bereitstellungsoption.":::

1. Geben Sie die grundlegenden Einstellungen für eine neue **Flexible Server**-Instanz ein.


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-all.PNG" alt-text="Screenshot: grundlegende Einstellungen zum Erstellen einer Flexible Server-Instanz.":::


    |Einstellung    |Vorgeschlagener Wert    |BESCHREIBUNG  |
    |---------|---------|---------|
    |Subscription    |Ihr Abonnement    |Wählen Sie die kostenlose Testversion des Azure-Abonnements aus.|
    |Resource group    |Ihre Ressourcengruppe    |Geben Sie eine neue Ressourcengruppe oder eine bereits vorhandene Gruppe aus Ihrem Abonnement ein.|
    |Servername    |mydemoserver-mysql    |Geben Sie einen eindeutigen Namen für Ihren flexiblen Server an. Der Domänenname mysql.database.azure.com wird dem angegebenen Servernamen angefügt. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen lang sein.|
    |Region    |Die Region, die Ihren Benutzern am nächsten liegt    |Wählen Sie einen Standort aus der Liste aus (vorzugsweise den Standort mit der geringsten Entfernung zu Ihren Benutzern).|
    |Workloadtyp    |Entwicklung    |Wählen Sie für eine kostenlose Testversion die Workload „Entwicklung“ aus. Wählen Sie für Produktionsworkloads je nach [max_connections](concepts-server-parameters.md)-Anforderungen eine kleine/mittlere Größe oder eine große Größe aus.|
    |Verfügbarkeitszone    |Keine Einstellung    |Wenn Ihre Anwendung (auf Azure-VMs, VM-Skalierungsgruppen oder AKS-Instanzen gehostet) in einer bestimmten Verfügbarkeitszone bereitgestellt wurde, erstellen Sie Ihren flexiblen Server in derselben Verfügbarkeitszone. Durch die gemeinsame Platzierung von Anwendung und Datenbank wird die Netzwerklatenz innerhalb der Zonen verringert, sodass sich die Leistung verbessern lässt.|
    |Hochverfügbarkeit    |Standard    |Lassen Sie die Option „Hochverfügbarkeit“ deaktiviert.|
    |MySQL-Version    |Die aktuelle Hauptversion    |Verwenden Sie die aktuelle Hauptversion. Weitere Informationen finden Sie unter [Unterstützte Azure-Datenbank für MySQL-Serverversionen](../concepts-supported-versions.md).|
    |Administratorbenutzername     |myadmin        |Erstellen Sie ein Anmeldekonto zum Herstellen der Verbindung mit dem Server. Der Administratorbenutzername darf nicht „azure_superuser“, „admin“, „administrator“, „root“, „guest“ oder „public“ lauten.         |
    |Kennwort     |Ihr Kennwort         |Geben Sie ein neues Kennwort für das Serveradministratorkonto ein. Das Kennwort muss zwischen acht und 128 Zeichen lang sein. Darüber hinaus muss es Zeichen aus drei der folgenden vier Kategorien enthalten: lateinische Großbuchstaben, lateinische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (!, $, #, % usw.).         |

1. Wählen Sie unter **Compute + Speicher** die Option **Server konfigurieren** aus.
 
    Wählen Sie die burstfähige B1MS-Instanz (1-2 vCores) aus. Geben Sie außerdem an, dass eine Speicherkapazität von maximal 32 GB verwendet werden soll, und übernehmen Sie für die übrigen Optionen die Standardeinstellungen.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage.png" alt-text="Screenshot: Blatt „Compute und Speicher“ nach Auswahl von „Server konfigurieren“ zur Festlegung der B1MS-SKU und einer Speicherkapazität von 32 GB.":::

1. Wählen Sie **Speichern** aus, um mit der Konfiguration fortzufahren.


1. Wählen Sie die Registerkarte **Netzwerk** aus, um die Einstellungen für den Zugriff auf Ihren Server festzulegen.

     Azure Database for MySQL – Flexible Server bietet zwei Optionen zum Herstellen einer Verbindung:
     
    - Öffentlicher Zugriff (zugelassene IP-Adressen)
    - Privater Zugriff (VNET-Integration)
     
    Bei öffentlichem Zugriff ist der Zugriff auf Ihren Server auf zugelassene IP-Adressen beschränkt, die in einer Firewallregel aufgenommen werden. Diese Methode verhindert, dass externe Anwendungen und Tools eine Verbindung mit der Serverinstanz oder mit Datenbanken auf dem Server herstellen – es sei denn, Sie erstellen eine Regel, um die Firewall für bestimmte IP-Adressen oder einen bestimmten IP-Adressbereich zu öffnen. 

    Bei privatem Zugriff (VNet-Integration) ist der Zugriff auf Ihren Server auf Ihr virtuelles Netzwerk beschränkt. Weitere Informationen zu Konnektivitätsmethoden finden Sie unter [Konnektivitäts- und Netzwerkkonzepte für Azure Database for MySQL – Flexible Server (Vorschau)](./concepts-networking.md).
     
    Aktivieren Sie für dieses Tutorial den öffentlichen Zugriff, um eine Verbindung mit dem Server herzustellen. 
    
1. Wählen Sie auf der Registerkarte **Netzwerk** als **Konnektivitätsmethode** die Option **Öffentlicher Zugriff** aus. 
1. Wählen Sie zum Konfigurieren von **Firewallregeln** die Option **Aktuelle Client-IP-Adresse hinzufügen** aus.
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking.png" alt-text="Screenshot: Auswahl der Netzwerkoptionen mit hervorgehobener Schaltfläche zum Hinzufügen der aktuellen Client-IP-Adresse.":::

    > [!NOTE]
    > Nach der Erstellung des Servers kann die Verbindungsmethode nicht mehr geändert werden. Wenn Sie während der Erstellung des Servers beispielsweise **Öffentlicher Zugriff (zugelassene IP-Adressen)** ausgewählt haben, können Sie nach der Erstellung nicht zu **Privater Zugriff (VNET-Integration)** wechseln. Es wird dringend empfohlen, den Server mit privatem Zugriff zu erstellen, um den Zugriff auf Ihren Server über die VNET-Integration zu schützen. Weitere Informationen über privaten Zugriff finden Sie unter [Konnektivitäts- und Netzwerkkonzepte für Azure Database for MySQL – Flexible Server (Vorschau)](./concepts-networking.md).

1. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-and-create.png" alt-text="Screenshot des Blatts „Überprüfen und erstellen“.":::

    >[!IMPORTANT]
    >Wenn Sie die flexible Serverinstanz über Ihr kostenloses Azure-Konto erstellen, sehen Sie weiterhin die **geschätzten Kosten pro Monat** auf dem Blatt **Compute und Speicher: Kostenübersicht** und der Registerkarte **Überprüfen und erstellen**. Solange Sie jedoch Ihr kostenloses Azure-Konto verwenden und die Nutzung des kostenlosen Dienstes innerhalb der monatlichen Grenzen liegt (Informationen zur Nutzung finden Sie im Abschnitt [**Überwachen und Verfolgen der Nutzung kostenloser Dienste**](#monitor-and-track-free-services-usage) weiter unten), werden Ihnen keine Kosten für den Dienst berechnet. Wir arbeiten aktuell an einer Verbesserung der **Kostenzusammenfassung** für kostenlose Dienste. 
    
1. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. 
 
    Die Bereitstellung kann einige Minuten dauern.

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** (Glockensymbol) aus, um den Bereitstellungsprozess zu überwachen. 
    
    Nach Abschluss der Bereitstellung können Sie **An Dashboard anheften** auswählen, um auf dem Dashboard im Azure-Portal eine Kachel für die Flexible Server-Instanz zu erstellen. Über diese Kachel gelangen Sie direkt zur Seite **Übersicht** des Servers. Wenn Sie **Zu Ressource wechseln** auswählen, wird die **Übersicht** des Servers geöffnet.

    Ihr Server enthielt standardmäßig folgende Datenbanken: „information_schema“, „mysql“, „performance_schema“ und „sys“.


## <a name="connect-and-query"></a>Herstellen einer Verbindung und Ausführen von Abfragen

Nachdem Sie nun eine Instanz von Azure Database for MySQL – Flexible Server in einer Ressourcengruppe erstellt haben, können Sie mit den folgenden Schnellstartanleitungen für Verbindungsherstellung und Abfragen eine Verbindung mit dem Server herstellen und Datenbanken abfragen:

- [mysql.exe](quickstart-create-server-portal.md)
- [MySQL Workbench](connect-workbench.md)
- [Azure-Befehlszeilenschnittstelle](connect-azure-cli.md)
- [PHP](connect-php.md) 
- [Java](connect-java.md)
- [Python](connect-python.md)
- [.NET](connect-csharp.md)


## <a name="monitor-and-track-free-services-usage"></a>Überwachen und Nachverfolgen der Nutzung kostenloser Dienste

Für Dienste von Azure Database for MySQL – Flexible Server, die im Rahmen Ihres kostenlosen Azure-Kontos kostenlos bereitgestellt werden, fallen keine Gebühren an (sofern Sie die Grenzwerte des kostenlosen Diensts nicht überschreiten). Sie können die Nutzung Ihrer kostenlosen Dienste im Azure-Portal nachverfolgen und überwachen, um sicherzustellen, dass Sie die Grenzwerte nicht überschreiten.

1. Suchen Sie im Azure-Portal nach **Abonnements**, und wählen Sie für das kostenlose Azure-Konto die **kostenlose Testversion** des Abonnements aus.

1. Scrollen Sie auf der Seite **Übersicht** nach unten zur Kachel **Kostenlose Top-Dienste nach Nutzung**, und wählen Sie dann **Alle kostenlosen Dienste anzeigen** aus.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="Screenshot: Übersicht für kostenlose Testversionsabonnements mit hervorgehobener Option „Alle kostenlosen Dienste anzeigen“.":::

1. Sie können die Nutzung mithilfe der Verbrauchseinheiten für **Azure Database for MySQL – Flexible Server** nachverfolgen.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="Screenshot: Blatt zur Anzeige und Nachverfolgung von Nutzungsinformationen im Azure-Portal für alle kostenlosen Dienste.":::

    |Zähler    |BESCHREIBUNG    |Monatslimit  |
    |---------|---------|---------|
    |Azure Database for MySQL, Flexible Server, burstfähige Computeleistung BS-Serie, B1MS | Verfolgt die Computenutzung als Anzahl von Ausführungsstunden nach | 750 Stunden pro Monat – burstfähige B1MS-Computeebene|
    |Azure Database for MySQL, Flexible Server, Speicher, gespeicherte Daten | Verfolgt den bereitgestellten Datenspeicher in verbrauchten GiB pro Monat nach | 32 GB pro Monat |

    
    - Verbrauchseinheit: Gibt die Maßeinheit für den Dienst an, für den der Verbrauch gemessen wird.
    - Nutzung/Limit: Nutzung und Grenzwert für die Verbrauchseinheit für den aktuellen Monat.
    - Status: Verwendungsstatus des Diensts. Basierend auf Ihrer Nutzung kann einer der folgenden Status gelten:
    - Nicht in Verwendung: Sie haben diese Verbrauchseinheit nicht verwendet, oder die Verwendung der Verbrauchseinheit hat das Abrechnungssystem nicht erreicht.
    - Überschritten am \<Date\>: Sie haben den Grenzwert für die Verbrauchseinheit am \<Date\> überschritten.
    - Überschreitung unwahrscheinlich: Es ist unwahrscheinlich, dass Sie den Grenzwert für diese Verbrauchseinheit überschreiten.
    - Überschreitung am \<Date\>: Sie werden den Grenzwert für die Verbrauchseinheit wahrscheinlich am \<Date\> überschreiten.
    

    >[!IMPORTANT]
    >Bei einem kostenlosen Azure-Konto erhalten Sie zudem ein Guthaben in Höhe von 200 US-Dollar, das Sie innerhalb von 30 Tagen nutzen können. Während dieser Zeit wird jegliche Nutzung, die über die kostenlosen monatlichen Dienste hinausgeht, von diesem Guthaben abgezogen.
    >Am Ende Ihrer ersten 30 Tage, oder nachdem Sie Ihr Guthaben in Höhe von 200 US-Dollar aufgebraucht haben (was zuerst eintritt), zahlen Sie nur für das, was Sie über die kostenlosen monatlichen Dienste hinaus nutzen. Wenn Sie nach 30 Tagen weiterhin kostenlose Dienste nutzen möchten, wechseln Sie zu einem Preismodell mit nutzungsbasierter Bezahlung. Wenn Sie nicht zur nutzungsbasierten Bezahlung wechseln, können Sie keine weiteren Azure-Dienste erwerben, nachdem Sie Ihr Guthaben in Höhe von 200 US-Dollar aufgebraucht haben. Ihr Konto und die Dienste werden letztendlich deaktiviert.
    >Weitere Informationen finden Sie unter [**FAQ zum kostenlosen Azure-Konto**](https://azure.microsoft.com/free/free-account-faq/).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den flexiblen Server für Entwicklungs-, Test- oder vorhersagbare, zeitgebundene Produktionsworkloads verwenden, optimieren Sie die Nutzung, indem Sie den Server nach Bedarf starten und beenden. Nachdem Sie den Server beenden, verbleibt er sieben Tage in diesem Zustand (sofern er nicht früher neu gestartet wird). Weitere Informationen finden Sie unter [Serverkonzepte](concept-servers.md).
Bei einer beendeten Flexible Server-Instanz wird keine Computenutzung erfasst, die Speichernutzung wird jedoch berücksichtigt. 

Alternativ können Sie diese Ressourcen löschen, wenn Sie sie in Zukunft nicht mehr benötigen. Löschen Sie dazu die Ressourcengruppe oder einfach den MySQL-Server. 

- Führen Sie die folgenden Schritte aus, um die Ressourcengruppe zu löschen:

    1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
    1. Wählen Sie den Namen Ihrer Ressourcengruppe in der Liste der Ressourcengruppen aus.
    1. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
    1. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

- Zum Löschen des MySQL-Servers wählen Sie auf der Seite **Übersicht** für den Server die Option **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)

