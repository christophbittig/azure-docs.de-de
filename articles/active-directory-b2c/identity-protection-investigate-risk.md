---
title: Untersuchen eines Risikos mit Identity Protection in Azure Active Directory B2C
description: Erfahren Sie, wie Sie Risikobenutzer und Erkennungen in Azure AD B2C Identity Protection untersuchen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ae32e78ea7d20b2e62ee91aa5182a7409f4ab0bb
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036955"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Untersuchen eines Risikos mit Identity Protection in Azure AD B2C

Identity Protection bietet eine kontinuierliche Risikoerkennung für Ihren Azure AD B2C-Mandanten. Damit können Organisationen identitätsbasierte Risiken entdecken, untersuchen und beheben. Identity Protection enthält Risikoberichte, die zur Untersuchung von Identitätsrisiken in Azure AD B2C-Mandanten verwendet werden können. In diesem Artikel erfahren Sie, wie Sie Risiken untersuchen und mindern.

## <a name="overview"></a>Übersicht

Azure AD B2C Identity Protection stellt zwei Berichte bereit. Im Bericht *Risikobenutzer* finden Administratoren Informationen zu gefährdeten Benutzern sowie Details zu Erkennungen. Der Bericht *Risikoerkennungen* enthält Informationen zu jeder Risikoerkennung, einschließlich Typ, andere gleichzeitig ausgelöste Risiken, Ort des Anmeldeversuchs und mehr.

Jeder Bericht wird mit einer Liste aller Erkennungen für den Zeitraum gestartet, der oben im Bericht angezeigt wird. Berichte können über die Filter am oberen Rand des Berichts gefiltert werden. Administratoren können die Daten herunterladen oder mit [MS Graph-API und Microsoft Graph PowerShell SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) fortlaufend exportieren.

## <a name="service-limitations-and-considerations"></a>Beschränkungen des Diensts und Überlegungen

Beachten Sie bei der Verwendung von Identity Protection Folgendes:

- Identity Protection ist standardmäßig aktiviert.
- Identity Protection ist sowohl für lokale Identitäten als auch für Identitäten in sozialen Netzwerken (z. B. Google oder Facebook) verfügbar. Für Identitäten in sozialen Netzwerken muss der bedingte Zugriff aktiviert sein. Die Erkennung ist begrenzt, weil die Anmeldeinformationen des Social Media-Kontos vom externen Identitätsanbieter verwaltet werden.
- In Azure AD B2C-Mandanten steht nur ein Teil der [Azure AD Identity Protection-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md) zur Verfügung. Für Azure AD B2C werden folgende Risikoerkennungen unterstützt:  

|Risikoerkennungstyp  |BESCHREIBUNG  |
|---------|---------|
| Ungewöhnlicher Ortswechsel     | Anmeldung von einem ungewöhnlichen Standort, basierend auf den letzten Anmeldevorgängen des Benutzers.        |
|Anonyme IP-Adresse     | Anmeldung von einer anonymen IP-Adresse (z. B. Tor-Browser, anonymisierte VPNs).        |
|Mit Schadsoftware verknüpfte IP-Adresse     | Anmeldung von einer mit Schadsoftware verknüpften IP-Adresse.         |
|Ungewöhnliche Anmeldeeigenschaften     | Anmeldung mit Eigenschaften, die für den angegebenen Benutzer in letzter Zeit nicht verwendet wurden.        |
|Benutzergefährdung durch Administrator bestätigt    | Ein Administrator hat angegeben, dass ein Benutzer kompromittiert wurde.             |
|Kennwortspray     | Anmeldung über einen Kennwortspray-Angriff.      |
|Azure AD Threat Intelligence     | Auf der Grundlage der internen und externen Quellen für Bedrohungsdaten von Microsoft wurde ein bekanntes Angriffsmuster identifiziert        |

## <a name="pricing-tier"></a>Tarif

Azure AD B2C Premium P2 ist für einige Identity Protection-Features erforderlich. [Stellen Sie Ihren Azure AD B2C-Tarif auf „Premium P2“ um](./billing.md) (falls erforderlich). In der folgenden Tabelle sind die Funktionen von Identity Protection und der erforderliche Tarif zusammengefasst.  

|Funktion   |P1   |P2|
|----------|:-----------:|:------------:|
|Bericht „Riskante Benutzer“     |&#x2713; |&#x2713; |
|Detaillierter Bericht „Risikobenutzer“  | |&#x2713; |
|Bericht „Risikobenutzer“ mit Behebungen    | &#x2713; |&#x2713; |
|Bericht „Risikoerkennung“   |&#x2713;|&#x2713;|
|Detaillierter Bericht „Risikoerkennung“  ||&#x2713;|
|Bericht herunterladen |  &#x2713;| &#x2713;|
|Zugriff auf MS Graph-API |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Untersuchen riskanter Benutzer

Mit den Informationen im Bericht „Riskante Benutzer“ können Administratoren folgendes ermitteln:

- Der **Risikozustand**, der anzeigt, welche Benutzer gefährdet sind (**Risiko**) und für welche Benutzer Risiken **bereinigt** oder **verworfen** wurden.
- Details zu Erkennungen
- Verlauf aller riskanten Anmeldungen
- Risikoverlauf
 
Administratoren können dann Aktionen für diese Ereignisse ausführen. Administratoren haben folgende Möglichkeiten:

- Benutzerkennwort festlegen
- Benutzergefährdung bestätigen
- Benutzerrisiko verwerfen
- Anmeldung eines Benutzers blockieren
- Mit Azure ATP weitere Untersuchungen ausführen

Ein Administrator kann auswählen, ob das Risiko eines Benutzers im Azure-Portal oder programmgesteuert über die Microsoft Graph-API unter [Benutzerrisiko ignorieren](/graph/api/riskyusers-dismiss?preserve-view=true&view=graph-rest-beta) verworfen werden soll. Es werden Administratorrechte benötigt, um das Risiko für einen Benutzer zu verwerfen. Die Bereinigung eines Risikos kann vom Risikobenutzer selbst oder von einem Administrator im Namen des Benutzers durchgeführt werden, z. B. per Kennwortzurücksetzung.

### <a name="navigating-the-risky-users-report"></a>Navigieren im Bericht „Risikobenutzer“

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Oder verwenden Sie das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie unter **Sicherheit** die Option **Risikobenutzer** aus.

   ![Riskante Benutzer](media/identity-protection-investigate-risk/risky-users.png)

    Werden einzelne Einträge ausgewählt, wird das Fenster „Details“ unterhalb der Erkennungen erweitert. In der Detailansicht können Administratoren die Erkennungen untersuchen und für jede Erkennung Aktionen ausführen.

    ![Risikobenutzer-Aktionen](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Bericht „Risikoerkennung“

Der Bericht „Risikoerkennungen“ enthält filterbare Daten der letzten 90 Tage (3 Monate).

Mit den Informationen im Bericht „Risikoerkennungen“ können Administratoren folgendes ermitteln:

- Informationen zu den einzelnen Risikoerkennungen, einschließlich des Typs.
- Andere, gleichzeitig ausgelöste Risiken.
- Ort des Anmeldeversuchs.

Administratoren können dann zum Risiko- oder Anmeldebericht des Benutzers zurückkehren, um basierend auf gesammelten Informationen Aktionen auszuführen.

### <a name="navigating-the-risk-detections-report"></a>Navigieren im Bericht „Risikoerkennungen“

1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Sicherheit** die Option **Risikoerkennung** aus.

   ![Risikoerkennungen](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von bedingtem Zugriff zu Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md)