---
title: Detaillierte Konfigurationsschritte für eine gehostete Testversion in Azure Marketplace
description: Erläuterung der Konfigurationsschritte für eine gehostete Testversion im kommerziellen Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/26/2021
ms.openlocfilehash: 5fde0e1d9dc78c735ae9e889af5fbd889a588540
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851019"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Detaillierte Konfigurationsschritte für gehostete Testversionen

In diesem Artikel wird beschrieben, wie Sie eine gehostete Testversion für Dynamics 365 for Customer Engagement und Power Apps oder Dynamics 365 for Operations konfigurieren.

> [!TIP]
> Die Ansicht der Testversion für den Kunden im kommerziellen Marketplace finden Sie unter [Was ist Azure Marketplace?](/marketplace/azure-marketplace-overview#take-action-on-a-listing) und [Was ist Microsoft AppSource?](/marketplace/appsource-overview).

## <a name="configure-for-dynamics-365-customer-engagement--power-apps"></a>Konfiguration für Dynamics 365 Customer Engagement und Power Apps

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) an.
2. Wenn Sie auf den obigen Link nicht zugreifen können, müssen Sie [hier](https://appsource.microsoft.com/partners/list-an-app) eine Anforderung senden, um Ihre Anwendung zu veröffentlichen. Nachdem wir die Anforderung überprüft haben, erhalten Sie Zugriff, um den Veröffentlichungsprozess zu starten.
3. Suchen Sie ein bestehendes **Dynamics 365 for Customer Engagement & Power Apps**-Angebot, oder erstellen Sie ein neues **Dynamics 365 for Customer Engagement & Power Apps**-Angebot.
4. Aktivieren Sie auf der Seite **Angebotseinrichtung** das Kontrollkästchen bei **Testversion aktivieren**, wählen Sie den **Typ der Testversion** aus (siehe folgender Punkt), und klicken Sie dann auf **Entwurf speichern**.

    [![Aktivieren des Kontrollkästchens bei „Testversion aktivieren“](./media/test-drive/enable-test-drive-check-box-workspaces.png)](./media/test-drive/enable-test-drive-check-box-workspaces.png#lightbox)

    - **Typ der Testversion**: Wählen Sie die Option **Von Microsoft gehostet (Dynamics 365 for Customer Engagement & PowerApps)** aus. Dies weist darauf hin, dass Microsoft den Dienst hosten und warten wird, durch den die Benutzerbereitstellung für die Testversion ausgeführt und wieder aufgehoben wird.

5. Erteilen Sie die Microsoft AppSource-Berechtigung, durch die die Benutzerbereitstellung für die Testversion im Mandanten ausgeführt und wieder aufgehoben werden kann. Befolgen Sie dazu die [hier](./test-drive-azure-subscription-setup.md) beschriebenen Anweisungen. In diesem Schritt generieren Sie die unten erwähnten Werte **Azure AD-App-ID** und **Azure AD-App-Schlüssel**.
6. Füllen Sie diese Felder auf der Seite **Testversion** > **Technische Konfiguration** aus.

    [![Seite Testversion > Technische Konfiguration](media/test-drive/technical-config-details-workspaces.png)](media/test-drive/technical-config-details-workspaces.png#lightbox)

    - **Maximale Anzahl gleichzeitiger Testversionen**: Die Anzahl der Benutzer, die gleichzeitig eine aktive Testversion verwenden können. Jeder Benutzer verwendet eine Dynamics-Lizenz, während seine Testversion aktiv ist. Folglich müssen Sie sicherstellen, dass Sie mindestens so viele Dynamics-Lizenzen für die Benutzer der Testversionen zur Verfügung stellen. Wie empfehlen drei bis fünf Lizenzen.
    - **Dauer der Testversion** – Die maximale Anzahl von Stunden, für die die Testversion des Benutzers aktiv ist. Nach Ablauf dieser Zeit wird die Bereitstellung des Benutzers für Ihren Mandanten aufgehoben. Je nach Komplexität der App empfehlen wir zwei bis 24 Stunden. Der Benutzer kann jederzeit eine weitere Testversion anfordern, wenn die Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.
    - **Instanz-URL**: Die URL, die an den Benutzer der Testversion gesendet wird, wenn er die Testversion startet. Dies ist in der Regel die URL der Dynamics 365-Instanz, auf der Ihre App und die Beispieldaten installiert sind. Beispielwert: `https://testdrive.crm.dynamics.com`.
    - **Web-API-URL der Instanz**: Geben Sie die Web-API-URL für Ihre Dynamics 365-Instanz an. Sie können diesen Wert abrufen, indem Sie sich bei Ihrer Microsoft Dynamics 365-Instanz anmelden und zu **Einstellungen** > **Anpassung** > **Entwicklerressourcen** > **Instanz-Web-API** navigieren und die Adresse (URL) kopieren. Beispielwert:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Ein Beispiel für eine Instanz-Web-API.":::

    - **Rollenname**: Geben Sie den Namen der benutzerdefinierten Dynamics 365-Sicherheitsrolle an, die Sie für die Testversion erstellt haben, oder verwenden Sie eine bestehende Rolle. Einer neuen Rolle sollten mindestens die erforderlichen Berechtigungen zugeteilt werden, die der Rolle zum Anmelden bei einer Customer Engagement-Instanz hinzugefügt wurden. Weitere Informationen finden Sie unter [Mindestens erforderliche Berechtigungen für die Anmeldung bei Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Dies ist die Rolle, die den Benutzern während der Nutzung der Testversion zugewiesen wird. Beispielwert: `testdriverole`.
    
        > [!IMPORTANT]
        > Stellen Sie sicher, dass die Überprüfung von Sicherheitsgruppen nicht hinzugefügt wird. Dadurch kann der Benutzer mit der Customer Engagement-Instanz synchronisiert werden.

    - **Azure Active Directory-Mandanten-ID**: Geben Sie die ID des Azure-Mandanten für Ihre Dynamics 365-Instanz an. Um diesen Wert abzurufen, melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Azure Active Directory** > **Eigenschaften**. Kopieren Sie anschließend die Verzeichnis-ID. Beispielwert: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory-Mandantenname**: Der Name des Azure-Mandanten für Ihre Dynamics 365-Instanz. Verwenden Sie das Format `<tenantname>.onmicrosoft.com`. Beispielwert: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory-Anwendungs-ID**: Die ID der Azure Active Directory (AD)-App, die Sie in Schritt 5 erstellt haben. Beispielwert: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clientgeheimnis für Azure Active Directory-Anwendung**: Das Geheimnis für die in Schritt 5 erstellte Azure AD-App. Beispielwert: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.

7. Füllen Sie die Felder auf der Seite **Marketplace-Auflistung der Testversion** aus.

    [ ![Seite mit Details zur Marketplace-Auflistung](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **Beschreibung**: Eine Übersicht Ihrer Testversion. Dieser Text wird dem Benutzer während der Bereitstellung der Testversion angezeigt. Dieses Feld unterstützt HTML, wenn Sie formatierte Inhalte bereitstellen möchten. (erforderlich)
    - **Benutzerhandbuch**: Ein Benutzerhandbuch im PDF-Format, in dem sich die Benutzer*innen der Testversion über die Verwendung der App informieren können (erforderlich)
    - **Demovideo zur Testversion**: Ein Video, in dem die App präsentiert wird (optional).

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) an.
2. Wenn Sie auf den obigen Link nicht zugreifen können, müssen Sie [hier](https://appsource.microsoft.com/partners/list-an-app) eine Anforderung senden, um Ihre Anwendung zu veröffentlichen. Nachdem wir die Anforderung überprüft haben, erhalten Sie Zugriff, um den Veröffentlichungsprozess zu starten.
3. Suchen Sie ein bestehendes **Dynamics 365 for Customer Engagement & Power Apps**-Angebot, oder erstellen Sie ein neues **Dynamics 365 for Customer Engagement & Power Apps**-Angebot.
4. Aktivieren Sie das Kontrollkästchen **Testversion aktivieren**, wählen Sie den **Typ der Testversion** (siehe den folgenden Gliederungspunkt) aus, und klicken Sie auf **Speichern**.

    [![Auswählen des Kontrollkästchens „Testversion aktivieren“](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Typ der Testversion**: Wählen Sie die Option **Von Microsoft gehostet (Dynamics 365 Customer Engagement und Power Apps)** aus. Dies weist darauf hin, dass Microsoft den Dienst hosten und warten wird, durch den die Benutzerbereitstellung für die Testversion ausgeführt und wieder aufgehoben wird.

5. Erteilen Sie die Microsoft AppSource-Berechtigung, durch die die Benutzerbereitstellung für die Testversion im Mandanten ausgeführt und wieder aufgehoben werden kann. Befolgen Sie dazu die [hier](./test-drive-azure-subscription-setup.md) beschriebenen Anweisungen. In diesem Schritt generieren Sie die unten erwähnten Werte **Azure AD-App-ID** und **Azure AD-App-Schlüssel**.
6. Füllen Sie diese Felder auf der Seite **Technische Konfiguration der Testversion** aus.

    [![Seite „Technische Konfiguration der Testversion“](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximale Anzahl gleichzeitiger Testversionen**: Die Anzahl der Benutzer, die gleichzeitig eine aktive Testversion verwenden können. Jeder Benutzer verwendet eine Dynamics-Lizenz, während seine Testversion aktiv ist. Folglich müssen Sie sicherstellen, dass Sie mindestens so viele Dynamics-Lizenzen für die Benutzer der Testversionen zur Verfügung stellen. Wie empfehlen drei bis fünf Lizenzen.
    - **Dauer der Testversion** – Die maximale Anzahl von Stunden, für die die Testversion des Benutzers aktiv ist. Nach Ablauf dieser Zeit wird die Bereitstellung des Benutzers für Ihren Mandanten aufgehoben. Je nach Komplexität der App empfehlen wir zwei bis 24 Stunden. Der Benutzer kann jederzeit eine weitere Testversion anfordern, wenn die Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.
    - **Instanz-URL**
        - *Customer Engangement*: Die URL, die an den Benutzer der Testversion gesendet wird, wenn er die Testversion startet. Dies ist in der Regel die URL der Dynamics 365-Instanz, auf der Ihre App und die Beispieldaten installiert sind. Beispielwert: `https://testdrive.crm.dynamics.com`.
        - *Canvas-App (Power Apps)*
            1. Öffnen Sie die Portalseite **PowerApps**, und melden Sie sich an.
            2. Wählen Sie **Apps** und dann die Auslassungspunkte neben der App aus.
            4. Wählen Sie **Details** aus.
            5. Kopieren Sie den **Weblink** aus der Registerkarte **Details**.

                [ ![Fenster TestDrive Canvas-App](./media/test-drive/testdrive-canvas-app.png) ](./media/test-drive/testdrive-canvas-app.png#lightbox)

    - **Web-API-URL der Instanz**
        - *Customer Engagement*: Geben Sie die Web-API-URL für Ihre Dynamics 365-Instanz an. Sie können diesen Wert abrufen, indem Sie sich bei Ihrer Microsoft Dynamics 365-Instanz anmelden, **Einstellungen** > **Anpassung** > **Entwicklerressourcen** > **Instanz-Web-API** auswählen und die Adresse (URL) kopieren. Beispielwert:

            :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Ein Beispiel für eine Instanz-Web-API.":::

        - *Canvas-App (Power Apps)* : Wenn Sie CE/Dataverse nicht als Back-End für Ihre Canvas-App verwenden, verwenden Sie `https://localhost` als Platzhalter.

    - **Rollenname**
        - *Customer Engagement*: Geben Sie den Namen der benutzerdefinierten Dynamics 365-Sicherheitsrolle an, die Sie für die Testversion erstellt haben, oder verwenden Sie eine bestehende Rolle. Einer neuen Rolle sollten mindestens die erforderlichen Berechtigungen zugeteilt werden, die der Rolle zum Anmelden bei einer Customer Engagement-Instanz hinzugefügt wurden. Weitere Informationen finden Sie unter [Mindestens erforderliche Berechtigungen für die Anmeldung bei Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Dies ist die Rolle, die den Benutzern während der Nutzung der Testversion zugewiesen wird. Beispielwert: `testdriverole`.
        - *Canvas-App (Power Apps)* : Verwenden Sie „NA“, wenn CE/Dataverse nicht als Back-End-Datenquelle verwendet wird.
    
    > [!IMPORTANT]
    > Stellen Sie sicher, dass die Überprüfung von Sicherheitsgruppen nicht hinzugefügt wird. Dadurch kann der Benutzer mit der Customer Engagement-Instanz synchronisiert werden.

    - **Azure Active Directory-Mandanten-ID**: Geben Sie die ID des Azure-Mandanten für Ihre Dynamics 365-Instanz an. Um diesen Wert abzurufen, melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Azure Active Directory** > **Eigenschaften**. Kopieren Sie anschließend die Verzeichnis-ID. Beispielwert: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory-Mandantenname**: Der Name des Azure-Mandanten für Ihre Dynamics 365-Instanz. Verwenden Sie das Format `<tenantname>.onmicrosoft.com`. Beispielwert: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory-Anwendungs-ID**: Die ID der Azure Active Directory (AD)-App, die Sie in Schritt 5 erstellt haben. Beispielwert: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clientgeheimnis für Azure Active Directory-Anwendung**: Das Geheimnis für die in Schritt 5 erstellte Azure AD-App. Beispielwert: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.

7. Geben Sie die Details zum Marketplace an. Wählen Sie **Sprache** aus, um weitere Pflichtfelder anzuzeigen.

    [ ![Seite mit Details zur Marketplace-Auflistung](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **Beschreibung**: Eine Übersicht Ihrer Testversion. Dieser Text wird dem Benutzer während der Bereitstellung der Testversion angezeigt. Dieses Feld unterstützt HTML, wenn Sie formatierte Inhalte bereitstellen möchten. (erforderlich)
    - **Benutzerhandbuch**: Ein Benutzerhandbuch im PDF-Format, in dem sich die Benutzer*innen der Testversion über die Verwendung der App informieren können (erforderlich)
    - **Demovideo zur Testversion**: Ein Video, in dem die App präsentiert wird (optional).

---

## <a name="configure-for-dynamics-365-operations"></a>Konfiguration für Dynamics 365 Operations

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) an.
2. Wenn Sie auf den obigen Link nicht zugreifen können, müssen Sie [hier](https://appsource.microsoft.com/partners/list-an-app) eine Anforderung senden, um Ihre Anwendung zu veröffentlichen. Nachdem wir die Anforderung überprüft haben, erhalten Sie Zugriff, um den Veröffentlichungsprozess zu starten.
3. Suchen Sie nach einem vorhandenen **Dynamics 365 for Operations**-Angebot, oder erstellen Sie ein neues **Dynamics 365 for Operations**-Angebot.
4. Aktivieren Sie auf der Seite **Angebotseinrichtung** das Kontrollkästchen bei **Testversion aktivieren**, wählen Sie den **Typ der Testversion** aus (siehe folgender Punkt), und klicken Sie dann auf **Entwurf speichern**.

    [![Auswählen des Kontrollkästchens „Testversion aktivieren“](media/test-drive/enable-test-drive-check-box-operations-workspaces.png)](media/test-drive/enable-test-drive-check-box-operations-workspaces.png#lightbox)

    - **Typ der Testversion**: Wählen Sie die Option **Dynamics 365 for Operations** aus. Dies bedeutet, dass Microsoft den Dienst hosten und warten wird, durch den die Benutzerbereitstellung für die Testversion ausgeführt und wieder aufgehoben wird.

5. Erteilen Sie die Microsoft AppSource-Berechtigung, durch die die Benutzerbereitstellung für die Testversion im Mandanten ausgeführt und wieder aufgehoben werden kann. Befolgen Sie dazu die [hier](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) beschriebenen Anweisungen. In diesem Schritt generieren Sie die unten erwähnten Werte **Azure AD-App-ID** und **Azure AD-App-Schlüssel**.
6. Füllen Sie diese Felder auf der Seite **Testversion** >  **Technische Konfiguration** aus.

    [ ![Seite „Technische Konfiguration“ im Marketplace](./media/test-drive/technical-config-details-operations-workspaces.png) ](./media/test-drive/technical-config-details-operations-workspaces.png#lightbox)

    - **Maximale Anzahl gleichzeitiger Testversionen**: Die Anzahl der Benutzer, die gleichzeitig eine aktive Testversion verwenden können. Jeder Benutzer verwendet eine Dynamics-Lizenz, während seine Testversion aktiv ist. Folglich müssen Sie sicherstellen, dass Sie mindestens so viele Dynamics-Lizenzen für die Benutzer der Testversionen zur Verfügung stellen. Wie empfehlen drei bis fünf Lizenzen.
    - **Dauer der Testversion** – Die maximale Anzahl von Stunden, für die die Testversion des Benutzers aktiv ist. Nach Ablauf dieser Zeit wird die Bereitstellung des Benutzers für Ihren Mandanten aufgehoben. Je nach Komplexität der App empfehlen wir zwei bis 24 Stunden. Der Benutzer kann jederzeit eine weitere Testversion anfordern, wenn die Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.
    - **Instanz-URL**: Die URL, die an den Benutzer der Testversion gesendet wird, wenn er die Testversion startet. Dies ist in der Regel die URL der Dynamics 365-Instanz, auf der Ihre App und die Beispieldaten installiert sind. Beispielwert: `https://testdrive.crm.dynamics.com`.
    - **Azure Active Directory-Mandanten-ID**: Geben Sie die ID des Azure-Mandanten für Ihre Dynamics 365-Instanz an. Um diesen Wert abzurufen, melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Azure Active Directory** > **Eigenschaften**. Kopieren Sie anschließend die Verzeichnis-ID. Beispielwert: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory-Mandantenname**: Der Name des Azure-Mandanten für Ihre Dynamics 365-Instanz. Verwenden Sie das Format `<tenantname>.onmicrosoft.com`. Beispielwert: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory-Anwendungs-ID**: Die ID der Azure Active Directory (AD)-App, die Sie in Schritt 5 erstellt haben. Beispielwert: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clientgeheimnis für Azure Active Directory-Anwendung**: Das Geheimnis für die in Schritt 5 erstellte Azure AD-App. Beispielwert: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.
    - **Juristische Person für Testversion**: Gibt eine juristische Person an, der ein Testbenutzer zugewiesen wird. Informieren Sie sich unter [Erstellen oder Ändern einer juristischen Person](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity), wie Sie eine neue juristische Person erstellen.
    - **Rollenname**: Der Name der Entwicklungsumgebung (Application Object Tree, AOT) der benutzerdefinierten Dynamics 365-Sicherheitsrolle, die Sie für die Testversion erstellt haben. Dies ist die Rolle, die den Benutzern während der Nutzung der Testversion zugewiesen wird.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Die Seite zur Sicherheitskonfiguration":::

7. Füllen Sie die Felder auf der Seite **Marketplace-Auflistung der Testversion** aus.

    [![Die Seite mit Details zur Marketplace-Auflistung](media/test-drive/marketplace-listing-details-ops-workspaces.png)](media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **Beschreibung**: Eine Übersicht Ihrer Testversion. Dieser Text wird dem Benutzer während der Bereitstellung der Testversion angezeigt. Dieses Feld unterstützt HTML, wenn Sie formatierte Inhalte bereitstellen möchten. (erforderlich)
    - **Benutzerhandbuch**: Ein Benutzerhandbuch im PDF-Format, in dem sich die Benutzer*innen der Testversion über die Verwendung der App informieren können (erforderlich)
    - **Demovideo zur Testversion**: Ein Video, in dem die App präsentiert wird (optional).

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2165507) an.
2. Wenn Sie auf den obigen Link nicht zugreifen können, müssen Sie [hier](https://appsource.microsoft.com/partners/list-an-app) eine Anforderung senden, um Ihre Anwendung zu veröffentlichen. Nachdem wir die Anforderung überprüft haben, erhalten Sie Zugriff, um den Veröffentlichungsprozess zu starten.
3. Suchen Sie nach einem vorhandenen **Dynamics 365 for Operations**-Angebot, oder erstellen Sie ein neues **Dynamics 365 for Operations**-Angebot.
4. Aktivieren Sie das Kontrollkästchen **Testversion aktivieren**, wählen Sie den **Typ der Testversion** (siehe den folgenden Gliederungspunkt) aus, und klicken Sie auf **Entwurf speichern**.

    [![Auswählen des Kontrollkästchens „Testversion aktivieren“](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Typ der Testversion**: Wählen Sie die Option **Dynamics 365 for Operations** aus. Dies bedeutet, dass Microsoft den Dienst hosten und warten wird, durch den die Benutzerbereitstellung für die Testversion ausgeführt und wieder aufgehoben wird.

5. Erteilen Sie die Microsoft AppSource-Berechtigung, durch die die Benutzerbereitstellung für die Testversion im Mandanten ausgeführt und wieder aufgehoben werden kann. Befolgen Sie dazu die [hier](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) beschriebenen Anweisungen. In diesem Schritt generieren Sie die unten erwähnten Werte **Azure AD-App-ID** und **Azure AD-App-Schlüssel**.
6. Füllen Sie diese Felder auf der Seite **Technische Konfiguration der Testversion** aus.

    [![Die Seite zur technischen Marketplace-Konfiguration](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximale Anzahl gleichzeitiger Testversionen**: Die Anzahl der Benutzer, die gleichzeitig eine aktive Testversion verwenden können. Jeder Benutzer verwendet eine Dynamics-Lizenz, während seine Testversion aktiv ist. Folglich müssen Sie sicherstellen, dass Sie mindestens so viele Dynamics-Lizenzen für die Benutzer der Testversionen zur Verfügung stellen. Wie empfehlen drei bis fünf Lizenzen.
    - **Dauer der Testversion** – Die maximale Anzahl von Stunden, für die die Testversion des Benutzers aktiv ist. Nach Ablauf dieser Zeit wird die Bereitstellung des Benutzers für Ihren Mandanten aufgehoben. Je nach Komplexität der App empfehlen wir zwei bis 24 Stunden. Der Benutzer kann jederzeit eine weitere Testversion anfordern, wenn die Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.
    - **Instanz-URL**: Die URL, die an den Benutzer der Testversion gesendet wird, wenn er die Testversion startet. Dies ist in der Regel die URL der Dynamics 365-Instanz, auf der Ihre App und die Beispieldaten installiert sind. Beispielwert: `https://testdrive.crm.dynamics.com`.
    - **Azure Active Directory-Mandanten-ID**: Geben Sie die ID des Azure-Mandanten für Ihre Dynamics 365-Instanz an. Um diesen Wert abzurufen, melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Azure Active Directory** > **Eigenschaften**. Kopieren Sie anschließend die Verzeichnis-ID. Beispielwert: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory-Mandantenname**: Der Name des Azure-Mandanten für Ihre Dynamics 365-Instanz. Verwenden Sie das Format `<tenantname>.onmicrosoft.com`. Beispielwert: `testdrive.onmicrosoft.com`.
    - **Azure Active Directory-Anwendungs-ID**: Die ID der Azure Active Directory (AD)-App, die Sie in Schritt 5 erstellt haben. Beispielwert: `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clientgeheimnis für Azure Active Directory-Anwendung**: Das Geheimnis für die in Schritt 5 erstellte Azure AD-App. Beispielwert: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.
    - **Juristische Person für Testversion**: Gibt eine juristische Person an, der ein Testbenutzer zugewiesen wird. Informieren Sie sich unter [Erstellen oder Ändern einer juristischen Person](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity), wie Sie eine neue juristische Person erstellen.
    - **Rollenname**: Der Name der Entwicklungsumgebung (Application Object Tree, AOT) der benutzerdefinierten Dynamics 365-Sicherheitsrolle, die Sie für die Testversion erstellt haben. Dies ist die Rolle, die den Benutzern während der Nutzung der Testversion zugewiesen wird.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Die Seite zur Sicherheitskonfiguration":::

7. Geben Sie die Details zum Marketplace an. Wählen Sie **Sprache** aus, um weitere Pflichtfelder anzuzeigen.

    [![Die Seite mit Details zur Marketplace-Auflistung](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beschreibung**: Eine Übersicht Ihrer Testversion. Dieser Text wird dem Benutzer während der Bereitstellung der Testversion angezeigt. Dieses Feld unterstützt HTML, wenn Sie formatierte Inhalte bereitstellen möchten.
    - **Benutzerhandbuch**: Ein Benutzerhandbuch (PDF-Datei), in dem sich die Benutzer der Testversion über die Verwendung der App informieren können.
    - **Demovideo zur Testversion**: Ein Video, in dem die App präsentiert wird (optional).

---

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
