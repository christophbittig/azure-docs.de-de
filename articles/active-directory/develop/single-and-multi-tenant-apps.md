---
title: Einzel- und mehrinstanzenfähige Apps in Azure AD
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Features und Unterschiede zwischen einzel- und mehrinstanzenfähige Apps in Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/13/2021
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 9a21d6f4a59709c74a02dff2334402510e156918
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987725"
---
# <a name="tenancy-in-azure-active-directory"></a>Mandanten in Azure Active Directory

Azure Active Directory (Azure AD) organisiert Objekten wie Benutzer und Apps in Gruppen, die als _Mandanten_ bezeichnet werden. Mandanten erlauben einem Administrator, Richtlinien für Benutzer innerhalb des Unternehmens und die Apps festzulegen, die dem Unternehmen gehören, um die Sicherheits- und Betriebsrichtlinien einzuhalten.

## <a name="who-can-sign-in-to-your-app"></a>Wer kann sich bei Ihrer App anmelden?

Wenn es um die Entwicklung von Apps geht, können Entwickler bei der App-Registrierung im [Azure-Portal](https://portal.azure.com) auswählen, ob ihre App als einzel- oder mehrinstanzenfähige App konfiguriert werden soll.

- Einzelinstanzenfähige Apps sind nur in dem Mandanten verfügbar, in dem sie registriert wurden. Dieser wird auch als Basismandant bezeichnet.
- Mehrinstanzenfähige Apps sind für Benutzer in ihrem Basismandanten und in anderen Mandanten verfügbar.

Im Azure-Portal können Sie Ihre App als einzel- oder mehrinstanzenfähig konfigurieren, indem Sie die Zielgruppe wie folgt festlegen.

| Zielgruppe                                                                                              | Einzel-/mehrinstanzenfähig | Wer kann sich anmelden?                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nur Konten in diesem Verzeichnis                                                                       | Einzelner Mandant       | Alle Benutzer- und Gastkonten in Ihrem Verzeichnis können Ihre Anwendung oder API verwenden.<br>_Verwenden Sie diese Option, wenn Ihre Zielgruppe sich innerhalb Ihrer Organisation befindet._                                                                                                                                                         |
| Konten in einem beliebigen Azure AD-Verzeichnis                                                                    | Mehrinstanzenfähig        | Alle Benutzer und Gäste mit einem Geschäfts-, Schul- oder Unikonto von Microsoft können Ihre Anwendung oder API verwenden. Dazu gehören auch Bildungseinrichtungen und Unternehmen, die Microsoft 365 verwenden.<br>_Verwenden Sie diese Option, wenn Ihre Zielgruppe Kunden aus dem Unternehmens- oder Bildungsbereich sind._                                                                    |
| Konten in beliebigen Azure AD-Verzeichnissen und persönliche Microsoft-Konten (z.B. Skype, Xbox, Outlook.com) | Mehrinstanzenfähig        | Alle Benutzer mit einem Geschäfts-, Schul- oder Unikonto bzw. einem persönlichen Microsoft-Konto können Ihre Anwendung oder API verwenden. Dazu gehören Bildungseinrichtungen und Unternehmen, die Microsoft 365 nutzen, sowie persönliche Konten, mit denen die Anmeldung bei Diensten wie Xbox und Skype erfolgt.<br>_Verwenden Sie diese Option, um die breiteste Auswahl an Microsoft-Konten als Zielgruppe zu verwenden._ |

## <a name="best-practices-for-multi-tenant-apps"></a>Bewährte Methoden für mehrinstanzenfähige Apps

Das Erstellen guter mehrinstanzenfähiger Apps kann aufgrund der Vielzahl unterschiedlicher Richtlinien, die IT-Administratoren in ihren Mandanten festlegen können, eine Herausforderung darstellen. Wenn Sie eine mehrinstanzenfähige App erstellen möchten, wenden Sie die folgenden bewährten Methoden an:

- Testen Sie Ihre App in einem Mandanten, für den [Richtlinien für bedingten Zugriff](../azuread-dev/conditional-access-dev-guide.md) konfiguriert sind.
- Befolgen Sie das Prinzip des geringstmöglichen Benutzerzugriffs, um sicherzustellen, dass Ihre App nur Berechtigungen anfordert, die sie tatsächlich benötigt.
- Geben Sie geeignete Namen und Beschreibungen für alle Berechtigungen an, die Sie als Teil Ihrer App bereitstellen. Auf diese Weise können sich Benutzer und Administratoren besser informieren, welchen Berechtigungen sie zustimmen, wenn sie versuchen, die APIs Ihrer App zu verwenden. Weitere Informationen finden Sie im Abschnitt zu den bewährten Methoden im [Berechtigungsleitfaden](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Mandanten in Azure AD finden Sie unter:

- [Konvertieren einer App in eine mehrinstanzenfähige App](howto-convert-app-to-be-multi-tenant.md)
- [Aktivieren von Anmeldungen von mehreren Mandanten](howto-convert-app-to-be-multi-tenant.md)
