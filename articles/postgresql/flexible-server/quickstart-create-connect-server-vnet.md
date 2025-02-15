---
title: Herstellen einer Verbindung mit einem flexiblen Azure Database for PostgreSQL-Server mit privatem Zugriff über das Azure-Portal
description: In diesem Artikel wird erläutert, wie Sie im Azure-Portal einen flexiblen Azure Database for PostgreSQL-Server mit privatem Zugriff oder VNet-Zugriff erstellen und eine Verbindung mit diesem herstellen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 07/29/2021
ms.openlocfilehash: 772cd5c647c3e03773bd6c3e7f57616093b4c7ce
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694145"
---
# <a name="connect-azure-database-for-postgresql-flexible-server-with-the-private-access-connectivity-method"></a>Herstellen einer Verbindung mit einem flexiblen Azure Database for PostgreSQL-Server mithilfe der Konnektivitätsmethode des privaten Zugriffs

Ein flexibler Azure Database for PostgreSQL-Server ist ein verwalteter Dienst, mit dem Sie hochverfügbare PostgreSQL-Server in der Cloud ausführen, verwalten und skalieren können. In diesem Schnellstart erfahren Sie, wie Sie über das Azure-Portal eine Flexibler Server-Instanz in einem VNet erstellen.

> [!IMPORTANT]
> Der flexible Azure Database for PostgreSQL-Server befindet sich derzeit in der öffentlichen Vorschau.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

## <a name="create-an-azure-database-for-postgresql-flexible-server"></a>Erstellen eines flexiblen Azure Database for PostgreSQL-Servers

Eine Flexible Server-Instanz wird mit einer definierten Gruppe von [Compute- und Speicherressourcen](./concepts-compute-storage.md) erstellt. Der Server wird in einer [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) erstellt.

Führen Sie die folgenden Schritte aus, um eine Flexible Server-Instanz zu erstellen:

1. Suchen Sie im Portal nach **Azure Database for PostgreSQL-Server**, und wählen Sie die Option aus:

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Screenshot: Suchen nach „Azure Database for PostgreSQL-Server“" lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. Wählen Sie **Hinzufügen**.

3. Wählen Sie auf der Seite **Option „Azure Database for PostgreSQL-Bereitstellung“ auswählen** die Bereitstellungsoption **Flexibler Server** aus.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="Screenshot: Option „Flexibler Server“" lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. Geben Sie auf der Registerkarte **Grundlagen** das **Abonnement**, die **Ressourcengruppe**, die **Region** und den **Servernamen** ein.  Mit den Standardwerten wird damit ein PostgreSQL-Server in Version 12 mit dem Tarif „Universell“ bereitgestellt, der 2 virtuelle Kerne, 8 GiB RAM und 28 GiB Speicher verwendet. Sicherungen werden **sieben** Tage lang aufbewahrt. Sie können die Workload **Entwicklung** verwenden, um standardmäßig einen günstigeren Tarif festzulegen.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png" alt-text="Screenshot: Registerkarte „Grundeinstellungen“ auf der Seite „Flexibler PostgreSQL-Server“" lightbox="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png":::

5. Geben Sie auf der Registerkarte **Grundlagen** einen eindeutigen **Administratorbenutzernamen** und ein **Administratorbenutzerkennwort** ein.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/db-administrator-account.png" alt-text="Screenshot: Informationsseite für den Administratorbenutzer" lightbox="./media/quickstart-create-connect-server-vnet/db-administrator-account.png":::

6.  Wechseln Sie zur Registerkarte **Netzwerk**, und wählen Sie **Privater Zugriff** aus. Nach der Erstellung des Servers kann die Verbindungsmethode nicht mehr geändert werden. Wählen Sie **Neues virtuelles Netzwerk erstellen** aus, um ein neues VNet namens **vnetenvironment1** zu erstellen. Wählen Sie **OK** aus, nachdem Sie den Namen des virtuellen Netzwerks und die Subnetzinformationen angegeben haben.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png" alt-text="Screenshot der Registerkarte „Netzwerkbetrieb“ mit dem neuen VNet" lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png":::

7. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.

8. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

9. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen wurde.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="Screenshot der Netzwerkeinstellungen mit dem neuen VNet" lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  Wählen Sie **Zu Ressource wechseln** aus, um die Seite **Übersicht** des Servers zu öffnen.

## <a name="create-an-azure-linux-virtual-machine"></a>Erstellen eines virtuellen Azure Linux-Computers

Da sich der Server in einem virtuellen Netzwerk befindet, können Sie nur von anderen Azure-Diensten eine Verbindung mit dem Server herstellen, die sich im selben virtuellen Netzwerk befinden. Erstellen Sie eine Linux-VM, um eine Verbindung mit dem Server herzustellen und ihn zu verwalten. Die VM muss in **derselben Region** und **demselben Abonnement** erstellt werden. Die Linux-VM kann als SSH-Tunnel zum Verwalten Ihres Datenbankservers verwendet werden. 

