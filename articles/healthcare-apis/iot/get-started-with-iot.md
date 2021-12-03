---
title: Erste Schritte mit dem IoT-Connector – Azure Healthcare-APIs
description: In diesem Dokument werden die ersten Schritte mit dem IoT-Connector in Azure Healthcare-APIs beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: 65080a0d884d6388f713ea80f4447f4c8d9fba7b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273146"
---
# <a name="get-started-with-the-iot-connector"></a>Erste Schritte mit dem IoT-Connector

In diesem Artikel werden die grundlegenden Schritte für die ersten Schritte mit dem IoT-Connector in [Azure Healthcare-APIs beschrieben.](../healthcare-apis-overview.md) 

Als Voraussetzung benötigen Sie ein Azure-Abonnement und haben die richtigen Berechtigungen zum Erstellen von Azure-Ressourcengruppen und Bereitstellen von Azure-Ressourcen erhalten. Sie können alle Schritte ausführen oder einige überspringen, wenn Sie über eine vorhandene Umgebung verfügen. Außerdem können Sie alle Schritte kombinieren und in PowerShell-, Azure CLI- und REST-API-Skripts ausführen.

[![Erste Schritte mit DICOM](media/get-started-with-iot.png)](media/get-started-with-iot.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Erstellen eines Arbeitsbereichs in Ihrem Azure-Abonnement

Sie können einen Arbeitsbereich über die [Azure-Portal](../healthcare-apis-quickstart.md) oder mithilfe von PowerShell, Azure CLI und rest-API erstellen. Skripts finden Sie in den [Beispielen für Gesundheits-APIs.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

> [!NOTE]
> Die Anzahl der Arbeitsbereiche und die Anzahl der IoT-Connectorinstanzen, die Sie in jedem Azure-Abonnement erstellen können, ist begrenzt.

## <a name="create-the-fhir-service-and-an-event-hub"></a>Erstellen des FHIR-Diensts und eines Event Hubs

Der IoT-Connector funktioniert mit dem Azure Event Hub und dem FHIR-Dienst. Sie können einen neuen [FHIR-Dienst](../fhir/get-started-with-fhir.md) erstellen oder einen vorhandenen Dienst im gleichen oder in einem anderen Arbeitsbereich verwenden. Auf ähnliche Weise können Sie einen neuen [Event Hub](../../event-hubs/event-hubs-create.md) erstellen oder einen vorhandenen verwenden.


## <a name="create-a-iot-connector-in-the-workspace"></a>Erstellen eines IoT-Connectors im Arbeitsbereich

Sie können einen IoT-Connector über die [Azure-Portal](deploy-iot-connector-in-azure.md) oder mithilfe von PowerShell, Azure CLI oder der REST-API erstellen. Skripts finden Sie in den [Beispielen für Gesundheits-APIs.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

Optional können Sie einen [FHIR-Dienst](../fhir/fhir-portal-quickstart.md) und [einen DICOM-Dienst](../dicom/deploy-dicom-services-in-azure.md) im Arbeitsbereich erstellen.

## <a name="assign-roles-to-allow-iot-to-access-event-hub"></a>Zuweisen von Rollen, um IoT den Zugriff auf Event Hub zu ermöglichen

Der IoT-Connector ruft mithilfe der vom System verwalteten Identität standardmäßig Daten vom angegebenen Event Hub ab. Weitere Informationen zum Zuweisen der Rolle zum IoT-Connector über [Event Hub.](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#granting-iot-connector-access)

## <a name="assign-roles-to-allow-iot-to-access-fhir-service"></a>Zuweisen von Rollen, um IoT den Zugriff auf den FHIR-Dienst zu ermöglichen

Der IoT-Connector speichert die Daten mithilfe der vom System verwalteten Identität im FHIR-Speicher. Weitere Informationen zum Zuweisen der Rolle zum IoT-Connector über den [FHIR-Dienst](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#accessing-the-iot-connector-from-the-fhir-service)finden Sie hier.

## <a name="sending-data-to-the-iot-connector"></a>Senden von Daten an den IoT-Connector

Sie können Daten an den Event Hub senden, der dem IoT-Connector zugeordnet ist. Wenn im FHIR-Speicher keine Daten angezeigt werden, überprüfen Sie die Zuordnungen und Rollenzuweisungen für den IoT-Connector.

## <a name="iot-connector-mappings-data-flow-ml-power-bi-and-teams-notifications"></a>IoT-Connectorzuordnungen, Datenfluss, ML, Power BI und Teams Benachrichtigungen

Weitere Informationen zu IoT-Connectorzuordnungen, Datenflüssen, Machine Learning-Diensten, Power BI und Teams Benachrichtigungen finden Sie in der Dokumentation zum [IoT-Connector.](iot-connector-overview.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die grundlegenden Schritte für die ersten Schritte mit dem IoT-Connector beschrieben. Informationen zum Bereitstellen des IoT-Connectors im Arbeitsbereich finden Sie unter

>[!div class="nextstepaction"]
>[Bereitstellen des IoT-Connectors im Azure-Portal](deploy-iot-connector-in-azure.md)