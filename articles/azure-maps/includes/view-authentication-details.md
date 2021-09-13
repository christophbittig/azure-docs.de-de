---
title: Anzeigen von Authentifizierungsdetails für Microsoft Azure Maps
description: Verwenden Sie das Azure-Portal, um Authentifizierungsdetails für Azure Maps anzuzeigen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: ccc0e8c43001f161648032be7fbb83aec43cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803457"
---
So zeigen Sie Ihre Azure Maps-Kontoauthentifizierungsdetails im Azure-Portal an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zum Menü des Azure-Portals. Wählen Sie **Alle Ressourcen** und dann Ihr Azure Maps-Konto aus.

3. Wählen Sie im linken Bereich unter **Einstellungen** die Option **Authentifizierung** aus.

    :::image type="content" border="true" source="../media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Authentifizierungsdetails":::

Bei der Erstellung des Azure Maps-Kontos werden drei Werte erstellt. Sie werden verwendet, um zwei Authentifizierungsarten in Azure Maps zu unterstützen:
- **Azure Active Directory-Authentifizierung:** Die Client-ID (`Client ID`) stellt das Konto dar, das für REST-API-Anforderungen verwendet werden soll. Der Wert der Client-ID (`Client ID`) sollte in der Anwendungskonfiguration gespeichert und später abgerufen werden, bevor Azure Maps-HTTP-Anforderungen mit Azure AD-Authentifizierung übermittelt werden.
- **Authentifizierung über gemeinsam verwendete Schlüssel:** Der Primärschlüssel (`Primary Key`) und der Sekundärschlüssel (`Secondary Key`) werden als Abonnementschlüssel für die Authentifizierung über gemeinsam verwendete Schlüssel verwendet. Bei der Authentifizierung über gemeinsam verwendete Schlüssel wird der durch das Azure Maps-Konto generierte Schlüssel bei jeder Anforderung an Azure Maps übergeben. Es wird empfohlen, die Schlüssel regelmäßig neu zu generieren. Um aktuelle Verbindungen während der Neugenerierung aufrechtzuerhalten, werden zwei Schlüssel bereitgestellt. Ein Schlüssel kann verwendet werden, während der andere neu generiert wird. Bei der Neugenerierung Ihrer Schlüssel müssen Sie alle Anwendungen, die auf dieses Konto zugreifen, so aktualisieren, dass die neuen Schlüssel verwendet werden. Weitere Informationen finden Sie unter [Authentifizierung mit Azure Maps](../azure-maps-authentication.md).
