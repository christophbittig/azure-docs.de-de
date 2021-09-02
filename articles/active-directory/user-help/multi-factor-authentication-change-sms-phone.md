---
title: Häufige Probleme bei der zweistufigen Überprüfung über SMS – Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein anderes mobiles Gerät als Methode für die zweistufige Überprüfung einrichten.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 07/28/2021
ms.author: curtand
ms.openlocfilehash: 2589fee3b974940a4ca60ad2e2f9861d8a5f69d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349555"
---
# <a name="common-problems-with-text-message-two-step-verification"></a>Häufige Probleme bei der zweistufigen Überprüfung über SMS

Der Empfang eines Überprüfungscodes in einer SMS ist eine gängige Überprüfungsmethode für die zweistufige Überprüfung. Wenn Sie keinen Code erwarten oder einen Code auf dem falschen Telefon erhalten, verwenden Sie die folgenden Schritte, um dieses Problem zu beheben.  

> [!Note]
> Wenn Ihre Organisation nicht zulässt, dass Sie eine SMS zur Überprüfung erhalten, müssen Sie eine andere Methode auswählen oder sich an Ihren Administrator wenden, um weitere Hilfe zu erhalten.

## <a name="if-you-received-the-code-on-the-wrong-phone"></a>Wenn Sie den Code auf dem falschen Telefon erhalten haben

1. Melden Sie sich unter **Meine Sicherheitsinformationen** an, um Ihre Sicherheitsinformationen zu verwalten.

1. Wählen Sie auf der Seite **Sicherheitsinformationen** in der Liste der registrierten Authentifizierungsmethoden die Telefonnummer aus, die Sie ändern möchten, und wählen Sie dann **Ändern** aus.

1. Wählen Sie Ihr Land oder Ihre Region für Ihre neue Nummer aus, und geben Sie dann Ihre Mobiltelefonnummer ein.

1. Wählen Sie **Senden Sie mir einen Code, um Prüfcodes per SMS zu erhalten** und dann **Weiter** aus.

1. Geben Sie den Prüfcode aus der SMS von Microsoft ein, wenn Sie dazu aufgefordert werden, und wählen Sie **Weiter** aus.

1. Wenn Sie benachrichtigt werden, dass Ihr Telefon erfolgreich registriert wurde, wählen Sie **Fertig** aus.

## <a name="if-you-receive-a-code-unexpectedly"></a>Wenn Sie unerwartet einen Code erhalten

### <a name="if-you-already-registered-your-phone-number-for-two-step-verification"></a>Wenn Sie Ihre Telefonnummer bereits für die zweistufige Überprüfung registriert haben

Das Empfangen einer unerwarteten SMS kann bedeuten, dass jemand Ihr Kennwort kennt und versucht, Ihr Konto zu übernehmen. Ändern Sie Ihr Kennwort sofort, und benachrichtigen Sie den Administrator Ihrer Organisation darüber, was passiert ist.

### <a name="if-you-never-registered-your-phone-number-for-two-step-verification"></a>Wenn Sie Ihre Telefonnummer noch nie für die zweistufige Überprüfung registriert haben

Sie können auf die SMS mit `STOP` im Text der SMS antworten. Diese Nachricht verhindert, dass der Anbieter in Zukunft Nachrichten an Ihre Telefonnummer sendet. Möglicherweise müssen Sie auf ähnliche Nachrichten mit unterschiedlichen Codes antworten.  

Wenn Sie jedoch bereits die zweistufige Überprüfung verwenden, wird durch das Senden dieser Nachricht verhindert, dass Sie diese Telefonnummer für die Anmeldung verwenden. Wenn Sie erneut SMS-Nachrichten empfangen möchten, antworten Sie auf die ursprüngliche SMS mit `START` im Text.

## <a name="next-steps"></a>Nächste Schritte

- [Hilfe bei der zweistufigen Überprüfung](multi-factor-authentication-end-user-troubleshoot.md)
- [Einrichten eines Mobiltelefons als Methode für die zweistufige Überprüfung](multi-factor-authentication-setup-phone-number.md)