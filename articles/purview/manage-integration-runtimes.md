---
title: Erstellen und Verwalten von Integration Runtimes
description: In diesem Artikel werden die Schritte zum Erstellen und Verwalten von Integration Runtimes in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 1b2748664046c97258ee3414b741075627064bbc
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867480"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Erstellen und Verwalten einer selbstgehosteten Integration Runtime

In diesem Artikel wird beschrieben, wie Sie eine selbstgehostete Integration Runtime (SHIR) erstellen und verwalten, um Datenquellen in Azure Purview zu überprüfen.

> [!NOTE]
> Die Purview-Integration Runtime kann nicht zusammen mit einer Azure Synapse Analytics- oder Azure Data Factory-Integration Runtime auf demselben Computer verwendet werden. Sie muss auf einem separaten Computer installiert werden.

> [!IMPORTANT]
> Wenn Sie Ihr Azure Purview-Konto nach dem 18. August 2021 erstellt haben, stellen Sie sicher, dass Sie die neueste Version der selbstgehosteten Integration Runtime aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) herunterladen und installieren.

## <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime

1. Wählen Sie auf der Startseite von Purview Studio im linken Navigationsbereich die Option **Verwaltungscenter** aus.

2. Wählen Sie im linken Bereich unter **Sources and scanning** (Quellen und Überprüfung) die Option **Integration Runtimes** und dann **+ Neu** aus.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Klicken auf „Integration Runtimes“":::

3. Wählen Sie auf der Seite **Integration Runtime-Setup** die Option **Selbstgehostet** aus, um eine selbstgehostete IR zu erstellen, und wählen Sie dann **Weiter** aus.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Erstellen einer neuen SHIR":::

4. Geben Sie einen Namen für Ihre IR ein, und wählen Sie Erstellen aus.

