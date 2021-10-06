---
title: Markieren einer App als durch den Herausgeber verifiziert – Microsoft Identity Platform | Azure
description: Hier wird beschrieben, wie Sie eine App als durch den Herausgeber verifiziert markieren. Wenn eine Anwendung als durch den Herausgeber verifiziert markiert ist, bedeutet dies, dass der Herausgeber seine Identität mithilfe eines Microsoft Partner Network-Kontos überprüft hat, für das der Überprüfungsvorgang abgeschlossen wurde, und dieses MPN-Konto seiner Anwendungsregistrierung zugeordnet hat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: bb1347b0dfcb2be5c485e503fb87c5a70dd7d30c
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153784"
---
# <a name="mark-your-app-as-publisher-verified"></a>Markieren Ihrer App als durch den Herausgeber verifiziert

Wenn eine App-Registrierung einen verifizierten Herausgeber hat, bedeutet dies, dass der Herausgeber der App seine Identität mithilfe seines Microsoft Partner Network (MPN)-Kontos [verifiziert](/partner-center/verification-responses) und dieses MPN-Konto mit seiner App-Registrierung verknüpft hat. In diesem Artikel wird beschrieben, wie Sie die [Herausgeberüberprüfung](publisher-verification-overview.md) ausführen.

## <a name="quickstart"></a>Schnellstart
Wenn Sie bereits bei Microsoft Partner Network (MPN) registriert sind und die [Voraussetzungen](publisher-verification-overview.md#requirements) erfüllt haben, können Sie sofort loslegen: 

1. Melden Sie sich über die [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) im [App-Registrierungsportal](https://aka.ms/PublisherVerificationPreview) an.

1. Wählen Sie eine App aus, und klicken Sie auf **Branding**. 

1. Klicken Sie auf **MPN-ID zum Überprüfen des Herausgebers hinzufügen**, und überprüfen Sie die aufgeführten Anforderungen.

1. Geben Sie Ihre MPN-ID ein, und klicken Sie auf **Überprüfen und speichern**.

Ausführlichere Informationen zu bestimmten Vorteilen und Anforderungen sowie häufig gestellte Fragen finden Sie in der [Übersicht](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Markieren Ihrer App als durch den Herausgeber verifiziert
Stellen Sie sicher, dass Sie die [Voraussetzungen](publisher-verification-overview.md#requirements) erfüllt haben, und führen Sie dann die folgenden Schritte aus, um Ihre App(s) als durch den Herausgeber verifiziert zu markieren.  

1. Stellen Sie sicher, dass Sie über die [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) bei einem Organisationskonto (Azure AD) angemeldet sind, das autorisiert ist, Änderungen an der/den App(s), die Sie als durch den Herausgeber verifiziert markieren möchten, und am MPN-Konto in Partner Center vorzunehmen.

    - In Azure AD muss dieser Benutzer ein Mitglied einer der folgenden [Rollen](../roles/permissions-reference.md) sein: „Anwendungsadministrator“, „Cloudanwendungsadministrator“ oder „Globaler Administrator“. 

    - In Partner Center muss dieser Benutzer über die folgenden [Rollen](/partner-center/permissions-overview) verfügen: „MPN-Administrator“, „Kontoadministrator“ oder „Globaler Administrator“ (dies ist eine freigegebene Rolle, die in Azure AD verwaltet wird). 

1. Navigieren Sie zum Blatt **App-Registrierungen**:  

1. Klicken Sie auf eine App, die Sie als durch den Herausgeber verifiziert markieren möchten, und öffnen Sie das Blatt **Branding**. 

1. Stellen Sie sicher, dass die [Herausgeberdomäne](howto-configure-publisher-domain.md) der App festgelegt ist. 

1. Sorgen Sie dafür, dass entweder die Herausgeberdomäne oder eine DNS-verifizierte [benutzerdefinierte Domäne](../fundamentals/add-custom-domain.md) des Mandanten mit der Domäne der bei der MPN-Kontoüberprüfung verwendeten E-Mal-Adresse übereinstimmt.

1. Klicken Sie unten auf der Seite auf **MPN-ID zum Überprüfen des Herausgebers hinzufügen**. 

1. Geben Sie Ihre **MPN-ID** ein. Diese MPN-ID muss für Folgendes verwendet werden: 

    - Ein gültiges Microsoft Partner Network-Konto, für das der Überprüfungsvorgang abgeschlossen wurde.  

    - Das globale Partnerkonto (Partner Global Account, PGA) für Ihre Organisation. 

1. Klicken Sie auf **Überprüfen und speichern**. 

1. Warten Sie, bis die Anforderung verarbeitet wurde. Dieser Vorgang kann einige Minuten dauern. 

1. Wenn die Überprüfung erfolgreich war, wird das Fenster für die Herausgeberüberprüfung geschlossen, und Sie kehren zum Blatt „Branding“ zurück. Neben dem überprüften **Anzeigenamen des Herausgebers** wird ein blauer Überprüfungsbadge angezeigt. 

1. Benutzer, die zur Zustimmung zu Ihrer App aufgefordert werden, sehen den Badge kurz nachdem der Prozess erfolgreich durchlaufen wurde. Allerdings kann es einige Zeit dauern, bis dies im gesamten System repliziert wird. 

1. Testen Sie diese Funktion, indem Sie sich bei Ihrer Anwendung anmelden und sich vergewissern, dass der Überprüfungsbadge auf dem Zustimmungsbildschirm angezeigt wird. Wenn Sie als Benutzer angemeldet sind, der dieser App bereits zugestimmt hat, können Sie den Abfrageparameter *prompt=consent* verwenden, um eine Zustimmungsaufforderung zu erzwingen. Dieser Parameter sollte nur zu Testzwecken verwendet und nie in den Anforderungen Ihrer App hartcodiert werden.

1. Wiederholen Sie diesen Vorgang nach Bedarf für alle weiteren Apps, für die der Badge angezeigt werden soll. Sie können Microsoft Graph verwenden, um dies in einem Massenvorgang schneller auszuführen. PowerShell-Cmdlets werden bald verfügbar sein. Weitere Informationen finden Sie unter [Ausführen von Microsoft Graph-API-Aufrufen](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls). 

Fertig! Lassen Sie uns wissen, ob Sie ggf. Feedback zum Vorgang, zu den Ergebnissen oder zur Funktion im Allgemeinen haben. 

## <a name="next-steps"></a>Nächste Schritte
Wenn Probleme auftreten, lesen Sie die [Informationen zur Problembehandlung](troubleshoot-publisher-verification.md).
