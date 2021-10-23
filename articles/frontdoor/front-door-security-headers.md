---
title: 'Tutorial: Hinzufügen von Sicherheitsheadern mit Regel-Engines: Azure Front Door'
description: In diesem Tutorial erfahren Sie, wie Sie einen Sicherheitsheader per Regel-Engine in Azure Front Door konfigurieren.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 48f70bd0cd71ffc517d7955f645c579f1ffa2b48
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714870"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Tutorial: Hinzufügen von Sicherheitsheadern mit Regel-Engines

In diesem Tutorial wird gezeigt, wie Sie Sicherheitsheader implementieren, um browserbasierte Sicherheitsrisiken zu vermeiden, z. B. HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy oder X-Frame-Options. Sicherheitsbasierte Attribute können auch mit Cookies definiert werden.

Das folgende Beispiel zeigt Ihnen, wie Sie einen Content-Security-Policy-Header zu allen eingehenden Anforderungen hinzufügen, die dem Pfad entsprechen, der in der Route definiert ist, mit der Ihre Regelmodulkonfiguration verknüpft ist. Hier wird in unseren Anwendungen nur die Ausführung von Skripts von unserer vertrauenswürdigen Website **https://apiphany.portal.azure-api.net** zugelassen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Konfigurieren einer Inhaltssicherheitsrichtlinie (Content-Security-Policy) im Regelmodul

## <a name="prerequisites"></a>Voraussetzungen

* Bevor Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst eine Azure Front Door Service-Konfiguration erstellen. Weitere Informationen finden Sie unter [Quickstart: Erstellen einer „Front Door“](quickstart-create-front-door.md).
* Wenn Sie das Feature für das Regelmodul zum ersten Mal verwenden, finden Sie weitere Informationen unter [Konfigurieren Ihres Regelmoduls](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Hinzufügen eines Content-Security-Policy-Headers im Azure-Portal

1. Klicken Sie auf **Hinzufügen**, um eine neue Regel hinzuzufügen. Geben Sie der Regel einen Namen, und klicken Sie dann auf **Aktion hinzufügen** > **Antwortheader**.

1. Legen Sie den Operator auf **Append** fest, damit dieser Header als Antwort auf alle unter dieser Route eingehenden Anforderungen hinzugefügt wird.

1. Fügen Sie den Headernamen **Content-Security-Policy** hinzu, und definieren Sie die Werte, die dieser Header akzeptieren soll. In diesem Szenario haben wir *„script-src ‚self‘ https://apiphany.portal.azure-api.net“ ausgewählt.*

   > [!NOTE]
   > Kopfzeilenwerte sind auf 640 Zeichen beschränkt.

1. Nachdem Sie alle gewünschten Regeln zu Ihrer Konfiguration hinzugefügt haben, vergessen Sie nicht, zu Ihrer bevorzugten Route zu navigieren und Ihre Regelmodulkonfiguration mit Ihrer Routenregel zu verknüpfen. Dieser Schritt ist erforderlich, damit die Regel funktioniert. 

![Portalbeispiel](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> In diesem Szenario haben wir der Regel keine [Übereinstimmungsbedingungen](front-door-rules-engine-match-conditions.md) hinzugefügt. Für alle eingehenden Anforderungen, die dem in der Routenregel definierten Pfad entsprechen, wird diese Regel angewendet. Wenn Sie die Regel nur auf eine Teilmenge dieser Anforderungen anwenden möchten, fügen Sie dieser Regel unbedingt Ihre spezifischen **Übereinstimmungsbedingungen** hinzu.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Sicherheitsheader mit dem Regelmodul konfiguriert. Wenn Sie die Regel nicht mehr benötigen, können Sie sie entfernen, indem Sie auf „Regel löschen“ klicken.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Löschen einer Regel":::

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial erfahren Sie, wie Sie eine Web Application Firewall für Ihre Front Door-Instanz konfigurieren:

> [!div class="nextstepaction"]
> [Web Application Firewall und Front Door](front-door-waf.md)
