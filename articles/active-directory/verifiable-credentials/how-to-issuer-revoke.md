---
title: 'Widerrufen eines Nachweises als Aussteller: Azure Active Directory-Nachweise (Vorschau)'
description: Hier erfahren Sie, wie Sie einen von Ihnen ausgestellten Nachweis widerrufen.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8548410c2df423f8635092002401501705fb24a8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006829"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Widerrufen eines ausgestellten Nachweises (Vorschau)

Die Arbeit mit Nachweisen (Verifiable Credentials, VCs) umfasst nicht nur das Ausstellen, sondern auch das Widerrufen von Nachweisen. Dieser Artikel enthält Informationen zur Eigenschaft **status** der VC-Spezifikation sowie zum Widerrufsprozess. Außerdem gehen wir auf mögliche Gründe für das Widerrufen eines Nachweises sowie auf einige Daten- und Datenschutzaspekte ein.

> [!IMPORTANT]
> Azure Active Directory-Nachweise befinden sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Eigenschaft „status“ in der Nachweisspezifikation

Bevor wir auf die Auswirkungen eines Nachweiswiderrufs eingehen, ist es hilfreich zu wissen, was die **Statusüberprüfung** ist und wie sie aktuell funktioniert.

Die Eigenschaft **status** wird im Abschnitt [4.9](https://www.w3.org/TR/vc-data-model/#status) der [Spezifikation für W3C-Nachweise](https://www.w3.org/TR/vc-data-model/) so beschrieben:

„Diese Spezifikation definiert die folgende Eigenschaft **credentialStatus** für die Ermittlung von Informationen zum aktuellen Status eines Nachweises (also ob er beispielsweise ausgesetzt oder widerrufen wurde).“

In der W3C-Spezifikation wird jedoch kein Format für die Implementierung der **Statusüberprüfung** definiert.

„Die Definition von Datenmodellen, Formaten und Protokollen für Statusschemas wird in dieser Spezifikation nicht behandelt. Mit „[VC-EXTENSION-REGISTRY]“ steht eine Nachweiserweiterungsregistrierung zur Verfügung, die verfügbare Statusschemas für Implementierer enthält, die Statusüberprüfungen für Nachweise implementieren möchten.“

>[!NOTE]
>Die Statusüberprüfungsimplementierung von Microsoft ist derzeit proprietär, wir arbeiten aber aktiv mit der DID-Community zusammen, um einen allgemeinen Standard zu etablieren.

## <a name="how-does-the-status-property-work"></a>Funktionsweise der Eigenschaft **status**

Jeder von Microsoft ausgestellte Nachweis verfügt über ein Attribut namens „credentialStatus“. Es wird mit einer Status-API aufgefüllt, die von Microsoft in Ihrem Auftrag verwaltet wird. Hier sehen Sie ein Beispiel:

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

Das Open Source-basierte Verifiable Credentials SDK kümmert sich um das Aufrufen der Status-API sowie um das Angeben der erforderlichen Daten.

Nach dem Aufrufen der API und dem Angeben der richtigen Informationen wird entweder „True“ oder „False“ zurückgegeben. „True“ bedeutet, dass der Nachweis weiterhin aktiv ist. „False“ bedeutet, dass der Nachweis vom Aussteller aktiv widerrufen wurde.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Mögliche Gründe für das Widerrufen eines Nachweises

Jeder Kunde hat seine eigenen Gründe für das Widerrufen von Nachweisen. Im Anschluss finden Sie jedoch einige allgemeine Gründe, die uns bislang mitgeteilt wurden: 

- Studenten-ID: Der Student ist kein aktiver Student an der Universität mehr.
- Mitarbeiter-ID: Der Mitarbeiter ist kein aktiver Mitarbeiter mehr.
- Führerschein eines Bundesstaats: Der Fahrer lebt nicht mehr in diesem Bundesstaat.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Einrichten eines widerrufbaren Nachweises

Standardmäßig werden keine Nachweisdaten bei Microsoft gespeichert. Daher stehen uns keine Daten zur Verfügung, auf die wir zurückgreifen können, um eine bestimmte Nachweis-ID zu widerrufen. Der Aussteller muss ein bestimmtes Feld aus dem Nachweisattribut angeben, das von Microsoft zur Indizierung und anschließend zu Salt- und Hashzwecken verwendet werden soll.

>[!NOTE]
>Das Hashing ist ein unidirektionaler kryptografischer Vorgang, der eine Eingabe, die als ```preimage``` bezeichnet wird, in eine Ausgabe (Hash) mit einer festen Länge umwandelt. Aktuell ist es rechnerisch nicht sinnvoll, einen Hashvorgang umzukehren.

Sie können Microsoft mitteilen, welches Attribut des Nachweises indiziert werden soll. Die Indizierung impliziert, dass indizierte Werte verwendet werden können, um Ihre Nachweise nach den zu widerrufenden VCs zu durchsuchen.

**Beispiel:** Alice war bei Woodgrove beschäftigt. Sie hat Woodgrove verlassen und arbeitet jetzt bei Contoso. Jane, die IT-Administratorin für Woodgrove, sucht in den Suchabfrage zum Widerrufen von Nachweisen nach der E-Mail-Adresse von Alice. In diesem Beispiel hat Jane das E-Mail-Feld des Woodgrove-Mitarbeiternachweises indiziert. 

Im folgenden Beispiel sehen Sie, wie die Regeldatei geändert wird, um den Index einzuschließen.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Über eine Regeldatei kann nur ein einzelnes Attribut indiziert werden.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Widerrufen eines Nachweises

Nachdem ein Indizierungsanspruch festgelegt wurde und Nachweise für Ihre Benutzer ausgestellt wurden, ist es an der Zeit, zu erfahren, wie Sie auf dem VC-Blatt einen Nachweis widerrufen können.

1. Navigieren Sie in Azure Active Directory zum Blatt für Nachweise.
1. Wählen Sie den Nachweis aus, bei dem Sie zuvor den Indizierungsanspruch eingerichtet und einen Nachweis für einen Benutzer ausgestellt haben. =
1. Wählen Sie im Menü auf der linken Seite die Option zum **Widerrufen eines Nachweises** aus.
   ![Widerrufen eines Nachweises](media/how-to-issuer-revoke/settings-revoke.png) 
1. Suchen Sie nach dem Indexattribut des Benutzers, für den Sie den Nachweis widerrufen möchten. 

   ![Suchen nach dem zu widerrufenden Nachweis](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Da wir nur einen Hash des indizierten Anspruchs aus dem Nachweis speichern, werden die Suchergebnisse nur durch eine exakte Entsprechung aufgefüllt. Wir verwenden die Sucheingabe des IT-Administrators und den gleichen Hashalgorithmus, um zu ermitteln, ob in unserer Datenbank eine Hashübereinstimmung vorhanden ist.
    
1. Wenn Sie einen Treffer gefunden haben, wählen Sie die Option **Widerrufen** rechts neben dem zu widerrufenden Nachweis aus.

   ![Warnung mit dem Hinweis, dass der Benutzer auch nach dem Widerruf noch über den Nachweis verfügt](media/how-to-issuer-revoke/warning.png) 

1. Nach erfolgreichem Widerruf sehen Sie die Statusaktualisierung, und am oberen Rand der Seite wird ein grünes Banner angezeigt. 
   ![Überprüfen dieser Domäne in den Einstellungen](media/how-to-issuer-revoke/revoke-successful.png) 

Wenn nun eine vertrauende Seite den Status dieses spezifischen Nachweises überprüft, wird von der Status-API von Microsoft, die im Auftrag des Mandanten agiert, die Antwort „False“ zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die Funktion mit einem Testnachweis selbst, um sich mit dem Ablauf vertraut zu machen. In [unseren Tutorials](get-started-verifiable-credentials.md) erfahren Sie, wie Sie Ihren Mandanten für die Ausstellung von Nachweisen konfigurieren.