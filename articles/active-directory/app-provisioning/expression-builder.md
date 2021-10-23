---
title: Informationen zur Funktionsweise des Ausdrucks-Generators mit der Anwendungsbereitstellung in Azure Active Directory
description: Informationen zur Funktionsweise des Ausdrucks-Generators mit der Anwendungsbereitstellung in Azure Active Directory
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/02/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ed5623f2b7d4585a7ffe417dbfa0943bf37ebf21
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991468"
---
# <a name="understand-how-expression-builder-in-application-provisioning-works"></a>Verstehen der Funktionsweise des Ausdrucks-Generators bei der Anwendungsbereitstellung

Sie können [Ausdrücke](functions-for-customizing-application-data.md) verwenden, um [Attribute zuzuordnen](./customize-application-attributes.md). Zuvor müssen Sie diese Ausdrücke manuell erstellen und in das Ausdrucksfeld eingeben. Der Ausdrucks-Generator ist ein Tool, mit dem Sie Ausdrücke erstellen können.

:::image type="content" source="media/expression-builder/expression-builder.png" alt-text="Die Standardseite des Ausdrucks-Generators vor dem Auswählen einer Funktion" lightbox="media/expression-builder/expression-builder.png":::

Referenzinformationen zum Erstellen von Ausdrücken finden Sie unter [Referenz zum Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md). 

## <a name="finding-the-expression-builder"></a>Suchen des Ausdrucks-Generators

Bei der Anwendungsbereitstellung verwenden Sie Ausdrücke für Attributzuordnungen. Sie greifen auf der Seite „Attributzuordnung“ auf den Ausdrucks-Generator zu, indem Sie **Erweiterte Optionen anzeigen** und dann **Ausdrucks-Generator** auswählen.

:::image type="content" source="media/expression-builder/accessing-expression-builder.png" alt-text="Das Kontrollkästchen zum Anzeigen erweiterter Einstellungen ist aktiviert, und es wird ein Link mit dem Ausdrucks-Generator angezeigt." lightbox="media/expression-builder/accessing-expression-builder.png":::

## <a name="using-expression-builder"></a>Verwendung des Ausdrucks-Generators

Wählen Sie eine Funktion und ein Attribut aus, und geben Sie bei Bedarf ein Suffix ein, um den Ausdrucks-Generator zu verwenden. Wählen Sie dann **Ausdruck hinzufügen** aus, um den Ausdruck dem Codefeld hinzuzufügen. Weitere Informationen zu den verfügbaren Funktionen und deren Verwendung finden Sie unter [Referenz zum Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md).

Testen Sie den Ausdruck, indem Sie nach einem Benutzer suchen oder Werte bereitstellen und **Testausdruck** auswählen. Die Ausgabe des Ausdruckstests wird im Feld **Ausdrucksausgabe anzeigen** angezeigt.

Wenn Sie mit dem Ausdruck zufrieden sind, verschieben Sie ihn in eine Attributzuordnung. Kopieren Sie ihn, und fügen Sie ihn in das Ausdrucksfeld für die Attributzuordnung ein, an der Sie arbeiten.

## <a name="known-limitations"></a>Bekannte Einschränkungen
* Erweiterungsattribute stehen im Ausdrucks-Generator nicht zur Auswahl zur Verfügung. Erweiterungsattribute können jedoch im Attributzuordnungsausdruck verwendet werden. 

## <a name="next-steps"></a>Nächste Schritte

[Referenz zum Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md)