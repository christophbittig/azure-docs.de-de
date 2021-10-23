---
title: Akzeptiert meine Azure AD-Anmeldeseite Microsoft-Konten? | Microsoft-Dokumentation
description: Informationen dazu, wie Meldungen auf dem Bildschirm das Nachschlagen des Benutzernamens bei der Anmeldung widerspiegeln
services: active-directory
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93bd814075bf4fc2603bc4dd55caec1b7e5543f5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985483"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Anmeldeoptionen für Microsoft-Konten in Azure Active Directory

Die Anmeldeseite von Microsoft 365 für Azure Active Directory (Azure AD) unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten. Je nach Situation des Benutzers werden allerdings möglicherweise nur eine oder auch beide Kontoarten unterstützt. Die Azure AD-Anmeldeseite unterstützt beispielsweise folgende Kontoarten:

* Apps, bei denen die Anmeldung über beide Kontotypen möglich ist
* Organisationen, die Gäste akzeptieren

## <a name="identification"></a>Identifikation
Sie können feststellen, ob die Anmeldeseite Ihrer Organisation Microsoft-Konten unterstützt, indem Sie sich den Hinweistext im Feld für den Benutzernamen ansehen. Wenn im Hinweistext „E-Mail, Telefon oder Skype“ angezeigt wird, unterstützt die Anmeldeseite Microsoft-Konten.

![Unterschied zwischen Konten für Anmeldeseiten](./media/signin-account-support/ui-prompt.png)

[Zusätzliche Anmeldeoptionen können nur für persönliche Microsoft-Konten](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ), jedoch nicht für die Anmeldung bei Geschäfts-, Schul- oder Unikonten verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Brandings Ihrer Anmeldung](../fundamentals/add-custom-domain.md)