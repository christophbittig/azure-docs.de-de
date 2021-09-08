---
title: Verwenden des Ausdrucks-Generators mit Azure AD Connect-Cloudsynchronisierung
description: Dieser Artikel beschreibt die Verwendung des Ausdrucks-Generators mit Cloudsynchronisierung.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef8a039b61a0dd787c65ec66a3acb48a09bc1b2
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506581"
---
# <a name="expression-builder-with-cloud-sync"></a>Ausdrucks-Generator mit Cloudsynchronisierung
Der Ausdrucks-Generator ist eine neue Funktion in Azure, die sich unter der Cloudsynchronisierung befindet und Sie beim Erstellen komplexer Ausdrücke unterstützt. Sie können sie verwenden, um diese Ausdrücke zu testen, bevor Sie sie auf Ihre Cloudsynchronisierungsumgebung anwenden.

## <a name="use-the-expression-builder"></a>Verwenden des Ausdrucks-Generators
So greifen Sie auf den Ausdrucks-Generator zu:

 1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
 1. Wählen Sie **Azure AD Connect** aus.
 1. Wählen Sie **Cloudsynchronisierung verwalten** aus.
 1. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.
 1. Wählen Sie unter **Attribute verwalten** die Option **Zum Bearbeiten von Zuordnungen klicken**.
 1. Wählen Sie im Bereich **Attributzuordnungen bearbeiten** die Option **Attributzuordnung hinzufügen** aus.
 1. Wählen Sie unter **Zuordnungstyp** die Option **Ausdruck** aus.
 1. Wählen Sie **Ausdrucks-Generator ausprobieren (Vorschau)** aus.
 
    ![Screenshot: Verwendung des Ausdrucks-Generators.](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>Generieren eines Ausdrucks
In diesem Abschnitt verwenden Sie die Dropdownliste, um eine der unterstützten Funktionen auszuwählen. Dann füllen Sie je nach der gewählten Funktion weitere Felder aus. Nach dem Auswählen von **Ausdruck anwenden** wird die Syntax im Feld **Ausdruckseingabe** angezeigt.

Wenn Sie beispielsweise in der Dropdownliste **Ersetzen** auswählen, werden zusätzliche Felder bereitgestellt. Die Syntax für die Funktion wird im hellblauen Feld angezeigt. Die angezeigten Felder entsprechen der Syntax der ausgewählten Funktion. „Ersetzen“ funktioniert je nach angegebenen Parametern unterschiedlich.

Wenn in diesem Beispiel **oldValue** und **replacementValue** bereitgestellt werden, werden alle Vorkommen von **oldValue** in der Quelle durch **replacementValue** ersetzt.

Weitere Informationen finden Sie unter [Ersetzen](reference-expressions.md#replace).

Zunächst müssen Sie das Attribut auswählen, das die Quelle für die Funktion „Ersetzen“ sein soll. In diesem Beispiel ist das Attribut **mail** ausgewählt.

Suchen Sie als Nächstes das Feld für **oldValue**, und geben Sie **@fabrikam.com** ein. Geben Sie schließlich in das Feld **replacementValue** den Wert **@contoso.com** ein.

Der Ausdruck ersetzt also im Grunde genommen das Attribut „mail“ für Benutzerobjekte, die den Wert @fabrikam.com haben, durch den Wert @contoso.com. Wenn Sie die Schaltfläche **Ausdruck hinzufügen** auswählen, wird die Syntax im Feld **Ausdruckseingabe** angezeigt.

>[!NOTE]
>Achten Sie darauf, die Werte in die Felder zu platzieren, die **oldValue** und **replacementValue** entsprechen – basierend auf der Syntax, die auftrat, als Sie **Ersetzen** ausgewählt haben.

Weitere Informationen zu unterstützten Ausdrücken finden Sie unter [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](reference-expressions.md).

### <a name="information-on-expression-builder-input-boxes"></a>Informationen zu Eingabefeldern des Ausdrucks-Generators
Je nach ausgewählter Funktion akzeptieren die vom Ausdrucks-Generator bereitgestellten Felder mehrere Werte. Beispielsweise akzeptiert die JOIN-Funktion Zeichenfolgen oder den Wert, der einem angegebenen Attribut zugeordnet ist. Beispielsweise können wir den Wert im Attributwert von **[givenName]** mit dem Zeichenfolgenwert **@contoso.com** verbinden, um eine E-Mail-Adresse zu erstellen.

  ![Screenshot: Eingabefeldwerte.](media/how-to-expression-builder/expression-8.png)

Weitere Informationen zu akzeptablen Werten und zum Schreiben von Ausdrücken finden Sie unter [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](reference-expressions.md).

## <a name="test-an-expression"></a>Testen eines Ausdrucks
In diesem Abschnitt können Sie Ihre Ausdrücke testen. Wählen Sie in der Dropdownliste das **mail**-Attribut aus. Füllen Sie den Wert mit **@fabrikam.com** aus, und wählen Sie **Ausdruck testen** aus.

Der Wert **@contoso.com** wird im Feld **Ausdrucksausgabe anzeigen** angezeigt.

 ![Screenshot: Testen Ihres Ausdrucks.](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>Bereitstellen des Ausdrucks
Wenn Sie mit dem Ausdruck zufrieden sind, wählen Sie **Ausdruck anwenden** aus.

![Screenshot: Hinzufügen Ihres Ausdrucks.](media/how-to-expression-builder/expression-5.png)

Mit dieser Aktion wird der Ausdruck der Agent-Konfiguration hinzugefügt.

![Screenshot: Agent-Konfiguration.](media/how-to-expression-builder/expression-6.png)

## <a name="set-a-null-value-on-an-expression"></a>Festlegen eines NULL-Werts für einen Ausdruck
Um den Wert eines Attributs auf NULL zu setzen, verwenden Sie einen Ausdruck mit dem Wert `""`. Mit diesem Ausdruck fließt der NULL-Wert zum Zielattribut.

![Screenshot: NULL-Wert.](media/how-to-expression-builder/expression-7.png)


## <a name="next-steps"></a>Nächste Schritte 

- [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](reference-expressions.md)
- [Konfiguration der Cloudsynchronisierung](how-to-configure.md)
