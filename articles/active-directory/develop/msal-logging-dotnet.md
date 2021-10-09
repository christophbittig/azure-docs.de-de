---
title: Protokollieren von Fehlern und Ausnahmen in MSAL.NET
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Fehler und Ausnahmen in MSAL.NET protokollieren
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 518fc85aff920ffece511e383b2322d8e81266ee
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091861"
---
# <a name="logging-in-msalnet"></a>Protokollierung in MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Konfigurieren der Protokollierung in MSAL.NET

In MSAL wird die Protokollierung bei der Erstellung der `.WithLogging` Anwendung mit dem Modifikator „builder“ eingestellt. Bei dieser Methode können optionale Parameter verwendet werden:

- Mit `Level` können Sie selbst über die gewünschte Protokollierungsebene entscheiden. Wenn Sie den Parameter auf „Errors“ festlegen, werden nur Fehler protokolliert.
- `PiiLoggingEnabled` ermöglicht es Ihnen, persönliche und organisatorische Daten (PII) zu protokollieren, wenn es auf „true“ gesetzt ist. Dieser Parameter ist standardmäßig auf „false“ festgelegt, damit Ihre Anwendung keine personenbezogenen Daten protokolliert.
- `LogCallback` ist auf einen Delegate festgelegt, der die Protokollierung ausführt. Ist `PiiLoggingEnabled` „true“, empfängt diese Methode Nachrichten, die PII enthalten können; in diesem Fall wird das Flag `containsPii` auf „true“ gesetzt.
- `DefaultLoggingEnabled`Aktiviert die standardmäßige Protokollierung für die Plattform. Der Parameter ist standardmäßig auf „false“ festgelegt. Wenn Sie den Parameter auf „true“ festlegen, wird in Desktop-/UWP-Anwendungen die Ereignisablaufverfolgung, unter iOS NSLog und unter Android Logcat verwendet.

```csharp
class Program
{
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
}
```

> [!TIP]
 > Beispiele für die MSAL.NET-Protokollierung und weitere Infos finden Sie unter [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki).

## <a name="next-steps"></a>Nächste Schritte

Weitere Codebeispiele finden Sie unter [Microsoft Identity Platform-Codebeispiele](sample-v2-code.md).
