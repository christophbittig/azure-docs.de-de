---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Datawiza
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung in Datawiza integrieren, um sicheren Hybridzugriff zu erhalten.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/07/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c33694c624deafa8cdc7ef37ac1dc39d98b5a4f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044739"
---
# <a name="tutorial-configure-azure-ad-b2c-with-datawiza-to-provide-secure-hybrid-access"></a>Tutorial: Konfigurieren von Azure AD B2C mit Datawiza zum Bereitstellen eines sicheren Hybridzugriffs

In diesem Beispieltutorial wird beschrieben, wie Sie die Azure Active Directory (AD) B2C-Authentifizierung in [Datawiza](https://www.datawiza.com/) integrieren.
[Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) von Datawiza ermöglicht einmaliges Anmelden (Single Sign-On, SSO) und eine präzise Zugriffssteuerung, indem Azure AD B2C erweitert wird, um lokale Legacyanwendungen zu schützen. Mithilfe dieser Lösung können Unternehmen schnell von Legacyanwendungen auf Azure AD B2C umsteigen, ohne Anwendungen neu schreiben zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- [Docker](https://docs.docker.com/get-docker/) ist für die DAB-Ausführung erforderlich. Ihre Anwendungen können auf einer beliebigen Plattform ausgeführt werden, z. B. auf VMs und Bare-Metal-Computern.

- Eine lokale Anwendung, die Sie von einem Legacyidentitätssystem auf Azure AD B2C umstellen. In diesem Beispiel wird DAB auf demselben Server bereitgestellt, auf dem sich die Anwendung befindet. Die Anwendung wird auf „localhost: 3001“ ausgeführt, und DAB leitet Datenverkehr per Proxy über „localhost: 9772“ an die Anwendung weiter. Der Datenverkehr an die Anwendung erreicht zuerst DAB und wird dann per Proxy an die Anwendung weitergeleitet.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Datawiza-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C**: Der Autorisierungsserver, mit dem die Anmeldeinformationen des Benutzers überprüft werden. Authentifizierte Benutzer können mit einem im Azure AD B2C-Verzeichnis gespeicherten lokalen Konto auf lokale Anwendungen zugreifen.

- **Datawiza Access Broker (DAB)** : Der Dienstbenutzer meldet sich an und übergibt die Identität transparent über HTTP-Header an Anwendungen.

- **Datawiza Cloud Management Console (DCMC)** : Eine zentrale Verwaltungskonsole, die den DAB verwaltet. DCMC stellt eine Benutzeroberfläche und Restful-APIs für Administratoren bereit, um die DAB-Konfigurationen und zugehörige Zugriffssteuerungsrichtlinien zu verwalten.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung: Architektur einer Azure AD B2C-Integration in Datawiza für den sicheren Zugriff auf Hybrid-Anwendungen](./media/partner-datawiza/datawiza-architecture-diagram.png)

| Schritte | Beschreibung |
|:-------|:---------------|
| 1. | Der Benutzer sendet eine Anforderung für den Zugriff auf die lokale gehostete Anwendung. DAB leitet die Benutzeranforderung per Proxy an die Anwendung weiter.|
| 2. | DAB überprüft den Authentifizierungsstatus des Benutzers. Wenn kein Sitzungstoken empfangen wird oder das angegebene Sitzungstoken ungültig ist, wird der Benutzer zur Authentifizierung an Azure AD B2C weitergeleitet.|
| 3. | Azure AD B2C sendet die Benutzeranforderung an den Endpunkt, der bei der Registrierung der DAB-Anwendung im Azure AD B2C-Mandanten angegeben wurde.|
| 4. | DAB wertet Zugriffsrichtlinien aus und berechnet die Attributwerte, die in die an die Anwendung weitergeleiteten HTTP-Header einbezogen werden sollen. In diesem Schritt ruft DAB möglicherweise den Identitätsanbieter auf, um die zum ordnungsgemäßen Festlegen der Headerwerte erforderlichen Informationen abzurufen. DAB legt die Headerwerte fest und sendet die Anforderung an die Anwendung. |
|5.  | Der Benutzer ist jetzt authentifiziert und hat Zugriff auf die Anwendung.|

## <a name="onboard-with-datawiza"></a>Onboarding mit Datawiza

Wenden Sie sich an [Datawiza](https://login.datawiza.com/df3f213b-68db-4966-bee4-c826eea4a310/b2c_1a_linkage/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile&client_id=4f011d0f-44d4-4c42-ad4c-88c7bbcd1ac8&redirect_uri=https%3A%2F%2Fconsole.datawiza.com%2Fhome&state=eyJpZCI6Ijk3ZjI5Y2VhLWQ3YzUtNGM5YS1hOWU2LTg1MDNjMmUzYWVlZCIsInRzIjoxNjIxMjg5ODc4LCJtZXRob2QiOiJyZWRpcmVjdEludGVyYWN0aW9uIn0%3D&nonce=08e1b701-6e42-427b-894b-c5d655a9a6b0&client_info=1&x-client-SKU=MSAL.JS&x-client-Ver=1.3.3&client-request-id=3ac285ba-2d4d-4ae5-8dc2-9295ff6047c6&response_mode=fragment), um Ihre lokale Legacy-App in Azure AD B2C zu integrieren.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Konfigurieren Ihres Azure AD B2C-Mandanten

1. [Registrieren](https://docs.datawiza.com/idp/azureb2c.html#microsoft-azure-ad-b2c-configuration) Sie Ihre Webanwendung im Azure AD B2C-Mandanten.

2. [Konfigurieren Sie einen Benutzerflow für Registrierung und Anmeldung](https://docs.datawiza.com/idp/azureb2c.html#configure-a-user-flow) im Azure-Verwaltungsportal.

  >[!NOTE]
  >Sie benötigen den Mandantennamen, den Namen des Benutzerflows, die Client-ID und den geheimen Clientschlüssel später, wenn Sie DAB in DCMC einrichten.

## <a name="create-an-application-on-dcmc"></a>Erstellen einer Anwendung in der DCMC

1. [Erstellen Sie eine Anwendung](https://docs.datawiza.com/step-by-step/step2.html), und generieren Sie ein Schlüsselpaar aus `PROVISIONING_KEY` und `PROVISIONING_SECRET` für diese Anwendung in der DCMC.

2. [Konfigurieren Sie Azure AD B2C](https://docs.datawiza.com/tutorial/web-app-azure-b2c.html#part-i-azure-ad-b2c-configuration) als Identitätsanbieter (IdP).

![Abbildung: Werte zum Konfigurieren des Identitätsanbieters](./media/partner-datawiza/configure-idp.png)

## <a name="run-dab-with-a-header-based-application"></a>Ausführen von DAB mit einer headerbasierten Anwendung

1. Sie können entweder Docker oder Kubernetes für die DAB-Ausführung verwenden. Das Docker-Image ist erforderlich, damit Benutzer eine headerbasierte Beispielanwendung erstellen können. Weitere Informationen finden Sie in den Anleitungen zum [Konfigurieren der DAB- und SSO-Integration](https://docs.datawiza.com/step-by-step/step3.html) und zum [Bereitstellen von DAB mit Kubernetes](https://docs.datawiza.com/tutorial/web-app-AKS.html) für Kubernetes-spezifische Anweisungen. Es wird ein Docker-Beispielimage `docker-compose.yml file` bereitgestellt, das Sie herunterladen und nutzen können. Melden Sie sich bei der Containerregistrierung an, um die Images für DAB und die headerbasierte Anwendung herunterzuladen. Gehen Sie wie [hier](https://docs.datawiza.com/step-by-step/step3.html#important-step) beschrieben vor.
 
   ```yaml
   version: '3'

    services:
    datawiza-access-broker:
    image: registry.gitlab.com/datawiza/access-broker
    container_name: datawiza-access-broker
    restart: always
    ports:
      - "9772:9772"
    environment:
      PROVISIONING_KEY: #############################
      PROVISIONING_SECRET: #############################

    header-based-app:
    image: registry.gitlab.com/datawiza/header-based-app
    container_name: ab-demo-header-app
    restart: always
    environment:
      CONNECTOR: B2C
    ports:
      - "3001:3001"
    ```

 2. Nach dem Ausführen von `docker-compose -f docker-compose.yml up` sollte die headerbasierte Anwendung für die einmalige Anmeldung (SSO) bei Azure AD B2C aktiviert sein. Öffnen Sie einen Browser, und geben Sie `http://localhost:9772/` ein.

3. Es wird eine Azure AD B2C-Anmeldeseite angezeigt.

## <a name="pass-user-attributes-to-the-header-based-application"></a>Übergeben von Benutzerattributen an die headerbasierte Anwendung

1. DAB ruft Benutzerattribute vom Identitätsanbieter ab und kann die Benutzerattribute per Header oder Cookie an die Anwendung übergeben. Lesen Sie die Anweisungen zum [Übergeben von Benutzerattributen](https://docs.datawiza.com/step-by-step/step4.html) (z. B. E-Mail-Adresse, Vornamen und Nachname) an die headerbasierte Anwendung. 

2. Nach der erfolgreichen Konfiguration der Benutzerattribute sollte für jedes Benutzerattribute ein grünes Häkchen angezeigt werden.

 ![Abbildung: übergebene Benutzerattribute](./media/partner-datawiza/pass-user-attributes.png)

## <a name="test-the-flow"></a>Testen des Flows

1. Navigieren Sie zur URL der lokalen Anwendung.

2. DAB sollte Sie an die Seite umleiten, die Sie in Ihrem Benutzerflow konfiguriert haben.

3. Wählen Sie auf der Seite den IdP aus der Liste aus.

4. Geben Sie nach der Weiterleitung an Identitätsanbieter Ihre Anmeldeinformationen wie angefordert an, einschließlich eines Azure AD-MFA-Tokens (Multi-Factor Authentication), sofern dies für diesen IdP erforderlich ist.

5. Nach der erfolgreichen Authentifizierung sollten Sie an die Azure AD B2C-Instanz umgeleitet werden, die die Anwendungsanforderung an den DAB-Umleitungs-URI weiterleitet.

6. DAB wertet Richtlinien aus, berechnet Header und leitet den Benutzer an die nachgeschaltete Anwendung weiter.  

7. Die angeforderte Anwendung sollte jetzt angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