5. Führen Sie auf der Seite **Integration Runtime settings** (Integration Runtime-Einstellungen) die Schritte im Abschnitt **Manual setup** (Manuelles Setup) aus. Sie müssen die Integration Runtime von der Downloadwebsite auf einen virtuellen oder physischen Computer herunterladen, den Sie für die Ausführung verwenden möchten.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Abrufen des Schlüssels":::

   - Kopieren Sie den Authentifizierungsschlüssel, und fügen Sie ihn ein.

   - Laden Sie die selbstgehostete Integration Runtime von [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf einen lokalen Windows-Computer herunter. Führen Sie das Installationsprogramm aus. Selbstgehostete Integration Runtime-Versionen wie 5.4.7803.1 und 5.6.7795.1 werden unterstützt. 

   - Fügen Sie auf der Seite **Integration Runtime (selbstgehostet) registrieren** einen der beiden Schlüssel ein, die Sie zuvor gespeichert haben, und wählen Sie **Registrieren** aus.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Einfügen des Schlüssels":::

   - Klicken Sie auf der Seite **Neuer Knoten der Integrationslaufzeit (selbstgehostet)** auf **Fertig stellen**.

6. Nachdem die selbstgehostete Integration Runtime erfolgreich registriert wurde, wird das folgende Fenster angezeigt:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Erfolgreiche Registrierung":::

## <a name="networking-requirements"></a>Netzwerkanforderungen

Ihr selbstgehosteter Integration Runtime-Computer muss eine Verbindung mit verschiedenen Ressourcen herstellen, um ordnungsgemäß zu funktionieren:

* Quellen, die Sie mithilfe der selbstgehosteten Integration Runtime überprüfen möchten
* Alle Azure Key Vault-Instanzen, die zum Speichern von Anmeldeinformationen für die Purview-Ressource verwendet werden
* Das verwaltete Speicherkonto und Event Hub-Ressourcen, die von Purview erstellt wurden

Die verwalteten Speicher- und Event Hub-Ressourcen finden Sie in Ihrem Abonnement unterhalb einer Ressourcengruppe, die den Namen Ihrer Purview-Ressource enthält. Azure Purview verwendet diese Ressourcen unter anderem zum Erfassen der Ergebnisse der Überprüfung, sodass die selbstgehostete Integration Runtime in der Lage sein muss, eine direkte Verbindung mit diesen Ressourcen herzustellen.

Nachfolgend werden die Domänen und Ports aufgeführt, die über Unternehmens- und Computerfirewalls zugelassen werden müssen.

> [!NOTE]
> Fügen Sie für aufgelistete Domänen mit der Kennzeichnung \<managed Purview storage account> den Namen des verwalteten Speicherkontos hinzu, das Ihrer Purview-Ressource zugeordnet ist. Sie finden diese Ressource im Portal. Durchsuchen Sie Ihre Ressourcengruppen nach einer Gruppe mit dem folgenden Namen: managed-rg-\<your Purview Resource name>. Beispiel: managed-rg-contosoPurview. Sie verwenden den Namen des Speicherkontos in dieser Ressourcengruppe.
> 
> Fügen Sie für aufgelistete Domänen mit der Kennzeichnung \<managed Event Hub resource> den Namen der verwalteten Event Hub-Instanz hinzu, die Ihrer Purview-Ressource zugeordnet ist. Diese befindet sich in derselben Ressourcengruppe wie das verwaltete Speicherkonto.

| Domänennamen                  | Ausgehende Ports | BESCHREIBUNG                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | Globale Infrastruktur, die Purview zum Ausführen der Überprüfungen verwendet. Der Platzhalter ist erforderlich, da keine dedizierte Ressource vorhanden ist. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview verwendet diese Domäne, um eine Verbindung mit der zugeordneten Service Bus-Instanz herzustellen. Sie wird durch die Zulassung der obigen Domäne abgedeckt, aber wenn Sie private Endpunkte verwenden, müssen Sie den Zugriff auf diese einzelne Domäne testen.|
| `*.frontend.clouddatahub.net` | 443            | Globale Infrastruktur, die Purview zum Ausführen der Überprüfungen verwendet. Der Platzhalter ist erforderlich, da keine dedizierte Ressource vorhanden ist. |
| `<managed Purview storage account>.core.windows.net`          | 443            | Wird von der selbstgehosteten Integration Runtime verwendet, um eine Verbindung mit dem verwalteten Azure Storage-Konto herzustellen.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Warteschlangen, die von Purview zum Ausführen des Überprüfungsprozesses verwendet werden. |
| `download.microsoft.com` | 443           | Optional, für SHIR-Updates. |

Basierend auf Ihren Quellen müssen Sie möglicherweise auch die Domänen anderer Azure- oder externer Quellen zulassen. Im Folgenden finden Sie einige Beispiele sowie die Azure Key Vault-Domäne, wenn Sie eine Verbindung mit Anmeldeinformationen herstellen, die im Key Vault gespeichert sind.

| Domänennamen                  | Ausgehende Ports | BESCHREIBUNG                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | Optional, zum Herstellen der Verbindung mit einem Azure Storage-Konto |
| `*.database.windows.net`      | 1433           | Optional, zum Herstellen der Verbindung mit einer Azure SQL-Datenbank oder Azure Synapse Analytics |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Optional, zum Herstellen der Verbindung mit Azure Data Lake Storage Gen 1. |
| `<datastoragename>.dfs.core.windows.net`    | 443            | Optional, zum Herstellen der Verbindung mit Azure Data Lake Storage Gen 2. |
| `<your Key Vault Name>.vault.azure.net` | 443           | Erforderlich, wenn Anmeldeinformationen in Azure Key Vault gespeichert werden. |
| Verschiedene Domänen | Abhängig von Umgebung          | Domänen für weitere Quellen zur Verbindungsherstellung über SHIR. |
  
> [!IMPORTANT]
> In den meisten Umgebungen müssen Sie außerdem bestätigen, dass DNS ordnungsgemäß konfiguriert ist. Dient der Bestätigung, dass Sie von Ihrem SHIR-Computer aus mithilfe von **nslookup** die Konnektivität mit jeder der oben genannten Domänen überprüfen können. Jede nslookup-Abfrage sollte die IP-Adresse der Ressource zurückgeben. Wenn Sie [private Endpunkte](catalog-private-link.md) verwenden, sollte die private IP-Adresse und nicht die öffentliche IP-Adresse zurückgegeben werden. Wenn keine IP-Adresse oder bei Verwendung privater Endpunkte die öffentliche IP-Adresse zurückgegeben wird, müssen Sie Ihre DNS-/VNet-Zuordnung oder Ihr privates Endpunkt-/VNet-Peering überprüfen.

## <a name="manage-a-self-hosted-integration-runtime"></a>Verwalten einer selbstgehosteten Integration Runtime

Sie können eine selbstgehostete Integration Runtime bearbeiten, indem Sie im **Verwaltungscenter** zu **Integration Runtimes** navigieren, die IR auswählen und dann auf „Bearbeiten“ klicken. Sie können die Beschreibung jetzt aktualisieren, den Schlüssel kopieren oder neue Schlüssel generieren.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Bearbeiten der IR":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Bearbeiten von Details der IR":::

Sie können eine selbstgehostete Integration Runtime löschen, indem Sie im Verwaltungscenter zu **Integration Runtimes** navigieren, die IR auswählen und dann auf **Löschen** klicken. Nachdem eine IR gelöscht wurde, treten für alle Überprüfungen, die darauf basieren, Fehler auf.

## <a name="next-steps"></a>Nächste Schritte

- [So werden gelöschte Ressourcen bei Scans erkannt](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Verwenden privater Endpunkte mit Purview](catalog-private-link.md)
