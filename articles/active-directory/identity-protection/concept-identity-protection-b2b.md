---
title: Identity Protection und B2B-Benutzer – Azure Active Directory
description: Verwenden von Identity Protection für B2B-Benutzer
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/03/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3a5efbb9c990173c7012b9ff6f938fed33f1ad
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734556"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection und B2B-Benutzer

Identity Protection erkennt kompromittierte Anmeldeinformationen für Azure AD-Benutzer. Wenn Ihre Anmeldeinformationen als kompromittiert erkannt werden, bedeutet dies, dass eine andere Person möglicherweise Ihr Kennwort kennt und es unrechtmäßig verwenden kann. Um weitere Risiken für Ihr Konto zu vermeiden, ist es wichtig, Ihr Kennwort sicher zurückzusetzen, damit der bösartige Akteur Ihr kompromittiertes Kennwort nicht mehr verwenden kann. Identity Protection kennzeichnet möglicherweise kompromittierte Konten als „gefährdet“.

Sie können die Anmeldeinformationen Ihrer Organisation verwenden, um sich als Gast bei einer anderen Organisation anzumelden. Dieser Prozess wird als [Business-to-Business oder B2B Collaboration](../external-identities/what-is-b2b.md) bezeichnet. Organisationen können Richtlinien konfigurieren, um die Anmeldung von Benutzern zu blockieren, wenn deren Anmeldeinformationen als [gefährdet](concept-identity-protection-risks.md) gelten. Wenn Ihr Konto gefährdet ist und Sie für die Anmeldung bei einer anderen Organisation als Gast gesperrt sind, können Sie Ihr Konto mithilfe der folgenden Schritte möglicherweise selbst bereinigen. Wenn Ihre Organisation die Self-Service-Kennwortzurücksetzung nicht aktiviert hat, muss Ihr Administrator Ihr Konto manuell bereinigen.

## <a name="how-to-unblock-your-account"></a>Entsperren Ihres Kontos 

Wenn Sie versuchen, sich bei einer anderen Organisation als Gast anzumelden und aufgrund eines Risikos gesperrt werden, wird die folgende Sperrmeldung angezeigt: „Ihr Konto wurde gesperrt. Für Ihr Konto wurde eine verdächtige Aktivität festgestellt.“ 

![Gastkonto gesperrt. Wenden Sie sich an den Administrator Ihrer Organisation.](./media/concept-identity-protection-b2b/risky-guest-user-blocked.png)

