---
title: Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal
description: Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fe594e7459f32d0639c39b9f694f31e7816ca118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558742"
---
# <a name="view-azure-arc-data-controller-resource-in-azure-portal"></a>Anzeigen einer Azure Arc-Datencontrollerressource im Azure-Portal

Um den Azure Arc-Datencontroller in Ihrem Azure-Portal anzuzeigen, muss einer der Nutzungs-/Metrik-/Protokolldaten aus Ihrem Kubernetes-Cluster exportiert und zu Azure hochgeladen werden. 

## <a name="direct-connected-mode"></a>Direkter Konnektivitätsmodus
Wenn der Azure Arc Data Controller im **direkten** Verbindungsmodus eingesetzt wird, werden die Nutzungsdaten automatisch nach Azure hochgeladen und die Kubernetes-Ressourcen werden in Azure projiziert.

## <a name="indirect-connected-mode"></a>Indirekter Konnektivitätsmodus
Im **indirekten** Verbindungsmodus müssen Sie mindestens eine der Nutzungsdaten, Metriken oder Protokolle exportieren und zu Azure hochladen, wie in [Nutzungsdaten, Metriken und Protokolle zu Azure hochladen](upload-metrics-and-logs-to-azure-monitor.md) beschrieben. Diese Aktion erstellt die entsprechenden Ressourcen in Azure.

## <a name="azure-portal"></a>Azure-Portal

Nachdem Sie Ihre ersten [Metriken oder Protokolle auf Azure hochgeladen](upload-metrics-and-logs-to-azure-monitor.md) oder [Nutzung hochgeladen](view-billing-data-in-azure.md) haben, können Sie den Azure Arc Data Controller und alle Azure Arc-aktivierten SQL Managed Instances oder Azure Arc-aktivierten Postgres Hyperscale Server-Ressourcen im Azure-Portal sehen.

Öffnen Sie das Azure-Portal unter der URL:  [https://portal.azure.com](https://portal.azure.com).

Suchen Sie mithilfe der Suchleiste den Namen Ihres Datencontrollers, und klicken Sie darauf.

