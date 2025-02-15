---
title: SQL-Aktionssyntax für Azure Service Bus-Abonnementregel | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Referenz für die SQL-Regelaktionssyntax. Die Aktionen werden in auf der SQL-Sprache basierender Syntax geschrieben, die für eine Nachricht ausgeführt wird.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 19d4ae9a188e2e675e055eae2f4aedd714e17e47
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211770"
---
# <a name="subscription-rule-sql-action-syntax"></a>SQL-Aktionssyntax für Abonnementregeln

Eine *SQL-Aktion* wird zum Bearbeiten von Nachrichtenmetadaten verwendet, nachdem eine Nachricht durch einen Filter einer Abonnementregel ausgewählt wurde. Es handelt sich dabei um einen Textausdruck, der auf einer Teilmenge des SQL-92-Standards basiert. Aktionsausdrücke werden mit dem `sqlExpression`-Element der action-Eigenschaft einer Service Bus-`Rule` in einer [Azure Resource Manager-Vorlage](service-bus-resource-manager-namespace-topic-with-rule.md) oder dem [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create)-Argument eines `az servicebus topic subscription rule create`-Befehls in der Azure-Befehlszeilenschnittstelle sowie mehreren SDK-Funktionen für das Verwalten von Abonnementregeln verwendet.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumente  
  
-   `<scope>` ist eine optionale Zeichenfolge, die den Bereich von `<property_name>` angibt. Gültige Werte sind `sys` und `user`. 
    - Der Wert `sys` gibt den Systembereich an, wobei es sich bei `<property_name>` um eine der Eigenschaften der Service Bus-Nachricht handelt, wie unter [Nachrichten, Nutzlasten und Serialisierung](service-bus-messages-payloads.md) beschrieben.
    - Der Wert `user` gibt den Benutzerbereich an, wobei es sich bei `<property_name>` um einen Schlüssel der benutzerdefinierten Eigenschaften handelt, die Sie beim Senden der Nachricht an die Service Bus-Instanz festlegen können.
    - Der `user`-Bereich ist der Standardbereich, wenn `<scope>` nicht angegeben wird.  
  
### <a name="remarks"></a>Bemerkungen  

Der Versuch, auf eine nicht existierende Systemeigenschaft zuzugreifen, löst einen Fehler aus, während der Versuch, auf eine nicht existierende Benutzereigenschaft zuzugreifen, keinen Fehler auslöst. Stattdessen wird eine nicht vorhandene Benutzereigenschaft intern als unbekannter Wert ausgewertet. Ein unbekannter Wert wird während der Operatorauswertung speziell behandelt.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumente  
 `<regular_identifier>` ist eine Zeichenfolge, die durch den folgenden regulären Ausdruck dargestellt wird:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Dies stellt beliebige Zeichenfolgen dar, die mit einem Buchstaben beginnen, dem ein oder mehrere Unterstriche/Buchstaben/Ziffern folgen.  
  
 `[:IsLetter:]` stellt beliebige Unicodezeichen dar, die als Unicodebuchstaben kategorisiert werden. `System.Char.IsLetter(c)` gibt `true` zurück, wenn `c` ein Unicodebuchstabe ist.  
  
 `[:IsDigit:]` stellt beliebige Unicodezeichen dar, die als Dezimalzahlen kategorisiert werden. `System.Char.IsDigit(c)` gibt `true` zurück, wenn `c` eine Unicodeziffer ist.  
  
 Ein `<regular_identifier>` kann kein reserviertes Schlüsselwort sein.  
  
 `<delimited_identifier>` ist eine beliebige Zeichenfolge, die in den linken und rechten eckigen Klammer ([]) eingeschlossen ist. Eine rechte eckige Klammer wird als zwei rechte eckige Klammern dargestellt. Hier einige Beispiele für `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` ist eine beliebige Zeichenfolge, die in doppelte Anführungszeichen eingeschlossen ist. Ein doppeltes Anführungszeichen im Bezeichner wird als zwei doppelte Anführungszeichen dargestellt. Die Bezeichner sollten nicht in Anführungszeichen eingeschlossen werden, da sie leicht mit einer Zeichenfolgenkonstante verwechselt werden können. Verwenden Sie einen Begrenzungsbezeichner, wenn möglich. Im Anschluss finden Sie ein Beispiel für `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Hinweise
  
 `<pattern>` muss ein Ausdruck sein, der als Zeichenfolge ausgewertet wird. Es wird als ein Muster für den LIKE-Operator verwendet.      Es kann die folgenden Platzhalterzeichen enthalten:  
  
-   `%`: Eine beliebige Zeichenfolge von null oder mehr Zeichen  
  
-   `_`: Ein einzelnes Zeichen  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Hinweise
  
 `<escape_char>` muss ein Ausdruck sein, der als Zeichenfolge der Länge 1 ausgewertet wird. Es wird als Escapezeichen für den LIKE-Operator verwendet.  
  
 Beispielsweise entspricht `property LIKE 'ABC\%' ESCAPE '\'` eher `ABC%` als einer Zeichenfolge, die mit `ABC` beginnt.  
  
