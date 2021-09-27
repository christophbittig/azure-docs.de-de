---
title: Debuggen von Azure API Management-Richtlinien in Visual Studio Code | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure API Management-Richtlinien mithilfe der Azure API Management-Erweiterung für Visual Studio Code debuggen.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: danlep
ms.openlocfilehash: 884ddb46c2182ae22df555b5ac32c4b4ff72c424
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625827"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Debuggen von Azure API Management-Richtlinien in Visual Studio Code

[Richtlinien](api-management-policies.md) in Azure API Management bieten leistungsstarke Funktionen, die API-Verlegern dabei helfen, übergreifende Aspekte wie Authentifizierung, Autorisierung, Drosselung, Zwischenspeicherung und Transformation zu behandeln. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. 

In diesem Artikel wird beschrieben, wie Sie API Management-Richtlinien mithilfe der [Azure API Management-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement) debuggen. 

## <a name="prerequisites"></a>Voraussetzungen

* Befolgen Sie zuerst [diesen Schnellstart](get-started-create-service-instance.md), um eine API Management-Instanz auf Entwicklerebene zu erstellen.

* Installieren Sie [Visual Studio Code](https://code.visualstudio.com/) und die neueste Version der [Azure API Management-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

* Importieren Sie eine API in Ihre API Management-Instanz. Beispiele für die Vorgehensweise finden Sie im [Tutorial: Verwenden der API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Einschränkungen

Dieses Feature ist nur auf der API Management-Ebene „Entwickler“ verfügbar. Jede API Management-Instanz unterstützt nur eine gleichzeitige Debugsitzung.

## <a name="initiate-a-debugging-session"></a>Initiieren einer Debugsitzung

1. Starten Sie Visual Studio Code.
2. Navigieren Sie unter den Azure-Erweiterungen zur API Management-Erweiterung.
3. Suchen Sie die zu debuggende API Management-Instanz.
4. Suchen Sie die zu debuggende API und den Vorgang.
5. Klicken Sie mit der rechten Maustaste auf den Vorgang und dann auf **Start policy debugging** (Richtliniendebugging starten).

An diesem Punkt versucht die Erweiterung, eine Debugsitzung mit dem API Management-Gateway zu initiieren und einzurichten.

![Initiieren des Debuggens](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Senden einer Testanforderung
Wenn die Debugsitzung eingerichtet wird, öffnet die Erweiterung einen neuen Editor, der es Ihnen ermöglicht, unter Verwendung der [REST-Clienterweiterung](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) eine HTTP-Testanforderung für diesen Vorgang zu erstellen und zu senden.

Sie werden bemerken, dass der Header **Ocp-Apim-Debug** bereits zur Anforderung hinzugefügt wurde. Dieser Header ist erforderlich, und der Wert muss auf den Abonnementschlüssel auf Dienstebene mit allen Zugriffsberechtigungen festgelegt werden, um die Debuggingfunktionalität im API Management-Gateway zu initiieren.

Ändern Sie die HTTP-Anforderung im Editor entsprechend dem Testszenario. Klicken Sie dann auf **Send request** (Anforderung senden), um die Testanforderung an das API Management-Gateway zu senden.

![Senden einer Testanforderung](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Debugrichtlinien
Nachdem die HTTP-Testanforderung gesendet wurde, öffnet die Erweiterung das Debuggingfenster, in dem die effektiven Richtlinien für diesen Vorgang angezeigt werden, und hält bei der ersten effektiven Richtlinie an. 

![Debugrichtlinien](media/api-management-debug-policies/main-window.png)

Sie können die Richtlinien einzeln durchlaufen oder einen Breakpoint bei einer Richtlinie festlegen und direkt zu dieser Richtlinie wechseln, um der Richtlinienpipeline zu folgen. 

Im Bereich **Variablen** können Sie die Werte von vom System bzw. Benutzer erstellten Variablen überprüfen. Im Bereich **Haltepunkte** finden Sie die Liste aller festgelegten Breakpoints. Im Bereich **Aufrufliste** sehen Sie den aktuellen Gültigkeitsbereich der effektiven Richtlinie. 

Wenn während der Ausführung der Richtlinie ein Fehler auftritt, werden die Details zum Fehler bei der Richtlinie angezeigt, in der der Fehler aufgetreten ist. 

![Ausnahmen](media/api-management-debug-policies/exception.png)

> [!TIP]
> Wenn Sie fertig sind, können Sie die Debugsitzung beenden, indem Sie auf die Schaltfläche **Beenden** klicken.


## <a name="next-steps"></a>Nächste Schritte

+ Informieren Sie sich weiter über die [API Management-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Melden Sie Issues im [GitHub-Repository](https://github.com/Microsoft/vscode-apimanagement).

