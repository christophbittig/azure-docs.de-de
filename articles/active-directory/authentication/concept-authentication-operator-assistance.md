---
title: Operatorunterstützung in Azure Active Directory
description: Erfahren Sie mehr über den Ablauf der Operatorunterstützungsfunktion in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: justinha
author: rckyplln
manager: daveba
ms.reviewer: ripull
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c00fb3c68955a888bbc383fe59aef8f7ed75835
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270651"
---
# <a name="how-to-enable-and-disable-operator-assistance"></a>Aktivieren und Deaktivieren der Operatorunterstützung

Die Operatorunterstützung ist eine Funktion in Azure AD, mit der anstelle der automatischen Durchwahl ein Operator Telefonanrufe manuell weiterleitet. Wenn diese Einstellung aktiviert ist, wird die geschäftliche Telefonnummer angewählt, und bei Annahme des Anrufs fordert das System den Operator auf, den Anruf an eine bestimmte Nebenstelle weiterzuleiten.

Die Operatorunterstützung kann für einen gesamten Mandanten oder nur für einzelne Benutzer aktiviert werden. Wenn die Einstellung auf **Ein** festgelegt ist, wird die Operatorunterstützung für den gesamten Mandanten aktiviert. Wenn Sie **Telefonanruf** als Standardmethode auswählen und eine Durchwahl als Teil Ihrer geschäftlichen Telefonnummer angegeben haben (erkennbar am Trennzeichen **x**), kann ein Operator den Telefonanruf manuell weiterleiten.

Angenommen, ein Kunde in den USA hat die geschäftliche Telefonnummer 425-555-1234x5678. Wenn die Operatorunterstützung aktiviert ist, wählt das System 425-555-1234. Wird der Anruf angenommen, wird der Kunde (auch als Operator bezeichnet) aufgefordert, den Anruf an die Nebenstelle 5678 weiterzuleiten. Nachdem der Anruf weitergeleitet und angenommen wurde, fordert das System zur Eingabe der normalen mehrstufigen Authentifizierung (MFA) auf und wartet auf Genehmigung.

Ist die Einstellung auf **Aus** festgelegt, wählt das System automatisch Nebenstellen an, die als Teil der Telefonnummer angegeben sind. Ihr Administrator kann weiterhin einzelne Benutzer angeben, für die Operatorunterstützung aktiviert werden soll, indem er der Nebenstelle ein „@“ vorangestellt. 425-555-1234x@5678 würde beispielsweise angeben, dass die Operatorunterstützung verwendet werden soll, obwohl die Einstellung auf **Aus** festgelegt ist.

Sie können den Status dieser Funktion in Ihrem eigenen Mandanten überprüfen, indem Sie zum [Azure AD-Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade) navigieren und dann im linken Bereich auf **Sicherheit** > **MFA** > **Einstellungen für Telefonanruf** klicken. Überprüfen Sie **Operator muss Durchwahlen übertragen**, um festzustellen, ob die Einstellung auf **Aus** oder **Ein** festgelegt ist. 

![Screenshot: Einstellungen für die Operatorunterstützung](./media/concept-authentication-operator-assistance/settings.png)

Mithilfe der folgenden Anleitung können Sie die Zuverlässigkeit und Sicherheit verbessern und eine reibungslose MFA-Erfahrung erstellen:

- Sie haben eine [direkte Telefonnummer (ohne Durchwahl)](https://aka.ms/mfasetup) oder eine [andere Methode registriert](concept-authentication-methods.md), die für Multi-Factor Authentication oder die Self-Service-Kennwortzurücksetzung verwendet werden soll (sofern aktiviert). 
- Ihre Administratoren haben im Namen des Benutzers eine direkte Telefonnummer (ohne Durchwahl) registriert, die für [Multi-Factor Authentication](howto-mfa-userdevicesettings.md#add-authentication-methods-for-a-user) oder die [Self-Service-Kennwortzurücksetzung](tutorial-enable-sspr.md) verwendet werden soll (sofern aktiviert). 
- Das Telefonsystem unterstützt die automatische Telefonzentrale (Auto-Attendant-Funktion). 
 