## <a name="constant"></a>Konstante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumente  
  
-   `<integer_constant>` ist eine Zahlenzeichenfolge, die nicht in Anführungszeichen eingeschlossen ist und keine Dezimaltrennzeichen enthält. Die Werte werden intern als `System.Int64` gespeichert und folgen dem gleichen Bereich.  
  
     Es folgen Beispiele langer Konstanten:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` ist eine Zahlenzeichenfolge, die nicht in Anführungszeichen eingeschlossen ist und ein Dezimaltrennzeichen enthält. Die Werte werden intern als `System.Double` gespeichert und folgen dem gleichen Bereich/der gleichen Genauigkeit.  
  
     In einer zukünftigen Version wird diese Zahl möglicherweise in einem anderen Datentyp gespeichert, zur Unterstützung der genauen Zahlensemantik, daher sollten Sie sich nicht darauf verlassen, dass der zugrunde liegende Datentyp `System.Double` für `<decimal_constant>` ist.  
  
     Es folgen Beispiele von Dezimalkonstanten:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` ist eine in wissenschaftlicher Notation geschriebene Zahl. Die Werte werden intern als `System.Double` gespeichert und folgen dem gleichen Bereich/der gleichen Genauigkeit. Es folgen Beispiele für die ungefähren Zahlenkonstanten:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Hinweise
  
Boolesche Konstanten werden durch die Schlüsselwörter `TRUE` oder `FALSE` dargestellt. Die Werte werden als `System.Boolean` gespeichert.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Hinweise
  
Zeichenfolgenkonstanten werden in einfache Anführungszeichen eingeschlossen und enthalten beliebige, gültige Unicodezeichen. Ein einfaches Anführungszeichen, das in eine Zeichenfolgenkonstante eingebettet ist, wird als zwei einfache Anführungszeichen dargestellt.  
  
## <a name="function"></a>Funktion  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Hinweise  

Die `newid()`-Funktion gibt ein `System.Guid` zurück, das von der `System.Guid.NewGuid()`-Methode generiert wird.  
  
Die Funktion `property(name)` gibt den Wert der Eigenschaft zurück, auf die von `name` verwiesen wird. Der Wert `name` kann ein beliebiger, gültiger Ausdruck sein, der einen Zeichenfolgenwert zurückgibt.  

## <a name="examples"></a>Beispiele
Beispiele finden Sie unter [Festlegen von Abonnementfiltern (Azure Service Bus)](service-bus-filter-examples.md).
  
## <a name="considerations"></a>Überlegungen

- SET wird zum Erstellen einer neuen Eigenschaft oder zum Aktualisieren des Werts einer vorhandenen Eigenschaft verwendet.
- REMOVE wird verwendet, um eine Eigenschaft zu entfernen.
- SET führt, wenn möglich, eine implizite Konvertierung durch, wenn der Ausdruckstyp und der vorhandene Eigenschaftentyp unterschiedlich sind.
- Die Aktion löst einen Fehler aus, wenn auf nicht vorhandene Systemeigenschaften verwiesen wurde.
- Die Aktion schlägt nicht fehl, wenn auf nicht vorhandene Benutzereigenschaften verwiesen wurde.
- Eine nicht existierende Benutzereigenschaft wird intern als „Unbekannt“ ausgewertet und folgt der gleichen Semantik wie [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) beim Auswerten der Operatoren.

## <a name="next-steps"></a>Nächste Schritte

- [SQLRuleAction-Klasse (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLRuleAction-Klasse (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction-Klasse (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)
