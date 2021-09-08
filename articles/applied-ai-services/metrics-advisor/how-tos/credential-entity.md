---
title: Erstellen einer Anmeldeinformationsentität
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie eine Anmeldeinformationsentität zum sicheren Verwalten Ihrer Anmeldeinformationen erstellen.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: mbullwin
ms.openlocfilehash: 0e16b92ff4a2316042e2177f9525734ba1949db1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341547"
---
# <a name="how-to-create-a-credential-entity"></a>Erstellen einer Anmeldeinformationsentität

Beim Laden eines Datenfeeds müssen Sie einen Authentifizierungstyp auswählen. Einige Authentifizierungstypen wie *Azure SQL-Verbindungszeichenfolge* und *Dienstprinzipal* benötigen eine Entität, die Anmeldeinformationen speichert, um Ihre Anmeldeinformationen sicher zu verwalten. In diesem Artikel erfahren Sie, wie Sie in Metrics Advisor eine Anmeldeinformationsentität für verschiedene Anmeldeinformationstypen erstellen.
    

## <a name="basic-procedure-create-a-credential-entity"></a>Grundlegendes Verfahren: Erstellen einer Anmeldeinformationsentität

Sie können eine **Anmeldeinformationsentität** erstellen, um Anmeldeinformationen zu speichern und zur Authentifizierung bei Ihren Datenquellen zu verwenden. Sie können die Anmeldeinformationsentität für andere Benutzer freigeben und ihnen ermöglichen, eine Verbindung mit Ihren Datenquellen herzustellen, ohne die tatsächlichen Anmeldeinformationen freizugeben. Sie kann auf der Registerkarte „Datenfeed hinzufügen“ oder „Anmeldeinformationsentität“ erstellt werden. Nachdem Sie eine Anmeldeinformationsentität für eine bestimmte Authentifizierungsart erstellt haben, können Sie nur eine Anmeldeinformationsentität auswählen, die Sie beim Hinzufügen eines neuen Datenfeeds erstellt haben. Dies ist hilfreich, wenn Sie mehrere Datenfeeds erstellen. Die allgemeine Vorgehensweise zum Erstellen und Verwenden einer Anmeldeinformationsentität wird nachstehend gezeigt:

1. Wählen Sie „+“ aus, um auf der Registerkarte „Datenfeed hinzufügen“ eine neue Anmeldeinformationsentität zu erstellen (Sie können auch eine auf der Registerkarte „Anmeldeinformationsentität“ erstellen).

   ![Erstellen einer Anmeldeinformationsentität](../media/create-credential-entity.png)
 
2. Legen Sie den Namen der Anmeldeinformationsentität, die Beschreibung (falls erforderlich), den Anmeldeinformationstyp (entspricht *Authentifizierungstyp*) und andere Einstellungen fest.

   ![Festlegen einer Anmeldeinformationsentität](../media/set-credential-entity.png)
 
3. Nachdem Sie eine Anmeldeinformationsentität erstellt haben, können Sie sie auswählen, wenn Sie den Authentifizierungstyp angeben.

   ![Wählen einer Anmeldeinformationsentität](../media/choose-credential-entity.png)
 
Es gibt in Metrics Advisor **vier Anmeldeinformationstypen**: Azure SQL-Verbindungszeichenfolge, Azure Data Lake Storage Gen2-Entität mit gemeinsam genutzten Schlüsseln, Dienstprinzipal, Dienstprinzipal aus Key Vault. Es folgen Anweisungen zu verschiedenen Einstellungen für die Anmeldeinformationstypen.

## <a name="azure-sql-connection-string"></a>Azure SQL-Verbindungszeichenfolge

Legen Sie **Name** und **Verbindungszeichenfolge** fest, und wählen Sie dann „Erstellen“ aus.

![Festlegen der Anmeldeinformationsentität für die SQL-Verbindungszeichenfolge](../media/credential-entity/credential-entity-sql-connection-string.png)

## <a name="azure-data-lake-storage-gen2-shared-key-entity"></a>Azure Data Lake Storage Gen2-Entität mit gemeinsam genutzten Schlüsseln

Legen Sie **Name** und **Kontoschlüssel** fest, und wählen Sie dann „Erstellen“ aus. Den Kontoschlüssel finden Sie in der Azure Storage-Kontoressource (Azure Data Lake Storage Gen2) in der Einstellung für **Zugriffsschlüssel**.

<!-- 增加basic说明，tips是错的；增加一下怎么管理；加一个step1的link
-->
![Festlegen der Anmeldeinformationsentität für Data Lake](../media/credential-entity/credential-entity-data-lake.png)

