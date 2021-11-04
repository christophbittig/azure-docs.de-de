---
title: Sicherer Hybridzugriff mit Datawiza
titleSuffix: Azure AD
description: In diesem Tutorial wird erläutert, wie Sie Datawiza mit Azure AD integrieren, um sicheren Hybridzugriff zu erhalten.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/27/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d527db391185c899bed3137c3783aa81f8a00c63
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067027"
---
# <a name="tutorial-configure-datawiza-with-azure-active-directory-for-secure-hybrid-access"></a>Tutorial: Konfigurieren von Datawiza mit Azure Active Directory für sicheren Hybridzugriff

In diesem Beispieltutorial wird erläutert, wie Sie Azure Active Directory (Azure AD) mit [Datawiza](https://www.datawiza.com/) integrieren, um sicheren Hybridzugriff zu erhalten.

[Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) von Datawiza erweitert Azure AD, um das einmalige Anmelden (Single Sign-on, SSO) und präzise Zugriffssteuerungen zum Schutz lokaler und in der Cloud gehosteter Anwendungen (z. B. Oracle E-Business Suite, Microsoft-Internetinformationsdienste und SAP) zu ermöglichen.

Mit dieser Lösung können Unternehmen schnell von Legacy-WAMs (Web Access Manager) wie Symantec SiteMinder, NetIQ, Oracle und IBM zu Azure AD wechseln, ohne dabei Anwendungen neu zu schreiben. Unternehmen können Datawiza auch als Code- oder Low-Code-Lösung verwenden, um neue Anwendungen in Azure AD zu integrieren. Auf diese Weise wird Entwicklungszeit eingespart, die Kosten werden reduziert, und die Projekte können sicher bereitgestellt werden.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [Testkonto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD-Mandanten](../fundamentals/active-directory-access-create-new-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- [Docker](https://docs.docker.com/get-docker/) und [docker-compose](https://docs.docker.com/compose/install/) sind zum Ausführen von DAB erforderlich. Ihre Anwendungen können auf jeder Plattform ausgeführt werden (z. B. auf dem virtuellen Computer und Bare-Metal-Computer).

- Eine Anwendung, die Sie von einem Legacyidentitätssystem auf Azure AD umstellen. In diesem Beispiel wird DAB auf demselben Server bereitgestellt, auf dem sich die Anwendung befindet. Die Anwendung wird auf „localhost: 3001“ ausgeführt, und DAB leitet Datenverkehr per Proxy über „localhost: 9772“ an die Anwendung weiter. Der Datenverkehr an die Anwendung erreicht zuerst DAB und wird dann per Proxy an die Anwendung weitergeleitet.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Datawiza-Integration umfasst die folgenden Komponenten:

- [Azure AD:](../fundamentals/active-directory-whatis.md) Dies ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft, mit dem sich Benutzer anmelden und auf externe und interne Ressourcen zugreifen können.

- Datawiza Access Broker (DAB): Der Dienstbenutzer meldet sich an und übergibt die Identität transparent über HTTP-Header an Anwendungen.

- Datawiza Cloud Management Console (DCMC): Dies ist eine zentralisierte Verwaltungskonsole, die DAB verwaltet. DCMC stellt eine Benutzeroberfläche und Restful-APIs für Administratoren bereit, um die DAB-Konfigurationen und zugehörige Zugriffssteuerungsrichtlinien zu verwalten.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung: Architekturdiagramm](./media/datawiza-with-azure-active-directory/datawiza-architecture-diagram.png)

|Schritte| Beschreibung|
|:----------|:-----------|
|  1. | Der Benutzer sendet eine Anforderung für den Zugriff auf die lokale oder in der Cloud gehostete Anwendung. DAB leitet die Benutzeranforderung per Proxy an die Anwendung weiter.|
| 2. |DAB überprüft den Authentifizierungsstatus des Benutzers. Wenn kein Sitzungstoken empfangen wird oder das angegebene Sitzungstoken ungültig ist, wird der Benutzer zur Authentifizierung an Azure AD weitergeleitet.|
| 3. | Azure AD sendet die Benutzeranforderung an den Endpunkt, der bei der Registrierung der DAB-Anwendung im Azure AD-Mandanten angegeben wurde.|
| 4. | DAB wertet Zugriffsrichtlinien aus und berechnet die Attributwerte, die in die an die Anwendung weitergeleiteten HTTP-Header einbezogen werden sollen. In diesem Schritt ruft DAB möglicherweise den Identitätsanbieter auf, um die zum ordnungsgemäßen Festlegen der Headerwerte erforderlichen Informationen abzurufen. DAB legt die Headerwerte fest und sendet die Anforderung an die Anwendung. |
| 5. |  Der Benutzer ist jetzt authentifiziert und hat Zugriff auf die Anwendung.|

## <a name="onboard-with-datawiza"></a>Onboarding mit Datawiza

Melden Sie sich bei [Datawiza Cloud Management Console](https://console.datawiza.com/) (DCMC) an, um Ihre lokale oder in der Cloud gehostete Anwendung mit Azure AD zu integrieren.

## <a name="create-an-application-on-dcmc"></a>Erstellen einer Anwendung in DCMC

[Erstellen Sie eine Anwendung](https://docs.datawiza.com/step-by-step/step2.html), und generieren Sie ein Schlüsselpaar aus `PROVISIONING_KEY` und `PROVISIONING_SECRET` für die Anwendung in DCMC.

Für Azure AD bietet Datawiza eine praktische [Integration mit nur einem Klick](https://docs.datawiza.com/tutorial/web-app-azure-one-click.html). Mit dieser Methode zum Integrieren von Azure AD mit DCMC kann eine Anwendungsregistrierung in Ihrem Namen in Ihrem Azure AD-Mandanten erstellt werden.

![Abbildung: Konfigurieren des Identitätsanbieters](./media/datawiza-with-azure-active-directory/configure-idp.png)

Wenn Sie stattdessen eine vorhandene Webanwendung in Ihrem Azure AD-Mandanten verwenden möchten, können Sie die Option deaktivieren und die Felder des Formulars ausfüllen. Sie benötigen die Mandanten-ID, die Client-ID und den geheimen Clientschlüssel. [Erstellen Sie eine Webanwendung, und rufen Sie diese Werte in Ihrem Mandanten ab.](https://docs.datawiza.com/idp/azure.html)

![Abbildung: Konfigurieren des Identitätsanbieters mithilfe des Formulars](./media/datawiza-with-azure-active-directory/use-form.png)

## <a name="run-dab-with-a-header-based-application"></a>Ausführen von DAB mit einer headerbasierten Anwendung

1. Sie können entweder Docker oder Kubernetes für die DAB-Ausführung verwenden. Das Docker-Image ist erforderlich, damit Benutzer eine headerbasierte Beispielanwendung erstellen können. In [diesem Artikel](https://docs.datawiza.com/step-by-step/step3.html) wird das Konfigurieren der DAB- und SSO-Integration beschrieben. [Hier](https://docs.datawiza.com/tutorial/web-app-AKS.html) erfahren Sie mehr über das Bereitstellen von DAB mit Kubernetes. Sie können die Docker-Beispielimagedatei `docker-compose.yml` herunterladen und verwenden. [Melden Sie sich bei der Containerregistrierung an](https://docs.datawiza.com/step-by-step/step3.html#important-step), um die Images für DAB und die headerbasierte Anwendung herunterzuladen.

    ```yaml
    services:
      datawiza-access-broker:
      image: registry.gitlab.com/datawiza/access-broker
      container_name: datawiza-access-broker
      restart: always
      ports:
      - "9772:9772"
      environment:
      PROVISIONING_KEY: #############################################
      PROVISIONING_SECRET: ##############################################
      
      header-based-app:
      image: registry.gitlab.com/datawiza/header-based-app
      restart: always
     ports:
     - "3001:3001"
   ```

2. Nach dem Ausführen von `docker-compose -f docker-compose.yml up` sollte das einmalige Anmelden (SSO) mit Azure AD für die headerbasierte Anwendung aktiviert sein. Öffnen Sie einen Browser, und geben Sie `http://localhost:9772/` ein.

3. Eine Azure AD-Anmeldeseite wird angezeigt.

## <a name="pass-user-attributes-to-the-header-based-application"></a>Übergeben von Benutzerattributen an die headerbasierte Anwendung

1. DAB ruft Benutzerattribute vom Identitätsanbieter ab und kann die Benutzerattribute per Header oder Cookie an die Anwendung übergeben. Lesen Sie die Anweisungen zum [Übergeben von Benutzerattributen](https://docs.datawiza.com/step-by-step/step4.html) (z. B. E-Mail-Adresse, Vornamen und Nachname) an die headerbasierte Anwendung.

2. Nach der erfolgreichen Konfiguration der Benutzerattribute sollte für jedes Benutzerattribute ein grünes Häkchen angezeigt werden.

   ![Abbildung: Startseite der Datawiza-Anwendung](./media/datawiza-with-azure-active-directory/datawiza-application-home-page.png)

## <a name="test-the-flow"></a>Testen des Flows

1. Navigieren Sie zur Anwendungs-URL.

2. DAB sollte Sie zur Azure AD-Anmeldeseite weiterleiten.

3. Nach der erfolgreichen Authentifizierung sollten Sie zu DAB weitergeleitet werden.

4. DAB wertet Richtlinien aus, berechnet Header und leitet den Benutzer an die Upstreamanwendung weiter.

5. Die angeforderte Anwendung sollte angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Konfigurieren von Azure AD B2C mit Datawiza zum Bereitstellen eines sicheren Hybridzugriffs](../../active-directory-b2c/partner-datawiza.md)

- [Datawiza-Dokumentation](https://docs.datawiza.com)