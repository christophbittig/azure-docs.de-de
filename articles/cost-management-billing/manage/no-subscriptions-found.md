---
title: Fehler „Keine Abonnements gefunden“ – Anmeldung beim Azure-Portal
description: Bietet die Lösung für das Problem, das während der Anmeldung beim Azure-Portal die Fehlermeldung „Keine Abonnements gefunden“ angezeigt wird.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/28/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a90285db3b95402581ba917cef0833e93da91f44
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425783"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal"></a>Anmeldefehler „Keine Abonnements gefunden“ für das Azure-Portal

Wenn Sie versuchen, sich beim [Azure-Portal](https://portal.azure.com/) anzumelden, erhalten Sie möglicherweise die Fehlermeldung „Keine Abonnements gefunden“. Dieser Artikel bietet eine Lösung dieses Problems.

## <a name="symptom"></a>Symptom

Wenn Sie versuchen, sich beim [Azure-Portal](https://portal.azure.com/) anzumelden, erhalten Sie die Fehlermeldung „Keine Abonnements gefunden“.

## <a name="cause"></a>Ursache

Dieses Problem tritt auf, wenn Sie das falsche Verzeichnis ausgewählt haben oder Ihr Konto nicht über ausreichende Berechtigungen verfügt.

## <a name="solution"></a>Lösung

### <a name="scenario-error-message-is-received-in-the-azure-portal"></a>Szenario: Fehlermeldung wird im [Azure-Portal](https://portal.azure.com) empfangen

So beheben Sie dieses Problem:

* Vergewissern Sie sich, dass das richtige Azure-Verzeichnis ausgewählt ist, indem Sie oben rechts auf Ihr Konto klicken.

  ![Auswählen des Verzeichnisses oben rechts im Azure-Portal](./media/no-subscriptions-found/directory-switch.png)
* Wenn das richtige Azure-Verzeichnis ausgewählt ist, die Fehlermeldung jedoch immer noch ausgegeben wird, [weisen Sie Ihrem Konto die Besitzerrolle zu](../../role-based-access-control/role-assignments-portal.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
