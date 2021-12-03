---
title: Bewährte Methoden bei der Entwicklung von Bicep-Dateien
description: Hier werden bewährte Methoden für die Erstellung von Bicep-Dateien beschrieben, die sicherstellen, dass Ihre Dateien ordnungsgemäß funktionieren und einfach zu verwalten sind.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 65f55208f0a2e09db39cedc8e5074b622b232834
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057668"
---
# <a name="best-practices-for-bicep"></a>Bewährte Methoden für Bicep

In diesem Artikel werden Methoden für die Entwicklung Ihrer Bicep-Dateien empfohlen. Diese Methoden machen Ihre Bicep-Datei besser verständlich und einfacher verwendbar.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen zu bewährten Methoden für Bicep und praktische Anleitungen finden Sie unter [Strukturieren Ihres Bicep-Codes für die Zusammenarbeit](/learn/modules/structure-bicep-code-collaboration/) auf **Microsoft Learn**.

## <a name="parameters"></a>Parameter

* Verwenden Sie eine gute Benennungskonvention für Parameterdeklarationen. Geeignete Namen machen Ihre Vorlagen besser les- und nachvollziehbar. Verwenden Sie klare, aussagekräftige und konsistente Namen.

* Machen Sie sich Gedanken zu den in Ihrer Vorlage verwendeten Parametern. Verwenden Sie nach Möglichkeit Parameter für Einstellungen, die sich zwischen Bereitstellungen ändern. Variablen und hartcodierte Werte können für Einstellungen verwendet werden, die sich zwischen Bereitstellungen nicht ändern.

* Achten Sie auf die von Ihnen verwendeten Standardwerte. Stellen Sie sicher, dass die Standardwerte von jedem sicher bereitgestellt werden können. So empfiehlt es sich beispielsweise unter Umständen, kostengünstige Tarife und SKUs zu verwenden, damit bei der Bereitstellung der Vorlage in einer Testumgebung keine unnötig hohen Kosten entstehen.

* Verwenden Sie das Decorator-Element `@allowed` sparsam. Bei zu großzügiger Verwendung dieses Decorator-Elements werden unter Umständen gültige Bereitstellungen blockiert. Wenn weitere SKUs und Größen für Azure-Dienste hinzukommen, ist Ihre Zulassungsliste möglicherweise nicht mehr auf dem neuesten Stand. Ein Beispiel: Es kann zwar sinnvoll sein, in der Produktionsumgebung nur Premium v3-SKUs zuzulassen, dies führt jedoch dazu, dass die gleiche Vorlage in produktionsfremden Umgebungen nicht verwendet werden kann.

* Es empfiehlt sich, Beschreibungen für Ihre Parameter anzugeben. Die Beschreibungen sollten hilfreich sein und alle wichtigen Informationen zu den erforderlichen Parameterwerten für die Vorlage enthalten.

  Für einige Informationen können auch Kommentare vom Typ `//` verwendet werden.

* Parameterdeklarationen können an beliebiger Stelle in der Vorlagendatei platziert werden. Zur besseren Lesbarkeit Ihres Bicep-Codes empfiehlt es sich jedoch in der Regel, sie am Anfang der Datei zu platzieren.

* Es empfiehlt sich, die minimale und maximale Zeichenlänge für Parameter anzugeben, die zur Steuerung der Benennung dienen. Diese Einschränkungen helfen bei der Vermeidung von Fehlern im weiteren Bereitstellungsverlauf.

Weitere Informationen zu Bicep-Parametern finden Sie unter [Parameter in Bicep](parameters.md).

## <a name="variables"></a>Variablen

* Beim Definieren einer Variablen ist der [Datentyp](data-types.md) nicht erforderlich. Bei Variablen wird der Typ vom Auflösungswert abgeleitet.

* Sie können Bicep-Funktionen verwenden, um eine Variable zu erstellen.

* Nachdem eine Variable in Ihrer Bicep-Datei definiert wurde, können Sie den Namen der Variablen verwenden, um auf den Wert zu verweisen.

Weitere Informationen zu Bicep-Variablen finden Sie unter [Variablen in Bicep](variables.md).

## <a name="names"></a>Namen

* Verwenden Sie Namen mit gemischter Groß-/Kleinschreibung wie `myVariableName` oder `myResource`.

