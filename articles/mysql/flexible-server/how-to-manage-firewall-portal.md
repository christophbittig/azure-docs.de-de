---
title: Verwalten von Firewallregeln – Azure-Portal – Azure Database for MySQL – Flexible Server
description: Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL – Flexible Server mit Azure-Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: fa41da095b196f13c423b56a0411e6b67dab24a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442223"
---
# <a name="manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Verwalten von Firewallregeln für Azure Database for MySQL – Flexible Server mit Azure-Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Azure Database for MySQL Flexible Server unterstützt zwei Arten von sich gegenseitig ausschließenden Netzwerkverbindungsmethoden, mit denen eine Verbindung mit Ihrem flexiblen Server hergestellt werden kann. Die zwei Optionen sind:

1. Öffentlicher Zugriff (zugelassene IP-Adressen)
2. Privater Zugriff (VNET-Integration)

In diesem Artikel konzentrieren wir uns auf die Erstellung eines MySQL-Servers mit **öffentlichem Zugriff (zulässige IP-Adressen)** unter Verwendung des Azure-Portals und bieten eine Übersicht über die Verwaltung von Firewallregeln nach der Erstellung eines flexiblen Servers. Mit *Öffentlicher Zugriff (zulässige IP-Adressen)* werden die Verbindungen mit dem MySQL-Server ausschließlich auf zulässige IP-Adressen beschränkt. Die Client-IP-Adressen müssen in Firewallregeln zugelassen werden. Weitere Informationen finden Sie unter [Öffentlicher Zugriff (zugelassene IP-Adressen)](./concepts-networking-public.md#public-access-allowed-ip-addresses). Die Firewallregeln können zum Zeitpunkt der Servererstellung definiert werden (empfohlen), jedoch können sie auch später hinzugefügt werden. In diesem Artikel erhalten Sie eine Übersicht über das Erstellen und Verwalten von Firewallregeln mithilfe von „Öffentlicher Zugriff (zulässige IP-Adressen)“.

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Erstellen einer Firewallregel beim Erstellen eines Servers

1. Wählen Sie links oben im Portal **Ressource erstellen** (+) aus.
2. Wählen Sie **Datenbanken** > **Azure-Datenbank für MySQL** aus. Sie können auch **MySQL** in das Suchfeld eingeben, um nach dem Dienst zu suchen.
3. Wählen Sie **Flexible Server** als Bereitstellungsoption aus.
4. Füllen Sie das **Basisformular** aus.
5. Wechseln Sie zur Registerkarte **Netzwerk**, um zu konfigurieren, wie Sie die Verbindung mit dem Server hergestellt werden soll.
6. Wählen Sie unter **Verbindungsmethode** die Option *Öffentlicher Zugriff (zulässige IP-Adressen)* aus. Um die **Firewallregeln** zu erstellen, geben Sie den Namen der Firewallregel und eine einzelne IP-Adresse oder einen Adressbereich an. Wenn Sie die Regel auf eine einzelne IP-Adresse beschränken möchten, geben Sie dieselbe Adresse in das Feld für IP-Startadresse und IP-Endadresse ein. Durch Öffnen der Firewall wird es Administratoren, Benutzern und Anwendungen ermöglicht, auf dem MySQL-Server auf alle Datenbanken zuzugreifen, für die sie über gültige Anmeldeinformationen verfügen.
   > [!Note]
   > Azure Database for MySQL Flexible Server erstellt eine Firewall auf Serverebene. Sie verhindert, dass externe Anwendungen und Tools eine Verbindung mit dem Server oder Datenbanken auf dem Server herstellen – es sei denn, Sie erstellen eine Regel, um die Firewall für bestimmte IP-Adressen zu öffnen.

7. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.
8. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Erstellen einer Firewallregel nach der Erstellung des Servers

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Azure Database for MySQL Flexible Server-Instanz aus, zu der Sie Firewallregeln hinzufügen möchten.
2. Klicken Sie auf der Flexible Server-Seite unter der Überschrift **Einstellungen** auf **Netzwerk**, um die Seite „Netzwerk“ für die Flexible Server-Instanz zu öffnen.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security":::-->

3. Klicken Sie in den Firewallregeln auf **Aktuelle Client-IP-Adresse hinzufügen**. Dadurch wird automatisch eine Firewallregel mit der öffentlichen IP-Adresse Ihres Computers (gemäß Erkennung durch das Azure-System) erstellt.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Add My IP":::-->

4. Überprüfen Sie Ihre IP-Adresse, bevor Sie die Konfiguration speichern. In einigen Situationen weicht die vom Azure-Portal erkannte IP-Adresse von der IP-Adresse ab, die für den Zugriff auf das Internet und die Azure-Server verwendet wird. Aus diesem Grund müssen Sie die Start-IP-Adresse und die End-IP-Adresse ändern, damit die Regel wie erwartet funktioniert.

   Sie können eine Suchmaschine oder ein anderes Onlinetool verwenden, um Ihre eigene IP-Adresse zu überprüfen. Suchen Sie beispielsweise nach „Wie lautet meine IP?“.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Bing search for What is my IP":::-->

5. Fügen Sie weitere Adressräume hinzu. In den Firewallregeln für Azure Database for MySQL Flexible Server können Sie eine einzelne IP-Adresse oder einen Adressbereich angeben. Wenn Sie die Regel auf eine einzelne IP-Adresse beschränken möchten, geben Sie dieselbe Adresse in das Feld für IP-Startadresse und IP-Endadresse ein. Durch Öffnen der Firewall wird es Administratoren, Benutzern und Anwendungen ermöglicht, auf dem MySQL-Server auf alle Datenbanken zuzugreifen, für die sie über gültige Anmeldeinformationen verfügen.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - firewall rules":::-->

6. Klicken Sie auf der Symbolleiste auf **Speichern**, um diese Firewallregel zu speichern. Warten Sie auf die Bestätigung, dass das Update für die Firewallregeln erfolgreich war.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Save":::-->

## <a name="connect-from-azure"></a>Herstellen einer Verbindung von Azure

Vielleicht möchten Sie Ressourcen oder Anwendungen, die in Azure bereitgestellt werden, das Verbinden mit Ihrer Flexible Server-Instanz ermöglichen. Dazu gehören in Azure App Service gehostete Webanwendungen, die auf einer Azure-VM, einem Datenmanagementgateway von Azure Data Factory und vielen anderen Komponenten ausgeführt werden.

Wenn eine Anwendung innerhalb von Azure versucht, eine Verbindung mit Ihrem Server herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Sie können diese Einstellung aktivieren, indem Sie im Portal auf der Registerkarte **Netzwerk** die Option **Von Azure-Diensten und -Ressourcen in Azure aus öffentlichen Zugriff auf diesen Server gestatten** auswählen und auf **Speichern** klicken.

Die Ressourcen müssen sich nicht im gleichen virtuellen Netzwerk (VNET) oder in der gleichen Ressourcengruppe für die Firewallregel befinden, um diese Verbindungen zu ermöglichen. Ist der Verbindungsversuch nicht zulässig, erreicht die Anforderung die Azure Database for MySQL Flexible Server-Instanz nicht.

> [!IMPORTANT]
>Diese Option konfiguriert die Firewall so, dass sie alle von Azure ausgehenden Verbindungen zulässt, auch Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
>
> Für den sicheren Zugriff auf die Flexible Server-Instanz sollten Sie **Privater Zugriff (VNET-Integration)** auswählen.
>

## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Verwalten vorhandener Firewallregeln im Azure-Portal

Wiederholen Sie die folgenden Schritte zum Verwalten der Firewallregeln.

- Klicken Sie zum Hinzufügen des aktuellen Computers auf **+ Aktuelle Client-IP-Adresse hinzufügen** in den Firewallregeln. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
- Um zusätzliche IP-Adressen hinzuzufügen, geben Sie den Regelnamen, die Start-IP-Adresse und die End-IP-Adresse ein. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
- Um eine vorhandene Regel zu ändern, klicken Sie auf eines der Felder in der Regel, und ändern Sie den betreffenden Wert. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
- Um eine vorhandene Regel zu löschen, klicken Sie auf die Auslassungspunkte [...] und dann auf **Löschen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- Erfahren Sie mehr über [Firewallregeln für Azure Database for MySQL Flexible Server](./concepts-networking-public.md#public-access-allowed-ip-addresses).
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL mithilfe der Azure CLI](./how-to-manage-firewall-cli.md)
