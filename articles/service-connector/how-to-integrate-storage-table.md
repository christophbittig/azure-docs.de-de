---
title: Integrieren von Azure Table Storage mit Service Connector
description: Integrieren von Azure Table Storage in Ihre Anwendung mit Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 646abc1fc11de0c1d9d5f3c6382e0824c020e85c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021525"
---
# <a name="integrate-azure-table-storage-with-service-connector"></a>Integrieren von Azure Table Storage mit Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungs- und Clienttypen von Azure Table Storage mit Service Connector veranschaulicht. Möglicherweise können Sie auch in anderen Programmiersprachen eine Verbindung mit Azure Table Storage herstellen, ohne Service Connector zu verwenden. Diese Seite zeigt auch den Standardumgebungsvariablennamen und -wert (oder die Spring Boot-Konfiguration) an, die Sie beim Erstellen der Dienstverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net | | | ![Ja-Symbol](./media/green-check.png) | |
| Java | | | ![Ja-Symbol](./media/green-check.png) | |
| Node.js | | | ![Ja-Symbol](./media/green-check.png) | |
| Python | | | ![Ja-Symbol](./media/green-check.png) | |


## <a name="default-environment-variable-names-or-application-properties"></a>Namen von Standard-Umgebungsvariablen oder Anwendungseigenschaften

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.JS und Python

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_STORAGETABLE_CONNECTIONSTRING | Table Storage-Verbindungszeichenfolge | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |


## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
