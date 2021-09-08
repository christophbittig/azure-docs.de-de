---
title: Verwenden von Azure Active Directory in Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Mithilfe von Azure Active Directory können Sie den Zugriff auf Azure Communication Services aus Anwendungen autorisieren, die auf Azure-VMs, in Funktions-Apps und in anderen Ressourcen ausgeführt werden.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 3559dc9f7adc10f11e13e18468bbbd964bf5db04
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253523"
---
# <a name="quickstart-authenticate-using-azure-active-directory"></a>Schnellstart: Authentifizieren mittels Azure Active Directory

Hier erfahren Sie mehr zu den ersten Schritten bei der Verwendung von Azure Active Directory mit Azure Communication Services. Die Identitäts- und SMS-SDKs von Communication Services unterstützen die Azure AD-Authentifizierung (Azure Active Directory).

In diesem Schnellstart erfahren Sie, wie Sie den Zugriff auf die Identitäts- und SMS-SDKs aus einer Azure-Umgebung autorisieren, die Active Directory unterstützt. Außerdem wird beschrieben, wie Sie Ihren Code in einer Entwicklungsumgebung testen, indem Sie einen Dienstprinzipal für Ihre Arbeit erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free)
- Eine aktive Azure Communication Services-Ressource. Informationen zum Erstellen einer Communication Services-Ressource finden Sie bei Bedarf [hier](../create-communication-resource.md).
- Eine [Telefonnummer](../telephony-sms/get-phone-number.md) zum Senden einer SMS.
- Einen Einrichtungsdienstprinzipal für eine Entwicklungsumgebung. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs mit der verwalteten Identität auf die Kommunikationsressource in Ihrer Entwicklungsumgebung](./service-principal-from-cli.md).

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/active-directory/service-principal-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/active-directory/service-principal-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/active-directory/service-principal-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/active-directory/service-principal-python.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur rollenbasierten Zugriffssteuerung in Azure](../../../../articles/role-based-access-control/index.yml)
- [Weitere Informationen zur Azure-Identitätsbibliothek für .NET](/dotnet/api/overview/azure/identity-readme)
- [Erstellen von Benutzerzugriffstoken](../../quickstarts/access-tokens.md)
- [Senden einer SMS](../../quickstarts/telephony-sms/send.md)
- [Weitere Informationen zu SMS](../../concepts/telephony-sms/concepts.md)
- [Schnelles Erstellen einer Identität zum Testen](./quick-create-identity.md).

