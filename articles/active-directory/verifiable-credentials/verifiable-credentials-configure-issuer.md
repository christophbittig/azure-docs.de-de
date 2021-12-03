---
title: 'Tutorial: Ausstellen von Azure AD-Nachweisen über eine Anwendung (Vorschau)'
description: In diesem Tutorial erfahren Sie anhand einer Beispiel-App, wie Sie Nachweise ausstellen.
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
manager: karenh444
ms.author: barclayn
ms.topic: tutorial
ms.date: 10/08/2021
ms.openlocfilehash: a7065441bc1354d27bef4f3523c8cfa01d789974
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508002"
---
# <a name="issue-azure-ad-verifiable-credentials-from-an-application-preview"></a>Ausstellen von Azure AD-Nachweisen über eine Anwendung (Vorschau)

In diesem Tutorial führen Sie eine Beispielanwendung auf Ihrem lokalen Computer aus, die eine Verbindung mit Ihrem Azure AD-Mandanten (Azure Active Directory) herstellt. Mithilfe der Anwendung stellen Sie eine Karte für Nachweisexperten aus und überprüfen sie.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> - Einrichten einer Azure Blob Storage-Instanz zum Speichern der Azure AD-Nachweiskonfigurationsdateien
> - Erstellen und Hochladen der Nachweiskonfigurationsdateien
> - Erstellen der Karte für Nachweisexperten in Azure
> - Sammeln von Nachweis- und Umgebungsdetails zum Einrichten der Beispielanwendung
> - Herunterladen des Beispielanwendungscodes auf Ihren lokalen Computer
> - Aktualisieren der Beispielanwendung mit Ihrer Karte für Nachweisexperten und den Umgebungsdetails
> - Ausführen der Beispielanwendung und Ausstellen der ersten Karte für Nachweisexperten
> - Überprüfen der Karte für Nachweisexperten

Im folgenden Diagramm sind die Architektur für Azure AD-Nachweise und die Komponente dargestellt, die Sie konfigurieren:

![Diagramm: Veranschaulichung der Architektur für Azure AD-Nachweise](media/verifiable-credentials-configure-issuer/verifiable-credentials-architecture.png)

## <a name="prerequisites"></a>Voraussetzungen