## <a name="service-principal"></a>Dienstprinzipal

Um einen Dienstprinzipal für Ihre Datenquelle zu erstellen, befolgen Sie die ausführlichen Anweisungen unter [Verbinden verschiedener Datenquellen](../data-feeds-from-different-sources.md). Nach Erstellen eines Dienstprinzipals müssen Sie die folgenden Konfigurationen in der Anmeldeinformationsentität ausfüllen.

![Anmeldeinformationsentität für Dienstprinzipal](../media/credential-entity/credential-entity-service-principal.png)

* **Name**: Legen Sie einen Namen für die Anmeldeinformationsentität Ihres Dienstprinzipals fest.
* **Mandanten- und Client-ID**: Nachdem Sie einen Dienstprinzipal im Azure-Portal erstellt haben, finden Sie `Tenant ID` und `Client ID` unter **Übersicht**.

    ![Client- und Mandanten-ID von Dienstprinzipal](../media/credential-entity/sp-client-tenant-id.png)

* **Geheimer Clientschlüssel**: Nach der Erstellung eines Dienstprinzipals im Azure-Portal wechseln Sie zu **Zertifikate und Geheimnisse**, um einen neuen geheimen Clientschlüssel zu erstellen. Der **Wert** muss in der Anmeldeinformationsentität als `Client Secret` verwendet werden. (Hinweis: Der Wert wird nur einmal angezeigt. Daher ist es besser, ihn an einem anderen Ort zu speichern.)


    ![Werts des geheimen Clientschlüssels für Dienstprinzipal](../media/credential-entity/sp-secret-value.png)

## <a name="span-idsp-from-kvservice-principal-from-key-vaultspan"></a><span id="sp-from-kv">Dienstprinzipal aus Key Vault</span>

Die Erstellung eines Dienstprinzipals aus Key Vault erfolgt in mehreren Schritten.

**Schritt 1: Erstellen eines Dienstprinzipals, dem Sie Zugriff auf Ihre Datenbank gewähren.** Befolgen Sie beim Erstellen des Dienstprinzipals für jede Datenquelle die ausführlichen Anweisungen unter [Verbinden verschiedener Datenquellen](../data-feeds-from-different-sources.md). 

Nachdem Sie einen Dienstprinzipal im Azure-Portal erstellt haben, finden Sie `Tenant ID` und `Client ID` unter **Übersicht**. Die **Verzeichnis- bzw. Mandanten-ID** muss in Konfigurationen der Anmeldeinformationsentität `Tenant ID` sein.

![Client- und Mandanten-ID von Dienstprinzipal](../media/credential-entity/sp-client-tenant-id.png)

**Schritt 2. Erstellen eines neuen geheimen Clientschlüssels.** Wechseln Sie zu **Zertifikate und Geheimnisse**, um einen neuen geheimen Clientschlüssel zu erstellen. Der **Wert** wird in den nächsten Schritten verwendet. (Hinweis: Der Wert wird nur einmal angezeigt. Daher ist es besser, ihn an einem anderen Ort zu speichern.)

![Werts des geheimen Clientschlüssels für Dienstprinzipal](../media/credential-entity/sp-secret-value.png)

