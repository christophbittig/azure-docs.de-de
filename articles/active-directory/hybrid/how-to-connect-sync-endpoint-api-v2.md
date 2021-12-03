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
ms.openlocfilehash: 8560ea04e57553f556122cfa1986cabb03442a4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399027"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect-Synchronisierung: V2-Endpunkt-API 
Microsoft hat einen neuen Endpunkt (API) für Azure AD Connect bereitgestellt, mit dem die Leistung der Synchronisierungsdienstvorgänge für Azure Active Directory verbessert wird. Durch die Verwendung des neuen V2-Endpunkts werden Sie spürbare Leistungssteigerungen beim Export und Import in Azure AD erleben. Dieser neue Endpunkt unterstützt:
    
 - Synchronisierung von Gruppen mit bis zu 250k Mitgliedern.
 - Leistungssteigerung beim Export und Import in Azure AD.
 
> [!NOTE]
> Der neue Endpunkt verfügt derzeit nicht über eine konfigurierte Beschränkung der Gruppengröße für Microsoft 365-Gruppen, die zurückgeschrieben werden. Dies hat möglicherweise Auswirkungen auf Ihr Active Directory und Synchronisierungszykluslatenzen. Es wird empfohlen, die Gruppengröße inkrementell zu erhöhen.  

>[!NOTE]
> Die Azure AD Connect sync V2 Endpunkt-API ist allgemein verfügbar, kann aber derzeit nur in diesen Azure-Umgebungen verwendet werden:
> - Azure Commercial
> - Azure China
> - Azure US Government. Wird nicht in Azure Deutschland zur Verfügung gestellt

## <a name="prerequisites"></a>Voraussetzungen  
Um den neuen V2-Endpunkt zu verwenden, müssen Sie Azure AD Connect v2.0 verwenden. Wenn Sie AADConnect V2.0 bereitstellen, wird der V2-Endpunkt automatisch aktiviert.
Es gibt ein bekanntes Problem, bei dem beim Upgrade auf den neuesten 1.6-Build das Gruppenmitgliedschaftslimit auf 50.000 zurückgesetzt wird. Wenn ein Server auf AADConnect 1.6 aktualisiert wird, sollte der Kunde die Regeländerungen, die er beim ersten Erhöhen des Gruppenmitgliedschaftslimits auf 250.000 angewendet hat, erneut vornehmen, bevor er die Synchronisierung für den Server aktiviert. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen  
 
**Wann wird der neue Endpunkt zum Standard für Upgrades und Neuinstallationen?**   Der V2-Endpunkt ist die Standardeinstellung für AADConnect V2.0, und wir empfehlen Kunden, ein Upgrade auf AADConnect V2.0 durchführen, um die Vorteile dieses Endpunkts zu nutzen.
Es gibt ein Problem, bei dem Kunden, die den V2-Endpunkt mit einer älteren Version betreiben und versuchen, auf eine neuere Version V1.6 zu aktualisieren, feststellen, dass die 50K-Beschränkung der Gruppenmitgliedschaft wieder eingeführt wird. Wenn ein Server auf AADConnect 1.6 aktualisiert wird, sollte der Kunde die Regeländerungen, die er beim ersten Erhöhen des Gruppenmitgliedschaftslimits auf 250.000 angewendet hat, erneut vornehmen, bevor er die Synchronisierung für den Server aktiviert. 

## <a name="next-steps"></a>Nächste Schritte

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