* Mit der Funktion [uniqueString()](bicep-functions-string.md#uniquestring) können global eindeutige Ressourcennamen erstellt werden. Bei Angabe der gleichen Parameter wird jedes Mal die gleiche Zeichenfolge zurückgegeben. Wenn Sie die Ressourcengruppen-ID übergeben, ist die Zeichenfolge bei jeder Bereitstellung für die gleiche Ressourcengruppe identisch und bei der Bereitstellung für andere Ressourcengruppen oder Abonnements unterschiedlich.

* Manchmal werden von der Funktion `uniqueString()` Zeichenfolgen erstellt, die mit einer Zahl beginnen. Bei einigen Azure-Ressourcen (z. B. Speicherkonten) dürfen die Namen nicht mit Zahlen beginnen. Aufgrund dieser Anforderung empfiehlt es sich, die Zeichenfolgeninterpolation für die Erstellung von Ressourcennamen zu verwenden. Sie können der eindeutigen Zeichenfolge ein Präfix hinzufügen.

* Es ist häufig sinnvoll, für das Erstellen von Ressourcennamen Vorlagenausdrücke zu verwenden. Viele Azure-Ressourcentypen weisen Regeln für die zulässigen Zeichen und die Länge der Namen auf. Das Einbetten der Erstellung von Ressourcennamen in die Vorlage bedeutet, dass sich Personen, die die Vorlage verwenden, nicht selbst um die Einhaltung der Regeln kümmern müssen.

* Vermeiden Sie die Verwendung von `name` in einem symbolischen Namen. Der symbolische Name stellt die Ressource dar, nicht den Namen der Ressource. Schreiben Sie beispielsweise nicht Folgendes:

  ```bicep
  resource cosmosDBAccountName 'Microsoft.DocumentDB/databaseAccounts@2021-04-15' = {
  ```

  Sondern verwenden Sie:

  ```bicep
  resource cosmosDBAccount 'Microsoft.DocumentDB/databaseAccounts@2021-04-15' = {
  ```

* Vermeiden Sie die Unterscheidung von Variablen und Parametern durch Verwendung von Suffixen.

## <a name="resource-definitions"></a>Ressourcendefinitionen

* Anstatt komplexe Ausdrücke direkt in Ressourceneigenschaften einzubetten, können Sie Variablen für die Ausdrücke verwenden. Dadurch wird Ihre Bicep-Datei besser lesbar und leichter verständlich. Außerdem werden Ihre Ressourcendefinitionen so nicht mit Logik überladen.

* Versuchen Sie, Ressourceneigenschaften als Ausgaben zu verwenden, anstatt Annahmen zum Verhalten von Ressourcen zu treffen. Wenn Sie beispielsweise die URL einer App Service-App ausgeben müssen, können Sie die Eigenschaft „defaultHostname“ der App verwenden, anstatt selbst eine Zeichenfolge für die URL zu erstellen. Manchmal sind diese Annahmen nicht in allen Umgebungen korrekt, oder die Funktionsweise von Ressourcen ändert sich. Daher ist es sicherer, wenn die Eigenschaften einer Ressource direkt von der Ressource stammen.

* Es ist eine gute Idee, eine aktuelle API-Version für jede Ressource zu verwenden. Neue Features in Azure-Diensten sind manchmal nur in neueren API-Versionen verfügbar.

* Vermeiden Sie nach Möglichkeit die Verwendung der Funktionen [reference](./bicep-functions-resource.md#reference) und [resourceId](./bicep-functions-resource.md#resourceid) in Ihrer Bicep-Datei. Sie können auf eine beliebige Ressource in Bicep zugreifen, indem Sie den symbolischen Namen verwenden. Wenn Sie also beispielsweise ein Speicherkonto mit dem symbolischen Namen „toyDesignDocumentsStorageAccount“ definieren, können Sie mithilfe des Ausdrucks `toyDesignDocumentsStorageAccount.id` auf die zugehörige Ressourcen-ID zugreifen. Durch die Verwendung des symbolischen Namens wird eine implizite Abhängigkeit zwischen Ressourcen erstellt.

* Bevorzugen Sie die Verwendung impliziter Abhängigkeiten gegenüber expliziten Abhängigkeiten. Obwohl es Ihnen die Ressourceneigenschaft `dependsOn` ermöglicht, eine explizite Abhängigkeit zwischen Ressourcen zu deklarieren, ist es in der Regel möglich, die Eigenschaften der anderen Ressource mithilfe ihres symbolischen Namens zu verwenden. Dadurch wird eine implizite Abhängigkeit zwischen den beiden Ressourcen erstellt, und Bicep kann die Beziehung selbst verwalten.

* Wenn die Ressource nicht in der Bicep-Datei bereitgestellt ist, können Sie mit dem Schlüsselwort `existing` trotzdem einen symbolischen Verweis auf die Ressource abrufen.

## <a name="child-resources"></a>Untergeordnete Ressourcen

* Vermeiden Sie Schachtelungen mit zu vielen Ebenen. Wenn Ihr Bicep-Code zu viele Schachtelungen enthält, ist er schlechter les- und verwendbar.

* Erstellen Sie am besten keine Ressourcennamen für untergeordnete Ressourcen. Sie verlieren die Vorteile, die Bicep bietet, wenn die Beziehungen zwischen Ihren Ressourcen nachvollzogen werden können. Verwenden Sie stattdessen die Eigenschaft `parent` oder die Schachtelung.

## <a name="outputs"></a>Ausgaben

* Achten Sie darauf, dass Sie keine Ausgaben für vertrauliche Daten erstellen. Jeder Benutzer, der Zugriff auf den Bereitstellungsverlauf hat, kann auch auf Ausgabewerte zugreifen. Sie sind nicht für Geheimnisse geeignet.

* Verwenden Sie das `[existing`Schlüsselwort](resource-declaration.md#existing-resources), um Eigenschaften von bereits vorhandenen Ressourcen nachzuschlagen, anstatt Eigenschaftswerte mithilfe von Ausgaben zu übergeben. Es ist eine bewährte Methode, Schlüssel aus anderen Ressourcen auf diese Weise nachzuschlagen, anstatt sie per Ausgaben zu übergeben. Sie erhalten so immer die neuesten Daten.

Weitere Informationen zu Bicep-Ausgaben finden Sie unter [Ausgaben in Bicep](outputs.md).

## <a name="tenant-scopes"></a>Mandantenbereiche

Sie können keine Richtlinien oder Rollenzuweisungen im [Mandantenbereich](deploy-to-tenant.md) erstellen. Wenn Sie jedoch in der gesamten Organisation Zugriff gewähren oder Richtlinien anwenden müssen, können Sie diese Ressourcen in der Stammverwaltungsgruppe bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in Bicep finden Sie unter [Schnellstart: Erstellen von Bicep-Dateien mit Visual Studio Code](quickstart-create-bicep-use-visual-studio-code.md).
* Informationen zu den Komponenten einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](file.md).
