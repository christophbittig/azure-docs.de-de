---
title: Grundlegende Informationsbezeichner im Azure AD-Anmeldeprotokoll | Microsoft-Dokumentation
description: Erfahren Sie, wofür die grundlegenden Informationsbezeichner verwendet werden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca1ea9524a10ac46a33f6430228d8a45ee66f28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368062"
---
# <a name="basic-info-identifiers-in-the-azure-ad-sign-in-log"></a>Grundlegende Informationsbezeichner im Azure AD-Anmeldeprotokoll

Azure AD protokolliert aus Compliancegründen alle Anmeldungen bei einem Azure-Mandanten. Als IT-Administrator*in müssen Sie wissen, was die Werte in einem Anmeldeprotokoll bedeuten, damit Sie die Protokollwerte richtig interpretieren können.
In diesem Artikel werden die Bezeichner auf der Registerkarte „Grundlegende Informationen“ des Anmeldeprotokolls erläutert.

## <a name="unique-identifiers"></a>Eindeutige Bezeichner 

In Azure AD weist jeder Ressourcenzugriff drei relevante Aspekte auf:

- **Wer:** die Identität (Benutzer*in), die die Anmeldung ausführt 
- **Wie:** der Client (Anwendung), der für den Zugriff verwendet wird  
- **Was:** das Ziel (Ressource), auf das von der Identität zugegriffen wird


Jeder dieser Informationen ist ein eindeutiger Bezeichner (ID) zugeordnet. Im Folgenden finden Sie ein Beispiel für die Verwendung der Microsoft Azure-Dienstverwaltungs-API für den Zugriff auf das Azure-Portal.

![Öffnen von Überwachungsprotokollen](./media/reference-basic-info-identifiers/sign-in-details-basic-info.png)

## <a name="tenant-identifiers"></a>Mandantenbezeichner

Das Anmeldeprotokoll verfolgt zwei Mandantenbezeichner:

- **Basismandant:** der Mandant, der die Benutzeridentität besitzt 
- **Ressourcenmandant:** der Mandant, der die Ressource (Ziel) besitzt

Diese Bezeichner sind in mandantenübergreifenden Szenarien wichtig. Um beispielsweise herauszufinden, wie Benutzer*innen außerhalb Ihres Mandanten auf Ihre Ressourcen zugreifen, wählen Sie alle Einträge aus, bei denen der Basismandant nicht mit dem Ressourcenmandanten übereinstimmt.

## <a name="request-id"></a>Anfrage-ID

Die Anforderungs-ID ist ein Bezeichner, der einem ausgestellten Token entspricht. Wenn Sie nach Anmeldungen suchen, die mit einem bestimmten Token durchgeführt wurden, müssen Sie zuerst die Anforderungs-ID aus dem Token extrahieren.


## <a name="correlation-id"></a>Korrelations-ID

Die Korrelations-ID ist ein Bezeichner, der mit Anmeldungen aus derselben Anmeldesitzung korreliert. Der Bezeichner wurde zur Vereinfachung implementiert. Die Genauigkeit ist nicht garantiert, da der Wert auf Parametern basiert, die von einem Client übergeben werden. 




## <a name="next-steps"></a>Nächste Schritte

* [Anmeldeprotokolle in Azure Active Directory](concept-sign-ins.md)
* [Was ist die Anmeldediagnose für Azure AD?](overview-sign-in-diagnostics.md)
