---
title: Migrieren zu MSAL.NET und Microsoft.Identity.Web
titleSuffix: Microsoft identity platform
description: Erfahren Sie,warum und wie Sie von der Azure AD Authentication Library for .NET (ADAL.NET) zu Microsoft Authentication Library for .NET (MSAL.NET) oder Microsoft.Identity.Web migrieren können.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5a3dd6265c9fbefb85cf72b80473538983c682b7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214701"
---
# <a name="migrating-applications-to-msalnet-or-microsoftidentityweb"></a>Migrieren von Anwendungen zu MSAL.NET oder Microsoft.Identity.Web

## <a name="why-migrate-to-msalnet-or-microsoftidentityweb"></a>Gründe für die Migration zu MSAL.NET oder Microsoft.Identity.Web

Sowohl die Microsoft Authentication Library für .NET (MSAL.NET) als auch die Azure AD Authentication Library für .NET (ADAL.NET) werden zum Authentifizieren von Azure AD-Entitäten und zum Anfordern von Token von Azure AD verwendet. Bis jetzt haben die meisten Entwickler mithilfe der Azure AD Authentication Library (ADAL) Token von der Azure AD für Entwickler-Plattform (v1.0) angefordert. Diese Token werden verwendet, um Azure AD-Identitäten (Arbeits- und Schulkonten) zu authentifizieren. 

MSAL bietet Vorteile gegenüber ADAL. Einige dieser Vorteile sind:

- Sie können eine größere Palette von Microsoft-Identitäten authentifizieren: Arbeits- oder Schulkonten, persönliche Microsoft-Konten und soziale oder lokale Konten mit Azure AD B2C.
- Ihre Benutzer erhalten die beste Single-Sign-On-Erfahrung.
- Ihre Anwendung kann inkrementelle Einwilligung und bedingten Zugriff anwenden.
- Sie profitieren von kontinuierlicher Innovationen im Bereich Sicherheit und Resilienz.
- Ihre Anwendung implementiert die Best Practices bezüglich Resilienz und Sicherheit.

**MSAL.NET oder Microsoft.Identity.Web sind jetzt die empfohlenen Authentifizierungsbibliotheken zur Verwendung mit Microsoft Identity Platform**. Für ADAL.NET werden keine neuen Features implementiert. Der Schwerpunkt liegt auf der Verbesserung von MSAL.NET. Details finden Sie in der Ankündigung: [Aktualisieren Ihrer Anwendungen für die Verwendung von Microsoft Authentication Library und der Microsoft Graph-API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>Migration zu MSAL.NET oder zu Microsoft.Identity.Web

Bevor Sie sich mit den Unterschieden zwischen MSAL.NET und ADAL.NET vertraut machen, können Sie prüfen, ob Sie MSAL.NET oder eine Abstraktion auf höherer Ebene wie [Microsoft.Identity.Web](microsoft-identity-web.md) verwenden möchten.

Weitere Informationen zur nachstehenden Entscheidungsstruktur finden Sie unter [MSAL.NET oder Microsoft.Identity.Web?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-or-Microsoft.Identity.Web)

![„Blockdiagramm zur Erläuterung der Vorgehensweise bei der Entscheidung zwischen MSAL.NET und/oder Microsoft.Identity.Web bei der Migration von ADAL.NET“](media/msal-net-migration/decision-diagram.png)

<!-- 1P
## Examples of 1P Migrations

[See examples](https://identitydivision.visualstudio.com/DevEx/_wiki/wikis/DevEx.wiki/20413/1P-ADAL.NET-to-MSAL.NET-migration-examples) of other 1P teams who have already, or are currently, migrating from ADAL to one of the MSAL+ solutions above. See their code, and in some cases read about their migration story.
 -->
 
## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [öffentliche Clientanwendungen und vertrauliche Clientanwendungen](msal-client-applications.md).
- Erfahren Sie, wie Sie [vertrauliche Clientanwendungen, die auf ASP.NET MVC oder dem klassischen .NET-Client basieren, von ADAL.NET zu MSAL.NET migrieren](msal-net-migration-confidential-client.md).
- Erfahren Sie, wie Sie [öffentliche Clientanwendungen, die auf .NET oder dem klassischen .NET-Client basieren, von ADAL.NET zu MSAL.NET migrieren](msal-net-migration-public-client.md).
- Erfahren Sie mehr über die [Unterschiede zwischen ADAL.NET- und MSAL.NET-Apps](msal-net-differences-adal-net.md).
- Erfahren Sie, wie Sie vertrauliche Clientanwendungen, die auf ASP.NET Core basieren, von ADAL.NET zu Microsoft.Identity.Web:
  -  [Web-Apps](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apps#migrating-from-previous-versions--adding-authentication)
  -  [Web-APIs](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apis)
