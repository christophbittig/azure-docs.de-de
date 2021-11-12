---
title: 'Verknüpfen Ihrer Domäne mit Ihrem dezentralisierten Bezeichner (Decentralized Identifier, DID) (Vorschau): Azure Active Directory-Nachweise'
description: Hier finden Sie Informationen zur DNS-Bindung.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50954d4fef64ffabddf3ec8898ef81a030a77f95
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577344"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did-preview"></a>Verknüpfen Ihrer Domäne mit Ihrem dezentralisierten Bezeichner (Decentralized Identifier, DID) (Vorschau)

> [!IMPORTANT]
> Nachweise für Azure Active Directory befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel:
> [!div class="checklist"]
> * Warum müssen wir unsere DID mit unserer Domäne verknüpfen?
> * Wie werden DIDs und Domänen miteinander verknüpft?
> * Wie funktioniert der Domänenverknüpfungsprozess?
> * Wie funktioniert die Logik für eine verifizierte/nicht verifizierte Domäne?

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre DID mit Ihrer Domäne verknüpfen zu können, muss Folgendes erfüllt sein:

- Schließen Sie die [ersten Schritte](get-started-verifiable-credentials.md) und die [Tutorials](enable-your-tenant-verifiable-credentials.md) ab.

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Warum müssen wir unsere DID mit unserer Domäne verknüpfen?

Eine DID ist zunächst ein Bezeichner, der nicht mit vorhandenen Systemen verankert ist. Eine DID ist praktisch, da sie sich im Besitz eines Benutzers oder einer Organisation befinden und von ihm bzw. von ihr gesteuert werden kann. Wenn eine mit der Organisation interagierende Entität allerdings nicht weiß, zu wem die DID gehört, ist sie nicht mehr ganz so praktisch.

Die Verknüpfung einer DID mit einer Domäne löst das anfängliche Vertrauensproblem, da jede Entität die Beziehung zwischen einer DID und einer Domäne kryptografisch überprüfen kann.

## <a name="how-do-we-link-dids-and-domains"></a>Wie werden DIDs und Domänen miteinander verknüpft?

