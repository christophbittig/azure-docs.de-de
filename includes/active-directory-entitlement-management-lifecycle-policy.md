---
title: Datei einfügen
description: include file
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: f078710392f895dc672c9562ac3a7b03f4623e1e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019229"
---
## <a name="lifecycle"></a>Lebenszyklus

Auf der Registerkarte **Lebenszyklus** geben Sie an, wann die Zuweisung eines Benutzers für das Zugriffspaket abläuft. Sie können auch angeben, ob Benutzer ihre Zuweisungen verlängern können.

1. Legen Sie im Abschnitt **Ablauf** die Option **Zugriffspaket läuft ab** auf **An Datum**, **Anzahl Tage**, **Anzahl der Stunden** oder **Nie** fest.

    Wählen Sie für **An Datum** ein Ablaufdatum in der Zukunft aus.

    Geben Sie für **Anzahl Tage** eine Zahl zwischen 0 und 3.660 Tagen an.

    Geben Sie unter **Anzahl der Stunden** eine Stundenzahl an.

    Basierend auf Ihrer Auswahl läuft die Zuweisung eines Benutzers zum Zugriffspaket an einem bestimmten Datum, nach einer bestimmten Anzahl von Tagen nach der Genehmigung oder nie ab.

1. Klicken Sie auf **Erweiterte Ablaufeinstellungen anzeigen**, um zusätzliche Einstellungen anzuzeigen.

    ![Zugriffspaket – Lebenszyklusablaufeinstellungen](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Um Benutzern zu erlauben, ihre Zuweisungen zu erweitern, setzen Sie **Benutzern die Verlängerung des Zugriffs erlauben** auf **Ja**.

    Wenn Verlängerungen in der Richtlinie zulässig sind, erhält der Benutzer 14 Tage und auch einen Tag vor Ablauf seiner Zugriffspaketzuweisung eine E-Mail, die ihn auffordert, die Zuweisung verlängern zu lassen. Der Benutzer muss sich noch immer im Gültigkeitsbereich der Richtlinie befinden, wenn er eine Verlängerung anfordert. Wenn die Richtlinie ein explizites Enddatum für Zuweisungen umfasst und der Benutzer eine Anforderung zum Verlängern des Zugriffs sendet, muss das Verlängerungsdatum in der Anforderung vor dem Ablauf der Zuweisungen liegen, der in der Richtlinie definiert ist, durch die dem Benutzer Zugriff auf das Zugriffspaket gewährt wurde. Wenn die Richtlinie beispielsweise angibt, dass Zuweisungen am 30. Juni ablaufen sollen, ist die maximale Verlängerung, die ein Benutzer anfordern kann, der 30. Juni.

    Wenn der Zugriff eines Benutzers verlängert wird, kann er das Zugriffspaket nach dem angegebenen Verlängerungsdatum (Datum, das in der Zeitzone des Benutzers festgelegt ist, der die Richtlinie erstellt hat) nicht mehr anfordern.

1. Wenn eine Genehmigung zum Gewähren einer Verlängerung erforderlich sein soll, legen Sie **Genehmigung erforderlich, um Verlängerung zu gewähren** auf **Ja** fest.

    Es werden die auf der Registerkarte **Anforderungen** festgelegten Genehmigungseinstellungen verwendet.

1. Klicken Sie auf **Weiter** oder **Aktualisieren**.
