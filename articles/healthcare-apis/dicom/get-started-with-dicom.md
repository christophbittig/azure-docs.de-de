---
title: Erste Schritte mit dem DICOM-Dienst – Azure Healthcare-APIs
description: In diesem Dokument werden die ersten Schritte mit dem DICOM-Dienst in Azure Healthcare-APIs beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: bc3c691d7a990e47b6afe624854d9085169fe0b2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273160"
---
# <a name="get-started-with-the-dicom-service"></a>Erste Schritte mit dem DICOM-Dienst

In diesem Artikel werden die grundlegenden Schritte für die ersten Schritte mit dem DICOM-Dienst in [Azure Healthcare-APIs beschrieben.](../healthcare-apis-overview.md) 

Als Voraussetzung benötigen Sie ein Azure-Abonnement und haben die richtigen Berechtigungen zum Erstellen von Azure-Ressourcengruppen und Bereitstellen von Azure-Ressourcen erhalten. Sie können alle Schritte ausführen oder einige überspringen, wenn Sie über eine vorhandene Umgebung verfügen. Außerdem können Sie alle Schritte kombinieren und in PowerShell-, Azure CLI- und REST-API-Skripts ausführen.

[![Erste Schritte mit DICOM](media/get-started-with-dicom.png)](media/get-started-with-dicom.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Erstellen eines Arbeitsbereichs in Ihrem Azure-Abonnement

Sie können einen Arbeitsbereich über die [Azure-Portal](../healthcare-apis-quickstart.md) erstellen oder PowerShell, Azure CLI und die REST-API verwenden. Skripts finden Sie in den [Beispielen für Gesundheits-APIs.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

> [!NOTE]
> Es gibt Grenzwerte für die Anzahl der Arbeitsbereiche und die Anzahl der DICOM-Dienstinstanzen, die Sie in jedem Azure-Abonnement erstellen können.

## <a name="create-a-dicom-service-in-the-workspace"></a>Erstellen eines DICOM-Diensts im Arbeitsbereich

Sie können eine DICOM-Dienstinstanz über die [Azure-Portal](deploy-dicom-services-in-azure.md) oder mit powerShell, Azure CLI und der REST-API erstellen. Skripts finden Sie in den [Beispielen für Gesundheits-APIs.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

Optional können Sie einen [FHIR-Dienst](../fhir/fhir-portal-quickstart.md) und [einen IoT-Connector](../iot/deploy-iot-connector-in-azure.md) im Arbeitsbereich erstellen.

## <a name="access-the-dicom-service"></a>Zugreifen auf den DICOM-Dienst

Der DICOM-Dienst wird durch Azure Active Directory (Azure AD) geschützt, die nicht deaktiviert werden können. Für den Zugriff auf die Dienst-API müssen Sie eine Clientanwendung erstellen, die in Azure AD auch als Dienstprinzipal bezeichnet wird, und ihr die richtigen Berechtigungen erteilen.

### <a name="register-a-client-application"></a>Registrieren einer Clientanwendung

Sie können eine Clientanwendung über die [Azure-Portal](../register-application.md)oder mithilfe von PowerShell und Azure CLI Skripts erstellen oder registrieren. Diese Clientanwendung kann für eine oder mehrere DICOM-Dienstinstanzen verwendet werden. Sie kann auch für andere Dienste in Azure Healthcare-APIs verwendet werden.

Wenn die Clientanwendung mit einem Zertifikat oder einem geheimen Clientschlüssel erstellt wird, stellen Sie sicher, dass Sie das Zertifikat oder den geheimen Clientschlüssel vor dem Ablauf erneuern und die Clientanmeldeinformationen in Ihren Anwendungen ersetzen.

Sie können eine Clientanwendung löschen. Stellen Sie zuvor sicher, dass sie nicht in Produktions-, Entwicklungs-, Test- oder Qualitätssicherungsumgebungen (Quality Assurance, QA) verwendet wird.

### <a name="grant-access-permissions"></a>Erteilen von Zugriffsberechtigungen

Sie können Zugriffsberechtigungen erteilen oder Rollen über die [Azure-Portal](../configure-azure-rbac.md)oder mithilfe von PowerShell und Azure CLI Skripts zuweisen.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>Ausführen von CRUD-Transaktionen (Create, Read, Update, Delete)

Sie können CRUD-Transaktionen (Create, Read (Search), Update and Delete (CRUD) für den DICOM-Dienst in Ihren Anwendungen oder mithilfe von Tools wie Postman, REST-Client, cURL und Python ausführen. Da der DICOM-Dienst standardmäßig geschützt ist, müssen Sie ein Zugriffstoken abrufen und es in Ihre Transaktionsanforderung einschließen.

#### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

Sie können ein Azure AD Zugriffstoken mit powerShell, Azure CLI, der REST-CLI oder dem .NET SDK abrufen.  Weitere Informationen finden Sie unter [Abrufen des Zugriffstokens.](../get-access-token.md)

#### <a name="access-using-existing-tools"></a>Zugriff mit vorhandenen Tools

- [Postman](../use-postman.md)
- [REST-Client](../using-rest-client.md)
- [.NET C #](dicomweb-standard-apis-c-sharp.md)
- [cURL](dicomweb-standard-apis-curl.md)
- [Python](dicomweb-standard-apis-python.md)

### <a name="dicomweb-standard-apis-and-change-feed"></a>DICOMweb-Standard-APIs und Änderungsfeed

Weitere Informationen zu DICOMweb-Standard-APIs und zum Änderungsfeed finden Sie in der Dokumentation zum [DICOM-Dienst.](dicom-services-overview.md)

#### <a name="dicomcast"></a>DICOMCast

Sie können das Open Source [DICOMCast-Projekt](https://github.com/microsoft/dicom-server/tree/main/converter/dicom-cast) verwenden, um mit FHIR-Daten zu arbeiten. In Zukunft wird diese Funktion im verwalteten Dienst verfügbar sein.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die grundlegenden Schritte für die ersten Schritte mit dem DICOM-Dienst beschrieben. Informationen zum Bereitstellen des DICOM-Diensts im Arbeitsbereich finden Sie unter

>[!div class="nextstepaction"]
>[Bereitstellen des DICOM-Diensts mithilfe der Azure-Portal](deploy-dicom-services-in-azure.md)