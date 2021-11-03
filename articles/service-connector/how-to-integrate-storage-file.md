---
title: Integrieren von Azure Queue Storage in Service Connector
description: Integrieren von Azure Queue Storage in Ihre Anwendung mit Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: dd6f94a65c1f4b04a81b1604109c6562b37826f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021385"
---
# <a name="integrate-azure-file-storage-with-service-connector"></a>Integrieren von Azure Queue Storage in Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungstypen und Clienttypen von Azure Queue Storage mithilfe des Service-Connectors angezeigt. Möglicherweise können Sie weiterhin eine Verbindung mit Azure Queue Storage in anderen Programmiersprachen herstellen, ohne den Service-Connector zu verwenden. Auf dieser Seite werden auch der Name und Wert der Standardumgebungsvariablen (oder Spring Boot Konfiguration) angezeigt, die Sie beim Erstellen der Dienstverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net | | | ![Ja-Symbol](./media/green-check.png) | |
| Java | | | ![Ja-Symbol](./media/green-check.png) | |
| Java - Spring Boot | | | ![Ja-Symbol](./media/green-check.png) | |
| Node.js | | | ![Ja-Symbol](./media/green-check.png) | |
| Python | | | ![Ja-Symbol](./media/green-check.png) | |
| PHP | | | ![Ja-Symbol](./media/green-check.png) | |
| Ruby | | | ![Ja-Symbol](./media/green-check.png) | |



## <a name="default-environment-variable-names-or-application-properties"></a>Standard-Umgebungsvariablennamen oder Anwendungseigenschaften

### <a name="net-java-nodejs-python-php-and-ruby"></a>.NET, Java, Node.JS, Python, PHP und Ruby

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_STORAGEFILE_CONNECTIONSTRING | Dateispeicher-Verbindungszeichenfolge | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |


### <a name="java---spring-boot"></a>Java - Spring Boot

**Geheimkennwort/Verbindungszeichenfolge**

| Anwendungseigenschaften | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| azure.storage.account-name | Name des Speicherkontos | `{storageAccountName}` |
| azure.storage.account-key | Dateispeicher-Kontoschlüssel | `{yourSecret}` |
| azure.storage.file-endpoint | Queue Storage-Endpunkt | `https://{storageAccountName}.file.core.windows.net/` |


## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
