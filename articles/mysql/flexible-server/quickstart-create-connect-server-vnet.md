---
title: Verbinden von Azure Database for MySQL – Flexibler Server mit privatem Zugriff im Azure-Portal
description: In diesem Artikel wird erläutert, wie Sie über das Azure-Portal eine Instanz von Azure Database for MySQL – Flexibler Server erstellen und über privaten Zugriff eine Verbindung damit herstellen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/18/2021
ms.openlocfilehash: f8d32c97014e9e38f2ad5a3c6d6a1b96d4e190f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425468"
---
# <a name="connect-azure-database-for-mysql-flexible-server-with-private-access-connectivity-method"></a>Verbinden von Azure Database for MySQL – Flexibler Server mit der Verbindungsmethode für privaten Zugriff

Azure Database for MySQL Flexible Server ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Serverinstanzen in der Cloud ausführen, verwalten und skalieren können. In diesem Schnellstart erfahren Sie, wie Sie über das Azure-Portal eine Flexibler Server-Instanz in einem VNet erstellen.



[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]


## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.
Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Erstellen einer Azure Database for MySQL Flexible Server-Instanz

Eine Flexible Server-Instanz wird mit einer definierten Gruppe von [Compute- und Speicherressourcen](./concepts-compute-storage.md) erstellt. Der Server wird in einer [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) erstellt.

Führen Sie die folgenden Schritte aus, um eine Flexible Server-Instanz zu erstellen:

1. Suchen Sie im Portal nach **Azure Database for MySQL-Server**, und wählen Sie die Option aus:

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Screenshot: Suchen nach „Azure Database for MySQL-Server“" lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. Wählen Sie **Hinzufügen**.

3. Wählen Sie auf der Seite **Option „Azure Database for MySQL-Bereitstellung“ auswählen** die Bereitstellungsoption **Flexibler Server** aus.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="Screenshot: Option „Flexibler Server“" lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. Geben Sie auf der Registerkarte **Grundlagen** das **Abonnement**, die **Ressourcengruppe**, die **Region**, den **Benutzernamen des Administrators** und das **Administratorkennwort** ein.  Mit den Standardwerten wird ein MySQL-Server der Version 5.7 mit burstfähiger SKU, einem virtuellen Kern, 2 GiB Arbeitsspeicher und 32 GiB Speicher bereitgestellt. Die Sicherungsaufbewahrung beträgt 7 Tage. Sie können die Konfiguration ändern.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png" alt-text="Screenshot: Registerkarte „Grundeinstellungen“ auf der Seite „Flexibler Server“" lightbox="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png":::

   > [!TIP]
   > Zum schnelleren Laden von Daten während der Migration wird empfohlen, den Wert für IOPS auf die maximale Größe zu erhöhen, die von der Computegröße unterstützt wird, und Sie später wieder herunterzuskalieren, um Kosten zu sparen.

5.  Navigieren Sie zur Registerkarte **Netzwerkbetrieb**, und wählen Sie **Privater Zugriff** aus. Nach der Erstellung des Servers kann die Verbindungsmethode nicht mehr geändert werden. Wählen Sie **Neues virtuelles Netzwerk erstellen** aus, um ein neues VNet namens **vnetenvironment1** zu erstellen.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png" alt-text="Screenshot der Registerkarte „Netzwerkbetrieb“ mit dem neuen VNet" lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png":::

6. Wählen Sie **OK** aus, nachdem Sie den Namen des virtuellen Netzwerks und die Subnetzinformationen angegeben haben.
    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/show-server-vnet-information.png" alt-text="Überprüfen der VNet-Informationen":::

7. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.

8. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

9. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen wurde.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="Screenshot der Netzwerkeinstellungen mit dem neuen VNet" lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  Wählen Sie **Zu Ressource wechseln** aus, um die Seite **Übersicht** des Servers zu öffnen.

## <a name="create-azure-linux-virtual-machine"></a>Erstellen einer Azure-Linux-VM

Da sich der Server im virtuellen Netzwerk befindet, können Sie nur von anderen Azure-Diensten im gleichen virtuellen Netzwerk wie der Server eine Verbindung mit diesem herstellen. Erstellen Sie eine Linux-VM, um eine Verbindung mit dem Server herzustellen und ihn zu verwalten. Die VM muss in **derselben Region** und **demselben Abonnement** erstellt werden. Die Linux-VM kann als SSH-Tunnel zum Verwalten Ihres Datenbankservers verwendet werden.

1. Wechseln Sie zu Ihrer Ressourcengruppe, in der der Server erstellt wurde. Wählen Sie **Hinzufügen**.
2. Klicken Sie auf **Ubuntu Server 18.04 LTS**.
3. Stellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** sicher, dass das richtige Abonnement ausgewählt ist, und wählen Sie dann **Neu erstellen** für „Ressourcengruppe“ aus. Geben Sie als Namen *myResourceGroup* ein.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="Screenshot: Abschnitt „Projektdetails“, der zeigt, wo Sie das Azure-Abonnement und die Ressourcengruppe für den virtuellen Computer auswählen" lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png":::

