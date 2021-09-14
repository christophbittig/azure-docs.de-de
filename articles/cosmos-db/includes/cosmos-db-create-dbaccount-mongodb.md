---
title: Datei einfügen
description: include file
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: 3f7a37ce78e49abe1356180818a215488fe82ea3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515616"
---
1. Melden Sie sich in einem neuen Browserfenster beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** aus.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png" alt-text="Screenshot: Erstellen einer Ressource im Azure-Portal":::
   
3. Wählen Sie auf der Seite **Neu** die Optionen **Datenbanken** > **Azure Cosmos DB** aus.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png" alt-text="Screenshot: Bereich „Datenbanken“ im Azure-Portal":::
   
4. Wählen Sie auf der Seite **API-Option auswählen** **Azure Cosmos DB-API für MongoDB** > **Erstellen** aus.

   Die API bestimmt den Typ des zu erstellenden Kontos. Wählen Sie **Azure Cosmos DB-API für MongoDB** aus, denn in diesem Schnellstart erstellen Sie eine Sammlung, für die MongoDB verwendet wird. Weitere Informationen finden Sie unter [Übersicht über die Azure Cosmos DB-API für MongoDB](../mongodb-introduction.md).

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-select-api.png" alt-text="Screenshot: Bereich „API-Option auswählen“":::

5. Geben Sie auf der Seite **Azure Cosmos DB-Konto erstellen** die Einstellungen für das neue Azure Cosmos DB-Konto ein:

   |Einstellung|Wert|BESCHREIBUNG |
   |---|---|---|
   |Subscription|Abonnementname|Wählen Sie das Azure-Abonnement aus, das Sie für dieses Azure Cosmos DB-Konto verwenden möchten. |
   |Ressourcengruppe|Ressourcengruppenname|Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen eindeutigen Namen für die Ressourcengruppe ein. |
   |Kontoname|Geben Sie einen eindeutigen Namen ein.|Geben Sie einen eindeutigen Namen ein, der Ihr Azure Cosmos DB-Konto identifiziert. Der Konto-URI lautet *mongo.cosmos.azure.com* und wird an Ihren eindeutigen Kontonamen angehängt.<br><br>Der Kontoname darf nur Kleinbuchstaben, Ziffern und Bindestriche (-) enthalten und muss zwischen 3 und 44 Zeichen lang sein.|
   |Standort|Die Region, die Ihren Benutzern am nächsten liegt|Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie den Standort, der Ihren Benutzern am nächsten ist, damit sie möglichst schnell auf die Daten zugreifen können.|
   |Kapazitätsmodus|Bereitgestellter Durchsatz oder serverlos|Wählen Sie **Bereitgestellter Durchsatz** aus, um ein Konto im Modus [Bereitgestellter Durchsatz](../set-throughput.md) zu erstellen. Wählen Sie **Serverlos** aus, um ein Konto im Modus [Serverlos](../serverless.md) zu erstellen.<br><br>**Hinweis**: Von serverlosen Konten werden nur die MongoDB-API-Versionen 4 und 3.6 unterstützt. Wenn Sie 3.2 als Version auswählen, wird das Konto im Modus für bereitgestellten Durchsatz erzwungen.|
   |Anwenden des Rabatts für den Free-Tarif von Azure Cosmos DB|**Anwenden** oder **Nicht anwenden**|Mit dem Azure Cosmos DB-Tarif „Free“ erhalten Sie die ersten 1000 RUs/Sek. sowie 25 GB Speicher kostenlos in einem Konto. Weitere Informationen zum [Tarif „Free“](https://azure.microsoft.com/pricing/details/cosmos-db/)|
   | Version | Auswählen der erforderlichen Serverversion | Die Azure Cosmos DB-API für MongoDB ist mit der Serverversion 4.0, 3.6 und 3.2 kompatibel. Sie können ein Konto [upgraden oder herabstufen](../mongodb/upgrade-mongodb-version.md), nachdem es erstellt wurde. |

   > [!NOTE]
   > Sie können pro Azure-Abonnement maximal ein Azure Cosmos DB-Konto im Free-Tarif einrichten und müssen sich beim Erstellen des Kontos anmelden. Wird die Option zum Anwenden des tarifspezifischen Rabatts für den Free-Tarif nicht angezeigt, bedeutet dies, dass bereits ein anderes Konto im Abonnement mit dem Free-Tarif aktiviert wurde.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png" alt-text="Screenshot: Seite „Neues Konto“ für Azure Cosmos DB"::: 

1. Konfigurieren Sie auf der Registerkarte **Globale Verteilung** die folgenden Details. Für diese Schnellstartanleitung können Sie die Standardwerte übernehmen:

   |Einstellung|Wert|Beschreibung |
   |---|---|---|
   |Georedundanz|Deaktivieren|Aktivieren oder deaktivieren Sie die globale Verteilung für Ihr Konto, indem Sie Ihre Region mit einer Region koppeln. Sie können später weitere Regionen zu Ihrem Konto hinzufügen.|
   |Schreibvorgänge in mehreren Regionen|Deaktivieren|Mit der Funktion zum Schreiben in mehreren Regionen können Sie den bereitgestellten Durchsatz für Ihre Datenbanken und Container in der ganzen Welt nutzen.|

   > [!NOTE]
   > Die folgenden Optionen sind nicht verfügbar, wenn Sie als **Kapazitätsmodus** die Option **Serverlos** auswählen:
   > - Tarifspezifischen Rabatt für den Free-Tarif anwenden
   > - Georedundanz
   > - Schreibvorgänge in mehreren Regionen

1. Optional können Sie auf den folgenden Registerkarten zusätzliche Details konfigurieren:

   * **Netzwerk**: Konfigurieren Sie den [Zugriff über ein virtuelles Netzwerk](../how-to-configure-vnet-service-endpoint.md).
   * **Sicherungsrichtlinie**: Konfigurieren Sie eine Richtlinie für [regelmäßige](../configure-periodic-backup-restore.md) oder [fortlaufende](../provision-account-continuous-backup.md) Sicherungen.
   * **Verschlüsselung**: Verwenden Sie entweder einen vom Dienst verwalteten Schlüssel oder einen [kundenseitig verwalteten Schlüssel](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account).
   * **Tags**: Tags sind Name-Wert-Paare, mit denen Sie Ressourcen kategorisieren und eine konsolidierte Abrechnung anzeigen können, indem Sie dasselbe Tag auf mehrere Ressourcen und Ressourcengruppen anwenden.

1. Klicken Sie auf **Überprüfen + erstellen**.

4. Die Kontoerstellung dauert einige Minuten. Warten Sie, bis im Portal die Seite **Congratulations! Ihr Azure Cosmos DB API for MongoDB account is ready** (Herzlichen Glückwunsch! Ihre Azure Cosmos DB-API für das MongoDB-Konto ist bereit) angezeigt wird.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png" alt-text="Screenshot: Bereich „Benachrichtigungen“ im Azure-Portal"::: 
