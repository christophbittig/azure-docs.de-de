---
title: 'Szenario: Verwenden von Azure Active Directory zum Schützen des Zugriffs auf SAP-Plattformen und -Anwendungen'
description: Leitfaden für Architekten und IT-Administratoren zum Sichern des Zugriffs auf SAP-Plattformen und -Anwendungen
services: active-directory
author: xstof
manager: alberts
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: christoc
ms.reviewer: ''
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 2c987a951cbaf3795757ab0b57e8dcea0eb47aee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063962"
---
# <a name="scenario---using-azure-active-directory-to-secure-access-to-sap-platforms-and-applications"></a>Szenario: Verwenden von Azure Active Directory zum Schützen des Zugriffs auf SAP-Plattformen und -Anwendungen

Dieses Dokument enthält Ratschläge zum technischen Entwurf und zur Konfiguration von SAP-Plattformen und -Anwendungen, wenn Azure Active Directory als primärer Authentifizierungsdienst für Benutzer verwendet wird.

## <a name="terminology-used-in-this-guide"></a>Terminologie in dieser Anleitung

| Abkürzung                                                                                                          | BESCHREIBUNG                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [BTP](https://www.sap.com/products/business-technology-platform.html)                                                 | SAP Business Technology Platform. Das gesamte Technologieangebot von SAP. Die meisten der hier beschriebenen SAP-Technologien sind Bestandteil von BTP. Die Produkte, die formell als SAP Cloud Platform bezeichnet werden, sind Bestandteil von SAP BTP. |
| [IAS](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US)                                       | SAP Cloud Identity Services – Identity Authentication Service. Der mehrinstanzenfähige cloudbasierte Identitätsanbieterdienst, der von SAP bereitgestellt wird. IAS unterstützt Benutzer bei der Authentifizierung bei ihren eigenen SAP-Dienstinstanzen.           |
| [IDS](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/d6a8db70bdde459f92f2837349f95090.html) | SAP ID Service. Eine Instanz von IAS, die von SAP für die Authentifizierung von Kunden und Partnern bei von SAP bereitgestellten PaaS- und SaaS-Diensten verwendet wird.                                                                                 |
| [IPS](https://help.sap.com/viewer/f48e822d6d484fa5ade7dda78b64d9f5/Cloud/en-US/2d2685d469a54a56b886105a06ccdae6.html) | SAP Cloud Identity Services – Identity Provisioning Service.  IPS unterstützt die Synchronisierung von Identitäten zwischen verschiedenen Speichern/Zielsystemen.                                                           |
| [XSUAA](https://blogs.sap.com/2019/01/07/uaa-xsuaa-platform-uaa-cfuaa-what-is-it-all-about/)                          | Extended Services for Cloud Foundry User Account and Authentication.  XSUAA ist ein mehrinstanzenfähiger OAuth-Autorisierungsserver innerhalb von SAP BTP.                                                                   |
| [CF](https://www.cloudfoundry.org/)                                                                                   | Cloud Foundry. Cloud Foundry ist die Umgebung, in der SAP sein Multi-Cloud-Angebot für BTP (AWS, Azure, GCP, Alibaba) erstellt hat.                                                                                      |
| [Fiori](https://www.sap.com/products/fiori/develop.html)                                                              | Die webbasierte Benutzeroberfläche von SAP (im Gegensatz zur desktopbasierten Benutzeroberfläche).                                                                                                                            |

## <a name="overview"></a>Überblick

Es gibt viele Dienste und Komponenten im SAP- und Microsoft-Technologiestapel, die in Szenarien für die Authentifizierung und Autorisierung von Benutzern eine Rolle spielen. Die wichtigsten Dienste sind im folgenden Diagramm aufgeführt.

![Übersicht über die SAP-Landschaft](./media/scenario-azure-first-sap-identity-integration/sap-landscape-overview.png)

Da es viele Variationen möglicher Szenarien gibt, die konfiguriert werden müssen, konzentrieren wir uns auf ein Szenario, in dem die Azure AD-Identität bevorzugt verwendet wird. Wir gehen von folgenden Annahmen aus:

- Sie möchten alle Ihre Identitäten zentral und nur über Azure AD steuern.
- Sie möchten den Wartungsaufwand so weit wie möglich reduzieren sowie Authentifizierung und App-Zugriff Microsoft- und SAP-übergreifend automatisieren.
- Die allgemeine Anleitung für Azure AD mit IAS gilt für Apps, die in BTP- und SAP SaaS-Apps bereitgestellt werden, die in IAS konfiguriert wurden. Darüber hinaus werden ggf. spezielle Empfehlungen für BTP (z. B. die Verwendung von Rollenzuordnungen über Azure AD Gruppen) und SAP SaaS-Apps (z. B. die Verwendung des Identitätsbereitstellungsdiensts für die rollenbasierte Autorisierung) bereitgestellt.
- Außerdem wird davon ausgegangen, dass Benutzer bereits in Azure AD und für alle SAP-Systeme bereitgestellt wurden, die voraussetzen, dass Benutzer bereitgestellt werden, damit sie funktionieren.  Dies gilt unabhängig davon, wie die Bereitstellung durchgeführt wurde: manuell, von einer lokalen Active Directory-Instanz über Azure AD Connect oder über Personalverwaltungssysteme wie SAP SuccessFactors. In diesem Dokument wird SuccessFactors daher wie jede andere Anwendung angesehen, bei der sich (vorhandene) Benutzer anmelden.  Die eigentliche Bereitstellung von Benutzern aus SuccessFactors in Azure AD wird nicht behandelt.

Basierend auf diesen Annahmen konzentrieren wir uns hauptsächlich auf die Produkte und Dienste, die im folgenden Diagramm dargestellt sind. Dabei handelt es sich um die verschiedenen Komponenten, die für die Authentifizierung und Autorisierung in einer cloudbasierten Umgebung besonders relevant sind.

![SAP-Dienste im Bereich](./media/scenario-azure-first-sap-identity-integration/sap-services-in-scope.png)

## <a name="recommendations"></a>Empfehlungen

### <a name="summary"></a>Zusammenfassung

- [1. Verwendung der Verbundauthentifizierung in SAP Business Technology Platform- und SAP SaaS-Anwendungen über SAP Identity Authentication Service](#1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service)
- [2. Verwendung von Azure AD für die Authentifizierung und von IAS/BTP für die Autorisierung](#2---use-azure-ad-for-authentication-and-iasbtp-for-authorization)
- [3. Verwendung von Azure AD-Gruppen für die Autorisierung über Rollensammlungen in IAS/BTP](#3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp)
- [4. Verwendung lediglich eines einzelnen BTP-Unterkontos für Anwendungen mit ähnlichen Identitätsanforderungen](#4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements)
- [5. Verwendung des IAS-Mandanten der Produktionsumgebung für die Authentifizierung und Autorisierung aller Endbenutzer](#5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization)
- [6. Definition eines Rolloverprozesses für SAML-Signaturzertifikate](#6---define-a-process-for-rollover-of-saml-signing-certificates)

### <a name="1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service"></a>1\. Verwendung der Verbundauthentifizierung in SAP Business Technology Platform- und SAP SaaS-Anwendungen über SAP Identity Authentication Service

#### <a name="context"></a>Kontext

Ihre Anwendungen in BTP können Identitätsanbieter über [Vertrauensstellungskonfigurationen](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/cb1bc8f1bd5c482e891063960d7acd78.html) verwenden, um Benutzer über das SAML 2.0-Protokoll zwischen BTP/XSUAA und dem Identitätsanbieter zu authentifizieren.  Beachten Sie, dass nur SAML 2.0 unterstützt wird, obwohl zwischen der Anwendung selbst und BTP/XSUAA das OpenID Connect-Protokoll verwendet wird (in diesem Kontext nicht relevant).

In BTP können Sie eine Vertrauensstellungskonfiguration für SAP ID Service (Standardeinstellung) einrichten. Wenn als autoritatives Benutzerverzeichnis jedoch Azure AD verwendet wird, können Sie einen **Verbund** einrichten, damit sich Benutzer mit ihren vorhandenen Azure AD-Konten anmelden können.

Zusätzlich zum Verbund können Sie optional auch die **Benutzerbereitstellung** so einrichten, dass Azure AD Benutzer vorab in BTP bereitgestellt werden. Es gibt dafür jedoch keine native Unterstützung (nur für Azure AD -> SAP Identity Authentication Service). Eine integrierte Lösung mit nativer Unterstützung wäre der BTP Identity Provisioning Service. Die Vorabbereitstellung von Benutzerkonten kann für Autorisierungszwecke nützlich sein (z. B. zum Hinzufügen von Benutzern zu Rollen). Abhängig von den jeweiligen Anforderungen können Sie dies jedoch auch mit Azure AD-Gruppen erreichen (siehe unten), sodass Sie u. U. überhaupt keine Benutzerbereitstellung benötigen.

Beim Einrichten der Verbundbeziehung gibt es mehrere Optionen:

- Sie können einen Verbund mit Azure AD direkt über BTP/XSUAA erstellen.
- Sie können einen Verbund mit IAS erstellen, der wiederum als Verbund mit Azure AD als Unternehmensidentitätsanbieter eingerichtet ist (auch als „SAML-Proxying“ bezeichnet).

Für SAP SaaS-Anwendungen wird IAS bereitgestellt und vorkonfiguriert, um das Onboarding von Endbenutzern zu vereinfachen.  (Beispiele hierfür sind SuccessFactors, Marketing Cloud, Cloud4Customer, Sales Cloud und andere.) Dieses Szenario ist weniger komplex, da IAS direkt mit der Ziel-App verbunden und nicht über einen Proxy mit XSUAA verbunden ist. In jedem Fall gelten für diese Einrichtung die gleichen Regeln wie für Azure AD mit IAS im Allgemeinen.

#### <a name="what-are-we-recommending"></a>Unsere Empfehlung

Wenn Sie Azure AD als autoritatives Benutzerverzeichnis verwenden, wird empfohlen, in BTP eine Vertrauensstellungskonfiguration zu IAS einzurichten.  IAS wiederum wird als Verbund mit Azure AD als Unternehmensidentitätsanbieter eingerichtet.

![SAP-Vertrauensstellungskonfiguration](./media/scenario-azure-first-sap-identity-integration/sap-trust-configuration.png)

Für die Vertrauensstellungskonfiguration in BTP wird empfohlen, die Erstellung von Schattenbenutzern bei der Anmeldung zu aktivieren.  Auf diese Weise erhalten Benutzer, die noch nicht in BTP erstellt wurden, automatisch ein Konto, wenn sie sich zum ersten Mal über IAS/Azure AD anmelden. Wenn diese Einstellung deaktiviert wird, dürfen sich nur vorab bereitgestellte Benutzer anmelden.

#### <a name="why-this-recommendation"></a>Gründe für diese Empfehlung

Bei Verwendung eines Verbunds können Sie die Vertrauensstellungskonfiguration auf der Ebene von BTP-Unterkonten definieren. In diesem Fall müssen Sie die Konfiguration für jedes andere verwendete Unterkonto wiederholen. Durch die Verwendung von IAS als zwischengelagerte Vertrauensstellungskonfiguration profitieren Sie von der zentralisierten unterkontoübergreifenden Konfiguration. Außerdem können Sie IAS-Features wie [risikobasierte Authentifizierung](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/bc52fbf3d59447bbb6aa22f80d8b6056.html) und zentrale [Anreicherung von Assertionsattributen](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/7124201682434efb946e1046fde06afe.html) verwenden. Um die Benutzerfreundlichkeit zu gewährleisten, sollten diese erweiterten Sicherheitsfeatures nur an einer einzigen Stelle erzwungen werden. Dies kann entweder IAS sein oder bei Beibehaltung von Azure AD als einzelner autoritativer Benutzerspeicher (wie in diesem Whitepaper vorausgesetzt) zentral über die [bedingte Zugriffsverwaltung](../conditional-access/overview.md) von Azure AD bewerkstelligt werden.

Beachten Sie, dass bei Verwendung von IAS jedes Unterkonto als „Anwendung“ gilt, obwohl im betreffenden Unterkonto eine oder mehrere Anwendungen bereitgestellt werden können.  In IAS kann jede derartige Anwendung für den Verbund mit demselben Unternehmensidentitätsanbieter eingerichtet werden (in diesem Fall Azure AD).

#### <a name="summary-of-implementation"></a>Zusammenfassung der Implementierung

In Azure AD:

- [Konfigurieren Sie Azure AD die nahtlose einmalige Anmeldung](../hybrid/how-to-connect-sso.md) (nahtlose SSO), bei der Benutzer automatisch auf ihren Unternehmensgeräten angemeldet werden, die mit dem Unternehmensnetzwerk verbunden sind. Wenn diese Funktion aktiviert ist, müssen Benutzer zur Anmeldung bei Azure AD nicht ihr Kennwort und in der Regel nicht einmal ihren Benutzernamen eingeben.

In Azure AD und IAS:

- Befolgen Sie die entsprechende Dokumentation, um Azure AD im Verbundmodus (Proxy) mit IAS zu verbinden [(SAP-Dokumentation](https://developers.sap.com/tutorials/cp-ias-azure-ad.html), [Microsoft-Dokumentation](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md)). Achten Sie auf die Einstellung `NameID` in Ihrer SSO-Konfiguration in Azure AD, da UPNs nicht unbedingt E-Mail-Adressen sind.
- Konfigurieren Sie die gebündelte Anwendung für die Verwendung von Azure AD. Rufen Sie dazu die Seite [Bedingte Authentifizierung](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/0143dce88a604533ab5ab17e639fec09.html) auf, und legen Sie den Standardauthentifizierungsidentitätsanbieter auf den Unternehmensidentitätsanbieter fest, der Ihr Azure AD-Verzeichnis darstellt.

In BTP:

- Richten Sie eine Vertrauensstellungskonfiguration zu IAS ein [(SAP-Dokumentation)](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/7c6aa87459764b179aeccadccd4f91f3.html#loio7c6aa87459764b179aeccadccd4f91f3), und vergewissern Sie sich, dass „[Available for User Logon](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/LATEST/en-US/affb201b1a36497996c2144c28683aed.html)“ (Für Benutzeranmeldung verfügbar) und „Create Shadow Users During Logon“ (Schattenbenutzer bei der Anmeldung erstellen) aktiviert sind.
- Deaktivieren Sie ggf. „Available for User Logon“ in der Standardkonfiguration für die SAP ID Service-Vertrauensstellung, sodass sich Benutzer immer über Azure AD authentifizieren und kein Bildschirm angezeigt wird, in dem sie den Identitätsanbieter auswählen können.

### <a name="2---use-azure-ad-for-authentication-and-iasbtp-for-authorization"></a>2\. Verwendung von Azure AD für die Authentifizierung und von IAS/BTP für die Autorisierung

#### <a name="context"></a>Kontext

Wenn BTP und IAS für die **Benutzerauthentifizierung** über einen Verbund mit Azure AD konfiguriert wurden, gibt es mehrere Konfigurationsoptionen für die **Autorisierung**:

- In Azure AD können Sie der Unternehmensanwendung, die Ihre SAP IAS-Instanz in Azure AD darstellt, Azure AD-Benutzer und -Gruppen zuweisen.
- In IAS können Sie die risikobasierte Authentifizierung verwenden, um Anmeldungen zuzulassen oder zu sperren und so den Zugriff auf die Anwendung in BTP zu verhindern.
- In BTP können Sie über Rollensammlungen definieren, welche Benutzer und Gruppen auf die Anwendung zugreifen und bestimmte Rollen erhalten können.

#### <a name="what-are-we-recommending"></a>Unsere Empfehlung

Es wird empfohlen, die Autorisierung nicht direkt in Azure AD selbst durchzuführen und in Azure AD „[Benutzerzuweisung erforderlich](../manage-apps/assign-user-or-group-access-portal.md)“ für die Unternehmensanwendung explizit zu deaktivieren. Beachten Sie, dass diese Einstellung für SAML-Anwendungen standardmäßig aktiviert ist, sodass Sie diese Einstellung explizit deaktivieren müssen.

#### <a name="why-this-recommendation"></a>Gründe für diese Empfehlung

Bei Erstellung des Anwendungsverbunds über IAS wird der Benutzer aus der Sicht von Azure AD im Rahmen des Anmeldeflows eigentlich bei IAS authentifiziert.  Dies bedeutet, dass Azure AD keine Informationen darüber hat, bei welcher BTP-Anwendung sich der Benutzer letztendlich anmelden möchte. Dies bedeutet auch, dass die Autorisierung in Azure AD nur für eine sehr grobe Autorisierung verwendet werden kann, z. B. dass sich der Benutzer bei einer *beliebigen* Anwendung in BTP oder bei *keiner* anmelden kann. Dies betont auch die SAP-Strategie, Apps und Authentifizierungsmechanismen auf der Ebene von BTP-Unterkonten zu isolieren.

Obwohl dies ein berechtigter Grund für die Verwendung von „Benutzerzuweisung erforderlich“ sein kann, bedeutet dies, dass es jetzt möglicherweise zwei verschiedene Stellen gibt, an denen Autorisierungsinformationen verwaltet werden müssen: sowohl in Azure AD für die Unternehmensanwendung (wo sie für *alle* BTP-Anwendungen gelten) als auch in jedem BTP-Unterkonto. Dies kann zu Verwirrung und Fehlkonfigurationen führen, wenn Autorisierungseinstellungen an einer Stelle, aber nicht an der anderen Stelle aktualisiert werden. Wenn beispielsweise ein Benutzer in BTP zugelassen, aber in Azure AD nicht der Anwendung zugewiesen wurde, schlägt die Authentifizierung fehl.

#### <a name="summary-of-implementation"></a>Zusammenfassung der Implementierung

Deaktivieren Sie für die Azure AD-Unternehmensanwendung, die die Verbundbeziehung mit IAS darstellt, „[Benutzerzuweisung erforderlich](../manage-apps/assign-user-or-group-access-portal.md)“. Dies bedeutet auch, dass Sie die Zuweisung von Benutzern problemlos überspringen können ([siehe Beschreibung in der Microsoft-Dokumentation](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md#assign-the-azure-ad-test-user)).

### <a name="3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp"></a>3\. Verwendung von Azure AD-Gruppen für die Autorisierung über Rollensammlungen in IAS/BTP

#### <a name="context"></a>Kontext

Wenn Sie die Autorisierung für Ihre BTP-Anwendungen konfigurieren möchten, gibt es mehrere Optionen:

- Sie können eine differenzierte Zugriffssteuerung in der Anwendung selbst abhängig vom jeweils angemeldeten Benutzer konfigurieren.
- Sie können den Zugriff über Rollen und Rollensammlungen in BTP basierend auf Benutzerzuweisungen oder Gruppenzuweisungen angeben.

Die endgültige Implementierung kann eine Kombination beider Strategien verwenden. Für die Zuweisung über Rollensammlungen kann dies benutzerspezifisch erfolgen, oder es können Gruppen des konfigurierten Identitätsanbieters verwendet werden.

#### <a name="what-are-we-recommending"></a>Unsere Empfehlung

Wenn Sie Azure AD als autoritative Quelle für eine differenzierte Autorisierung verwenden möchten, empfiehlt es sich, Azure AD-Gruppen zu verwenden und diese Rollensammlungen in BTP zuzuweisen. Um Benutzer Zugriff auf bestimmte Anwendungen zu gewähren, müssen die Benutzer lediglich zu den relevanten Azure AD-Gruppen hinzugefügt werden, ohne dass eine weitere Konfiguration in IAS/BTP erforderlich ist.

Bei dieser Konfiguration wird empfohlen, die Gruppen-ID (Objekt-ID) der Azure AD-Gruppe und nicht den Anzeigenamen („sAMAccountName“) als eindeutigen Bezeichner der Gruppe zu verwenden. Dies bedeutet, dass Sie die Gruppen-ID als „Gruppen“-Assertion im SAML-Token verwenden müssen, das von Azure AD ausgestellt wird.  Darüber hinaus wird die Gruppen-ID für die Zuweisung zur Rollensammlung in BTP verwendet.

![Verwendung von Rollensammlungen in SAP](./media/scenario-azure-first-sap-identity-integration/sap-use-role-collections.png)

#### <a name="why-this-recommendation"></a>Gründe für diese Empfehlung

Wenn Sie *Benutzer* direkt Rollensammlungen in BTP zuweisen, werden Autorisierungsentscheidungen nicht in Azure AD zentralisiert. Dies bedeutet auch, dass der Benutzer bereits in IAS vorhanden sein muss, damit er einer Rollensammlung in BTP zugewiesen werden kann. Da wir einen Verbund anstelle der Benutzerbereitstellung empfehlen, bedeutet dies, dass das Schattenkonto des Benutzers zum gewünschten Zeitpunkt der Benutzerzuweisung u. U. noch nicht in IAS vorhanden ist. Wenn Sie Azure AD-Gruppen verwenden und diese Gruppen Rollensammlungen zuweisen, entfallen diese Probleme.

Das Zuweisen von Gruppen zu Rollensammlungen scheint der vorherigen Empfehlung zu widersprechen, Azure AD nicht für die *Autorisierung* zu verwenden. Aber sogar in diesem Fall fällt die Autorisierungsentscheidung weiterhin in BTP. Die Entscheidung basiert jetzt allerdings nur auf der Gruppenmitgliedschaft, die in Azure AD verwaltet wird.

Es wird empfohlen, die Gruppen-ID der Azure AD-Gruppe und nicht den Namen der Gruppe zu verwenden, da die Gruppen-ID global eindeutig und unveränderlich ist und später nie für eine andere Gruppe wiederverwendet werden kann. Demgegenüber kann die Verwendung des Gruppennamens zu Problemen führen, wenn der Name geändert wird. Außerdem besteht ein Sicherheitsrisiko darin, dass eine Gruppe gelöscht und eine andere Gruppe mit dem gleichen Namen erstellt wird, die aber Benutzer enthält, die keinen Zugriff auf die Anwendung haben sollten.

#### <a name="summary-of-implementation"></a>Zusammenfassung der Implementierung

In Azure AD:

- Erstellen Sie Gruppen, zu denen die Benutzer hinzugefügt werden können, die Zugriff auf Anwendungen in BTP benötigen. (Erstellen Sie z. B. eine Azure AD-Gruppe für jede Rollensammlung in BTP.)
- Konfigurieren Sie für die Azure AD-Unternehmensanwendung, die die Verbundbeziehung mit IAS darstellt, die SAML-Benutzerattribute und -Ansprüche, um [einen Gruppenanspruch für Sicherheitsgruppen hinzuzufügen](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration):
    - Legen Sie das Quellattribut auf „Gruppen-ID“ und den Namen auf `Groups` fest (exakt wie hier mit dem Großbuchstaben „G“ geschrieben).
    - Außerdem wird dringend empfohlen, die in den Ansprüchen zurückgegebenen Gruppen auf die Gruppen zu beschränken, die explizit zugewiesen wurden, um die Anspruchsnutzdaten klein zu halten und zu vermeiden, dass Azure AD die Anzahl von Gruppenansprüchen in SAML-Assertionen auf 150 beschränkt:  
        - Wählen Sie unter „Welche dem Benutzer zugeordneten Gruppen sollen im Anspruch zurückgegeben werden?“ die Option „Der Anwendung zugewiesene Gruppen“ aus.  Weisen Sie sie dann die Gruppen, die Sie als Ansprüche einbeziehen möchten, der Unternehmensanwendung zu, indem Sie im Abschnitt „Benutzer und Gruppen“ die Option „Benutzer/Gruppe hinzufügen“ auswählen.

        ![Konfiguration von Azure AD-Gruppenansprüchen](./media/scenario-azure-first-sap-identity-integration/sap-aad-group-claim-configuration.png)

In IAS:

- Achten Sie in der Konfiguration des Unternehmensidentitätsanbieters unter den Optionen für den Identitätsverbund darauf, dass „[Use Identity Authentication user store](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/LATEST/en-US/c029bbbaefbf4350af15115396ba14e2.html)“ (Identity Authentication-Benutzerspeicher verwenden) deaktiviert ist. Andernfalls gehen die Gruppeninformationen von Azure AD im SAML-Token für BTP verloren, sodass die Autorisierung fehlschlägt.

In BTP:

- Führen Sie für die Rollensammlungen, die von den Anwendungen im betreffenden Unterkonto verwendet werden, eine [Zuordnung der Rollensammlungen zu Benutzergruppen](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/51acfc82c0c54db59de0a528f343902c.html) durch, indem Sie eine Konfiguration für den IAS-Identitätsanbieter hinzufügen und den Namen auf die Gruppen-ID (Objekt-ID) der Azure AD-Gruppe festlegen.

### <a name="4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements"></a>4\. Verwendung lediglich eines einzelnen BTP-Unterkontos für Anwendungen mit ähnlichen Identitätsanforderungen

#### <a name="context"></a>Kontext

In BTP kann jedes Unterkonto mehrere Anwendungen enthalten. Aus Sicht von IAS handelt es sich bei einer „gebündelten Anwendung“ jedoch um ein vollständiges BTP-Unterkonto, und nicht um die darin differenzierten Anwendungen. Dies bedeutet, dass alle Vertrauenseinstellungen, Authentifizierung und Zugriffskonfiguration sowie Branding- und Layoutoptionen in IAS für alle Anwendungen innerhalb dieses Unterkontos gelten. Ebenso gelten alle Vertrauensstellungskonfigurationen und Rollensammlungen in BTP auch für alle Anwendungen im betreffenden Unterkonto.

#### <a name="what-are-we-recommending"></a>Unsere Empfehlung

Es wird empfohlen, mehrere Anwendungen nur dann in einem einzelnen BTP-Unterkonto zu gruppieren, wenn sie ähnliche Anforderungen auf Identitätsebene haben (Benutzer, Gruppen, Identitätsanbieter, Rollen, Vertrauensstellungskonfiguration, Branding usw.).

#### <a name="why-this-recommendation"></a>Gründe für diese Empfehlung

Wenn Sie mehrere Anwendungen mit sehr unterschiedlichen Identitätsanforderungen in einem einzelnen Unterkonto in BTP gruppieren, kann eine Konfiguration entstehen, die unsicher ist oder leichter falsch konfiguriert werden kann.  Wenn beispielsweise für eine freigegebene Ressource, z. B. einen Identitätsanbieter, für eine einzelne Anwendung in BTP eine Konfigurationsänderung vorgenommen wird, wirkt sich dies auf alle Anwendungen aus, die auf dieser freigegebenen Ressource basieren.

#### <a name="summary-of-implementation"></a>Zusammenfassung der Implementierung

Überlegen Sie sorgfältig, wie Sie mehrere Anwendungen über Unterkonten in BTP gruppieren möchten. Weitere Informationen finden Sie in der [Dokumentation zum SAP-Kontenmodell](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/8ed4a705efa0431b910056c0acdbf377.html).

### <a name="5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization"></a>5\. Verwendung des IAS-Mandanten der Produktionsumgebung für die Authentifizierung und Autorisierung aller Endbenutzer

#### <a name="context"></a>Kontext

Wenn Sie IAS verwenden, verfügen Sie in der Regel über einen Mandanten für die Produktionsumgebung und einen Mandanten für die Entwicklungs-/Testumgebung. Für die verschiedenen Unterkonten oder Anwendungen in BTP können Sie auswählen, welcher Identitätsanbieter (IAS-Mandant) verwendet werden soll.

#### <a name="what-are-we-recommending"></a>Unsere Empfehlung

Es wird empfohlen, für alle Interaktionen mit Endbenutzern immer den IAS-Mandanten für die Produktionsumgebung zu verwenden, auch im Kontext einer Entwicklungs-/Testversion oder -umgebung der *Anwendung*, bei der sich die Endbenutzer anmelden müssen.

Es wird empfohlen, andere IAS-Mandanten nur zum Testen der identitätsbezogenen Konfiguration zu verwenden, die isoliert vom Produktionsmandanten erfolgen muss.

#### <a name="why-this-recommendation"></a>Gründe für diese Empfehlung

Da IAS die zentrale Komponente ist, die für den Verbund mit Azure AD eingerichtet wurde, gibt es nur eine einzige Stelle, an der die Verbund- und Identitätskonfiguration eingerichtet und verwaltet werden muss. Eine Duplizierung in andere IAS-Mandanten kann zu Fehlkonfigurationen oder Inkonsistenzen zwischen Umgebungen führen, wenn es um den Endbenutzerzugriff geht.

### <a name="6---define-a-process-for-rollover-of-saml-signing-certificates"></a>6\. Definition eines Rolloverprozesses für SAML-Signaturzertifikate

#### <a name="context"></a>Kontext

Beim Konfigurieren des Verbunds zwischen Azure AD und IAS sowie zwischen IAS und BTP werden SAML-Metadaten ausgetauscht, die X.509-Zertifikate enthalten. Diese Zertifikate werden für die Verschlüsselung und kryptografische Signaturen der SAML-Token verwendet, die zwischen beiden Parteien gesendet werden. Die Zertifikate verfügen über Ablaufdaten und müssen regelmäßig aktualisiert werden (auch in Notfallsituationen, wenn z. B. ein Zertifikat kompromittiert wurde).

Beachten Sie, dass die Standardgültigkeitsdauer des anfänglichen Azure AD-Zertifikats, das zum Signieren von SAML-Assertionen verwendet wird, 3 Jahre beträgt. (Beachten Sie weiterhin, dass das Zertifikat im Gegensatz zu OpenID Connect- und OAuth 2.0-Token, die von einem globalen Zertifikat in Azure AD signiert werden, speziell für die Unternehmensanwendung gilt.) Sie können [ein neues Zertifikat mit einem anderen Ablaufdatum generieren](../manage-apps/manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate) oder ein eigenes Zertifikat erstellen und importieren.

Wenn Zertifikate ablaufen, können sie nicht mehr verwendet werden, sodass neue Zertifikate konfiguriert werden müssen. Daher muss ein Prozess eingerichtet werden, um die Zertifikatkonfiguration in der vertrauenden Seite (die die Signaturen überprüfen muss) mit den tatsächlichen Zertifikaten, die zum Signieren der SAML-Token verwendet werden, auf dem aktuellen Stand zu halten.

In einigen Fällen kann die vertrauende Seite dies automatisch erledigen, indem die Konfiguration über einen Metadatenendpunkt bereitgestellt wird, der die aktuellen Metadateninformationen dynamisch zurückgibt. Dafür wird in der Regel eine öffentlich zugängliche URL verwendet, von der die vertrauende Seite die Metadaten in regelmäßigen Abständen abrufen und ihren internen Konfigurationsspeicher aktualisieren kann.

Allerdings lässt IAS nur die Einrichtung von Unternehmensidentitätsanbietern durch einen Import der XML-Metadatendatei zu. Die Bereitstellung eines Metadatenendpunkts für den dynamischen Abruf der Azure AD-Metadaten (z. B. `https://login.microsoftonline.com/my-azuread-tenant/federationmetadata/2007-06/federationmetadata.xml?appid=my-app-id`) wird nicht unterstützt. Ebenso lässt BTP keine Einrichtung einer neuen Vertrauensstellungskonfiguration über den IAS-Metadatenendpunkt (z. B. `https://my-ias-tenant.accounts.ondemand.com/saml2/metadata`) zu. Es ist ebenfalls ein einmaliger Upload einer XML-Metadatendatei erforderlich.

#### <a name="what-are-we-recommending"></a>Unsere Empfehlung

Stellen Sie beim Einrichten des Identitätsverbunds zwischen zwei Systemen (z. B. Azure AD und IAS sowie IAS und BTP) sicher, dass Sie das Ablaufdatum der verwendeten Zertifikate erfassen. Stellen Sie sicher, dass diese Zertifikate rechtzeitig ersetzt werden können und dass ein dokumentierter Prozess zum Aktualisieren der neuen Metadaten in allen vertrauenden Seiten vorhanden ist, die von diesen Zertifikaten abhängig sind.

Wie bereits erwähnt, wird die Einrichtung einer Vertrauensstellungskonfiguration in BTP für IAS empfohlen, die wiederum für den Verbund mit Azure AD als Unternehmensidentitätsanbieter eingerichtet ist. In diesem Fall sind die folgenden Zertifikate (die für die SAML-Signatur und Verschlüsselung verwendet werden) wichtig:

- Das Unterkontozertifikat in BTP: Wenn sich dieses Zertifikat ändert, muss die SAML 2.0-Konfiguration der Anwendung in IAS aktualisiert werden.
- Das Mandantenzertifikat in IAS: Wenn sich dieses Zertifikat ändert, müssen sowohl die SAML 2.0-Konfiguration der Unternehmensanwendung in Azure AD als auch die Vertrauensstellungskonfiguration in BTP aktualisiert werden.
- Das Unternehmensanwendungszertifikat in Azure AD: Wenn sich dieses Zertifikat ändert, muss die SAML 2.0-Konfiguration des Unternehmensidentitätsanbieters in IAS aktualisiert werden.

![Rollover von SAML-Signaturzertifikaten](./media/scenario-azure-first-sap-identity-integration/sap-rollover-saml-signing-certs.png)

SAP stellt [hier](https://blogs.sap.com/2017/12/06/sap-cloud-platform-integration-automated-notification-of-keystore-entries-reaching-expiry/) und [hier](https://blogs.sap.com/2019/03/01/sap-cloud-platform-integration-automated-notification-for-client-certificates-reaching-expiry/) Beispielimplementierungen für Clientzertifikatbenachrichtigungen mit SAP Cloud Platform Integration bereit. Diese können mit Azure Integration Services oder PowerAutomate angepasst werden. Sie müssen jedoch für die Verwendung von Serverzertifikaten angepasst werden. Dafür ist eine benutzerdefinierte Implementierung erforderlich.

#### <a name="why-this-recommendation"></a>Gründe für diese Empfehlung

Wenn die Zertifikate ablaufen dürfen oder rechtzeitig ersetzt werden, aber die von den Zertifikaten abhängigen vertrauenden Seiten nicht mit den neuen Zertifikatinformationen aktualisiert werden, können sich Benutzer nicht mehr über den Verbund bei Anwendungen anmelden. Dies kann zu erheblichen Ausfallzeiten für alle Benutzer führen, während der Dienst durch Neukonfiguration der Metadaten wiederhergestellt wird.

#### <a name="summary-of-implementation"></a>Zusammenfassung der Implementierung

[Fügen Sie eine E-Mail-Benachrichtigungsadresse für den Zertifikatablauf](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration) in Azure AD hinzu, und legen Sie sie auf ein Gruppenpostfach fest, damit die Benachrichtigung nicht an eine einzelne Person gesendet wird (die beim Ablauf des Zertifikats möglicherweise sogar kein Konto mehr hat). Standardmäßig erhält nur der Benutzer, der die Unternehmensanwendung erstellt hat, eine Benachrichtigung.

Erwägen Sie eine Automatisierung, um den gesamten Rolloverprozess für Zertifikat auszuführen.  Sie können z. B. regelmäßig überprüfen, ob Zertifikate ablaufen, und diese ersetzen, während alle vertrauenden Seiten mit den neuen Metadaten aktualisiert werden.
