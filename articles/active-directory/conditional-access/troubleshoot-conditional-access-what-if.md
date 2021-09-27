---
title: Beheben von Problemen beim bedingten Zugriff mit dem Was-wäre-wenn-Tool – Azure Active Directory
description: Wo Sie ermitteln, welche Richtlinien für bedingten Zugriff angewendet werden und warum sie angewendet werden
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 051fd45f844cdf70d0c5d4ad9145c83719b79fea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552710"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Beheben von Problemen beim bedingten Zugriff mit dem Was-wäre-wenn-Tool

Das [Was-wäre-wenn-Tool](what-if-tool.md) für den bedingten Zugriff ist effektiv, wenn Sie wissen möchten, warum eine Richtlinie in einem bestimmten Szenario auf einen Benutzer angewendet oder nicht angewendet wurde oder ob eine Richtlinie in einem bekannten Zustand angewendet würde.

Das Was-wäre-wenn-Tool befindet sich in **Azure-Portal** > **Azure Active Directory** > **Bedingter Zugriff** > **What If**.

![Was-wäre-wenn-Tool für den bedingten Zugriff im Standardzustand](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Derzeit ist es mit dem Was-wäre-wenn-Tool nicht möglich, Richtlinien im reinen Berichtsmodus auszuwerten.

## <a name="gathering-information"></a>Sammeln von Informationen

Zum Starten des Was-wäre-wenn-Tools ist lediglich ein **Benutzer** erforderlich. 

Die folgenden zusätzlichen Informationen sind optional, sie helfen jedoch, den Bereich für spezielle Fälle einzugrenzen.

* Cloud-Apps oder -aktionen
* IP-Adresse 
* Land/Region
* Geräteplattform
* Client-Apps (Vorschau)
* Gerätezustand (Vorschau) 
* Anmelderisiko

Diese Informationen können über den Benutzer, das Gerät oder das Azure AD-Anmeldungsprotokoll gesammelt werden.

## <a name="generating-results"></a>Generieren von Ergebnissen

Geben Sie die im vorherigen Abschnitt gesammelten Kriterien ein, und wählen Sie **What If** aus, um eine Liste mit Ergebnissen zu generieren. 

Sie können jederzeit **Zurücksetzen** auswählen, um die eingegebenen Kriterien zu löschen und den Standardzustand wiederherzustellen.

## <a name="evaluating-results"></a>Auswerten der Ergebnisse

### <a name="policies-that-will-apply"></a>Anzuwendende Richtlinien

In dieser Liste wird angezeigt, welche Richtlinien für bedingten Zugriff unter Berücksichtigung der Bedingungen gelten. Die Liste enthält die anzuwendenden Gewährungs- und Sitzungssteuerelemente. Beispiele hierfür sind das Verlangen von Multi-Factor Authentication für den Zugriff auf eine bestimmte Anwendung.

### <a name="policies-that-will-not-apply"></a>Nicht anzuwendende Richtlinien

Diese Liste enthält Richtlinien für bedingten Zugriff, die nicht angewendet werden, wenn die Bedingungen gelten. Die Liste enthält alle Richtlinien und den Grund, warum sie nicht angewendet werden. Beispiele hierfür sind Benutzer und Gruppen, die möglicherweise aus einer Richtlinie ausgeschlossen sind.

## <a name="use-case"></a>Anwendungsfall

Viele Organisationen erstellen Richtlinien auf der Grundlage von Netzwerkadressen, sodass vertrauenswürdige Standorte zugelassen und Standorte, auf die kein Zugriff erfolgen darf, blockiert werden.

Um zu überprüfen, ob eine Konfiguration ordnungsgemäß vorgenommen wurde, kann ein Administrator mit dem Was-wäre-wenn-Tool den Zugriff von einem Standort, der zugelassen werden sollte, und von einem Standort, der blockiert werden sollte, imitieren.

[ ![What If-Tool mit den Ergebnissen von „Zugriff blockieren“](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

In diesem Fall ist der Zugriff des Benutzers während seiner Reise nach Nordkorea auf jede Cloud-App gesperrt, da Contoso den Zugriff von diesem Standort gesperrt hat.

In diesen Test können weitere Datenpunkte aufgenommen werden, um den Bereich einzugrenzen.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist bedingter Zugriff?](overview.md)
* [Was ist Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
* [Was ist eine Geräteidentität?](../devices/overview.md)
* [So funktioniert's: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)