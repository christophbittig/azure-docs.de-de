---
title: Exportieren von APIs aus Azure API Management in Microsoft Power Platform | Microsoft-Dokumentation
description: Sie erfahren, wie Sie eine API aus API Management als benutzerdefinierten Connector für Power Apps und Power Automate in Microsoft Power Platform exportieren.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 07/27/2021
ms.author: danlep
ms.openlocfilehash: fb27e1a96152885446da2e77e0901680affe3569
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494841"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exportieren von APIs aus Azure API Management in Power Platform 

Entwickler ohne Programmiererfahrung, die Microsoft [Power Platform](https://powerplatform.microsoft.com) nutzen, benötigen häufig Zugang zu den geschäftlichen Funktionen, die von professionellen Entwicklern entwickelt und in Azure bereitgestellt werden. [Azure API Management](https://aka.ms/apimrocks) ermöglicht professionellen Entwicklern die Veröffentlichung ihres Back-End-Diensts als APIs sowie das einfache Exportieren dieser APIs in Power Platform ([Power Apps](/powerapps/powerapps-overview) und [Power Automate](/power-automate/getting-started)) als benutzerdefinierte Connectors, die Entwickler ohne Programmiererfahrung suchen und nutzen können. 

In diesem Artikel werden die Schritte im Azure-Portal zum Erstellen eines benutzerdefinierten Power Platform Connectors für eine API in API Management beschrieben. Mit dieser Funktion können Entwickler mithilfe von Microsoft Power Platform Apps erstellen und verteilen, die auf internen und externen APIs basieren, die von API Management verwaltet werden.

## <a name="prerequisites"></a>Voraussetzungen

+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Vergewissern Sie sich, dass Ihre API Management-Instanz die API enthält, die Sie in Power Platform exportieren möchten.
+ Stellen Sie sicher, dass Sie über eine Power Apps- oder Power Automate-[Umgebung](/powerapps/powerapps-overview#power-apps-for-admins) verfügen. 

## <a name="create-a-custom-connector-to-an-api"></a>Erstellen eines benutzerdefinierten Connectors für eine API

1. Navigieren Sie zu Ihrem API Management-Dienst im Azure-Portal.
1. Wählen Sie im Menü unter **APIs** die Option **Power Platform** aus.
1. Wählen Sie **Einen Connector erstellen** aus.
1. Gehen Sie im Fenster **Einen Connector erstellen** folgendermaßen vor:
    1. Wählen Sie eine API aus, die auf Power Platform veröffentlicht werden soll.
    1. Wählen Sie eine Power Platform-Umgebung aus, in der die API veröffentlicht werden soll. 
    1. Geben Sie einen Anzeigenamen an, der als Name des benutzerdefinierten Connectors verwendet wird.  
    1. Optional: Wenn die API [durch einen OAuth 2.0-Server geschützt wird](api-management-howto-protect-backend-with-aad.md), müssen Sie darüber hinaus weitere Details angeben, u. a. **Client-ID**, **Clientgeheimnis**, **Autorisierungs-URL**, **Token-URL** und **Aktualisierungs-URL**.  
1. Klicken Sie auf **Erstellen**. 

    :::image type="content" source="media/export-api-power-platform/create-custom-connector.png" alt-text="Erstellen eines benutzerdefinierten Connectors für die API in API Management":::

Navigieren Sie nach dem Erstellen des Connectors zu Ihrer [Power Apps](https://make.powerapps.com)- oder [Power Automate](https://flow.microsoft.com)-Umgebung. Die API wird unter **Daten > benutzerdefinierte Connectors** angezeigt.

:::image type="content" source="media/export-api-power-platform/custom-connector-power-app.png" alt-text="Benutzerdefinierter Connector in Power Platform":::

> [!NOTE]
> Zum Aufrufen der API über die PowerApps-Testkonsole müssen Sie die URL „https://flow.microsoft.com“ als Ursprung der [CORS-Richtlinie](api-management-cross-domain-policies.md#CORS) in Ihrer API Management-Instanz hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über [Power Platform](https://powerplatform.microsoft.com/).
* [Weitere Informationen zum Erstellen und Verwenden von benutzerdefinierten Connectors](/connectors/custom-connectors/)
* [Machen Sie sich anhand der Tutorials ausführlicher mit allgemeinen Aufgaben in API Management vertraut.](./import-and-publish.md)
