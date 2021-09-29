---
title: Tutorial – Konfigurieren Sie Azure Active Directory so, dass es überprüfbare Anmeldedaten ausgibt (Vorschau)
description: In diesem Lernprogramm bauen Sie die Umgebung auf, die zur Bereitstellung von überprüfbaren Berechtigungsnachweisen in Ihrem Mandant erforderlich ist.
documentationCenter: ''
author: barclayn
manager: daveba
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 06/24/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 9867825794c363b9df53849ad3fd1cb644492677
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816283"
---
# <a name="tutorial---configure-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Tutorial – Konfigurieren Sie Azure Active Directory so, dass es überprüfbare Anmeldedaten ausgibt (Vorschau)

In diesem Tutorial bauen Sie auf der Arbeit, die im Rahmen des Artikels [Erste Schritte](get-started-verifiable-credentials.md) ausgeführt wurde, und richten Azure Active Directory (Azure AD) mit einem eigenen [dezentralisierten Bezeichner](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) ein. Sie verwenden den DID zum Ausstellen überprüfbarer Anmeldeinformationen mithilfe der Beispiel-App und Ihres Ausstellers. In diesem Tutorial verwenden Sie weiterhin den Azure B2C-Beispiel-Mandanten für die Authentifizierung. Im nächsten Tutorial werden Sie Schritte durchführen, um die App so zu konfigurieren, dass sie mit Azure AD funktioniert.

> [!IMPORTANT]
> Überprüfbare Anmeldeinformationen für Azure Active Directory-befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Erstellen Sie die notwendigen Dienste, um Azure AD für überprüfbare Anmeldeinformationen einzubinden.
> * Erstellen Sie Ihren DID.
> * Passen Sie die Regel- und Anzeigedateien an.
> * Konfigurieren Sie die überprüfbaren Anmeldeinformationen in Azure AD.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie dieses Tutorial erfolgreich abschließen können, müssen Sie zuerst:

- Führen Sie die Schritte im Tutorial [Erste Schritte](get-started-verifiable-credentials.md) aus.
- Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Sie benötigen Azure AD mit einer P2-[Lizenz](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing). Wenn Sie nicht darüber verfügen, befolgen Sie die Schritte unter [Erstellen eines kostenlosen Entwicklerkontos](how-to-create-a-free-developer-account.md).
- Sie benötigen eine Instanz von [Azure Key Vault](../../key-vault/general/overview.md), in der Sie über das Recht zum Erstellen von Schlüsseln und Geheimnissen verfügen.

## <a name="azure-active-directory"></a>Azure Active Directory

Bevor Sie beginnen, benötigen Sie also einen Azure AD-Mandanten. Wenn Ihr Mandant für überprüfbare Anmeldeinformationen aktiviert ist, wird ihm ein DID zugewiesen. Er wird zudem mit einem Ausstellerdienst zum Ausstellen überprüfbarer Anmeldeinformationen aktiviert. Alle überprüfbaren Anmeldeinformationen, die Sie ausgeben, werden von Ihrem Mandanten und seiner DID ausgegeben. Der DID wird auch verwendet, wenn Sie überprüfbare Anmeldeinformationen überprüfen.

Wenn Sie gerade ein Test-Azure-Abonnement erstellt haben, muss Ihr Mandant nicht mit Benutzerkonten gefüllt werden. Sie benötigen mindestens ein Benutzertestkonto, um spätere Tutorials abschließen zu können.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Wenn Sie mit überprüfbaren Anmeldeinformationen arbeiten, haben Sie die komplette Kontrolle und Verwaltung der kryptografischen Schlüssel, die Ihr Mandant zum digitalen Signieren überprüfbarer Anmeldeinformationen verwendet. Zum Ausstellen und Überprüfen von Anmeldeinformationen müssen Sie Azure AD mit Zugriff auf Ihre eigene Instanz von Key Vault bereitstellen.