- [Einrichten eines Mandanten für Azure AD-Nachweise](./verifiable-credentials-configure-tenant.md)
- Installieren von [Git](https://git-scm.com/downloads), um das Repository zu klonen, das als Host für die Beispiel-App fungiert
- [Visual Studio Code](https://code.visualstudio.com/Download) oder ein ähnlicher Code-Editor
- [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
- [ngrok](https://ngrok.com/) (kostenlos)
- Ein mobiles Gerät mit Microsoft Authenticator:
  - Installation von Android, Version 6.2108.5654 oder höher
  - Installation von iOS, Version 6.5.82 oder höher

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Azure Blob Storage ist eine Objektspeicherlösung für die Cloud. Azure AD-Nachweise nutzen [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md), um die Konfigurationsdateien zu speichern, wenn der Dienst Nachweise ausstellt.

Erstellen und konfigurieren Sie Blob Storage mithilfe der folgenden Schritte:

1. Wenn Sie kein Azure Blob Storage-Konto haben, müssen Sie [eines erstellen](../../storage/common/storage-account-create.md).
1. Erstellen Sie nach der Erstellung des Speicherkontos einen Container. Scrollen Sie im linken Menü für das Speicherkonto zum Abschnitt **Datenspeicher**, und wählen Sie **Container** aus.
1. Wählen Sie **+ Container** aus.
1. Geben Sie einen Namen für den neuen Container ein. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten. Beispiel: *vc-container*
1. Legen Sie **Öffentliche Zugriffsebene** auf **Privat** (kein anonymer Zugriff) fest.
1. Klicken Sie auf **Erstellen**.  

   ![Screenshot: Erstellen eines Containers](media/verifiable-credentials-configure-issuer/create-container.png)

## <a name="grant-access-to-the-container"></a>Gewähren des Zugriffs auf den Container

Gewähren Sie nach dem Erstellen des Containers dem angemeldeten Benutzer die richtige Rollenzuweisung, damit er auf die Dateien in Blob Storage zugreifen kann.

1. Wählen Sie in der Containerliste **vc-container** aus.

1. Wählen Sie im Menü die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **+ Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.

     ![Screenshot: Hinzufügen einer neuen Rollenzuweisung zum Blobcontainer](media/verifiable-credentials-configure-issuer/add-role-assignment.png)

1. Gehen Sie unter **Rollenzuweisung hinzufügen** wie folgt vor:

    1. Wählen Sie unter **Rolle** die Option **Storage-Blobdatenleser** aus.

    1. Wählen Sie für **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.

    1. Suchen Sie dann nach dem Konto, das Sie zum Ausführen dieser Schritte verwenden, und wählen Sie es aus.

        ![Screenshot: Einrichten der neuen Rollenzuweisung](media/verifiable-credentials-configure-issuer/add-role-assignment-container.png)

>[!IMPORTANT]
>Containererstellern wird standardmäßig die Rolle „Besitzer“ zugewiesen. Die Rolle „Besitzer“ ist allein nicht ausreichend. Ihr Konto benötigt die Rolle „Leser von Speicherblobdaten“. Weitere Informationen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../../storage/blobs/assign-azure-role-data-access.md).

### <a name="upload-the-configuration-files"></a>Hochladen der Konfigurationsdateien

Azure AD-Nachweise verwenden zwei JSON-Konfigurationsdateien: die Regel- und die Anzeigedatei. 

- In der *Regeldatei* sind wichtige Eigenschaften der Nachweise beschrieben. Insbesondere werden die Ansprüche beschrieben, die Antragsteller (Benutzer) bereitstellen müssen, damit ein Nachweis für sie ausgestellt wird. 
- Mit der *Anzeigedatei* wird das Branding des Nachweises und das Format der Ansprüche gesteuert.

In diesem Abschnitt laden Sie eine Beispielregeldatei und eine Beispielanzeigedatei in Ihren Speicher hoch. Weitere Informationen finden Sie unter [Gewusst wie: Anpassen Ihrer Nachweise (Vorschau)](credential-design.md).

Führen Sie zum Hochladen der Konfigurationsdateien die folgenden Schritte aus:

1. Kopieren Sie den folgenden JSON-Code, und speichern Sie den Inhalt in einer Datei namens *VerifiedCredentialExpertDisplay.json*:

    ```json
    {
      "default": {
        "locale": "en-US",
        "card": {
          "title": "Verified Credential Expert",
          "issuedBy": "Microsoft",
          "backgroundColor": "#2E4053",
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

1. Kopieren Sie den folgenden JSON-Code, und speichern Sie den Inhalt in einer Datei namens *VerifiedCredentialExpertRules.json*. Im folgenden Nachweis sind einige einfache Ansprüche definiert: `firstName` und `lastName`.

    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "id": "https://self-issued.me",
            "mapping": {
              "firstName": { "claim": "$.given_name" },
              "lastName": { "claim": "$.family_name" }
            },
            "configuration": "https://self-issued.me",
            "client_id": "",
            "redirect_uri": ""
          }
        ]
      },
      "validityInterval": 2592001,
      "vc": {
        "type": [ "VerifiedCredentialExpert" ]
      }
    }
    ```
    
1. Navigieren Sie im Azure-Portal zu dem [von Ihnen erstellten](#create-a-storage-account) Azure Blob Storage-Container.

1. Wählen Sie im Menü auf der linken Seite **Container** aus, um eine Liste mit den darin enthaltenen Blobs anzuzeigen. Wählen Sie den zuvor erstellten Container **vc-container** aus.

1. Wählen Sie **Hochladen** aus, um den Uploadbereich zu öffnen, und navigieren Sie in Ihrem lokalen Dateisystem zu einer Datei, um sie hochzuladen. Wählen Sie die Dateien **VerifiedCredentialExpertDisplay.json** und **VerifiedCredentialExpertRules.json** aus. Wählen Sie dann **Hochladen** aus, um die Datei in den Container hochzuladen.

## <a name="create-the-verified-credential-expert-card-in-azure"></a>Erstellen der Karte für Nachweisexperten in Azure

In diesem Schritt erstellen Sie die Karte für Nachweisexperten mithilfe von Azure AD-Nachweisen. Nach dem Erstellen eines Nachweises kann Ihr Azure AD-Mandant diesen Nachweis für Benutzer ausstellen, die den Prozess initiieren.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach *Nachweise*. Wählen Sie dann **Nachweise (Vorschau)** aus.
1. Nach dem [Einrichten Ihres Mandanten](verifiable-credentials-configure-tenant.md) sollte das Fenster **Neue Anmeldeinformationen erstellen** angezeigt werden. Wenn er nicht geöffnet wird oder Sie weitere Nachweise erstellen möchten, wählen Sie **Leistungsnachweise** aus. Wählen Sie anschließend **+ Leistungsnachweis** aus.
1. Gehen Sie unter **Neue Anmeldeinformationen erstellen** wie folgt vor:

    1. Geben Sie in das Feld **Name** den Namen **VerifiedCredentialExpert** ein. Dieser Name wird im Portal verwendet, um Ihre überprüfbaren Anmeldeinformationen zu identifizieren. Er ist im Rahmen des Vertrags für überprüfbare Anmeldeinformationen enthalten.

    1. Wählen Sie unter **Abonnement** das Azure AD-Abonnement aus, unter dem Sie die Blob Storage-Instanz erstellt haben.

    1. Wählen Sie unter **Anzeigedatei** die Option **Anzeigedatei auswählen** aus. Wählen Sie im Abschnitt „Speicherkonten“ die Option **vc-container** aus. Wählen Sie dann die Datei **VerifiedCredentialExpertDisplay.json** aus, und klicken Sie auf **Auswählen**.

    1. Sie müssen unter **Rules file** (Regeldatei) die **Regeldatei auswählen**. Wählen Sie im Abschnitt „Speicherkonten“ die Option **vc-container** aus. Wählen Sie dann die Datei **VerifiedCredentialExpertRules.json** und anschließend **Auswählen** aus.

    1. Klicken Sie auf **Erstellen**.    

Der folgende Screenshot zeigt, wie Sie einen neuen Nachweis erstellen:

  ![Screenshot: Erstellen eines neuen Nachweises](media/verifiable-credentials-configure-issuer/how-create-new-credential.png)

## <a name="gather-credentials-and-environment-details"></a>Erfassen von Anmeldeinformationen und Umgebungsdetails

Nachdem Sie nun über einen neuen Nachweis verfügen, sammeln Sie einige Informationen zu Ihrer Umgebung und dem von Ihnen erstellten Nachweis. Sie verwenden diese Informationen beim Einrichten Ihrer Beispielanwendung.

1. Wählen Sie dann unter „Nachweise“ die Option **Nachweise** aus. Wählen Sie in der Liste der Nachweise den zuvor erstellten Nachweis **VerifiedCredentialExpert** aus.

    ![Screenshot: Auswählen des neu erstellten Nachweises](media/verifiable-credentials-configure-issuer/select-verifiable-credential.png)

1. Kopieren Sie die **URL für das Ausstellen von Nachweisen**. Diese URL setzt sich aus den Regel- und Anzeigedateien zusammen. Dies ist die URL, die vom Authenticator ausgewertet wird, bevor dem Benutzer Ausstellungsvoraussetzungen für überprüfbare Anmeldeinformationen angezeigt werden. Notieren Sie sie zur späteren Verwendung.

1. Kopieren Sie den Wert unter **Dezentraler Bezeichner**, und notieren Sie ihn zur späteren Verwendung.

1. Kopieren Sie die **Mandanten-ID**, und notieren Sie sie zur späteren Verwendung.

   ![Screenshot: Kopieren der erforderlichen Werte für die Nachweise](media/verifiable-credentials-configure-issuer/copy-the-issue-credential-url.png)

## <a name="download-the-sample-code"></a>Herunterladen des Beispielcodes

Die Beispielanwendung ist in .NET verfügbar, und der Code wird in einem GitHub-Repository verwaltet. Laden Sie den Beispielcode von [GitHub](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet) herunter, oder klonen Sie das Repository auf Ihrem lokalen Computer:


```bash
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet.git
```

## <a name="configure-the-verifiable-credentials-app"></a>Konfigurieren der Nachweis-App

Erstellen Sie einen geheimen Clientschlüssel für die von Ihnen erstellte registrierte Anwendung. Die Beispielanwendung verwendet den geheimen Clientschlüssel beim Anfordern von Token als Identitätsnachweis.

1. Navigieren Sie zur Seite **App-Registrierungen** in **Azure Active Directory**.

1. Wählen Sie die zuvor erstellte Anwendung **verifiable-credentials-app** aus.

1. Wählen Sie den Namen aus, der in den Registrierungsdetails angezeigt werden soll.

1. Kopieren Sie den Wert unter **Anwendungs-ID (Client)** , und speichern Sie ihn zur späteren Verwendung.  

     ![Screenshot: Kopieren der App-Registrierungs-ID](media/verifiable-credentials-configure-issuer/copy-app-id.png)

1. Wählen Sie im Hauptmenü unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.

1. Wählen Sie **Neuer geheimer Clientschlüssel** aus, und führen Sie die folgenden Schritte aus:

    1. Geben Sie im Feld **Beschreibung** eine Beschreibung für den geheimen Clientschlüssel ein (z. B. **vc-sample-secret**).

    1. Wählen Sie unter **Gültig bis** einen Gültigkeitszeitraum für das Geheimnis (beispielsweise sechs Monate) aus. Wählen Sie anschließend **Hinzufügen**.

    1. Notieren Sie den **Wert** des Geheimnisses. Dieser Wert wird in einem späteren Schritt für die Konfiguration verwendet. Der Wert des Geheimnisses wird nicht erneut angezeigt und kann auch nicht auf andere Weise abgerufen werden. Notieren Sie ihn, sobald er angezeigt wird.

An diesem Punkt sollten Sie über alle erforderlichen Informationen verfügen, die Sie zum Einrichten Ihrer Beispielanwendung benötigen.

## <a name="update-the-sample-application"></a>Aktualisieren der Beispielanwendung

Nun nehmen Sie Änderungen am Aussteller-Code der Beispiel-App vor, um ihn mit Ihrer URL für die überprüfbaren Anmeldeinformationen zu aktualisieren. Dieser Schritt ermöglicht es Ihnen, überprüfbare Anmeldeinformationen mit Ihrem eigenen Mandanten auszugeben.

1. Öffnen Sie im Ordner *active-directory-verifiable-credentials-dotnet-main* Visual Studio Code, und wählen Sie das Projekt im Ordner *1.asp-net-core-api-idtokenhint* aus.

1. Öffnen Sie im Projektstammordner die Datei *appsettings.json*. Diese Datei enthält Informationen zu Ihren Azure AD-Nachweisen. Aktualisieren Sie die folgenden Eigenschaften mit den Informationen, die Sie in den vorherigen Schritten notiert haben:

    1. **TenantID**: Ihre Mandanten-ID
    1. **ClientID**: Ihre Client-ID
    1. **ClientSecret**: Ihr geheimer Clientschlüssel
    1. **IssuerAuthority**: Ihr dezentraler Bezeichner
    1. **VerifierAuthority**: Ihr dezentraler Bezeichner
    1. **CredentialManifest**: Die URL für das Ausstellen von Nachweisen

1. Speichern Sie die *appsettings.json*-Datei.

Der folgende JSON-Code zeigt die vollständige Datei *appsettings.json*:

```json
{
  "AppSettings": {
    "Endpoint": "https://beta.did.msidentity.com/v1.0/{0}/verifiablecredentials/request",
    "VCServiceScope": "bbb94529-53a3-4be5-a069-7eaf2712b826/.default",
    "Instance": "https://login.microsoftonline.com/{0}",

    "TenantId": "12345678-0000-0000-0000-000000000000",
    "ClientId": "33333333-0000-0000-0000-000000000000",
    "ClientSecret": "123456789012345678901234567890",
    "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]",
    "IssuerAuthority": "did:ion:EiCcn9dz_OC6HY60AYBXF2Dd8y5_2UYIx0Ni6QIwRarjzg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfN2U4MmYzNjUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiaUo0REljV09aWVA...",
    "VerifierAuthority": " did:ion:EiCcn9dz_OC6HY60AYBXF2Dd8y5_2UYIx0Ni6QIwRarjzg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfN2U4MmYzNjUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiaUo0REljV09aWVA...",
    "CredentialManifest":  "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert"
  }
}
```

## <a name="issue-your-first-verified-credential-expert-card"></a>Ausstellen Ihrer ersten Karte für Nachweisexperten

Sie können jetzt Ihre erste Karte für Nachweisexperten ausstellen, indem Sie die Beispielanwendung ausführen.

1. Führen Sie in Visual Studio Code das Projekt *Verifiable_credentials_DotNet* aus. Führen Sie alternativ in der Befehlsshell die folgenden Befehle aus:

    ```bash
    cd active-directory-verifiable-credentials-dotnet/1-asp-net-core-api-idtokenhint  dotnet build "AspNetCoreVerifiableCredentials.csproj" -c Debug -o .\\bin\\Debug\\netcoreapp3.  dotnet run
    ```

1. Führen Sie in einem anderen Terminal den folgenden Befehl aus. Mit diesem Befehl wird [ngrok](https://ngrok.com/) ausgeführt, um eine URL an 3000 einzurichten und öffentlich im Internet verfügbar zu machen.

    ```bash
    ngrok http 5000
    ```

    >[!NOTE]
    > Auf einigen Computern müssen Sie den Befehl unter Umständen im folgenden Format ausführen: `./ngrok http 3000`.

1. Öffnen Sie die HTTPS-URL, die von ngrok generiert wird.

     ![Screenshot: Abrufen der öffentlichen ngrok-URL](media/verifiable-credentials-configure-issuer/ngrok-url.png)

1. Wählen Sie in einem Webbrowser **Get Credential** (Nachweis abrufen) aus.

     ![Screenshot: Auswählen von „Get Credential“ (Nachweis abrufen) in der Beispiel-App](media/verifiable-credentials-configure-issuer/get-credentials.png)

1. Scannen Sie mithilfe Ihres mobilen Geräts den QR-Code mit der Authenticator-App. Sie können den QR-Code auch direkt mit Ihrer Kamera scannen. Dadurch wird die Authenticator-App für Sie geöffnet.

    ![Screenshot: Scannen des QR-Codes](media/verifiable-credentials-configure-issuer/scan-issuer-qr-code.png)

1. Zu diesem Zeitpunkt wird eine Meldung mit der Warnung angezeigt, dass diese App oder Website riskant sein könnte. Wählen Sie **Erweitert** aus.

     ![Screenshot: Reagieren auf die Warnmeldung](media/verifiable-credentials-configure-issuer/at-risk.png)

1. Wählen Sie in der Warnung zur riskanten Website die Option **Trotzdem fortfahren (unsicher)** aus. Diese Warnung wird angezeigt, weil Ihre Domäne nicht mit dem dezentralen Bezeichner (DID) verknüpft ist. Befolgen Sie zum Überprüfen Ihrer Domäne die Anleitung unter [Verknüpfen Ihrer Domäne mit Ihrem dezentralisierten Bezeichner (Decentralized Identifier, DID)](how-to-dnsbind.md). Für dieses Tutorial können Sie die Domänenregistrierung überspringen und **Trotzdem fortfahren (unsicher)** auswählen.

     ![Screenshot: Fortfahren bei der Warnung zu riskanter App oder Website](media/verifiable-credentials-configure-issuer/proceed-anyway.png)

1. Sie werden zur Eingabe eines PIN-Codes aufgefordert, der auf dem Bildschirm angezeigt wird, auf dem Sie den QR-Code gescannt haben. Die PIN schützt die Ausstellung zusätzlich. Der PIN-Code wird jedes Mal nach dem Zufallsprinzip generiert, wenn ein QR-Code für die Ausstellung angezeigt wird.

     ![Screenshot: Eingeben des PIN-Codes](media/verifiable-credentials-configure-issuer/enter-verification-code.png)

1. Nach der Eingabe der PIN wird der Bildschirm **Nachweis hinzufügen** angezeigt. Am oberen Bildschirmrand sehen Sie eine rot angezeigte Meldung **Nicht verifiziert**. Diese Warnung bezieht sich auf die oben erwähnte Domänenüberprüfung.

1. Wählen Sie **Hinzufügen** aus, um Ihren neuen Nachweis zu akzeptieren.

    ![Screenshot: Hinzufügen des neuen Nachweises](media/verifiable-credentials-configure-issuer/new-verifiable-credential.png)

Glückwunsch! Sie verfügen nun über einen Nachweis für verifizierte Anmeldeinformationsexperten.

  ![Screenshot: Neu hinzugefügter Nachweis](media/verifiable-credentials-configure-issuer/verifiable-credential-has-been-added.png)

Kehren Sie zur Beispiel-App zurück. Sie sehen, dass die Ausstellung des Nachweises erfolgreich war.

  ![Screenshot: Erfolgreich ausgestellter Nachweis](media/verifiable-credentials-configure-issuer/credentials-issued.png)

## <a name="verify-the-verified-credential-expert-card"></a>Überprüfen der Karte für Nachweisexperten

Sie können jetzt Ihre Karte für Nachweisexperten überprüfen, indem Sie die Beispielanwendung erneut ausführen.

1. Klicken Sie in Ihrem Browser auf die Schaltfläche „Zurück“, um zur Startseite der Beispiel-App zurückzukehren.

1. Wählen Sie **Verify credential** (Nachweis überprüfen) aus.  

   ![Screenshot: Auswählen der Schaltfläche „Verify credential“ (Nachweis überprüfen)](media/verifiable-credentials-configure-issuer/verify-credential.png)

1. Scannen Sie den QR-Code mithilfe der Authenticator-App oder direkt mit der Kamera des Mobilgeräts.

1. Wenn die Warnmeldung angezeigt wird, wählen Sie **Erweitert** aus. Wählen Sie anschließend **Trotzdem fortfahren (unsicher)** aus.

1. Genehmigen Sie die Präsentationsanforderung, indem Sie **Zulassen** auswählen.

    ![Screenshot: Genehmigen der neuen Präsentationsanforderung für Nachweise](media/verifiable-credentials-configure-issuer/approve-presentation-request.jpg)

1. Nach dem Genehmigen der Präsentationsanforderung sehen Sie, dass die Anforderung genehmigt wurde. Sie können auch das Protokoll überprüfen. Wählen Sie zum Anzeigen des Protokolls den Nachweis aus:  

    ![Screenshot: Karte für Nachweisexperten](media/verifiable-credentials-configure-issuer/verifable-credential-info.png)

1. Wählen Sie dann **Letzte Aktivität** aus.  

    ![Screenshot: Schaltfläche „Letzte Aktivität“, über die Sie zum Nachweisverlauf gelangen](media/verifiable-credentials-configure-issuer/verifable-credential-history.jpg)

1. Sie können nun die aktuellen Aktivitäten Ihres Nachweises anzeigen.

    ![Screenshot: Verlauf der Nachweise](media/verifiable-credentials-configure-issuer/verify-credential-history.jpg)

1. Kehren Sie zur Beispiel-App zurück. Sie sehen, dass die Präsentation der Nachweise empfangen wurde.  
    ![Screenshot: Präsentation wurde empfangen.](media/verifiable-credentials-configure-issuer/verifiable-credential-expert-verification.png)

## <a name="verifiable-credential-names"></a>Nachweisnamen 

Ihr Nachweis enthält **Megan Bowen** für die Werte für Vorname und Nachname im Nachweis. Diese Werte wurden in der Beispielanwendung hartcodiert und dem Nachweis zum Zeitpunkt der Ausstellung in den Nutzdaten hinzugefügt. 

In realen Szenarien pullt Ihre Anwendung die Benutzerdetails von einem Identitätsanbieter. Der folgende Codeausschnitt zeigt, wo der Name in der Beispielanwendung festgelegt wird. 

```csharp
//file: IssuerController.cs
[HttpGet("/api/issuer/issuance-request")]
public async Task<ActionResult> issuanceRequest()
  {
    ...

    // Here you could change the payload manifest and change the first name and last name.
    payload["issuance"]["claims"]["given_name"] = "Megan";
    payload["issuance"]["claims"]["family_name"] = "Bowen";
    ...
}
  ```

## <a name="next-steps"></a>Nächste Schritte

Im [nächsten Schritt](verifiable-credentials-configure-verifier.md) erfahren Sie, wie eine Drittanbieteranwendung (auch als „anspruchsbasierte Anwendung“ bezeichnet) Ihre Nachweise mit einem eigenen API-Dienst für Azure AD-Mandantennachweise überprüfen kann.

