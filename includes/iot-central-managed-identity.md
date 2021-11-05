---
title: Datei einfügen
description: Datei einfügen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0a2e035cdc5118cc7d952e4046e9093f95094585
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070394"
---
So konfigurieren Sie die verwaltete Identität, die es Ihrer IoT Central-Anwendung ermöglicht, Daten sicher in Ihren Event Hub zu exportieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer IoT Central-Anwendung.

    > [!TIP]
    > Standardmäßig werden IoT Central-Anwendungen in der **IOTC-Ressourcengruppe** in Ihrem Abonnement erstellt.

1. Wählen Sie **Identität** aus. Ändern Sie dann auf der Seite **Systemseitig zugewiesen** den Status in **Ein**, und wählen Sie dann **Speichern** aus.

1. Nach einigen Sekunden ist die systemseitig zugewiesene verwaltete Identität für Ihre IoT Central-Anwendung aktiviert, und Sie können **Azure-Rollenzuweisungen** auswählen:

    :::image type="content" source="media/iot-central-managed-identity/azure-role-assignments.png" alt-text="Screenshot der Identitätsseite für die IoT Central-Anwendung im Azure-Portal":::

1. Wählen Sie auf der Seite **Azure-Rollenzuweisungen** die Option **Rollenzuweisung hinzufügen** aus.
