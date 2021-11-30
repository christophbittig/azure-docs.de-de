---
title: Erste Schritte mit dem FHIR-Dienst – Azure Healthcare-APIs
description: In diesem Dokument werden die ersten Schritte mit dem FHIR-Dienst in Azure Healthcare-APIs beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: f679f82531c84b5c05de4bddc2551c9c51c6d09b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273169"
---
# <a name="get-started-with-the-fhir-service"></a>Erste Schritte mit dem FHIR-Dienst

In diesem Artikel werden die grundlegenden Schritte für die ersten Schritte mit dem FHIR-Dienst in [Azure Healthcare-APIs beschrieben.](../healthcare-apis-overview.md)

Als Voraussetzung benötigen Sie ein Azure-Abonnement und haben die richtigen Berechtigungen zum Erstellen von Azure-Ressourcengruppen und Bereitstellen von Azure-Ressourcen erhalten. Sie können alle Schritte ausführen oder einige überspringen, wenn Sie über eine vorhandene Umgebung verfügen. Außerdem können Sie alle Schritte kombinieren und in PowerShell-, Azure CLI- und REST-API-Skripts ausführen.

[![Erste Schritte mit dem FHIR-Dienstflussdiagramm.](media/get-started-with-fhir.png)](media/get-started-with-fhir.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Erstellen eines Arbeitsbereichs in Ihrem Azure-Abonnement

Sie können einen Arbeitsbereich über die [Azure-Portal](../healthcare-apis-quickstart.md)oder mithilfe von PowerShell, Azure CLI und der REST-API erstellen. Skripts finden Sie in den [Beispielen für Gesundheits-APIs.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

> [!NOTE]
> Es gibt Grenzwerte für die Anzahl der Arbeitsbereiche und die Anzahl der FHIR-Dienstinstanzen, die Sie in jedem Azure-Abonnement erstellen können.

## <a name="create-a-fhir-service-in-the-workspace"></a>Erstellen eines FHIR-Diensts im Arbeitsbereich

Sie können eine FHIR-Dienstinstanz über die [Azure-Portal](../fhir/fhir-portal-quickstart.md)oder mithilfe von PowerShell, Azure CLI und der REST-API erstellen. Skripts finden Sie in den [Beispielen für Gesundheits-APIs.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

Optional können Sie einen [DICOM-Dienst](../dicom/deploy-dicom-services-in-azure.md) und [einen IoT-Connector](../iot/deploy-iot-connector-in-azure.md) im Arbeitsbereich erstellen.

## <a name="access-the-fhir-service"></a>Zugreifen auf den FHIR-Dienst

Der FHIR-Dienst wird durch Azure Active Directory (Azure AD) geschützt, die nicht deaktiviert werden können. Für den Zugriff auf die Dienst-API müssen Sie eine Clientanwendung erstellen, die in Azure AD auch als Dienstprinzipal bezeichnet wird, und ihr die richtigen Berechtigungen erteilen.

### <a name="register-a-client-application"></a>Registrieren einer Clientanwendung

Sie können eine Clientanwendung über die [Azure-Portal](../register-application.md)oder mithilfe von PowerShell und Azure CLI Skripts erstellen oder registrieren. Diese Clientanwendung kann für eine oder mehrere FHIR-Dienstinstanzen verwendet werden. Sie kann auch für andere Dienste in Azure Healthcare-APIs verwendet werden.

Wenn die Clientanwendung mit einem Zertifikat oder einem geheimen Clientschlüssel erstellt wird, stellen Sie sicher, dass Sie das Zertifikat oder den geheimen Clientschlüssel vor dem Ablauf erneuern und die Clientanmeldeinformationen in Ihren Anwendungen ersetzen.

Sie können eine Clientanwendung löschen. Stellen Sie vor dem Löschen einer Clientanwendung sicher, dass sie nicht in Produktions-, Entwicklungs-, Test- oder Qualitätssicherungsumgebungen verwendet wird.

### <a name="grant-access-permissions"></a>Erteilen von Zugriffsberechtigungen

Sie können Zugriffsberechtigungen erteilen oder Rollen über die [Azure-Portal](../configure-azure-rbac.md)oder mithilfe von PowerShell und Azure CLI Skripts zuweisen.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>Ausführen von CRUD-Transaktionen (Create, Read, Update, Delete)

Sie können CRUD-Transaktionen (Create, Read (Search), Update und Delete (CRUD) für den FHIR-Dienst in Ihren Anwendungen oder mithilfe von Tools wie Postman, REST-Client und cURL ausführen. Da der FHIR-Dienst standardmäßig geschützt ist, müssen Sie ein Zugriffstoken abrufen und es in Ihre Transaktionsanforderung einschließen.

#### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

Sie können ein Azure AD Zugriffstoken mitHilfe von PowerShell, Azure CLI, REST CCI oder .NET SDK abrufen.  Weitere Informationen finden Sie unter [Abrufen des Zugriffstokens.](../get-access-token.md)

#### <a name="access-using-existing-tools"></a>Zugriff mit vorhandenen Tools

- [Postman](../use-postman.md)
- [REST-Client](../using-rest-client.md)
- [cURL](../using-curl.md)

#### <a name="load-data"></a>Laden der Daten

Sie können Daten direkt mithilfe der POST- oder PUT-Methode für den FHIR-Dienst laden. Zum Massenladen von Daten können Sie eines der unten aufgeführten Open Source-Tools verwenden.
 
- [FHIR-Ladeprogramm](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/FHIRDL) Dies ist eine .NET-Konsolen-App, die in Azure Storage gespeicherte Daten in den FHIR-Dienst lädt. Es ist eine einzelne Thread-App, aber Sie können mehrere Kopien lokal oder in einem Docker-Container ausführen. 
- [FHIR-Massenladeprogramm](https://github.com/microsoft/fhir-loader) Dieses Tool ist eine Azure-Funktions-App (Microservice) und wird in parallelen Threads ausgeführt.
- [Massenimport](https://github.com/microsoft/fhir-server/blob/main/docs/BulkImport.md) Dieses Tool funktioniert nur mit dem Open Source-FHIR-Server. Sie wird jedoch in Zukunft für Azure Healthcare-APIs verfügbar sein.

### <a name="cms-search-profile-validation-and-reindex"></a>CMS, Suche, Profilvalidierung und Neuindizierung

Weitere Informationen zu Interoperabilität und Patientenzugriff, Suche, Profilvalidierung und Neuindizierung finden Sie in der [FHIR-Dienstdokumentation.](overview.md)

### <a name="export-data"></a>Exportieren von Daten

Optional können Sie Daten ($export) exportieren, um [sie zu Azure Storage](../data-transformation/export-data.md) und in Ihren Analyse- oder Machine Learning-Projekten zu verwenden. Sie können die Daten im Format "as-is" oder ["de-id"](../data-transformation/de-identified-export.md) `ndjson` exportieren. 

Sie können Daten auch mithilfe des Open Source-Projekts nach [Synapse](../data-transformation/move-to-synapse.md) exportieren. In Zukunft wird dieses Feature in den verwalteten Dienst integriert.

### <a name="converting-data"></a>Konvertieren von Daten

Optional können Sie [HL7 v2](../data-transformation/convert-data.md) und andere Formatdaten in FHIR konvertieren.

### <a name="using-fhir-data-in-power-bi-dashboard"></a>Verwenden von FHIR-Daten in Power BI Dashboard

Optional können Sie Power BI Dashboardberichte mit FHIR-Daten erstellen.

- [Power Query-Connector für FHIR](https://docs.microsoft.com/power-query/connectors/fhir/fhir)
- [IoT-Connector und Microsoft Power BI](../iot/iot-connector-power-bi.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die grundlegenden Schritte für die ersten Schritte mit dem FHIR-Dienst beschrieben. Informationen zum Bereitstellen des FHIR-Diensts im Arbeitsbereich finden Sie unter

>[!div class="nextstepaction"]
>[Bereitstellen eines FHIR-Diensts in Azure Healthcare-APIs](fhir-portal-quickstart.md)