Zur Erstellung einer Verknüpfung zwischen einer Domäne und einer DID implementieren wir einen offenen, von der Decentralized Identity Foundation geschriebenen Standard, der als [Well Known DID Configuration](https://identity.foundation/.well-known/resources/did-configuration/) (Bekannte DID-Konfiguration) bezeichnet wird. Der Dienst für überprüfbare Anmeldeinformationen in Azure Active Directory (Azure AD) hilft Ihrer Organisation, die Verbindung zwischen der DID und der Domäne herzustellen, indem er die Domäneninformationen einbezieht, die Sie in Ihrer DID angegeben haben, und die bekannte Konfigurationsdatei generiert:

1. Azure AD verwendet die im Rahmen der Organisationseinrichtung angegebenen Domäneninformationen, um einen Dienstendpunkt innerhalb des DID-Dokuments zu schreiben. Alle Parteien, die mit Ihrer DID interagieren, können die Domäne sehen, mit der Ihre DID laut Angabe verknüpft wurde.  

    ```json
    "service": [
      {
        "id": "#linkeddomains",
        "type": "LinkedDomains",
        "serviceEndpoint": {
          "origins": [
            "https://www.contoso.com/"
          ]
        }
      }
    ]
    ```

2. Vom Nachweisdienst in Azure AD wird eine kompatible Ressource mit der bekannten Konfiguration generiert, die Sie in Ihrer Domäne hosten können. Die Konfigurationsdatei enthält einen selbst ausgestellten Nachweis mit dem Nachweistyp „DomainLinkageCredential“ (signiert mit Ihrer DID, die über einen Ursprung Ihrer Domäne verfügt). Das folgende Beispiel zeigt das Konfigurationsdokument, das unter der Stammdomänen-URL gespeichert ist.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Wenn Sie über die Datei mit der bekannten Konfiguration verfügen, müssen Sie die Datei verfügbar machen. Verwenden Sie dabei den Domänennamen, den Sie beim Aktivieren von AAD für Nachweise angegeben haben.

* Hosten Sie die Datei mit der bekannten DID-Konfiguration am Stamm der Domäne.
* Verwenden Sie keine Umleitungen.
* Verwenden Sie HTTPS, um die Konfigurationsdatei zu verteilen.

>[!IMPORTANT]
>Von Microsoft Authenticator werden keine Umleitungen berücksichtigt. Bei der angegebenen URL muss es sich um die endgültige Ziel-URL handeln.

## <a name="user-experience"></a>Benutzererfahrung 

Wenn ein Benutzer einen Ausstellungsvorgang durchläuft oder einen Nachweis präsentiert, muss er etwas über die Organisation und die zugehörige DID wissen. Wenn sich die Domäne in unserem Nachweis-Wallet befindet, wird die Beziehung einer DID mit der Domäne im DID-Dokument durch Microsoft Authenticator überprüft, und den Benutzern werden abhängig vom Ergebnis zwei verschiedene Oberflächen angezeigt.

## <a name="verified-domain"></a>Verifizierte Domäne

Damit von Microsoft Authenticator ein Symbol vom Typ **Verifiziert** angezeigt wird, müssen mehrere Dinge zutreffen:

* Die zum Signieren der SIOP-Anforderung (Self-Issued Open ID) verwendete DID muss über einen Dienstendpunkt für die verknüpfte Domäne verfügen.
* Die Stammdomäne verwendet keine Umleitung, aber HTTPS.
* Die im DID-Dokument aufgeführte Domäne verfügt über eine auflösbare bekannte Ressource.
* Der Nachweis der bekannten Ressource ist mit der gleichen DID signiert, die auch zum Signieren der SIOP verwendet wurde, die von Microsoft Authenticator zum Starten des Flows verwendet wurde.

Wenn alle der obigen Bedingungen erfüllt sind, zeigt Microsoft Authenticator eine Verifizierungsseite an und schließt die validierte Domäne ein.

![Neue Berechtigungsanforderung](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Nicht verifizierte Domäne

Trifft einer der obigen Punkte nicht zu, wird von Microsoft Authenticator eine ganzseitige Warnung angezeigt. Darin wird der Benutzer darauf hingewiesen, dass die Domäne nicht verifiziert wurde und es sich hierbei um eine riskante Transaktion handelt, bei der Vorsicht geboten ist. Dieser Weg wurde aus folgenden Gründen gewählt:

* Die DID ist in keiner Domäne verankert.
* Die Konfiguration wurde nicht ordnungsgemäß eingerichtet.
* Die DID, mit der der Benutzer interagiert, ist schädlich und kann nicht als Beleg für den Besitz einer Domäne verwendet werden (da dies nicht der Fall ist). Aufgrund dieses letzten Punkts ist es zwingend erforderlich, Ihre DID mit der Domäne zu verknüpfen, mit der der Benutzer vertraut ist, um die Anzeige der Warnmeldung zu vermeiden.

![Warnung im Zusammenhang mit einer nicht verifizierten Domäne auf dem Bildschirm zum Hinzufügen eines Nachweises](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Verteilen der bekannten Konfiguration

1. Navigieren Sie unter „Verifizierbare Anmeldeinformationen“ zur Seite „Einstellungen“, und wählen Sie die Option zum **Überprüfen dieser Domäne aus**.

   ![Überprüfen dieser Domäne in den Einstellungen](media/how-to-dnsbind/settings-verify.png) 

2. Laden Sie die Datei „did-configuration.json“ herunter, die in der folgenden Abbildung zu sehen ist:

   ![Herunterladen der bekannten Konfiguration](media/how-to-dnsbind/verify-download.png) 

3. Kopieren Sie den linked_did Wert (JWT), öffnen Sie [https://jwt.ms/](https://www.jwt.ms), fügen Sie das JWT ein, und überprüfen Sie, ob die Domäne korrekt ist.

4. Kopieren Sie Ihre DID, und öffnen Sie den [ION-Netzwerk-Explorer](https://identity.foundation/ion/explorer), um sich zu vergewissern, dass die gleiche Domäne im DID-Dokument enthalten ist. 

5. Hosten Sie die Ressource mit der bekannten Konfiguration am angegebenen Ort. Ein Beispiel: `https://www.example.com/.well-known/did-configuration.json`

6. Testen Sie zur Validierung das Ausstellen oder Präsentieren mit Microsoft Authenticator. Achten Sie darauf, dass in Authenticator die Einstellung „Warnung zu unsicheren Apps“ aktiviert ist.

>[!NOTE]
>„Warnung zu unsicheren Apps“ ist standardmäßig aktiviert.

Glückwunsch! Sie haben das Vertrauensnetz mittels Bootstrap mit Ihrer DID verknüpft.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie beim Onboarding falsche Domäneninformationen eingeben oder die Angaben ändern möchten, müssen Sie die [Funktion deaktivieren](how-to-opt-out.md). Das Aktualisieren Ihres DID-Dokuments wird derzeit nicht unterstützt. Wenn Sie die Funktion deaktivieren und anschließend erneut aktivieren, wird eine neue DID neu erstellt.
