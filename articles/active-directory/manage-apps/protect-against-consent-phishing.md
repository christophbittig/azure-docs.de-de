---
title: Schutz vor Einwilligungsphishing
titleSuffix: Azure AD
description: Erfahren Sie mehr über Möglichkeiten zum Schutz vor Phishingangriffen bei App-basierten Einwilligungen mit Azure AD.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: tilarso
ms.openlocfilehash: cad0102bf76cd63553049045ea55e0e7fb8f16d0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550682"
---
# <a name="protecting-against-consent-phishing"></a>Schutz vor Einwilligungsphishing

Die Produktion ist nicht mehr auf private Netzwerke beschränkt, und die Arbeit hat sich erheblich in Richtung Clouddienste verlagert. Cloudanwendungen ermöglichen Mitarbeitern zwar, remote produktiv zu sein, Angreifer können jedoch auch anwendungsbasierte Angriffe nutzen, um Zugriff auf wertvolle Unternehmensdaten zu erhalten. Möglicherweise sind Sie bereits mit Angriffen auf Benutzer vertraut, z. B. dem E-Mail-Phishing oder dem Kompromittieren von Anmeldeinformationen. ***Einwilligungsphishing*** ist ein weiterer Bedrohungsvektor, den Sie beachten sollten.
In diesem Artikel wird beschrieben, was Einwilligungsphishing ist, was Microsoft zu Ihrem Schutz unternimmt und welche Schritte Organisationen ergreifen können, um die Sicherheit zu gewährleisten.

## <a name="what-is-consent-phishing"></a>Was ist Einwilligungsphishing?

Durch Phishingangriffe bei der Einwilligung werden Benutzer dazu verleitet, böswilligen Cloud-Apps Berechtigungen zu gewähren. Diese böswilligen Apps können dann Zugriff auf die legitimen Clouddienste und -daten der Benutzer erhalten. Im Gegensatz zur Kompromittierung von Anmeldeinformationen zielen die *Akteure hinter dieser Bedrohung* auf Benutzer ab, damit diese ihnen direkten Zugriff auf ihre persönlichen Daten oder die ihrer Organisation gewähren. Auf dem Einwilligungsbildschirm werden alle Berechtigungen angezeigt, die der App gewährt werden. Da die Anwendung von einem legitimen Anbieter gehostet wird (z. B. Microsoft Identity Platform), akzeptieren ahnungslose Benutzer die Bedingungen oder wählen einfach *Akzeptieren* aus. Damit gewähren sie jedoch einer böswilligen Anwendung die angeforderten Berechtigungen für die Daten des Benutzers oder der Organisation.

:::image type="content" source="./media/protect-consent-phishing/permissions-requested.png" alt-text="Screenshot des Fensters „Angeforderte Berechtigungen“ mit der Aufforderung zur Einwilligung durch den Benutzer":::

*Ein Beispiel für eine OAuth-App, die Zugriff auf eine Vielzahl von Berechtigungen anfordert*

## <a name="mitigating-consent-phishing-attacks-using-azure-ad"></a>Vermindern von Angriffen zum Einwilligungsphishing mit Azure AD

Administratoren, Benutzer oder Microsoft-Sicherheitsexperten können OAuth-Anwendungen kennzeichnen, die sich verdächtig zu verhalten scheinen. Eine so gekennzeichnete Anwendung wird von Microsoft überprüft, um festzustellen, ob die App gegen Vertragsbedingungen verstößt. Wenn der Verstoß bestätigt wird, deaktiviert Azure AD die Anwendung und verhindert die weitere Verwendung in sämtlichen Microsoft-Diensten.

Wenn Azure AD eine OAuth-Anwendung deaktiviert, passiert Folgendes:
- Die böswillige Anwendung und die zugehörigen Dienstprinzipale werden vollständig deaktiviert. Alle neuen Tokenanforderungen oder Anforderungen für Aktualisierungstoken werden abgelehnt, vorhandene Zugriffstoken bleiben jedoch bis zu ihrem Ablauf gültig.
- Die Deaktivierung wird über die *disabledByMicrosoftStatus*-Eigenschaft verfügbar gemacht, die für die zugehörigen Ressourcentypen der [Anwendung](/graph/api/resources/application?view=graph-rest-1.0&preserve-view=true) und des [Dienstprinzipals](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true) in Microsoft Graph gilt.
- Globale Administratoren, in deren Organisation möglicherweise ein Benutzer vor der Deaktivierung durch Microsoft einer Anwendung zugestimmt hat, sollten eine E-Mail mit den ergriffenen Maßnahmen sowie den empfohlenen Schritten erhalten, mit denen sie ihren Sicherheitsstatus untersuchen und verbessern können.

## <a name="recommended-response-and-remediation"></a>Empfohlene Reaktion und Wartung

Wenn Ihre Organisation von einer durch Microsoft deaktivierten Anwendung betroffen ist, sollten Sie die folgenden Sofortmaßnahmen durchführen, um die Sicherheit in Ihrer Umgebung sicherzustellen:

1. Untersuchen Sie die Anwendungsaktivitäten der deaktivierten Anwendung, einschließlich:
    - Die von der Anwendung angeforderten delegierten Berechtigungen oder Anwendungsberechtigungen
    - Die Azure AD-Überwachungsprotokolle für Aktivitäten durch die Anwendung und die Anmeldeaktivität für Benutzer, die zur Verwendung der Anwendung autorisiert sind
