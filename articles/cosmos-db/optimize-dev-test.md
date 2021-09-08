---
title: Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB
description: In diesem Artikel wird erläutert, wie Sie mit Azure Cosmos DB mehrere kostenlose Möglichkeiten für die Entwicklung und das Testen des Diensts nutzen können.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 60f586e3f2ead0bde743f75d7810bf2d43c737af
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028603"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimieren der Entwicklungs- und Testkosten in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel werden die unterschiedlichen Optionen zur kostenlosen Verwendung von Azure Cosmos DB zu Entwicklungs- und Testzwecken sowie Verfahren zum Optimieren der Kosten für Entwicklungs- oder Testkonten beschrieben.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB-Emulator (lokal zum Download verfügbare Version)

[Azure Cosmos DB-Emulator](local-emulator.md) ist eine Version, die lokal heruntergeladen werden kann und den Azure Cosmos DB-Clouddienst imitiert. Sie können ohne jegliche Kosten Code schreiben und testen, der die Azure Cosmos DB-APIs verwendet, selbst wenn Sie keine Verbindung zum Netzwerk haben. Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung für Entwicklungszwecke mit hoher Wiedergabetreue für den Clouddienst bereit. Sie können Ihre Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen. Wenn Sie soweit sind, dass Sie Ihre Anwendung in der Cloud bereitstellen möchten, aktualisieren Sie einfach die Verbindungszeichenfolge, um eine Verbindung mit dem Azure Cosmos DB-Endpunkt in der Cloud herzustellen. Weitere Änderungen sind nicht erforderlich. Um Tests auszuführen, können Sie auch [eine CI/CD-Pipeline mit dem Buildtask des Azure Cosmos DB-Emulators in Azure DevOps einrichten](tutorial-setup-ci-cd.md). Informationen zum Einstieg finden Sie im Artikel [Azure Cosmos DB-Emulator](local-emulator.md).

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB: Free-Tarif

Der Free-Tarif von Azure Cosmos DB erleichtert Ihnen den Einstieg, die Entwicklung und die Durchführung von Tests für Ihre Anwendungen. Sie können sogar kleinere Produktionsworkloads kostenlos ausführen. Wenn für ein Konto der Free-Tarif aktiviert ist, sind die ersten 1.000 RU/s und 25 GB Speicher für das Konto kostenlos.

