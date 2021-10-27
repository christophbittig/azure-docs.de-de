---
title: Verwalten von Firewallregeln – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL – Hyperscale (Citus) im Azure-Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: 23874f83e55fb8ff1ea3f60872cdfc10a64dfc80
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071573"
---
# <a name="manage-public-access-for-azure-database-for-postgresql---hyperscale-citus"></a>Verwalten des öffentlichen Zugriffs für Azure Database for PostgreSQL – Hyperscale (Citus)

Mithilfe von Firewallregeln auf Serverebene kann der [öffentliche Zugriff](concepts-hyperscale-firewall-rules.md) auf einen Hyperscale (Citus)-Koordinatorknoten von einer einzelnen IP-Adresse oder einem Bereich von IP-Adressen im öffentlichen Internet aus verwaltet werden.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Eine Servergruppe – [Erstellen einer Azure Database for PostgreSQL – Hyperscale (Citus)-Servergruppe](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Erstellen einer Firewallregel auf Serverebene im Azure-Portal

> [!NOTE]
> Auf diese Einstellungen kann auch während der Erstellung einer Azure Database for PostgreSQL – Hyperscale (Citus)-Servergruppe zugegriffen werden. Klicken Sie auf der Registerkarte **Netzwerk** auf **Öffentlicher Endpunkt**.

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Azure-Portal – Registerkarte „Netzwerk“":::

1. Klicken Sie auf der Seite der PostgreSQL-Servergruppe unter der Überschrift „Sicherheit“ auf **Netzwerk**, um die Firewallregeln zu öffnen.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure-Portal – Klicken auf „Netzwerk“":::

2. Wählen Sie bei Bedarf die Option **Zugriff auf die Workerknoten aktivieren** aus. Mit dieser Option ermöglichen die Firewallregeln Zugriff auf alle Workerknoten sowie auf den Koordinatorknoten.

3. Klicken Sie auf **Aktuelle Client-IP-Adresse hinzufügen**, um eine Firewallregel mit der öffentlichen IP-Adresse Ihres Computers zu erstellen, die vom Azure-System erkannt wird.

Wenn Sie alternativ auf **+Hinzufügen 0.0.0.0 – 255.255.255.255** (rechts neben Option B) klicken, kann nicht nur über die IP-Adresse, sondern über das gesamte Internet auf Port 5432 des Koordinatorknotens zugegriffen werden. In dieser Situation müssen sich Clients weiterhin mit dem richtigen Benutzernamen und Kennwort anmelden, um den Cluster zu verwenden. Trotzdem empfiehlt es sich, den weltweiten Zugriff nur für kurze Zeit und nur für Datenbanken zuzulassen, die nicht in der Produktion eingesetzt werden.

4. Überprüfen Sie Ihre IP-Adresse, bevor Sie die Konfiguration speichern. In einigen Situationen weicht die vom Azure-Portal erkannte IP-Adresse von der IP-Adresse ab, die für den Zugriff auf das Internet und die Azure-Server verwendet wird. Aus diesem Grund müssen Sie möglicherweise die Start-IP-Adresse und die End-IP-Adresse ändern, damit die Regel wie erwartet funktioniert.
   Verwenden Sie eine Suchmaschine oder ein anderes Onlinetool, um Ihre eigene IP-Adresse zu überprüfen. Suchen Sie beispielsweise nach „Wie lautet meine IP?“.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Bing-Suche für „Wie lautet meine IP?“":::

5. Fügen Sie weitere Adressbereiche hinzu. In den Firewallregeln können Sie eine einzelne IP-Adresse oder einen Adressbereich angeben. Wenn Sie die Regel auf eine einzelne IP-Adresse beschränken möchten, geben Sie dieselbe Adresse in das Feld für Start-IP und End-IP ein. Durch das Öffnen der Firewall können Administratoren, Benutzer und Anwendungen auf den Koordinatorknoten an Port 5432 zugreifen.

6. Klicken Sie auf der Symbolleiste auf **Speichern**, um diese Firewallregel auf Serverebene zu speichern. Warten Sie auf die Bestätigung, dass das Update für die Firewallregeln erfolgreich war.

## <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure

Es gibt eine einfache Möglichkeit, in Azure gehosteten Anwendungen (z. B. einer Azure-Web-Apps-Anwendung oder Anwendungen, die auf einem virtuellen Azure-Computer ausgeführt werden) Datenbankzugriff mit Hyperscale (Citus) zu gewähren. Aktivieren Sie im Portal im Bereich **Netzwerk** das Kontrollkästchen **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe gestatten**, und wählen Sie **Speichern** aus.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall so, dass sie alle von Azure ausgehenden Verbindungen zulässt, auch Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Verwalten vorhandener Firewallregeln auf Serverebene über das Azure-Portal
Wiederholen Sie die Schritte zum Verwalten der Firewallregeln.
* Klicken Sie zum Hinzufügen des aktuellen Computers auf die Schaltfläche für **+ Aktuelle Client-IP-Adresse hinzufügen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um weitere IP-Adressen hinzuzufügen, geben Sie den Regelnamen, die Start-IP-Adresse und die End-IP-Adresse ein. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu ändern, klicken Sie auf eines der Felder in der Regel, und ändern Sie den betreffenden Wert. Klicken Sie zum Speichern der Änderungen auf **Speichern**.
* Um eine vorhandene Regel zu löschen, klicken Sie auf die Auslassungspunkte [...] und dann auf **Löschen**. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Konzept von Firewallregeln](concepts-hyperscale-firewall-rules.md) und über die Behandlung von Verbindungsproblemen.