1. Lesen Sie den [Leitfaden zum Schutz vor unzulässigen Einwilligungen](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants), und implementieren Sie ihn in Microsoft-Cloudprodukten, einschließlich der Überwachung von Berechtigungen und Einwilligungen für die deaktivierte Anwendung oder andere verdächtige Apps, die während der Überprüfung gefunden wurden.
1. Implementieren Sie die unten beschriebenen bewährten Methoden für die Härtung gegen Einwilligungsphishing.


## <a name="best-practices-for-hardening-against-consent-phishing-attacks"></a>Bewährte Methoden für die Härtung gegen Angriffe durch Einwilligungsphishing

Microsoft möchte Administratoren volle Kontrolle über die richtigen Erkenntnisse und Funktionen zur Verfügung stellen, um zu steuern, wie Anwendungen in Organisationen zugelassen und verwendet werden. Die Angreifer werden sich zwar nie nachlassen, aber es gibt Schritte, mit denen Organisationen ihren Sicherheitsstatus verbessern können. Zu den bewährten Methoden gehören:

* Informieren Sie Ihre Organisation darüber, wie unser Framework aus Berechtigungen und Einwilligungen funktioniert.
    - Machen Sie sich mit den Daten und Berechtigungen vertraut, nach denen eine Anwendung fragt. Informieren Sie sich außerdem darüber, wie  [Berechtigungen und Einwilligungen](../develop/v2-permissions-and-consent.md) auf unserer Plattform funktionieren.
    - Stellen Sie sicher, dass Administratoren wissen, wie  [Einwilligungsanforderungen verwaltet und ausgewertet werden](./manage-consent-requests.md).
    - [Überprüfen Sie Apps und zugelassene Berechtigungen](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) in Ihrer Organisation regelmäßig, um sicherzustellen, dass die verwendeten Anwendungen nur auf die benötigten Daten zugreifen und das Prinzip der geringsten Rechte einhalten.
* Erkennen und Blockieren gängiger Taktiken beim Einwilligungsphishing
    - Prüfen Sie auf schlechte Rechtschreibung und Grammatik. Wenn eine E-Mail oder der Einwilligungsbildschirm der Anwendung Fehler hinsichtlich Rechtschreibung und Grammatik aufweist, handelt es sich wahrscheinlich um eine verdächtige Anwendung. In diesem Fall können Sie dies direkt mit dem Link *Hier melden* auf der [Einwilligungsaufforderung](../develop/application-consent-experience.md#building-blocks-of-the-consent-prompt) melden. Microsoft untersucht dann, ob es sich um eine böswillige Anwendung handelt, und deaktiviert sie, wenn sich der Verdacht bestätigt.
    - Verlassen Sie sich nicht auf App-Namen und Domänen-URLs als Quelle für die Authentizität. Angreifer fälschen gerne App-Namen und Domänen, damit sie den Anschein erwecken, dass sie von legitimen Dienstanbietern oder Unternehmen stammen, und die Einwilligung für die böswillige App fördern. Überprüfen Sie stattdessen die Quelle der Domänen-URL, und verwenden Sie nach Möglichkeit Anwendungen von [verifizierten Herausgebern](../develop/publisher-verification-overview.md).
    - Blockieren Sie [Phishing-E-Mails zur Einwilligung mit Microsoft Defender für Office 365](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies#impersonation-settings-in-anti-phishing-policies-in-microsoft-defender-for-office-365), indem Sie sich vor Phishingkampagnen schützen, bei denen ein Angreifer die Identität eines bekannten Benutzers in Ihrer Organisation annimmt.
    - Konfigurieren Sie Microsoft Defender für Cloud-Apps-Richtlinien wie  [Aktivitätsrichtlinien](/cloud-app-security/user-activity-policies),  [Anomalieerkennung](/cloud-app-security/anomaly-detection-policy) und  [OAuth-App-Richtlinien](/cloud-app-security/app-permission-policy) , um ungewöhnliche Anwendungsaktivitäten in Ihrer Organisation zu verwalten und Maßnahmen zu ergreifen.
    - Untersuchen Sie Angriffen zum Einwilligungsphishing, und suchen Sie speziell danach. Nutzen Sie dazu den Leitfaden zum [erweiterten Hunting mit Microsoft 365 Defender](/microsoft-365/security/defender/advanced-hunting-overview).
* Lassen Sie den Zugriff auf vertrauenswürdige Apps zu, und schützen Sie sich vor Apps, denen Sie nicht vertrauen.
    - Verwenden Sie Anwendungen, die vom Herausgeber überprüft wurden. Die [Herausgeberüberprüfung](../develop/publisher-verification-overview.md) unterstützt Administratoren und Endbenutzer durch einen von Microsoft unterstützten Überprüfungsvorgang dabei, die Authentizität von Anwendungsentwicklern zu verstehen.
    - [Konfigurieren Sie Richtlinien für die Benutzereinwilligung](./configure-user-consent.md?tabs=azure-portal), um Benutzern nur bei bestimmten Anwendungen, denen Sie vertrauen (z. B. Anwendungen, die von Ihrer Organisation oder von verifizierten Herausgebern entwickelt wurden), die Einwilligung zu gestatten.
    - Erstellen Sie proaktive [App-Governancerichtlinien](/microsoft-365/compliance/app-governance-manage-app-governance), um das Verhalten von Drittanbieter-Apps auf der Microsoft 365-Plattform zu überwachen und allgemeine verdächtige App-Verhaltensweisen zu erkennen.

## <a name="next-steps"></a>Nächste Schritte

* [Untersuchung der Zuweisung der App-Einwilligung](/security/compass/incident-response-playbook-app-consent)
* [Verwalten des Zugriffs auf Apps](./what-is-access-management.md)
* [Einschränken von Benutzereinwilligungsvorgängen in Azure AD](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations)