Wenn Ihre Organisation die Self-Service-Kennwortzurücksetzung aktiviert hat, können Sie diese Option verwenden, um Ihr Konto zu entsperren und den sicheren Zustand Ihrer Anmeldeinformationen wiederherzustellen.
1. Navigieren Sie zum [Portal für die Kennwortzurücksetzung](https://passwordreset.microsoftonline.com/), und initiieren Sie die Kennwortzurücksetzung. Wenn die Self-Service-Kennwortzurücksetzung für Ihr Konto nicht aktiviert ist und Sie nicht fortfahren können, wenden Sie sich mit den [unten angegebenen](#how-to-remediate-a-users-risk-as-an-administrator) Informationen an Ihren IT-Administrator.
2. Wenn die Self-Service-Kennwortzurücksetzung für Ihr Konto aktiviert ist, werden Sie aufgefordert, Ihre Identität mithilfe von Sicherheitsmethoden zu überprüfen, bevor Sie Ihr Kennwort ändern. Weitere Informationen finden Sie im Artikel [Zurücksetzen des Kennworts für Ihr Geschäfts-, Schul- oder Unikonto](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e).
3. Nachdem Sie Ihr Kennwort erfolgreich und sicher zurückgesetzt haben, wird Ihr Benutzerrisiko bereinigt. Sie können jetzt erneut versuchen, sich als Gastbenutzer anzumelden.

Wenn Sie nach dem Zurücksetzen Ihres Kennworts aufgrund eines Risikos weiterhin als Gast gesperrt werden, wenden Sie sich an den IT-Administrator Ihrer Organisation.

## <a name="how-to-remediate-a-users-risk-as-an-administrator"></a>Bereinigen des Risikos eines Benutzers als Administrator

Identity Protection erkennt Risikobenutzer für Azure AD-Mandanten automatisch. Wenn Sie die Identity Protection-Berichte vorher nicht überprüft haben, enthalten diese möglicherweise eine große Anzahl von Benutzern mit Risiken. Da Ressourcenmandanten Benutzerrisikorichtlinien auf Gastbenutzer anwenden können, ist es möglich, dass Ihre Benutzer aufgrund von Risiken gesperrt werden, auch wenn sie sich zuvor ihres Risikozustands nicht bewusst waren. Wenn Ihr Benutzer meldet, dass er aufgrund eines Risikos als Gastbenutzer in einem anderen Mandanten gesperrt wurde, ist es wichtig, den Benutzer zu „rehabilitieren“, um sein Konto zu schützen und die Zusammenarbeit zu ermöglichen. 

### <a name="reset-the-users-password"></a>Das Kennwort des Benutzers zurücksetzen

Suchen Sie im Azure AD-Sicherheitsmenü im [Bericht „Riskante Benutzer“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers) mithilfe des Filters „Benutzer“ nach dem betroffenen Benutzer. Wählen Sie den betroffen Benutzer im Bericht aus, und klicken Sie auf der oberen Symbolleiste auf „Kennwort zurücksetzen“. Dem Benutzer wird ein temporäres Kennwort zugewiesen, das bei der nächsten Anmeldung geändert werden muss. Durch diesen Vorgang wird das Benutzerrisiko bereinigt, und die Anmeldeinformationen des Benutzers werden wieder in einen sicheren Zustand versetzt.

### <a name="manually-dismiss-users-risk"></a>Manuelles Verwerfen des Benutzerrisikos

Wenn die Kennwortzurücksetzung im Azure AD-Portal keine Option für Sie ist, haben Sie die Möglichkeit, das Benutzerrisiko manuell zu verwerfen. Dieser Prozess führt dazu, dass der Benutzer nicht mehr gefährdet ist, hat aber keine Auswirkungen auf das vorhandene Kennwort. Es ist wichtig, dass Sie das Kennwort des Benutzers mit den verfügbaren Mitteln ändern, damit die Identität wieder in einen sicheren Zustand versetzt wird. 

Um das Benutzerrisiko zu verwerfen, wechseln Sie im Azure AD-Sicherheitsmenü zum [Bericht „Riskante Benutzer“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers). Suchen Sie mithilfe des Filters „Benutzer“ nach dem betroffen Benutzer, und klicken Sie auf den Benutzer. Klicken Sie auf der oberen Symbolleiste auf die Option „Benutzerrisiko verwerfen“. Es kann einige Minuten dauern, bis diese Aktion abgeschlossen ist und der Benutzerrisikostatus im Bericht aktualisiert wird.

Unter [Was ist Identity Protection?](overview-identity-protection.md) finden Sie weitere Informationen zu Identity Protection.

## <a name="how-does-identity-protection-work-for-b2b-users"></a>Wie funktioniert Identity Protection für B2B-Benutzer?

Das Benutzerrisiko für Benutzer von B2B-Zusammenarbeit wird in deren Basisverzeichnis ausgewertet. Das Echtzeit-Anmelderisiko für diese Benutzer wird im Ressourcenverzeichnis ausgewertet, wenn sie versuchen, auf die Ressource zuzugreifen. Azure AD B2B-Zusammenarbeit bietet Organisationen die Möglichkeit, risikobasierte Richtlinien für B2B-Benutzer mithilfe von Identity Protection zu erzwingen. Diese Richtlinien können auf zwei Arten konfiguriert werden:

- Administratoren können die integrierten risikobasierten Richtlinien von Identity Protection konfigurieren, die für alle Apps mit Gastbenutzern gelten.
- Administratoren können eigene Richtlinien für den bedingten Zugriff konfigurieren und das Anmelderisiko als Bedingung verwenden, die auch Gastbenutzer umfasst.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Einschränkungen von Identity Protection für Benutzer von B2B-Zusammenarbeit

Es gibt Einschränkungen bei der Implementierung von Identity Protection für Benutzer von B2B-Zusammenarbeit in einem Ressourcenverzeichnis, weil sich ihre Identität im eigenen Basisverzeichnis befindet. Die wichtigsten Einschränkungen sind folgende:

- Wenn ein Gastbenutzer die Richtlinie zum Benutzerrisiko von Identity Protection auslöst, um eine Kennwortzurücksetzung zu erzwingen, **wird er gesperrt**. Diese Sperrung ist darauf zurückzuführen, dass Kennwörter nicht im Ressourcenverzeichnis zurückgesetzt werden können.
- **Gastbenutzer werden im Bericht „Riskante Benutzer“ nicht angezeigt**. Diese Einschränkung ist darauf zurückzuführen, dass die Risikobewertung im Basisverzeichnis des B2B-Benutzers erfolgt.
- Administratoren **können einen riskanten Benutzer von B2B-Zusammenarbeit in ihrem Ressourcenverzeichnis nicht verwerfen oder bereinigen**. Diese Einschränkung ist darauf zurückzuführen, dass Administratoren im Ressourcenverzeichnis keinen Zugriff auf das Basisverzeichnis des B2B-Benutzers haben.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Warum kann ich riskante Benutzer von B2B-Zusammenarbeit in meinem Verzeichnis nicht bereinigen?

Die Risikobewertung und Bereinigung für B2B-Benutzer erfolgt in deren Basisverzeichnis. Aus diesem Grund werden die Gastbenutzer im Bericht „Riskante Benutzer“ im Ressourcenverzeichnis nicht angezeigt und Administratoren im Ressourcenverzeichnis können keine sichere Kennwortzurücksetzung für diese Benutzer erzwingen.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Was kann ich tun, wenn ein Benutzer von B2B-Zusammenarbeit aufgrund einer risikobasierten Richtlinie in meiner Organisation gesperrt wurde?

Wenn ein riskanter B2B-Benutzer in Ihrem Verzeichnis durch die risikobasierte Richtlinie gesperrt wird, muss der Benutzer dieses Risiko im eigenen Basisverzeichnis bereinigen. Benutzer können ihr Risiko bereinigen, indem sie eine sichere Kennwortzurücksetzung im eigenen Basisverzeichnis ausführen, [wie oben beschrieben](#how-to-unblock-your-account). Wenn die Self-Service-Kennwortzurücksetzung im Basisverzeichnis der Benutzer nicht aktiviert ist, müssen diese sich an das IT-Personal der eigenen Organisation wenden, damit ein Administrator das Risiko manuell verwirft oder das Kennwort zurücksetzt.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Wie verhindere ich, dass Benutzer von B2B-Zusammenarbeit durch risikobasierte Richtlinien betroffen sind?

Durch das Ausschließen von B2B-Benutzern aus den risikobasierten Richtlinien für den bedingten Zugriff Ihrer Organisation wird verhindert, dass B2B-Benutzer von der Risikobewertung betroffen sind oder dadurch gesperrt werden. Zum Ausschließen dieser B2B-Benutzer erstellen Sie eine Gruppe in Azure AD, die alle Gastbenutzer Ihrer Organisation enthält. Fügen Sie diese Gruppe dann als Ausschlusselement für Ihre integrierten Identity Protection-Benutzerrisiko- und -Anmelderisikorichtlinien sowie für alle Richtlinien für bedingten Zugriff hinzu, die das Anmelderisiko als Bedingung verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](../external-identities/what-is-b2b.md)