---
title: 'Azure Cosmos DB: Free-Tarif'
description: Nutzen Sie den kostenlosen Azure Cosmos DB-Free-Tarif, um mit der Entwicklung und dem Testen Ihrer Anwendungen zu beginnen. Mit dem Free-Tarif erhalten Sie die ersten 1000 RU/s und 25 GB Speicherplatz im Konto kostenlos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: sngun
ms.openlocfilehash: ddc09c428a7779f5e059ead02dbd869a592813e5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554771"
---
# <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB: Free-Tarif 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Der kostenlose Azure Cosmos DB-Free-Tarif erleichtert den Einstieg, die Entwicklung, das Testen Ihrer Anwendungen oder sogar die Ausführung kleiner Produktions-Workloads – und das komplett kostenlos. Wenn der kostenlose Free-Tarif für ein Konto aktiviert ist, erhalten Sie die ersten 1000 RU/s und 25 GB Speicherplatz im Konto kostenlos. Der über diese Grenzen hinaus verbrauchte Durchsatz und Speicherplatz wird zum Normaltarif berechnet. Der Free-Tarif ist für alle API-Konten mit bereitgestelltem Durchsatz, automatisch skaliertem Durchsatz, einzelnen oder mehreren Schreibregionen verfügbar.

Der Free-Tarif gilt auf unbestimmte Zeit für die Lebensdauer des Kontos und beinhaltet alle [Vorteile und Funktionen](introduction.md#key-benefits) eines regulären Azure Cosmos DB-Kontos. Zu diesen Vorteilen gehören unbegrenzter Speicher und Durchsatz (RU/s), SLAs, hohe Verfügbarkeit, schlüsselfertige globale Verteilung in allen Azure-Regionen und mehr.

Sie können pro Azure-Abonnement maximal ein Azure Cosmos DB-Konto im Free-Tarif einrichten und müssen sich beim Erstellen des Kontos registrieren. Wird die Option zum Anwenden des tarifspezifischen Rabatts für den Free-Tarif nicht angezeigt, bedeutet dies, dass bereits ein anderes Konto im Abonnement mit dem Free-Tarif aktiviert wurde. Wenn Sie ein Konto mit dem Free-Tarif erstellen und dann löschen, können Sie den Free-Tarif auf ein neues Konto anwenden. Beim Anlegen eines neuen Kontos wird empfohlen, den kostenlosen Free-Tarif-Rabatt zu aktivieren, wenn er verfügbar ist.

> [!NOTE]
> Der Free-Tarif ist derzeit nicht für serverlose Konten verfügbar.

## <a name="free-tier-with-shared-throughput-database"></a>Der Free-Tarif mit gemeinsam genutzter Durchsatz-Datenbank

Beim gemeinsamen Durchsatz-Modell wird bei der Bereitstellung des Durchsatzes für eine Datenbank der Durchsatz auf alle Container in der Datenbank verteilt. Wenn Sie den Free-Tarif verwenden, können Sie eine gemeinsam genutzte Datenbank mit bis zu 1000 RU/s kostenlos bereitstellen. Alle Container in der Datenbank teilen den Durchsatz. 

Genau wie beim regulären Konto kann eine Datenbank mit gemeinsamem Durchsatz im Free-Tarif-Konto maximal 25 Container enthalten. Alle zusätzlichen Datenbanken mit gemeinsamem Durchsatz oder Container mit dediziertem Durchsatz über 1000 RU/s werden zum Normaltarif abgerechnet.

## <a name="free-tier-with-azure-discount"></a>Free-Tarif mit Azure-Rabatt

Der Azure Cosmos DB-Free-Tarif ist mit dem [kostenlosen Azure-Konto](optimize-dev-test.md#azure-free-account) kompatibel. Um beizutreten, erstellen Sie in Ihrem kostenlosen Azure-Konto-Abonnement ein Azure Cosmos DB-Konto im Free-Tarif. Für die ersten 12 Monate erhalten Sie einen kombinierten Rabatt von 1400 RU/s (1000 RU/s vom Azure Cosmos DB Free-Tarif und 400 RU/s vom kostenlosen Azure-Konto) und 50 GB Speicherplatz (25 GB vom Azure Cosmos DB Free-Tarif und 25 GB vom kostenlosen Azure-Konto). Nach Ablauf der 12 Monate erhalten Sie weiterhin 1000 RU/s und 25 GB aus dem kostenlosen Azure Cosmos DB-Free-Tarif, und zwar für die gesamte Lebensdauer des Azure Cosmos DB-Kontos. Ein Beispiel dafür, wie die Gebühren gestapelt werden, finden Sie unter [Abrechnungsbeispiele mit Free-Tarif-Konten](understand-your-bill.md#azure-free-tier).

> [!NOTE]
> Der Free-Tarif von Azure Cosmos DB unterscheidet sich vom kostenlosen Azure-Konto. Das kostenlose Azure-Konto bietet Azure-Gutschriften und -Ressourcen kostenlos für einen begrenzten Zeitraum. Wenn Sie Azure Cosmos DB im Rahmen dieses kostenlosen Kontos nutzen, erhalten Sie 25 GB Speicher und 400 RU/s von bereitgestelltem Durchsatz für 12 Monate.

## <a name="best-practices-to-keep-your-account-free"></a>Bewährte Methoden, damit Ihr Konto kostenlos bleibt

Wenn Sie den kostenlosen Azure Cosmos DB-Free-Tarif verwenden, sollte Ihr Konto keine zusätzlichen RU/s oder Speicherverbrauch aufweisen, außer denen, die vom kostenlosen Free-Tarif angeboten werden, um Ihr Konto vollständig kostenlos zu halten.

Nachfolgend finden Sie beispielsweise einige Optionen, die keine monatlichen Kosten verursachen:

* Eine Datenbank mit einem max. bereitgestellten Durchsatz von 1000 RU/s.
* Zwei Container, einer mit einem maximalen Durchsatz von 400 RU/s und der andere mit einem maximal bereitgestelltem Durchsatz von 600 RU/s.
* Ein Konto mit 2 Bereichen und einem einzigen Bereich, der einen Container mit einem maximalen bereitgestellten Durchsatz von 500 RU/s hat.

## <a name="create-an-account-with-free-tier"></a>Erstellen Sie ein Konto mit dem Free-Tarif

Sie können ein kostenlose Free-Tarif-Konto mit Azure-Portal, PowerShell, CLI oder Azure Resource Manager (ARM)-Vorlagen erstellen. Sie können den Free-Tarif während der Erstellung des Kontos auswählen, Sie können es nicht einstellen, nachdem das Konto erstellt wurde.

### <a name="azure-portal"></a>Azure-Portal

Setzen Sie beim Erstellen des Kontos über das Azure-Portal die Option **Free-Tarif-Rabatt anwenden** auf **Anwenden**. Eine Schritt-für-Schritt-Anleitung finden Sie im Artikel [Erstellen eines neuen Kontos mit dem Free-Tarif](create-cosmosdb-resources-portal.md).

### <a name="arm-template"></a>ARM-Vorlage

Um ein kostenloses Free-Tarif-Konto unter Verwendung einer ARM-Vorlage zu erstellen, stellen Sie die Eigenschaft `"enableFreeTier": true` ein. Die vollständige Vorlage finden Sie unter Beispiel für die Bereitstellung einer [ARM-Vorlage mit Free-Tarif](manage-with-templates.md#free-tier).

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Um ein Konto mit Free-Tarif mittels CLI zu erstellen, stellen Sie den `--enable-free-tier`Parameter auf „true“ ein:

```azurecli-interactive
# Create a free tier account for SQL API
az cosmosdb create \
    -n "Myaccount" \
    -g "MyResourcegroup" \
    --enable-free-tier true \
    --default-consistency-level "Session"
    
```

### <a name="powershell"></a>PowerShell

Um ein Konto mit Free-Tarif mittels Azure PowerShell zu erstellen, stellen Sie den `-EnableFreeTier`Parameter auf „true“ ein:

```powershell-interactive
# Create a free tier account for SQL API. 
New-AzCosmosDBAccount -ResourceGroupName "MyResourcegroup" `
    -Name "Myaccount" `
    -ApiKind "sql" `
    -EnableFreeTier true `
    -DefaultConsistencyLevel "Session" `
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie ein kostenloses Free-Tarif-Konto erstellt haben, können Sie mit der Erstellung von Apps mit Azure Cosmos DB gemäß den folgenden Artikeln beginnen:

* [Erstellen Sie eine Konsolen-App mit dem .NET V4 SDK](create-sql-api-dotnet-v4.md), um Azure Cosmos DB-Ressourcen zu verwalten.
* [Erstellen einer .NET-Web-App mit der API für MongoDB von Azure Cosmos DB](mongodb/create-mongodb-dotnet.md)
* [Laden Sie ein Notebook aus der Galerie](publish-notebook-gallery.md#download-a-notebook-from-the-gallery) herunter und analysieren Sie Ihre Daten.
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
