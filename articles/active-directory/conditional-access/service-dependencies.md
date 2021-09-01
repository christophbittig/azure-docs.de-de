---
title: Dienstabhängigkeiten beim bedingten Zugriff – Azure Active Directory
description: Erfahren Sie, wie Bedingungen beim bedingten Azure Active Directory-Zugriff verwendet werden, um eine Richtlinie auszulösen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4174ea12f687e370eb1ff5508876e44a1430cdfb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864020"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Was sind Dienstabhängigkeiten beim bedingten Azure Active Directory-Zugriff? 

Mit Richtlinien für bedingten Zugriff können Sie Zugriffsanforderungen für Websites und Dienste angeben. Ihre Zugriffsanforderungen können beispielsweise die Multi-Factor Authentication (MFA) oder [verwaltete Geräte](require-managed-devices.md) umfassen. 

Wenn Sie direkt auf eine Site oder einen Dienst zugreifen, kann die Auswirkung einer zugehörigen Richtlinie normalerweise leicht bewertet werden. Wenn Sie beispielsweise über eine Richtlinie verfügen, die eine Multi-Factor Authentication für SharePoint Online erfordert, wird MFA bei jeder Anmeldung am SharePoint-Webportal erzwungen. Es ist aber nicht immer einfach, die Auswirkung einer Richtlinie zu bewerten, weil Cloud-Apps mit Abhängigkeiten von anderen Cloud-Apps vorhanden sind. Beispielsweise kann Microsoft Teams den Zugriff auf Ressourcen in SharePoint Online ermöglichen. Wenn Sie in Ihrem aktuellen Szenario also auf Microsoft Teams zugreifen, unterliegen Sie auch der SharePoint-MFA-Richtlinie. 

> [!TIP]
> Bei Verwendung der [Office 365-](concept-conditional-access-cloud-apps.md#office-365)App werden alle Office-Apps als Ziel verwendet, um Probleme mit Dienstabhängigkeiten im Office-Stack zu vermeiden.

## <a name="policy-enforcement"></a>Durchsetzung von Richtlinien 

Wenn Sie eine Dienstabhängigkeit konfiguriert haben, kann die Richtlinie per früh oder spät gebundener Erzwingung angewendet werden. 

- **Früh gebundene Richtlinienerzwingung** bedeutet, dass ein Benutzer die abhängige Dienstrichtlinie erfüllen muss, bevor er auf die aufrufende App zugreift. Ein Benutzer muss beispielsweise die SharePoint-Richtlinie erfüllen, bevor er sich an MS Teams anmeldet. 
- Die **spät gebundene Richtlinienerzwingung** erfolgt, nachdem sich der Benutzer an der aufrufenden App angemeldet hat. Die Erzwingung wird zurückgestellt, wenn App-Anforderungen (Token für den Downstreamdienst) aufgerufen werden. Beispiele hierfür sind der Zugriff auf Planner durch MS Teams und auf SharePoint durch Office.com. 

In der Abbildung unten sind MS Teams-Dienstabhängigkeiten dargestellt. Durchgehende Pfeile weisen auf eine früh gebundene Erzwingung hin, während der gestrichelte Pfeil für Planner für eine spät gebundene Erzwingung steht. 

![MS Teams-Dienstabhängigkeiten](./media/service-dependencies/01.png)

Die bewährte Methode besteht darin, für alle zugehörigen Apps und Dienste nach Möglichkeit immer gemeinsame Richtlinien festzulegen. Ein einheitlicher Sicherheitsstatus sorgt für die bestmögliche Benutzererfahrung. Wenn Sie beispielsweise eine gemeinsame Richtlinie für Exchange Online, SharePoint Online, Microsoft Teams und Skype for Business festlegen, werden unerwartete Eingabeaufforderungen, die sich aus der Anwendung unterschiedlicher Richtlinien auf Downstreamdienste ergeben können, deutlich reduziert. 

Eine gute Möglichkeit, dies mit Anwendungen im Office-Stack zu erreichen, besteht darin, anstelle einzelner Anwendungen als Ziel die [Office 365-App](concept-conditional-access-cloud-apps.md#office-365) zu verwenden.

In der Tabelle unten sind die zusätzlichen Dienstabhängigkeiten aufgeführt, die von den Client-Apps erfüllt werden müssen.  

| Client-Apps         | Downstreamdienst                          | Erzwingung |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure-Verwaltung (Portal und API) | Früh gebunden |
| Microsoft Classroom | Exchange                                    | Früh gebunden |
|                     | SharePoint                                  | Früh gebunden |
| Microsoft Teams     | Exchange                                    | Früh gebunden |
|                     | MS Planner                                  | Spät gebunden  |
|                     | Microsoft Stream                            | Spät gebunden  |
|                     | SharePoint                                  | Früh gebunden |
|                     | Skype for Business Online                   | Früh gebunden |
| Office-Portal       | Exchange                                    | Spät gebunden  |
|                     | SharePoint                                  | Spät gebunden  |
| Outlook-Gruppen      | Exchange                                    | Früh gebunden |
|                     | SharePoint                                  | Früh gebunden |
| Power Apps           | Microsoft Azure-Verwaltung (Portal und API) | Früh gebunden |
|                     | Microsoft Azure Active Directory              | Früh gebunden |
| Project             | Dynamics CRM                                | Früh gebunden |
| Skype for Business  | Exchange                                    | Früh gebunden |
| Visual Studio       | Microsoft Azure-Verwaltung (Portal und API) | Früh gebunden |
| Microsoft Forms     | Exchange                                    | Früh gebunden |
|                     | SharePoint                                  | Früh gebunden |
| Microsoft To-Do     | Exchange                                    | Früh gebunden |

## <a name="next-steps"></a>Nächste Schritte

Unter [Anleitung: Planen der Bereitstellung von bedingtem Zugriff in Azure Active Directory](plan-conditional-access.md) erfahren Sie, wie Sie bedingten Zugriff in Ihrer Umgebung implementieren.
