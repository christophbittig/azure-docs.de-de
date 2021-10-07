---
title: Verwenden von REST-APIs für Purview-Datenebenen
description: In diesem Tutorial wird beschrieben, wie Sie mit den Azure Purview-REST-APIs auf Purview-Inhalte zugreifen.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/17/2021
ms.openlocfilehash: 5074e0017a5811b9418771aeef3b0883e10891c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212815"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutorial: Verwenden der REST-APIs

In diesem Tutorial erfahren Sie, wie Sie die Azure Purview-REST-APIs verwenden. Jeder Benutzer, der Daten an Azure Purview übermitteln, Purview im Rahmen eines automatisierten Prozesses einsetzen oder eine eigene Benutzerumgebung für Purview erstellen möchte, kann hierfür die REST-APIs verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Für die ersten Schritte benötigen Sie ein vorhandenes Azure Purview-Konto. Wenn Sie keinen Katalog besitzen, finden Sie weitere Informationen in der [Schnellstartanleitung zum Erstellen eines Azure Purview-Kontos](create-catalog-portal.md).

## <a name="create-a-service-principal-application"></a>Erstellen eines Dienstprinzipals (Anwendung)

Damit ein REST-API-Client auf den Katalog zugreifen kann, muss der Client über einen Dienstprinzipal (Anwendung) und eine Identität verfügen, die der Katalog erkennt und der er konfigurationsgemäß vertraut. Bei REST-API-Aufrufen an den Katalog wird die Identität des Dienstprinzipals verwendet.

Bei Kunden, die vorhandene Dienstprinzipale (Anwendungs-IDs) verwendet haben, gab es eine hohe Fehlerrate. Daher empfiehlt es sich, einen neuen Dienstprinzipal zum Aufrufen von APIs zu erstellen.

So erstellen Sie einen neuen Dienstprinzipal:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie im Portal nach **Azure Active Directory**, und wählen Sie es aus.
1. Wählen Sie auf der Seite **Azure Active Directory** im linken Bereich die Option **App-Registrierungen** aus.
1. Wählen Sie **Neue Registrierung** aus.
1. Gehen Sie auf der Seite **Registrieren einer Anwendung** folgendermaßen vor:
    1. Geben Sie einen **Namen** für die Anwendung (Dienstprinzipalname) ein.
    1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis (nur _&lt;Name Ihres Mandanten&gt;_  – einzelner Mandant)** aus.
    1. Wählen Sie unter **Umleitungs-URI (optional)** die Option **Web** aus, und geben Sie einen Wert ein. Dieser Wert muss kein gültiger URI sein.
    1. Wählen Sie **Registrieren**.
1. Kopieren Sie auf der Seite des neuen Dienstprinzipals die Werte unter **Anzeigename** und **Anwendungs-ID (Client)** , und speichern Sie sie zur späteren Verwendung.

   Die Anwendungs-ID ist der Wert `client_id` im Beispielcode.

Zur Verwendung des Dienstprinzipals (Anwendung) müssen Sie sein Kennwort abrufen. Gehen Sie dabei folgendermaßen vor:

1. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie den Dienst aus. Wählen Sie anschließend im linken Bereich die Option **App-Registrierungen** aus.
1. Wählen Sie in der Liste Ihren Dienstprinzipal (Anwendung) aus.
1. Wählen Sie im linken Bereich die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie **Neuer geheimer Clientschlüssel**.
1. Geben Sie auf der Seite **Geheimen Clientschlüssel hinzufügen** unter **Beschreibung** eine Beschreibung ein, und wählen Sie unter **Gültig bis** eine Ablaufzeit und anschließend **Hinzufügen** aus.

   Auf der Seite **Geheime Clientschlüssel** ist die Zeichenfolge in der Spalte **Wert** des neuen Geheimnisses Ihr Kennwort. Speichern Sie diesen Wert.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Screenshot: Geheimer Clientschlüssel":::

## <a name="set-up-authentication-using-service-principal"></a>Einrichten der Authentifizierung mit einem Dienstprinzipal

Nachdem der Dienstprinzipal erstellt wurde, müssen Sie dem oben erstellten Dienstprinzipal Rollen auf Datenebene Ihres Purview-Kontos zuweisen. Beim Zuweisen einer Rolle müssen die folgenden Schritte müssen befolgt werden, um eine Vertrauensstellung zwischen dem Dienstprinzipal und dem Purview-Konto einzurichten.

1. Navigieren Sie zu [Purview Studio](https://web.purview.azure.com/resource/).
1. Wählen Sie im linken Menü Data Map aus.
1. Wählen Sie „Sammlungen“ aus.
1. Wählen Sie im Sammlungsmenü die Stammsammlung aus. Dies ist die oberste Sammlung in der Liste. Sie hat den gleichen Namen wie Ihr Purview-Konto.
1. Klicken Sie auf die Registerkarte Rollenzuweisungen.
1. Weisen Sie dem oben erstellten Dienstprinzipal die folgenden Rollen zu, um auf die verschiedenen Datenebenen in Purview zuzugreifen.
    1. Rolle „Datenkurator“ für den Zugriff auf die Katalogdatenebene.
    1. Rolle „Datenquellenadministrator“ für den Zugriff auf die Überprüfungsdatenebene. 
    1. Rolle „Sammlungsadministrator“ für den Zugriff auf die Kontodatenebene.
    1. Rolle „Sammlungsadministrator“ für den Zugriff auf die Datenebene der Metadatenrichtlinie.

> [!Note]
> Nur ein Sammlungsadministrator kann Rollen auf Datenebene in Purview zuweisen (siehe [Zugriffssteuerung in Azure Purview](./catalog-permissions.md)).

## <a name="get-token"></a>Abrufen von Token
Sie können eine POST-Anforderung an die folgende URL senden, um das Zugriffstoken abzurufen.

https://login.microsoftonline.com/{your-tenant-id}/oauth2/token

An die obige URL müssen die folgenden Parameter übergeben werden.

- **client_id**: Die Client-ID der Anwendung, die in Azure Active Directory registriert und einer Rolle auf Datenebene für das Purview-Konto zugewiesen ist.
- **client_secret**: Der geheime Clientschlüssel, der für die obige Anwendung erstellt wird.
- **grant_type**: Dieser Parameter muss „client_credentials“ sein.
- **resource**: Dieser Parameter muss „https://purview.azure.net“ sein.
 
Beispiel für Antworttoken:

```json
    {
        "token_type": "Bearer",
        "expires_in": "86399",
        "ext_expires_in": "86399",
        "expires_on": "1621038348",
        "not_before": "1620951648",
        "resource": "https://purview.azure.net",
        "access_token": "<<access token>>"
    }
```

Verwenden Sie das oben genannte Zugriffstoken, um die Datenebenen-APIs aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Datenquellen](manage-data-sources.md)
> [REST-APIs für Purview-Datenebenen](/rest/api/purview/)