Der Free-Tarif gilt unbegrenzt während der Lebensdauer des Kontos und verfügt über alle [Vorteile und Features](introduction.md#key-benefits) eines regulären Azure Cosmos DB-Kontos, z. B. unbegrenzter Speicher und Durchsatz (RU/s), SLAs, Hochverfügbarkeit, globale, sofort einsatzbereite Verteilung in allen Azure-Regionen usw. Sie können ein Konto im Free-Tarif über das Azure-Portal, die Befehlszeilenschnittstelle, PowerShell und eine Resource Manager-Vorlage erstellen. Weitere Informationen finden Sie im Artikel [Erstellen eines Kontos im Free-Tarif](free-tier.md) und auf der Seite [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB kostenlos testen

Unter [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) erhalten Sie eine kostenlose Benutzeroberfläche, die Sie zum Experimentieren mit Azure Cosmos DB in der Cloud verwenden können, ohne dass Sie sich für ein Azure-Konto registrieren oder Ihre Kreditkarte nutzen müssen. Die Azure Cosmos DB-Testkonten sind für einen begrenzten Zeitraum verfügbar (derzeit 30 Tage). Sie können jederzeit erneuert werden. Mit Azure Cosmos DB-Testkonten können Sie Azure Cosmos DB ganz einfach evaluieren und mit den Schnellstartanleitungen oder Tutorials eine Anwendung erstellen und testen. Sie können auch eine Demo erstellen, Komponententests durchführen oder sogar ein Konto mit mehreren Regionen erstellen und dafür eine App ausführen, ohne dass Kosten anfallen. Für ein Azure Cosmos DB-Testkonto können Sie eine Datenbank mit gemeinsam genutztem Durchsatz mit maximal 25 Containern und einem Durchsatz von 20.000 RU/s bzw. einem Container mit bis zu 5.000 RU/s verwenden. Informationen zum Einstieg finden Sie auf der Seite [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Kostenloses Azure-Konto

Azure Cosmos DB ist in dem [kostenlosen Azure-Konto](https://azure.microsoft.com/free) enthalten. Mit diesem Konto erhalten Sie ein kostenloses Azure-Guthaben und -Ressourcen für einen bestimmten Zeitraum. Speziell für Azure Cosmos DB bietet dieses kostenlose Konto einen Speicher von 25 GB und bereitgestellten Durchsatz von 400 RUs/Sek. für das ganze Jahr. Diese Funktion ermöglicht jedem Entwickler ganz einfach und kostenlos das Testen der Funktionen von Azure Cosmos DB oder der Integration mit anderen Azure-Diensten. Mit dem kostenlosen Azure-Konto erhalten Sie ein Guthaben von 200 US-Dollar, das Sie in den ersten 30 Tagen verbrauchen können. Selbst wenn Sie die Dienste schon nutzen, fallen Kosten erst an, wenn Sie ein Upgrade durchführen. Informationen zum Einstieg finden Sie auf der Seite [Kostenloses Azure-Konto](https://azure.microsoft.com/free).

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB serverlos

Mit [Azure Cosmos DB serverlos](serverless.md) können Sie Ihr Azure Cosmos-Konto auf verbrauchsbasierte Weise verwenden. Dabei werden Ihnen nur die Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen und dem von Ihren Daten genutzten Speicher beansprucht werden. Wenn Sie Azure Cosmos DB im serverlosen Modus verwenden, fällt keine Mindestgebühr an. Da das Konzept der bereitgestellten Kapazität entfällt, eignet sich dieser Modus am besten für Entwicklungs- oder Testaktivitäten, insbesondere wenn sich Ihre Datenbank die meiste Zeit im Leerlauf befindet.

## <a name="use-shared-throughput-databases"></a>Verwenden von Datenbanken mit gemeinsam genutztem Durchsatz

Bei einer [Datenbank mit gemeinsam genutztem Durchsatz](set-throughput.md#set-throughput-on-a-database) wird der bereitgestellte Datenbankdurchsatz (RU/s) für alle Container der Datenbank genutzt. Wenn Sie beispielsweise eine Datenbank mit 400 RU/s bereitstellen und vier Container verwenden, wird der Durchsatz von 400 RU/s von allen vier Containern genutzt. In einer Entwicklungs- oder Testumgebung, in der auf die einzelnen Container ggf. weniger häufig zugegriffen wird und somit weniger als 400 RU/s benötigt werden, kann die Anordnung von Containern in einer Datenbank mit gemeinsam genutztem Durchsatz eine Kostenoptimierung bewirken.

Angenommen, Ihr Entwicklungs- oder Testkonto verfügt über vier Container. Wenn Sie vier Container mit einem dedizierten Durchsatz erstellen (mindestens 400 RU/s), beträgt der Gesamtwert 1.600 RU/s. Wenn Sie dagegen eine Datenbank mit gemeinsam genutztem Durchsatz erstellen (mindestens 400 RU/s) und Ihre Container darin anordnen, beträgt der Gesamtwert nur 400 RU/s. Im Allgemeinen eignen sich Datenbanken mit gemeinsam genutztem Durchsatz gut für Szenarien, in denen Sie für einzelne Container keinen garantierten Durchsatz benötigen.  Erfahren Sie mehr zu [Datenbanken mit gemeinsam genutztem Durchsatz](set-throughput.md#set-throughput-on-a-database).

## <a name="next-steps"></a>Nächste Schritte

Zum Einstieg in die Verwendung des Emulators oder des kostenlosen Azure Cosmos DB-Kontos sind folgende Artikel verfügbar:

* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen zu [Azure Cosmos DB serverlos](serverless.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen für das [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen für das [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB Kapazitätsplaner](estimate-ru-with-capacity-planner.md)