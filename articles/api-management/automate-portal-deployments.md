---
title: Automatisieren von Bereitstellungen im Entwicklerportal
titleSuffix: Azure API Management
description: Erfahren Sie, wie Sie Inhalte des selbstgehosteten Entwicklerportals automatisch zwischen zwei API Management-Diensten migrieren.
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a2fab946179fb0bef9db0068bb8f660f91ca8c04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669149"
---
# <a name="automate-developer-portal-deployments"></a>Automatisieren von Bereitstellungen im Entwicklerportal

Das API Management-Entwicklerportal unterstützt den programmgesteuerten Zugriff auf Inhalte. Es ermöglicht Ihnen das Importieren oder Exportieren von Daten in einen API Management-Dienst über die [REST-API für die Inhaltsverwaltung](/rest/api/apimanagement/). Der REST-API-Zugriff funktioniert sowohl für verwaltete als auch für selbstgehostete Portale.

## <a name="automated-migration-script"></a>Automatisiertes Migrationsskript

Sie können die API verwenden, um die Migration von Inhalten zwischen zwei API Management-Diensten zu automatisieren, z. B. einem Dienst in der Testumgebung und einem Dienst in der Produktionsumgebung. Das `scripts.v3/migrate.js`-Skript im API Management-Entwicklerportal [GitHub-Repository](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) vereinfacht diesen Automatisierungsprozess.

> [!WARNING]
> Mit dem Skript wird der Inhalt des Entwicklerportals im Ziel-API-Management entfernt. Wenn Sie Bedenken haben, stellen Sie sicher, dass Sie eine Sicherung durchführen.

> [!NOTE]
> Wenn Sie ein selbstgehostetes Portal mit einem explizit definierten benutzerdefinierten Speicherkonto zum Hosten von Mediendateien verwenden (d. h. Sie definieren die `blobStorageUrl`-Einstellung in der `config.design.json`-Konfigurationsdatei), müssen Sie das ursprüngliche `scripts/migrate.js` [Skript](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js) verwenden. Das ursprüngliche Skript funktioniert nicht für verwaltete oder selbstgehostete Portale mit dem Medienspeicherkonto, das von API Management verwaltet wird. Verwenden Sie in diesem Fall stattdessen das Skript aus dem `/scripts.v3`-Ordner.

Das Skript führt die folgenden Schritte aus:

1. Erfassen Sie die Portalinhalte und -medien aus dem API Management-Dienst.
1. Entfernen Sie die Portalinhalte und -medien aus dem API Management-Dienst.
1. Laden Sie die Portalinhalte und -medien im angestrebten API Management-Dienst hoch.
1. Optional und nur für verwaltete Portale: Veröffentlichen Sie das Portal automatisch.

Nachdem das Skript erfolgreich ausgeführt wurde, sollte der Ziel-API-Management-Dienst den gleichen Portalinhalt wie der Quelldienst enthalten, und Sie können ihn als Administrator sehen.

* Wenn Sie ein verwaltetes Portal verwenden, können Sie das Skript so festlegen, dass das Zielportal automatisch veröffentlicht wird, damit die migrierte Version den Besucher automatisch zur Verfügung gestellt wird. 
* Wenn Sie ein selbstgehostetes Portal verwenden, müssen Sie das Zielportal manuell veröffentlichen. Befolgen Sie die Anweisungen zum Veröffentlichen und Hosten im Tutorial, um [ein selbstgehostetes Entwicklerportal einzurichten](developer-portal-self-host.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)
- [Selbsthosten des API Management-Entwicklerportals](developer-portal-self-host.md)