1. Wählen Sie im Azure-Portal oder auf der Startseite die Option **Ressource erstellen** aus.
1. Geben Sie **Key Vault** in das Suchfeld ein.
1. Wählen Sie in der Ergebnisliste **Key Vault** aus.
1. Wählen Sie im Abschnitt **Key Vault** die Option **Erstellen** aus.
1. Geben Sie im Abschnitt **Schlüsseltresor erstellen** folgende Informationen ein:
    - **Abonnement**: Wählen Sie ein Abonnement aus.
    - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie einen Ressourcengruppennamen wie beispielsweise **vc-resource-group** ein. Wir verwenden denselben Ressourcengruppennamen in mehreren Artikeln.
    - **Name**: Es ist ein eindeutiger Name erforderlich. Wir verwenden **woodgroup-vc-kv**, also ersetzen Sie diesen Wert durch Ihren eigenen eindeutigen Namen.
    - **Standort**: Wählen Sie einen Standort aus.
    - Behalten Sie bei den anderen Optionen die Standardeinstellungen bei.
1. Wählen Sie nach der Angabe der Informationen **Zugriffsrichtlinie** aus.

    ![Screenshot, der den Bildschirm „Schlüsseltresor erstellen“ zeigt.](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

1. Wählen Sie auf dem Bildschirm **Zugriffsrichtlinie** die Option **Zugriffsrichtlinie hinzufügen** aus.

    >[!NOTE]
    > Standardmäßig ist das Konto, mit dem der Schlüsseltresor erstellt wird, das einzige mit Zugriff. Der Dienst für überprüfbare Anmeldeinformationen benötigt Zugriff auf den Schlüsseltresor. Der Schlüsseltresor muss über eine Zugriffsrichtlinie verfügen, die es dem Administrator ermöglicht, Schlüssel zu erstellen und Schlüssel zu löschen, wenn Sie sich abmelden, und die den Administrator berechtigt, zu signieren, um die Domänenbindung für überprüfbare Anmeldeinformationen zu erstellen. Wenn Sie beim Testen dasselbe Konto verwenden, ändern Sie die Standardrichtlinie, um dem Konto die Berechtigung **Signieren** zusätzlich zu den Standardberechtigungen zu erteilen, die Tresorerstellern erteilt wurden.

1. Stellen Sie für den Benutzeradministrator sicher, dass im Abschnitt mit den Schlüsselberechtigungen die Berechtigungen **Erstellen**, **Löschen** und **Signieren** aktiviert sind. Standardmäßig sind **Erstellen** und **Löschen** bereits aktiviert. **Signieren** sollte die einzige Schlüsselberechtigung sein, die Sie aktualisieren müssen.

    ![Screenshot, der die Schlüsseltresorberechtigungen zeigt.](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.
1. Gehen Sie zum Tresor, und notieren Sie sich den Tresornamen und den URI.

Beachten Sie die beiden folgenden Eigenschaften:

- **Tresorname**: Im Beispiel lautet der Schlüsseltresorname **woodgrove-vc-kv**. Sie verwenden diesen Namen noch für andere Schritte.
- **Tresor-URI**: in diesem Beispiel, die Value ist https://woodgrove-vc-kv.vault.azure.net/. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

>[!NOTE]
> Jede Schlüsseltresortransaktion führt zu weiteren Kosten für Azure-Abonnements. Weitere Informationen finden Sie auf der Seite [Preisübersicht für Schlüsseltresor](https://azure.microsoft.com/pricing/details/key-vault/).

>[!IMPORTANT]
> Während der Vorschau der Azure Active Directory überprüfbaren Anmeldeinformationen ändern Sie die in Ihrem Tresor erstellten Schlüssel und Geheimnisse nicht, nachdem sie erstellt wurden. Durch das Löschen, deaktivieren oder Aktualisieren Ihrer Schlüssel und Geheimnisse werden alle ausgestellten Anmeldeinformationen ungültig.

## <a name="create-modified-rules-and-display-files"></a>Erstellen von geänderten Regel- und Anzeigedateien

In diesem Abschnitt verwenden Sie die Regel- und Anzeigedateien aus der [Beispielaussteller-App](https://github.com/Azure-Samples/active-directory-verifiable-credentials/) und ändern sie geringfügig, um die erste überprüfbare Anmeldeinformation Ihres Mandanten zu erstellen.

1. Kopieren Sie die Regel- und Anzeige-JSON-Dateien in einen temporären Ordner. Benennen Sie sie in **MyFirstVC-display.json** bzw. **MyFirstVC-rules.json** um. Beide Dateien finden Sie unter **issuer\issuer_config**.

   ![Screenshot, der die Regel- und Anzeigedateien als Teil des Beispiel-App-Verzeichnisses zeigt.](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![Screenshot, der die Regel- und Anzeigedateien in einem temporären Ordner zeigt.](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

1. Öffnen Sie die Datei **MyFirstVC-rules.json** in Ihrem Code-Editor.

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

   Ändern Sie nun das Feld "type" in **"MyFirstVC"** .

   ```json
    "type": ["MyFirstVC"]
  
   ```

   Diese Änderung speichern.

   >[!NOTE]
   > Sie ändern die Werte für "configuration" oder "client_id" an diesem Punkt des Tutorials nicht. Sie verwenden weiterhin den Azure-Beispielmandanten, den Sie im Tutorial [Erste Schritte](get-started-verifiable-credentials.md) verwendet haben. Sie verwenden Azure AD im nächsten Tutorial.

1. Öffnen Sie die Datei **MyFirstVC-display.json** in Ihrem Code-Editor.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

   Nehmen Sie Änderungen vor, so dass sich diese überprüfbare Anmeldeinformation von der Version des Beispielcodes sichtbar unterscheidet.

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > Um sicherzustellen, dass Ihre Anmeldeinformation lesbar und zugänglich ist, wählen Sie Text- und Hintergrundfarben mit einem [Kontrastverhältnis](https://www.w3.org/WAI/WCAG21/Techniques/general/G18) von mindestens 4,5:1 aus.

   Speichern der Änderungen.

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Bevor Sie Ihre erste überprüfbare Anmeldeinformation erstellen, erstellen Sie einen Azure Blob Storage-Container, der Ihre Konfigurations- und Regeldateien enthalten kann.

1. Erstellen Sie mithilfe der gezeigten Optionen ein Speicherkonto. Ausführliche Schritte finden Sie unter [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md?tabs=azure-portal).

   - **Abonnement**: Das Abonnement, das Sie für diese Tutorials verwenden.
   - **Ressourcengruppe**: Dieselbe Ressourcengruppe, die Sie in früheren Tutorials verwendet haben (**vc-resource-group**).
   - **Name**: Ein eindeutiger Name
   - **Standort**: (USA) USA, Osten
   - **Leistung**: Standard
   - **Kontoart**: Speicher V2
   - **Replikation**: Lokal redundant
 
   ![Screenshot, der den Bildschirm „Speicherkonto erstellen“ zeigt.](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

1. Nachdem Sie das Speicherkonto erstellt haben, erstellen Sie einen Container. Wählen Sie unter **Blob Storage** die Option **Container** aus. Erstellen Sie einen Container mit den folgenden Werten:

    - **Name**: vc-container
    - **Öffentliche Zugriffsebene**: Privat (kein anonymer Zugriff)
  
   Klicken Sie auf **Erstellen**.

   ![Screenshot, der das Erstellen eines Containers zeigt.](media/enable-your-tenant-verifiable-credentials/new-container.png)

1. Wählen Sie nun Ihren neuen Container aus, und laden Sie die beiden neuen Regel- und Anzeigedateien **MyFirstVC-display.json** und **MyFirstVC-rules.json**, die Sie zuvor erstellt haben, hoch.

   ![Screenshot, der das Hochladen von Regeldateien zeigt.](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-a-blob-role"></a>Zuweisen einer BLOB-Rolle

Bevor Sie die Anmeldeinformation erstellen, müssen Sie dem angemeldeten Benutzer zuerst die richtige Rollenzuweisung zuweisen, damit er oder sie auf die Dateien im Speicherblob zugreifen kann.

1. Wechseln Sie zu **Speicher** > **Container**.
1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um die Seite **Rollenzuweisung hinzufügen** zu öffnen.
1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | Leser von Speicherblobdaten |
    | Zugriff zuweisen zu | Benutzer, Gruppe oder Dienstprinzipal |
    | Auswählen | &lt;Konto, das Sie zum Ausführen dieser Schritte verwenden&gt; |

    ![Screenshot, der die Seite „Rollenzuweisung hinzufügen“ im Azure-Portal zeigt.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

  >[!IMPORTANT]
  >Standardmäßig wird die Rolle " Besitzer " dem Container Ersteller zugewiesen. Die Rolle „Besitzer“ ist allein nicht ausreichend. Ihr Konto benötigt die Rolle „Leser von Speicherblobdaten“. Weitere Informationen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../../storage/blobs/assign-azure-role-data-access.md).

## <a name="set-up-verifiable-credentials-preview"></a>Einrichten der Vorschau für überprüfbare Anmeldeinformationen

Nun führen Sie den letzten Schritt zum Einrichten Ihres Mandanten für überprüfbare Anmeldeinformationen aus.

1. Suchen Sie im Azure-Portal nach **Nachweise**.
1. Wählen Sie **Nachweise (Vorschau)** aus.
1. Klicken Sie auf **Get started** (Los geht‘s).
1. Richten Sie Ihre Organisation ein, indem Sie die folgenden Informationen bereitstellen:

      - **Organisationsname**: Geben Sie einen Namen ein, um in den überprüfbaren Anmeldeinformationen auf Ihr Unternehmen zu verweisen. Dieser Wert ist nicht kundenbasiert.
      - **Domäne:** Geben Sie eine Domäne ein, die einem Dienstendpunkt in Ihrem DID-Dokument hinzugefügt wird. [Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a) und andere Geldbörsen verwenden diese Informationen, um zu überprüfen, ob Ihre DID mit [Ihrer Domäne verknüpft](how-to-dnsbind.md)ist. Wenn das Wallet die DID überprüfen kann, wird ein verifiziertes Symbol angezeigt. Wenn das Wallet die DID nicht überprüfen kann, wird der Benutzer darüber informiert, dass die Anmeldeinformationen von einer Organisation ausgestellt wurden, die sie nicht überprüfen konnte. Die Domäne bindet Ihre DID an etwas Konkretes, das der Benutzer möglicherweise über Ihr Unternehmen kennt.
      - **Schlüsseltresor:** Geben Sie den Namen des Schlüsseltresors ein, den Sie zuvor erstellt haben.

   >[!IMPORTANT]
   > Die Domäne kann keine Umleitung sein. Andernfalls können DID und Domäne nicht verknüpft werden. Stellen Sie sicher, dass Sie das Format https://www.domain.com verwenden.
  
1. Wählen Sie **Anmeldeinformation speichern und erstellen** aus.

    ![Screenshot, der das Einrichten Ihrer Organisationsidentität zeigt.](media/enable-your-tenant-verifiable-credentials/save-create.png)

Herzlichen Glückwunsch, Ihr Mandant ist jetzt für die überprüfbare Anmeldeinformationen-Vorschau aktiviert!

## <a name="create-your-verifiable-credential-in-the-portal"></a>Erstellen Ihrer überprüfbaren Anmeldeinformationen im Portal

Nach dem vorherigen Schritt wird der Bildschirm **Neue Anmeldeinformation erstellen** angezeigt.

   ![Screenshot, der den Bildschirm "Erste Schritte" zeigt.](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Geben Sie als Anmeldeinformationsname **MyFirstVC** ein. Dieser Name wird im Portal verwendet, um Ihre überprüfbaren Anmeldeinformationen zu identifizieren. Er ist im Rahmen des Vertrags für überprüfbare Anmeldeinformationen enthalten.
1. Wählen Sie im Abschnitt **Anzeigedatei** die Option **Anzeigedatei konfigurieren** aus.
1. Wählen Sie im Abschnitt " **Speicherkonten** " die Option **woodgrovevcstorage** aus.
1. Wählen Sie in der Liste der verfügbaren Container **vc-container** aus.
1. Wählen Sie die Datei **MyFirstVC-display.json** aus, die Sie zuvor erstellt haben.
1. Wählen Sie auf dem Bildschirm **Neue Anmeldeinformation erstellen** im Abschnitt **Regeldatei** die Option **Regeldatei konfigurieren** aus.
1. Wählen Sie im Abschnitt **Speicherkonten** die Option **woodgrovecstorage** aus.
1. Wählen Sie **vc-container** aus.
1. Wählen Sie **MyFirstVC-rules.json** aus.
1. Wählen Sie auf dem Bildschirm **Neue Anmeldeinformation erstellen** die Option **Erstellen** aus.

   ![Screenshot, der das Erstellen einer neuen Anmeldeinformation zeigt.](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Anmeldeinformation-URL

Nachdem Sie nun über neue Anmeldeinformationen verfügen, kopieren Sie die Anmeldeinformationen-URL.

   ![Screenshot, der die Anmeldeinformation-URL zeigt.](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>Die Anmeldeinformationen-URL ist die Kombination der Regeln und Anzeige Dateien. Dies ist die URL, die vom Authenticator ausgewertet wird, bevor dem Benutzer Ausstellungsvoraussetzungen für überprüfbare Anmeldeinformationen angezeigt werden.

## <a name="update-the-sample-app"></a>Aktualisieren der Beispiel-App

Nun nehmen Sie Änderungen am Aussteller-Code der Beispiel-App vor, um ihn mit Ihrer URL für die überprüfbaren Anmeldeinformationen zu aktualisieren. Dieser Schritt ermöglicht es Ihnen, überprüfbare Anmeldeinformationen mit Ihrem eigenen Mandanten auszugeben.

1. Gehen Sie zu dem Ordner, in den Sie die Dateien der Beispiel-App eingefügt haben.
1. Öffnen Sie den Ausstellerordner, und öffnen Sie dann app.js in Visual Studio Code.
1. Aktualisieren Sie die Konstante **credential** mit Ihrer neuen Anmeldeinformation-URL. Legen Sie die Konstante **credentialType** auf **'MyFirstVC'** fest, und speichern Sie die Datei.

    ![Screenshot, der Visual Studio Code mit relevanten Bereichen hervorgehoben zeigt.](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zum Ausstellerordner.
1. Führen Sie das aktualisierte Skript aus.

    ```terminal
    node app.js
    ```

1. Führen Sie mit einer anderen Eingabeaufforderung ngrok aus, um eine URL an 8081 einzurichten. Sie können ngrok global mithilfe des [npm-Pakets ngrok](https://www.npmjs.com/package/ngrok/) installieren.

    ```terminal
    ngrok http 8081
    ```

    >[!IMPORTANT]
    > Möglicherweise wird eine Warnung mit dem Hinweis angezeigt, dass diese App oder Website riskant ist. Diese Meldung wird zu diesem Zeitpunkt erwartet, da Sie Ihren DID nicht mit Ihrer Domäne verknüpft haben. Konfigurationsanweisungen finden Sie unter [DNS-Bindung](how-to-dnsbind.md).

1. Öffnen Sie die HTTPS-URL, die von ngrok generiert wird.

    ![Screenshot, der NGROK-Weiterleitungsendpunkte zeigt.](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

1. Wählen Sie **ANMELDEINFORMATION ERHALTEN** aus.
1. Scannen Sie den QR-Code in Authenticator.
1. Wählen Sie auf dem Bildschirm **Diese App oder Website weist möglicherweise Risiken auf.** die Option **Erweitert** aus.

   ![Screenshot, der die erste Warnung zeigt.](media/enable-your-tenant-verifiable-credentials/site-warning.png)

1. Wählen Sie auf dem nächsten Bildschirm **Diese App oder Website weist möglicherweise Risiken auf.** die Option **Trotzdem fortfahren (unsicher)** aus.

   ![Screenshot, der die zweite Warnung zum Aussteller zeigt.](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)

1. Beachten Sie auf dem Bildschirm **Anmeldeinformation hinzufügen** Folgendes:
    1. Am oberen Bildschirmrand sehen Sie eine rot angezeigte Meldung **Nicht verifiziert**.
    1. Die Anmeldeinformation wird auf der Grundlage der Änderungen angepasst, die Sie an der Anzeigedatei vorgenommen haben.
    1. Die Option **Bei Ihrem Konto anmelden** verweist auf didplayground.b2clogin.com.
    
      ![Screenshot, der den Bildschirm „Anmeldeinformation hinzufügen“ mit Warnung anzeigt.](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

1. Wählen Sie **Bei Ihrem Konto anmelden** aus, und authentifizieren Sie sich mit den Anmeldeinformationen, die Sie im Tutorial [Erste Schritte](get-started-verifiable-credentials.md) angegeben haben.
1. Nach erfolgreicher Authentifizierung wird nun die Schaltfläche **Hinzufügen** aktiviert. Wählen Sie **Hinzufügen**.

    ![Screenshot, der den Bildschirm „Anmeldeinformation hinzufügen“ nach der Authentifizierung zeigt.](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

   Sie erhalten jetzt eine überprüfbare Anmeldeinformation, indem Sie unseren Mandanten verwenden, um eine überprüfbare Anmeldeinformation zu generieren, während Sie weiterhin den Azure B2C-Beispiel-Mandanten für die Authentifizierung verwenden.

     ![Screenshot, der eine überprüfbare Anmeldeinformation zeigt, die von Azure AD ausgestellt und vom Azure B2C-Beispiel-Mandanten authentifiziert wird.](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)

## <a name="verify-the-verifiable-credential-by-using-the-sample-app"></a>Überprüfen der überprüfbaren Anmeldeinformation mithilfe der Beispiel-App

Nachdem Sie nun die überprüfbare Anmeldeinformation von unserem Mandanten erhalten haben, überprüfen Sie sie mithilfe der Beispiel-App.

>[!IMPORTANT]
> Verwenden Sie beim Testen dieselbe E-Mail-Adresse und dasselbe Kennwort wie während des Tutorials [Erste Schritte](get-started-verifiable-credentials.md). Während Ihr Mandant die überprüfbare Anmeldeinformation ausgibt, stammt die Eingabe von einem ID-Token, das vom Azure B2C-Beispiel-Mandanten ausgestellt wurde. Im zweiten Tutorial wechseln Sie für die Tokenausstellung zu Ihrem Mandanten.

1. Wählen Sie im Azure-Portal im Bereich **Überprüfbare Anmeldeinformationen** die Option **Einstellungen** aus. Kopieren Sie den DID.

   ![Screenshot, der das Kopieren des DID zeigt.](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

1. Öffnen Sie jetzt den Überprüfungsordnerteil der Beispiel-App-Dateien. Sie müssen die Datei app.js im Verifizierungs-Beispielcode aktualisieren und die folgenden Änderungen vornehmen:

    - **credential**: Ändern Sie den Eintrag in Ihre Anmeldeinformation-URL.
    - **credentialType**: Geben Sie **'MyFirstVC'** ein.
    - **issuerDid**: Kopieren Sie diesen Wert von Azure-Portal > **Überprüfbare Anmeldeinformationen (Vorschau)**  > **Einstellungen** > **Dezentralisierter Bezeichner (DID)** .
    
   ![Screenshot, der zeigt, dass die Konstante „issuerDid“ (Aussteller-DID) aktualisiert wird, damit sie Ihrem Ausstellerbezeichner entspricht.](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

1. Starten Sie den ngrok-Dienst für den Aussteller nicht mehr.

    ```cmd
    control-c
    ```

1. Führen Sie nun ngrok mit dem Überprüfungs-Port 8082 aus.

    ```cmd
    ngrok http 8082
    ```

1. Wechseln Sie in einem anderen Terminalfenster zur Überprüfungs-App, und führen Sie sie in ähnlicher Weise wie die Aussteller-App aus.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

1. Öffnen Sie die ngrok-URL in Ihrem Browser, und scannen Sie den QR-Code mithilfe von Authenticator auf Ihrem mobilen Gerät.
1. Wählen Sie auf Ihrem mobilen Gerät auf dem Bildschirm **Neue Berechtigungsanforderung** die Option **Zulassen** aus.

    >[!NOTE]
    > Der DID, der diese überprüfbare Anmeldeinformation signiert, ist weiterhin der Azure B2C. Der Überprüfer-DID stammt weiterhin vom Mandanten der Microsoft-Beispiel-App. Da der Microsoft-DID mit einer Domäne verknüpft ist, die wir besitzen, wird die Warnung nicht wie während des Ausstellungsablaufs angezeigt. Diese Situation wird im nächsten Abschnitt aktualisiert.
    
   ![Screenshot, der den Bildschirm „Neue Berechtigungsanforderung“ zeigt.](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

1. Sie haben nun Ihre Anmeldeinformationen erfolgreich überprüft.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über den Beispielcode verfügen, der eine überprüfbare Anmeldeinformation von Ihrem Aussteller ausstellt, fahren Sie mit dem nächsten Abschnitt fort. Sie verwenden Ihren eigenen Identitätsanbieter, um Benutzer zu authentifizieren, die versuchen, überprüfbare Anmeldeinformationen zu erhalten. Sie verwenden auch Ihren DID zum Signieren von Präsentationsanforderungen.

> [!div class="nextstepaction"]
> [Tutorial: Ausstellen und Überprüfen überprüfbarer Anmeldeinformationen mithilfe Ihres Mandanten](issue-verify-verifiable-credentials-your-tenant.md)