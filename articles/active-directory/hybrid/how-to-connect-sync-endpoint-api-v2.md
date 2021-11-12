---
title: Azure AD Connect-Synchronisierungsendpunkt V2 | Microsoft-Dokumentation
description: In diesem Dokument werden Aktualisierungen der Synchronisierungsendpunkt-API V2 für Azure AD Connect behandelt.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964829c0d775bd33ea0c44386820ae2ba921ce1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466046"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect-Synchronisierung: V2-Endpunkt-API 
Microsoft hat einen neuen Endpunkt (API) für Azure AD Connect bereitgestellt, mit dem die Leistung der Synchronisierungsdienstvorgänge für Azure Active Directory verbessert wird. Wenn Sie den neuen V2-Endpunkt verwenden, treten beim Exportieren aus und Importieren in Azure AD spürbare Leistungssteigerungen auf. Dieser neue Endpunkt unterstützt Folgendes:
    
 - Synchronisieren von Gruppen mit bis zu 250.000 Mitgliedern
 - Leistungssteigerungen beim Exportieren aus und Importieren in Azure AD
 
> [!NOTE]
> Der neue Endpunkt verfügt derzeit nicht über eine konfigurierte Beschränkung der Gruppengröße für Microsoft 365-Gruppen, die zurückgeschrieben werden. Dies hat möglicherweise Auswirkungen auf Ihr Active Directory und Synchronisierungszykluslatenzen. Es wird empfohlen, die Gruppengröße inkrementell zu erhöhen.  

>[!NOTE]
> Die Azure AD Connect sync V2 Endpunkt-API ist allgemein verfügbar, kann aber derzeit nur in diesen Azure-Umgebungen verwendet werden:
> - Azure Commercial
> - Azure China
> - Azure US Government. Wird nicht in Azure Deutschland zur Verfügung gestellt

## <a name="prerequisites"></a>Voraussetzungen  
Um den neuen V2-Endpunkt zu verwenden, müssen Sie Azure AD Connect v2.0 verwenden. Wenn Sie AADConnect V2.0 bereitstellen, wird der V2-Endpunkt automatisch aktiviert.
Beachten Sie, dass die Unterstützung für den V2-Endpunkt für V1.x-Versionen nicht mehr verfügbar ist. Wenn Sie Gruppen mit mehr als 50.000 Mitgliedern synchronisieren möchten, müssen Sie ein Upgrade auf Azure AD Connect V2.0 durchführen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen  
 
**Wann wird der neue Endpunkt zum Standard für Upgrades und Neuinstallationen?**   Der V2-Endpunkt ist die Standardeinstellung für AADConnect V2.0 und wird für AADConnect V1.x nicht unterstützt

## <a name="next-steps"></a>Nächste Schritte

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