2. Geben Sie unter **Instanzdetails** die Zeichenfolge *myVM* als **Name der VM** ein, und wählen Sie die **Region** mit Ihrem Datenbankserver aus.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="Screenshot des Abschnitts „Instanzdetails“, in dem Sie einen Namen für die VM angeben und Region, Image und Größe für ihn auswählen" lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. Wählen Sie unter **Administratorkonto** die Option **Öffentlicher SSH-Schlüssel** aus.

4. Geben Sie in **Benutzername** die Zeichenfolge *azureuser* ein.

5. Für **Öffentliche SSH-Schlüsselquelle** belassen Sie die Voreinstellung **Neues Schlüsselpaar generieren**, und geben Sie dann *myKey* für den **Schlüsselpaarnamen** ein.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="Screenshot: Abschnitt „Administratorkonto“, in dem Sie einen Authentifizierungstyp auswählen und die Administratoranmeldeinformationen angeben" lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. Wählen Sie unter **Regeln für eingehende Ports** > **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann **SSH (22)** und **HTTP-(80)** aus der Dropdownliste aus.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="Screenshot: Abschnitt „Regeln für eingehende Ports“, in dem Sie festlegen, an welchen Ports eingehende Verbindungen zulässig sind" lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. Wählen Sie die Seite **Netzwerkbetrieb** aus, um das VNet zu konfigurieren. Wählen Sie als virtuelles Netzwerk **vnetenvironment1** aus, das Sie für den Datenbankserver erstellt haben.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="Screenshot der Auswahl des vorhandenen VNet mit dem Datenbankserver" lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. Um ein Subnetz für den Server zu erstellen, wählen Sie **Subnetzkonfiguration verwalten** aus.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="Screenshot der Subnetzverwaltung" lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. Fügen Sie ein neues Subnetz für die VM hinzu.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="Screenshot des Hinzufügens eines neuen Subnetzes für die VM" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png":::

10. Schließen Sie die Seite, nachdem das Subnetz erfolgreich erstellt wurde.
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png" alt-text="Screenshot des erfolgreichen Hinzufügens eines neuen Subnetzes für die VM" lightbox="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png":::

11. Klicken Sie auf **Überprüfen + erstellen**.
12. Klicken Sie auf **Erstellen**. Wenn das Fenster **Neues Schlüsselpaar generieren** geöffnet wird, wählen Sie **Privaten Schlüssel herunterladen und Ressource erstellen** aus. Ihre Schlüsseldatei wird als **myKey.pem** heruntergeladen.

   >[!IMPORTANT]
   > Vergewissern Sie sich, dass Sie wissen, wo die `.pem`-Datei heruntergeladen wurde. Sie benötigen im nächsten Schritt den Pfad zu dieser Datei.

13. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-create-success.png" alt-text="Screenshot der erfolgreichen Bereitstellung" lightbox="./media/quickstart-create-connect-server-vnet/vm-create-success.png":::

11. Wählen Sie auf der Seite für Ihren neuen virtuellen Computer (VM) die öffentliche IP-Adresse aus, und kopieren Sie sie in die Zwischenablage.
   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="Screenshot des Kopierens der IP-Adresse für die VM" lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-mysql-client-tools"></a>Installieren von MySQL-Clienttools

Erstellen Sie mithilfe von Bash oder PowerShell eine SSH-Verbindung mit der VM. Öffnen Sie an Ihrer Eingabeaufforderung eine SSH-Verbindung mit Ihrem virtuellen Computer. Ersetzen Sie die IP-Adresse durch die Ihres virtuellen Computers, und ersetzen Sie den Pfad zur `.pem` durch den Pfad, in den die Schlüsseldatei heruntergeladen wurde.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Der von Ihnen erstellte SSH-Schlüssel kann bei der nächsten Erstellung einer VM in Azure verwendet werden. Wählen Sie einfach die Option **In Azure gespeicherten Schlüssel verwenden** für **Öffentliche SSH-Schlüsselquelle** aus, wenn Sie das nächste Mal einen virtuellen Computer erstellen. Der private Schlüssel befindet sich bereits auf Ihrem Computer, sodass Sie nichts herunterladen müssen.

Sie müssen das Tool mysql-client installieren, um eine Verbindung mit dem Server herstellen zu können.

```bash
sudo apt-getupdate
sudo apt-get install mysql-client
```

Für Verbindungen mit der Datenbank wird SSL erzwungen. Daher müssen Sie das öffentliche SSL-Zertifikat herunterladen.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Herstellen einer Verbindung von der Azure-Linux-VM mit dem Server
Nachdem Sie das Clienttool [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) installiert haben, können Sie jetzt aus Ihrer lokalen Umgebung eine Verbindung mit dem Server herstellen.

```bash
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie haben jetzt eine Azure Database for MySQL Flexible Server-Instanz in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, können Sie sie löschen, indem Sie die Ressourcengruppe oder einfach den MySQL-Server löschen. Um die Ressourcengruppe zu löschen, führen Sie die folgenden Schritte aus:

1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
1. Wählen Sie den Namen Ihrer Ressourcengruppe in der Liste der Ressourcengruppen aus.
1. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)
