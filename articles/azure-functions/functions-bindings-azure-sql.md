---
title: Azure SQL-Bindungen für Functions
description: Erfahren Sie, wie Azure SQL-Bindungen in Azure Functions verwendet werden.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: ece79028f5cf0211f7d7345d54e1ccdb096cc6c9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490741"
---
# <a name="azure-sql-bindings-for-azure-functions-overview-preview"></a>Übersicht SQL Azure Azure Functions-Bindungen (Vorschau)

In dieser Gruppe von Artikeln wird die Verwendung von [Azure SQL](/azure/azure-sql/)-Bindungen in Azure Functions erläutert. Azure Functions unterstützt Eingabe- und Ausgabebindungen für die Azure SQL- und SQL Server-Produkte.

| Aktion | type |
|---------|---------|
| Lesen von Daten aus einer Datenbank | [Eingabebindung](./functions-bindings-azure-sql-input.md) |
| Speichern von Daten in einer Datenbank |[Ausgabebindung](./functions-bindings-azure-sql-output.md) |

> [!NOTE]
> Dieser Verweis gilt für [Azure Functions Version 2.x oder höher](functions-versions.md). 
>
> Diese Bindung erfordert eine Verbindung mit einer Azure SQL- oder SQL Server-Datenbank.

## <a name="add-to-your-functions-app"></a>Hinzufügen zu Ihrer Funktions-App

### <a name="functions"></a>Functions

Das Arbeiten mit Triggern und Bindungen erfordert, dass Sie auf das entsprechende Paket verweisen. Das NuGet-Paket wird für .NET-Klassenbibliotheken verwendet, während das Erweiterungspaket für alle anderen Anwendungstypen verwendet wird.

| Sprache                                        | Hinzufügen nach...                                   | Bemerkungen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installieren des [NuGet-Vorschaupakets] | |
<!--| C#-Skript, Java, JavaScript, Python, PowerShell | Registrieren des [Erweiterungspaket]          | Die [Erweiterung für Azure-Tools] wird zur Verwendung mit Visual Studio Code empfohlen. | -->


[NuGet-Vorschaupaket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Sql
[core tools]: ./functions-run-local.md
[Erweiterungspaket]: ./functions-bindings-register.md#extension-bundles
[Azure-Tools-Erweiterung]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack


## <a name="known-issues"></a>Bekannte Probleme

- Ausgabebindungen für Tabellen mit Spalten der Datentypen `NTEXT`, `TEXT` oder `IMAGE` werden nicht unterstützt, und Daten-Upserts sind nicht möglich. Diese Typen werden in einer zukünftigen Version von SQL Server [entfernt](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql) und sind nicht mit der Funktion `OPENJSON` kompatibel, die von dieser Azure Functions verwendet wird.
- [Sortierungen](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#Collation_Defn), bei der Groß-/Kleinschreibung beachtet wird, werden derzeit nicht unterstützt. [GitHub Element #133](https://github.com/Azure/azure-functions-sql-extension/issues/133) verfolgt den Fortschritt dieses Problems nach.


## <a name="open-source"></a>Open Source

Die Azure SQL-Bindungen für Azure Functions sind Open Source und im Repository unter [https://github.com/Azure/azure-functions-sql-extension](https://github.com/Azure/azure-functions-sql-extension) verfügbar.


## <a name="next-steps"></a>Nächste Schritte

- [Lesen von Daten aus einer Datenbank (Eingabebindung)](./functions-bindings-azure-sql-input.md)
- [Speichern von Daten in einer Datenbank (Ausgabebindung)](./functions-bindings-azure-sql-output.md)