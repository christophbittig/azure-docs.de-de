---
title: Verwenden einer Regel-Engine zum Erzwingen von HTTPS in Azure CDN Standard | Microsoft-Dokumentation
description: Verwenden Sie die Regel-Engine für Microsoft Azure CDN Standard (Content Delivery Network), um die Art und Weise anzupassen, wie Azure CDN HTTP-Anforderungen verarbeitet. Dazu gehören das Blockieren der Übermittlung bestimmter Inhaltstypen, das Definieren einer Cacherichtlinie und das Ändern von HTTP-Headern. In diesem Artikel erfahren Sie, wie Sie eine Regel erstellen, um Benutzer an HTTPS umzuleiten.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: duau
ms.openlocfilehash: 7e3215c82acf476d84c58a6c6fb2bc4eeac13d99
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426428"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Einrichten der Standardregel-Engine für Azure CDN

In diesem Artikel wird beschrieben, wie Sie die Standardregel-Engine für Azure Content Delivery Network (Azure CDN) einrichten und verwenden.

## <a name="standard-rules-engine"></a>Standardregel-Engine

Sie können die Standardregel-Engine für Azure CDN verwenden, um anzupassen, wie HTTP-Anforderungen verarbeitet werden. Beispielsweise können Sie mit der Regel-Engine die Inhaltsübermittlung zwingen, bestimmte Protokolle zu verwenden, eine Cacherichtlinie definieren oder einen HTTP-Header ändern. In diesem Artikel wird veranschaulicht, wie Sie eine Regel erstellen, mit der Benutzer automatisch an HTTPS umgeleitet werden. 

> [!NOTE]
> Die in diesem Artikel beschriebene Regel-Engine ist nur für Azure CDN Standard von Microsoft verfügbar. 

## <a name="redirect-users-to-https"></a>Umleiten von Benutzern an HTTPS

1. Navigieren Sie in Ihren Microsoft-Profilen zu „Azure Content Delivery Network“.

1. Wählen Sie auf der Seite **CDN-Profil** den Endpunkt aus, für den Sie Regeln erstellen möchten.
  
1. Klicken Sie auf die Registerkarte **Regel-Engine**.
   
    Der Bereich **Regel-Engine** wird geöffnet und zeigt die Liste der verfügbaren globalen Regeln an. 
   
    [![Seite für neue Azure CDN-Regeln](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Die Reihenfolge, in der mehrere Regeln aufgelistet sind, beeinflusst die Verarbeitung der Regeln. Die in einer Regel angegebenen Aktionen werden möglicherweise durch eine nachfolgende Regel überschrieben.
   >

1. Wählen Sie **Regel hinzufügen** aus, und geben Sie einen Regelnamen ein. Regelnamen müssen mit einem Buchstaben beginnen und dürfen nur Ziffern und Buchstaben enthalten.

1. Um die Anforderungstypen zu identifizieren, auf die die Regel angewendet wird, erstellen Sie eine Übereinstimmungsbedingung:
    1. Wählen Sie **Bedingung hinzufügen** aus, und wählen Sie dann die Übereinstimmungsbedingung **Anforderungsprotokoll** aus.
    1. Wählen Sie die Option **Ist gleich** als **Operator** aus.
    1. Wählen Sie als **Wert** die Option **HTTP** aus.
   
   [![Übereinstimmungsbedingung für die Azure CDN-Regel](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > In der Dropdownliste **Bedingung hinzufügen** können Sie unter mehreren Übereinstimmungsbedingungen wählen. Eine ausführliche Liste der Übereinstimmungsbedingungen finden Sie unter [Übereinstimmungsbedingungen in der Standardregel-Engine](cdn-standard-rules-engine-match-conditions.md).
   
1. Wählen Sie die Aktion aus, die auf die Anforderungen angewendet wird, die die Übereinstimmungsbedingung erfüllen:
   1. Wählen Sie **Aktion hinzufügen** aus, und wählen Sie dann **URL-Umleitung** aus.
   1. Wählen Sie als **Typ** die Option **Gefunden (302)** aus.
   1. Wählen Sie als **Protokoll** die Option **HTTPS** aus.
   1. Lassen Sie alle weiteren Felder leer, um die eingehenden Werte zu verwenden.
   
   [![Azure CDN-Regelaktion](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > In der Dropdownliste **Aktion hinzufügen** können Sie unter mehreren Aktionen wählen. Eine detaillierte Liste der Aktionen finden Sie unter [Aktionen der Standardregel-Engine](cdn-standard-rules-engine-actions.md).

6. Klicken Sie auf **Speichern**, um die neue Regel zu speichern. Die Regel ist jetzt für die Verwendung verfügbar.
   
   > [!IMPORTANT]
   > Es kann bis zu 15 Minuten dauern, bis Regeländerungen über Azure CDN verteilt wurden.
   >
   

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
- [Referenz zur Standardregel-Engine](cdn-standard-rules-engine-reference.md)
- [Übereinstimmungsbedingungen in der Standardregel-Engine](cdn-standard-rules-engine-match-conditions.md)
- [Aktionen in der Standardregel-Engine](cdn-standard-rules-engine-actions.md)
