---
title: Verwalten von Protokollen und Verschlüsselungen in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Protokolle (TLS) und Verschlüsselungen (DES) in Azure API Management verwalten.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2021
ms.author: danlep
ms.openlocfilehash: 5fc47f2238427c7875d2af29324e06d8b6487f1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001787"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Verwalten von Protokollen und Verschlüsselungen in Azure API Management

Azure API Management unterstützt mehrere Versionen des Transport Layer Security (TLS)-Protokolls für:
* Clientseitig
* Back-End-Seite
* Die 3DES-Verschlüsselung

Dieser Leitfaden zeigt Ihnen, wie Sie Protokoll- und Verschlüsselungskonfigurationen für eine Azure API Management-Instanz verwalten können.

![Verwalten von Protokollen und Verschlüsselungen in API Management](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Voraussetzungen

* Eine API Management-Instanz. [Erstellen Sie eine, falls nicht schon geschehen](get-started-create-service-instance.md).

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Verwalten von TLS-Protokollen und 3DES-Verschlüsselungen

1. Navigieren Sie im Azure-Portal zu Ihrer **API Management-Instanz**.
1. Scrollen Sie im Seitenmenü zum Abschnitt **Sicherheit.**
1. Wählen Sie im Abschnitt „Sicherheit“ die Option **Protokolle + Verschlüsselungen** aus.  
1. Aktivieren bzw. deaktivieren Sie die gewünschten Protokolle oder Verschlüsselungen.
1. Klicken Sie auf **Speichern**. Die Änderungen werden innerhalb einer Stunde angewendet.  

> [!NOTE]
> Einige Protokolle oder Verschlüsselungssuiten (wie backend-seitiges TLS 1.2) können nicht über das Azure-Portal aktiviert oder deaktiviert werden. Stattdessen müssen Sie den REST-Aufruf anwenden. Verwenden Sie die `properties.customProperties`Struktur aus dem Artikel [API-Verwaltungsdienst REST-API erstellen/aktualisieren](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [TLS](/dotnet/framework/network-programming/tls).
* Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
