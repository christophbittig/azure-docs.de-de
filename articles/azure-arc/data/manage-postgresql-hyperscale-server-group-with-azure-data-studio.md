---
title: Verwalten von PostgreSQL-Instanzen mithilfe von Azure Data Studio
description: Verwalten von PostgreSQL-Instanzen mithilfe von Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 1849e317c4245eb1aa96ff96f0ffac8bd8425299
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349551"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Verwalten einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung mithilfe von Azure Data Studio


Dieser Artikel beschreibt, wie Sie:
- PostgreSQL-Instanzen mit Dashboardansichten wie Übersicht, Verbindungszeichenfolgen, Eigenschaften, Resource Health verwalten...
- mit Daten und Schemas arbeiten

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Installieren von azdata, Azure Data Studio und Azure CLI](install-client-tools.md)
- Installieren der Erweiterungen für **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** und **Azure Arc** und **PostgreSQL** in Azure Data Studio

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- Erstellen des [Azure Arc-Datencontrollers](./create-data-controller-indirect-cli.md)
- Starten von Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Herstellen einer Verbindung mit dem Azure Arc-Datencontroller

Erweitern Sie in Azure Data Studio den Knoten **Azure Arc-Controller**, und klicken Sie dann auf die Schaltfläche **Controller verbinden**:

Geben Sie die Verbindungsinformationen Ihres Azure-Datencontrollers ein:

- **Controller-URL:**

    Die URL, über die eine Verbindung mit Ihrem Controller in Kubernetes hergestellt wird. Die URL wird als `https://<IP_address_of_the_controller>:<Kubernetes_port.` eingegeben. Beispiel:

    ```console
    https://12.345.67.890:30080
    ```
- **Benutzername:**

    Der Name des Benutzerkontos, über das Sie eine Verbindung mit dem Controller herstellen. Verwenden Sie den Namen, den Sie normalerweise beim Ausführen von `az login` verwenden. Es handelt sich nicht um den Namen des PostgreSQL-Benutzers, den Sie normalerweise in „psql“ verwenden, um eine Verbindung mit dem PostgreSQL-Datenbankmodul herzustellen.
- **Kennwort:** Das Kennwort des Benutzerkontos, über das Sie eine Verbindung mit dem Controller herstellen.


Ihr Arc-Datencontroller wird in Azure Data Studio angezeigt. Erweitern Sie den Eintrag, um die Liste der von ihm verwalteten PostgreSQL-Instanzen anzuzeigen.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Verwalten von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung

Klicken Sie mit der rechten Maustaste auf die PostgreSQL-Instanz, die Sie verwalten möchten, und wählen Sie [Verwalten] aus.

Die PostgreSQL Dashboard-Ansicht:

Die Ansicht umfasst mehrere Dashboards, die auf der linken Seite des Bereichs aufgeführt sind:

- **Übersicht:** Zeigt Übersichtsinformationen zu Ihrer Instanz an wie z. B. den Namen, den Namen des PostgreSQL-Administratorbenutzers, die Azure-Abonnement-ID, die Konfiguration, die Version des Datenbankmoduls, die Endpunkte für Grafana und Kibana...
- **Verbindungszeichenfolgen:** Zeigt verschiedene Verbindungszeichenfolgen an, die Sie möglicherweise benötigen, um eine Verbindung mit Ihrer PostgreSQL-Instanz wie psql, Node.js, PHP oder Ruby herzustellen...
- **Diagnose und Problembehandlung:** Auf dieser Landing Page finden Sie verschiedene Ressourcen, über die Sie die Problembehandlung für Ihre Instanz durchführen, während wir die Notebooks zur Problembehandlung erweitern.
- **Neue Supportanfrage:** Über diese Landing Page können Sie ab der Ankündigung der Public Preview Unterstützung von unseren Supportdiensten anfordern.

## <a name="work-with-your-data-and-schema"></a>Arbeiten mit Daten und Schemas

Erweitern Sie auf der linken Seite des Azure Data Studio Fensters den Knoten **Server**:

Wählen Sie [Verbindung hinzufügen] aus, und geben Sie die Verbindungsdetails für Ihre PostgreSQL-Instanz ein:
- **Verbindungstyp**: PostgreSQL
- **Servername:** Geben Sie den Namen der PostgreSQL-Instanz ein. Beispiel: postgres01
- **Authentifizierungstyp:** Kennwort
- **Benutzername:** Hier können Sie den Standardnamen des PostgreSQL-Administratorbenutzers eingeben. Für dieses Feld wird zwischen Groß- und Kleinschreibung unterschieden.
- **Kennwort:** Das Kennwort des PostgreSQL-Benutzernamens befindet sich in der Ausgabe des Befehls `az postgres arc-server endpoint -n postgres01` in der psql-Verbindungszeichenfolge.
- **Datenbankname:** Legen Sie den Namen der Datenbank fest, mit der Sie eine Verbindung herstellen möchten. Sie können den Namen __Standard__ übernehmen.
- **Servergruppe:** Sie können den Namen __Standard__ übernehmen.
- **Name (optional):** Dieses Feld kann leer bleiben.
- **Erweitert:**
    - **Host-IP-Adresse:** Die öffentliche IP-Adresse des Kubernetes-Clusters.
    - **Port:** Der Port, an dem die PostgreSQL-Instanz lauscht. Sie finden diesen Port am Ende der psql-Verbindungszeichenfolge in der Ausgabe des Befehls `az postgres arc-server endpoint -n postgres01`. Es handelt sich nicht um Port 30080, an dem Kubernetes lauscht und den Sie zum Herstellen einer Verbindung mit dem Azure-Datencontroller in Azure Data Studio eingegeben haben.
    - **Weitere Parameter:** Diese sollten selbsterklärend sein. Sie können die bestehenden Standardwerte/Leerstellen übernehmen.

Klicken Sie auf **[OK] und [Verbinden]** , um eine Verbindung mit dem Server herzustellen.

Sobald die Verbindung besteht, sind mehrere Umgebungen verfügbar:
- **Neue Abfrage**
- **Neues Notebook**
- **Erweitern Sie die Anzeige des Servers und durchsuchen/arbeiten Sie mit Objekten in der Datenbank.**
- **...**

## <a name="next-step"></a>Nächster Schritt
[Überwachen der Servergruppe](monitor-grafana-kibana.md)