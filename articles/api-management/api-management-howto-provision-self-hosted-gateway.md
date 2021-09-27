---
title: Bereitstellen eines selbstgehosteten Gateways in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein selbstgehostetes Gateway in Azure API Management bereitstellen.
services: api-management
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: danlep
ms.openlocfilehash: 93e7feb2d1b91cb8d5fa58d52244c901e1eeaea2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598746"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Bereitstellen eines selbstgehosteten Gateways in Azure API Management

Das Bereitstellen einer Gatewayressource in Ihrer Azure API Management-Instanz ist eine Voraussetzung für die Bereitstellung eines selbstgehosteten Gateways. Dieser Artikel führt Sie schrittweise durch die Bereitstellung einer Gatewayressource in API Management.

## <a name="prerequisites"></a>Voraussetzungen

Absolvieren Sie den folgende Schnellstart: [Erstellen einer Azure API Management-Instanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Bereitstellen eines selbstgehosteten Gateways

1. Wählen Sie die **Gateways** unter **Bereitstellung und Infrastruktur** aus.
2. Klicken Sie auf **+ Hinzufügen**.
3. Geben Sie den **Namen** und die **Region** des Gateways ein.
> [!TIP]
> **Region** gibt den vorgesehenen Speicherort der Gatewayknoten an, die dieser Gatewayressource zugeordnet werden. Der Wert ist semantisch äquivalent mit einer ähnlichen Eigenschaft, die jeder Azure-Ressource zugeordnet wird, ihm kann aber ein beliebiger Zeichenfolgenwert zugewiesen werden.

4. Geben Sie optional eine **Beschreibung** der Gatewayressource ein.
5. Wählen Sie optional **+** unter **APIs** aus, um dieser Gatewayressource mindestens eine API zuzuordnen.
> [!IMPORTANT]
> Standardmäßig wird keine der vorhandenen APIs der neuen Gatewayressource zugeordnet. Daher führt der Versuch, sie über das neue Gateway aufzurufen, zu Antworten des Typs `404 Resource Not Found`.

6. Klicken Sie auf **Hinzufügen**.

Die Gatewayressource wurde nun in Ihrer API Management-Instanz bereitgestellt. Sie können mit der Bereitstellung des Gateways fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Azure API Management: Übersicht über das selbstgehostete Gateway](self-hosted-gateway-overview.md).
* Weitere Informationen zum [Bereitstellen eines selbstgehosteten Gateways für Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
- Hier erfahren Sie mehr zum [Bereitstellen eines selbstgehosteten Gateways in einem Azure Arc-fähigen Kubernetes-Cluster](how-to-deploy-self-hosted-gateway-azure-arc.md).
* Weitere Informationen zum [Bereitstellen eines selbstgehosteten Gateways für Docker](how-to-deploy-self-hosted-gateway-docker.md)