1. Wechseln Sie zu Ihrer Ressourcengruppe, in der der Server erstellt wurde. Wählen Sie **Hinzufügen**.
2. Wählen Sie **Ubuntu Server 18.04 LTS** aus.
3. Stellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** sicher, dass das richtige Abonnement ausgewählt ist, und wählen Sie dann **Neu erstellen** für „Ressourcengruppe“ aus. Geben Sie als Namen *myResourceGroup* ein.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="Screenshot: Abschnitt „Projektdetails“, der zeigt, wo Sie das Azure-Abonnement und die Ressourcengruppe für den virtuellen Computer auswählen." lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. Geben Sie unter **Instanzdetails** die Zeichenfolge *myVM* als **Name der VM** ein, und wählen Sie die **Region** mit Ihrem Datenbankserver aus.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="Screenshot: Abschnitt „Instanzdetails“, in dem Sie einen Namen für den virtuellen Computer angeben und Region, Image und Größe für ihn auswählen." lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. Wählen Sie unter **Administratorkonto** die Option **Öffentlicher SSH-Schlüssel** aus.

4. Geben Sie in **Benutzername** die Zeichenfolge *azureuser* ein.

5. Für **Öffentliche SSH-Schlüsselquelle** belassen Sie die Voreinstellung **Neues Schlüsselpaar generieren**, und geben Sie dann *myKey* für den **Schlüsselpaarnamen** ein.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="Screenshot: Abschnitt „Administratorkonto“, in dem Sie einen Authentifizierungstyp auswählen und die Administratoranmeldeinformationen angeben." lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. Wählen Sie unter **Regeln für eingehende Ports** > **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann **SSH (22)** und **HTTP-(80)** aus der Dropdownliste aus.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="Screenshot: Abschnitt „Regeln für eingehende Ports“, in dem Sie festlegen, an welchen Ports eingehende Verbindungen zulässig sind." lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. Wählen Sie die Seite **Netzwerk** aus, um das virtuelle Netzwerk zu konfigurieren. Wählen Sie als virtuelles Netzwerk das Netzwerk **vnetenvironment1** aus, das Sie für den Datenbankserver erstellt haben.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="Screenshot: Auswahl des vorhandenen virtuellen Netzwerks des Datenbankservers" lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. Um ein Subnetz für den Server zu erstellen, wählen Sie **Subnetzkonfiguration verwalten** aus.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="Screenshot: Subnetzverwaltung" lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. Fügen Sie das neue Subnetz für die VM hinzu.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="Screenshot: Hinzufügen eines neuen Subnetzes für die VM" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. Schließen Sie die Seite, nachdem das Subnetz erfolgreich erstellt wurde.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnet-create-success.png" alt-text="Screenshot: erfolgreiches Hinzufügen eines neuen Subnetzes für die VM" lightbox="./media/quickstart-create-connect-server-vnet/subnet-create-success.png":::

11. Klicken Sie auf **Überprüfen + erstellen**.
12. Klicken Sie auf **Erstellen**. Wenn das Fenster **Neues Schlüsselpaar generieren** geöffnet wird, wählen Sie **Privaten Schlüssel herunterladen und Ressource erstellen** aus. Ihre Schlüsseldatei wird als **myKey.pem** heruntergeladen.

    >[!IMPORTANT]
    > Notieren Sie sich den Speicherort, in den die `.pem`-Datei heruntergeladen wird. Sie benötigen den Pfad zu diesem Speicherort im nächsten Schritt.

13. Wenn die Bereitstellung abgeschlossen ist, wählen Sie **Zu Ressource wechseln** aus, um die Seite **Übersicht** für den virtuellen Computer anzuzeigen.

14. Wählen Sie die öffentliche IP-Adresse aus, und kopieren Sie sie in die Zwischenablage.

    :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="Screenshot, der zeigt, wie die IP-Adresse für den virtuellen Computer kopiert wird." lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-postgresql-client-tools"></a>Installieren von PostgreSQL-Clienttools

Erstellen Sie mithilfe von Bash oder PowerShell eine SSH-Verbindung mit der VM. Öffnen Sie an Ihrer Eingabeaufforderung eine SSH-Verbindung mit Ihrem virtuellen Computer. Ersetzen Sie die IP-Adresse durch die Ihres virtuellen Computers, und ersetzen Sie den Pfad zur `.pem` durch den Pfad, in den die Schlüsseldatei heruntergeladen wurde.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Der von Ihnen erstellte SSH-Schlüssel kann bei der nächsten Erstellung einer VM in Azure verwendet werden. Wählen Sie einfach die Option **In Azure gespeicherten Schlüssel verwenden** für **Öffentliche SSH-Schlüsselquelle** aus, wenn Sie das nächste Mal einen virtuellen Computer erstellen. Der private Schlüssel befindet sich bereits auf Ihrem Computer, sodass Sie nichts herunterladen müssen.

Sie müssen das Tool „postgresql-client“ installieren, um eine Verbindung mit dem Server herstellen zu können.

```bash
sudo apt-getupdate
sudo apt-get install postgresql-client
```

Für Verbindungen mit der Datenbank wird SSL erzwungen. Daher müssen Sie das öffentliche SSL-Zertifikat herunterladen.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Herstellen einer Verbindung von der Azure-Linux-VM mit dem Server
Nachdem Sie das Clienttool **psql** installiert haben, können Sie jetzt aus Ihrer lokalen Umgebung eine Verbindung mit dem Server herstellen.

```bash
psql --host=mydemoserver-pg.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres --set=sslmode=require --set=sslrootcert=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie haben jetzt einen flexiblen Azure Database for PostgreSQL-Server in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, können Sie sie löschen, indem Sie die Ressourcengruppe oder einfach den PostgreSQL-Server löschen. Führen Sie die folgenden Schritte aus, um die Ressourcengruppe zu löschen:

1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
1. Wählen Sie den Namen Ihrer Ressourcengruppe in der Liste der Ressourcengruppen aus.
1. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Python-App](connect-python.md)
