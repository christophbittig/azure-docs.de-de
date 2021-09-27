---
title: Richtlinien in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie Richtlinien in API Management erstellen, bearbeiten und konfigurieren. Siehe Codebeispiele und andere verfügbare Ressourcen.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/25/2021
ms.author: danlep
ms.openlocfilehash: 37e25908bd08ca9618bc56bb2e10ff8e48a46fc6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649676"
---
# <a name="policies-in-azure-api-management"></a>Richtlinien in Azure API Management

In Azure API Management können API-Herausgeber das API-Verhalten über eine Konfiguration mithilfe von Richtlinien ändern. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell auf die Anforderung oder Antwort einer API ausgeführt werden. Zu den gängigen Anweisungen gehören:

* Formatumwandlung von XML in JSON
* Aufrufratenbeschränkung zum Begrenzen der Anzahl eingehender Aufrufe von einem Entwickler 

Viele weitere Richtlinien sind vorkonfiguriert verfügbar.

Richtlinien werden im Gateway angewendet, das sich zwischen API-Consumer und der verwalteten API befindet. Während das Gateway Anforderungen empfängt und diese unverändert an die zugrunde liegende API weiterleitet, kann eine Richtlinie sowohl auf die eingehende Anforderung als auch auf die ausgehende Antwort Änderungen anwenden.

Sofern in der Richtlinie nicht anders angegeben, können Richtlinienausdrücke als Attribut- oder Textwerte in allen API Management-Richtlinien verwendet werden. Einige Richtlinien, z. B. [Ablaufsteuerung][Control flow] und [Variable festlegen][Set variable], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie in den Artikeln [Erweiterte Richtlinien][Advanced policies] und [Richtlinienausdrücke][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Grundlegendes zur Richtlinienkonfiguration

Richtliniendefinitionen sind einfache XML-Dokumente, die eine Sequenz eingehender und ausgehender Anweisungen beschreiben. Sie können den XML-Code direkt im Definitionsfenster bearbeiten, der außerdem Folgendes bereitstellt:
* Eine Liste der Anweisungen auf der rechten Seite.
* Für den aktuellen Bereich anwendbare Anweisungen, diese sind aktiviert und hervorgehoben.

Wenn Sie auf eine aktivierte Anweisung klicken, wird der entsprechende XML-Ausschnitt an der Cursorposition in der Definitionsansicht eingefügt. 

> [!NOTE]
> Wenn die Richtlinie, die Sie hinzufügen möchten, nicht aktiviert ist, stellen Sie sicher, dass Sie sich im richtigen Bereich für diese Richtlinie befinden. Für jede Richtlinienanweisung sind bestimmte Bereiche und Richtlinienabschnitte vorgesehen. Informationen zu den Richtlinienabschnitten und den Bereichen für eine Richtlinie finden Sie im Abschnitt **Verwendung** in der [Richtlinienreferenz][Policy Reference].

Die Konfiguration ist in `inbound`, `backend`, `outbound` und `on-error` unterteilt. Die Richtlinienanweisungen werden für eine Anforderung und eine Antwort nacheinander ausgeführt.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Wenn es während der Verarbeitung einer Anforderung zu einem Fehler kommt, gilt Folgendes:
* Alle verbleibenden Schritte in den Abschnitten `inbound`, `backend` oder `outbound` werden übersprungen.
* Die Ausführung wechselt zu den Anweisungen im Abschnitt `on-error`.

Das Platzieren von Richtlinienanweisungen im Abschnitt `on-error` ermöglicht Ihnen Folgendes:
* Sie können den Fehler mithilfe der Eigenschaft `context.LastError` überprüfen.
* Sie können die Fehlerantwort mithilfe der Richtlinie `set-body` untersuchen und anpassen.
* Sie können konfigurieren, was bei Auftreten eines Fehlers passiert. 

Unter [Fehlerbehandlung bei API Management-Richtlinien](./api-management-error-handling-policies.md) finden Sie Informationen zu Fehlercodes für:
* Integrierte Schritte
* Fehler, die während der Verarbeitung von Richtlinienanweisungen auftreten können 

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Konfigurieren von Richtlinien

Informationen zum Konfigurieren von Richtlinien finden Sie unter [How to set or edit Azure API Management policies](set-edit-policies.md) (Festlegen oder Bearbeiten von Azure API Management-Richtlinien).

## <a name="policy-reference"></a>Richtlinienreferenz

In der [Richtlinienreferenz](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.

## <a name="policy-samples"></a>Richtlinienbeispiele

Weitere Codebeispiele finden Sie unter [Richtlinien für die API-Verwaltung](./policy-reference.md).

## <a name="examples"></a>Beispiele

### <a name="apply-policies-specified-at-different-scopes"></a>Anwenden von Richtlinien, die für verschiedene Bereiche angegeben sind

Wenn Sie eine Richtlinie auf der globalen Ebene und eine Richtlinie für eine API konfiguriert haben, dann werden bei jeder Verwendung dieser API immer beide Richtlinien angewendet. API Management ermöglicht eine deterministische Festlegung der Reihenfolge kombinierter Richtlinienanweisungen über das `base`-Element. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Für die oben gezeigte Beispielrichtliniendefinition gilt Folgendes:
* Die Anweisung `cross-domain` wird vor jeder höheren Richtlinien ausgeführt.
* Die Richtlinie `find-and-replace` wird nach jeder höheren Richtlinien ausgeführt. 

>[!NOTE]
> Wenn Sie das Tag `<base />` im API-Bereich entfernen, werden nur Richtlinien angewendet, die im API-Bereich konfiguriert sind. Es werden weder Produktrichtlinien noch Richtlinien auf globaler Ebene angewendet.

### <a name="restrict-incoming-requests"></a>Einschränken eingehender Anforderungen

Um eine neue Anweisung zur Einschränkung eingehender Anfragen auf bestimmte IP-Adressen zu erstellen, platzieren Sie den Cursor innerhalb des `inbound`-XML-Elements, und klicken Sie auf die Anweisung **Restrict caller IPs**.

![Einschränkungsrichtlinien][policies-restrict]

Daraufhin wird ein XML-Ausschnitt in das `inbound` -Element eingefügt, der Anweisungen zur Konfiguration der Anweisung enthält.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Ändern Sie den XML-Ausschnitt wie folgt, um nur eingehende Anfragen von der IP-Adresse 1.2.3.4 zu erlauben:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management-Richtlinienbeispiele](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
