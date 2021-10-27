---
title: Beispiele für APIs zum Ändern Ihrer Azure Active Directory B2C-Benutzerflows
description: Codebeispiele zum Ändern von den Benutzerabläufen mit API-Connectors
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 07/16/2021
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d4a807d725caa224eba67ee7419b91753dcc439c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038040"
---
# <a name="api-connector-rest-api-samples"></a>API-Connector – REST-API-Beispiele

Die folgenden Tabellen enthalten Links zu Codebeispielen für die Verwendung von Web-APIs in den Benutzerabläufen mithilfe von [API-Connectors](api-connectors-overview.md). Diese Beispiele sind in erster Linie für die Verwendung mit integrierten Benutzerabläufen konzipiert.

## <a name="azure-function-quickstarts"></a>Azure-Funktion – Schnellstarts
| Beispiel                                                                                                                          | BESCHREIBUNG                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Dieses .NET Core-Beispiel für eine Azure-Funktion veranschaulicht, wie Sie Registrierungen auf bestimmte E-Mail-Domänen beschränken und vom Benutzer bereitgestellte Informationen überprüfen. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Dieses Node.js-Beispiel für eine Azure-Funktion veranschaulicht, wie Sie Registrierungen auf bestimmte E-Mail-Domänen beschränken und vom Benutzer bereitgestellte Informationen überprüfen.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Dieses Python-Beispiel für eine Azure-Funktion veranschaulicht, wie Sie Registrierungen auf bestimmte E-Mail-Domänen beschränken und vom Benutzer bereitgestellte Informationen überprüfen.    |


## <a name="automated-fraud-protection-services--captcha"></a>Automatisierte Betrugsschutzdienste und CAPTCHA
| Beispiel                                                                                                            | BESCHREIBUNG                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Betrugs- und Missbrauchsschutz von Arkose Labs](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | In diesem Beispiel wird gezeigt, wie Sie Ihre Benutzerregistrierungen mithilfe des Betrugs- und Missbrauchsschutzdiensts von Arkose Labs schützen. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | In diesem Beispiel wird gezeigt, wie Sie Ihre Benutzerregistrierungen mithilfe einer reCAPTCHA-Prüfung schützen, um automatisierten Missbrauch zu verhindern. |


## <a name="identity-verification"></a>Identitätsüberprüfung

| Beispiel                                                                                                            | BESCHREIBUNG                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Dieses Beispiel zeigt, wie der IDology-Dienst die Benutzer-ID als Teil des Anmeldevorgangs überprüfen kann. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Dieses Beispiel zeigt, wie der Experian-Dienst eine Benutzer-ID als Teil des Anmeldevorgangs überprüft. |


## <a name="other"></a>Sonstiges

| Beispiel                                                                                                            | BESCHREIBUNG                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Einladungscode](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | In diesem Beispiel wird gezeigt, wie Sie die Registrierung mithilfe von Einladungscodes auf bestimmte Zielgruppen beschränken.|
| [Beispiele für Community im Zusammenhang mit API-Connector](https://github.com/azure-ad-b2c/api-connector-samples) | Dieses Repository enthält von der Community verwaltete Beispiele für Szenarien, die durch API-Connectors aktiviert wurden.|