**Schritt 3: Erstellen eines Schlüsseltresors.** Wählen Sie im [Azure-Portal](https://ms.portal.azure.com/#home) die Option **Schlüsseltresore** aus, um einen zu erstellen.

![Erstellen eines Schlüsseltresors im Azure-Portal](../media/credential-entity/create-key-vault.png)

Nach Erstellung eines Schlüsseltresors ist der **Tresor-URI** die Entität `Key Vault Endpoint` in der Anmeldeinformationsentität von Metrics Advisor.

![Schlüsseltresorendpunkt](../media/credential-entity/key-vault-endpoint.png)

**Schritt 4. Erstellen von Geheimnissen für Key Vault.** Generieren Sie im Azure-Portal für den Schlüsseltresor unter **Einstellungen->Geheimnisse** zwei Geheimnisse.
Das erste ist für `Service Principal Client Id`, das andere für `Service Principal Client Secret`. Beide Namen werden in den Konfigurationen der Anmeldeinformationen verwendet.

![Generieren von Geheimnissen](../media/credential-entity/generate-secrets.png)

* **Client-ID des Dienstprinzipals**: Legen Sie für dieses Geheimnis `Name` fest. Der Name wird in der Konfiguration der Anmeldeinformationsentität verwendet. Der Wert muss Ihrem Dienstprinzipal `Client ID` in **Schritt 1** entsprechen.

    ![Geheimnis 1: Client-ID des Dienstprinzipals](../media/credential-entity/secret-1-sp-client-id.png)

* **Geheimer Clientschlüssel des Dienstprinzipals**: Legen Sie für dieses Geheimnis `Name` fest. Der Name wird in der Konfiguration der Anmeldeinformationsentität verwendet. Der Wert muss Ihrem Dienstprinzipal `Client Secret Value` in **Schritt 2** entsprechen.

    ![Geheimnis 2: geheimer Clientschlüssel des Dienstprinzipals](../media/credential-entity/secret-2-sp-secret-value.png)

Bis dato werden die *Client-ID* und das *Clientgeheimnis* des Dienstprinzipals in Key Vault endgültig gespeichert. Als Nächstes müssen Sie einen weiteren Dienstprinzipal erstellen, um den Schlüsseltresor zu speichern. Dafür müssen Sie **zwei Dienstprinzipale** erstellen: einen zum Speichern der Client-ID und des geheimen Clientschlüssels, die in einem Schlüsseltresor gespeichert werden, und einen zum Speichern des Schlüsseltresors.

**Schritt 5. Erstellen eines Dienstprinzipals zum Speichern des Schlüsseltresors.** 

1. Wechseln Sie im [Azure-Portal zu AAD (Azure Active Directory)](https://portal.azure.com/?trace=diagnostics&feature.customportal=false#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview), und erstellen Sie eine neue Registrierung.

    ![Erstellen einer neuen Registrierung](../media/credential-entity/create-registration.png)

    Nach der Erstellung des Dienstprinzipals ist die **Anwendungs- bzw. Client-ID** in der Übersicht in der Konfiguration der Anmeldeinformationsentität die `Key Vault Client ID`.

2. Erstellen Sie unter **Verwalten->Zertifikate und Geheimnisse** einen geheimen Clientschlüssel, indem Sie „Neuer geheimer Clientschlüssel“ auswählen. Anschließend sollten Sie **den Wert kopieren**, da er nur einmal angezeigt wird. Der Wert ist in der Konfiguration der Anmeldeinformationsentität `Key Vault Client Secret`.

    ![Hinzufügen eines geheimen Clientschlüssels](../media/credential-entity/add-client-secret.png)

**Schritt 6. Gewähren des Zugriffs auf Key Vault für einen Dienstprinzipal.**  Wechseln Sie in **Einstellungen->Zugriffsrichtlinien** zur von Ihnen erstellten Schlüsseltresorressource, indem Sie „Zugriffsrichtlinie hinzufügen“ auswählen, um die Verbindung zwischen dem Schlüsseltresor und dem zweiten Dienstprinzipal in **Schritt 5** herzustellen. Speichern Sie Ihre Eingaben.

![Gewähren des Zugriffs auf den Schlüsseltresor für den Dienstprinzipal](../media/credential-entity/grant-sp-to-kv.png)


## <a name="configurations-conclusion"></a>Zusammenfassung der Konfigurationen
Abschließend sind die Konfigurationen der Anmeldeinformationsentität im Metrics Advisor für den *Dienstprinzipal aus Key Vault* und ihr Abruf in der nachstehenden Tabelle aufgeführt:

| Konfiguration | Möglichkeit des Abrufs |
|-------------| ---------------------|
| Schlüsseltresorendpunkt | **Schritt 3**: Tresor-URI des Schlüsseltresors. |
| Mandanten-ID | **Schritt 1:** Verzeichnis-ID (Mandant) Ihres ersten Dienstprinzipals. |
| Client-ID des Schlüsseltresors | **Schritt 5**: Die Anwendungs- bzw. Client-ID Ihres zweiten Dienstprinzipals. |
| Key Vault-Clientgeheimnis | **Schritt 5**: Der Wert des geheimen Clientschlüssels Ihres zweiten Dienstprinzipals. |
| Client-ID-Name des Dienstprinzipals | **Schritt 4**: Der Geheimnisname, den Sie für die Client-ID festgelegt haben. |
| Namen des geheimen Clientschlüssels des Dienstprinzipals | **Schritt 4**: Der Geheimnisname, den Sie für den Wert des geheimen Clientschlüssels festgelegt haben. |


## <a name="next-steps"></a>Nächste Schritte

- [Durchführen des Onboardings für Ihre Daten](onboard-your-data.md)
- [Verbinden verschiedener Datenquellen](../data-feeds-from-different-sources.md)
