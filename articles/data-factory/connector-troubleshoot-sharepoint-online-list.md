---
title: Problembehandlung für den SharePoint Online-Listenconnector
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit dem SharePoint Online-Listenconnector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 2407521ef6abcea5a27f7810af527e47a2739410
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390523"
---
# <a name="troubleshoot-the-sharepoint-online-list-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den SharePoint Online-Listenconnector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem SharePoint Online-Listenconnector in Azure Data Factory und Azure Synapse.

## <a name="error-code-sharepointonlineauthfailed"></a>Fehlercode: SharePointOnlineAuthFailed

- **Meldung**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Ursache:** Die Dienstprinzipal-ID und der Schlüssel sind möglicherweise nicht richtig festgelegt.

- **Empfehlung**:  Überprüfen Sie, ob die registrierte Anwendung (Dienstprinzipal-ID) und der Schlüssel richtig festgelegt sind.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
