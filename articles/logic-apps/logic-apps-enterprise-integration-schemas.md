---
title: Hinzufügen von Schemata für die XML-Validierung in Arbeitsabläufen
description: Hinzufügen von Schemata zur Validierung von XML-Dokumenten für Workflows in Azure Logic Apps unter Verwendung des Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: f80ed9c7fa9aa2d291e4f045b6cfc7da695cb22b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611432"
---
# <a name="add-schemas-to-validate-xml-documents-for-workflows-in-azure-logic-apps"></a>Hinzufügen von Schemata zur Validierung von XML-Dokumenten für Workflows in Azure Logic Apps

Um zu prüfen, ob Dokumente gültiges XML verwenden und die erwarteten Daten im vordefinierten Format enthalten, kann Ihr Logic-App-Workflow XML-Schemata mit der **XML-Validierungsaktion** verwenden. Ein XML-Schema beschreibt ein Geschäftsdokument, das mit Hilfe der [XML Schema Definition (XSD)](https://www.w3.org/TR/xmlschema11-1/) in XML dargestellt wird.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie in [B2B-Unternehmensintegrations-Workflows mit Azure Logic Apps und Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Zur Erstellung von Schemata können Sie die folgenden Werkzeuge verwenden:

  * Visual Studio 2019 und die [Microsoft Azure Logic Apps Unternehmensintegration Tools Extension](https://aka.ms/vsenterpriseintegrationtools).

  * Visual Studio 2015 und die Erweiterung [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas).

   > [!IMPORTANT]
   > Installieren Sie die Erweiterung nicht zusammen mit der BizTalk Server-Erweiterung. Sind beide Erweiterungen installiert, kann dies zu unerwartetem Verhalten führen. Stellen Sie sicher, dass nur eine dieser Erweiterungen installiert ist.

   > [!NOTE]
   > Auf Monitoren mit hoher Auflösung kann ein [Anzeigeproblem des Zuordnungs-Designers](/visualstudio/designers/disable-dpi-awareness) in Visual Studio auftreten. Um dieses Anzeigeproblem zu beheben, [starten Sie Visual Studio im nicht DPI-fähigen Modus neu](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process), oder fügen Sie den [DPIUNAWARE-Registrierungswert](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry) hinzu.

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. zur Verwendung in Ihren Unternehmensintegrations- und B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Das Konto befindet sich am gleichen Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource, in der Sie die Aktion **XML-Überprüfung** verwenden möchten.

  * Wenn Sie den [**Logic App (Verbrauch)** Ressourcentyp](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, müssen Sie Ihr Integrationskonto [verknüpfen mit Ihrer Logic-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account), bevor Sie Ihre Artefakte in Ihrem Workflow verwenden können.

    Zum Erstellen und Hinzufügen von Schemata für die Verwendung in **Logic App (Consumption)** Workflows benötigen Sie noch keine Logic App-Ressource. Wenn Sie jedoch bereit sind, diese Schemas in Ihren Workflows zu verwenden, benötigt Ihre Logik-App-Ressource ein verknüpftes Integrationskonto, das diese Schemas speichert.

  * Wenn Sie den [Ressourcentyp **Logic App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigen Sie eine vorhandene Logic-App-Ressource, da Sie keine Schemata in Ihrem Integrationskonto speichern. Stattdessen können Sie Ihrer Logic-App-Ressource entweder über das Azure-Portal oder über Visual Studio Code direkt Schemata hinzufügen. Sie können diese Schemas dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden.

    Sie benötigen weiterhin ein Integrationskonto, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zu speichern und um die [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgänge zu verwenden. Allerdings brauchen Sie Ihre Logic-App-Ressource nicht mit Ihrem Integrationskonto verknüpfen, so dass die Verknüpfungsfunktionalität nicht besteht. Ihr Integrationskonto muss noch andere Anforderungen erfüllen, z.B. dass es dasselbe Azure-Abonnement verwendet und sich am selben Ort befindet wie Ihre Logic-App-Ressource.

    > [!NOTE]
    > Derzeit unterstützt nur der **Logic App (Verbrauch)** Ressourcentyp [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logic App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

* Wenn Ihr Schema [2 MB oder kleiner](#smaller-schema) ist, können Sie Ihr Schema dem Integrationskonto *direkt* über das Azure-Portal hinzufügen. Wenn Ihr Schema größer als 2 MB ist, den [Grenzwert für die Schemagröße](logic-apps-limits-and-config.md#artifact-capacity-limits) aber nicht überschreitet, können Sie Ihr Schema in ein Azure Storage-Konto hochladen. Um dieses Schema Ihrem Integrationskonto hinzuzufügen, können Sie Ihr Speicherkonto mit Ihrem Integrationskonto verknüpfen. Für diese Aufgabe benötigen Sie Folgendes:

    | Element | BESCHREIBUNG |
    |------|-------------|
    | [Azure-Speicherkonto](../storage/common/storage-account-overview.md) | Unter diesem Konto erstellen Sie einen Azure-Blobcontainer für Ihr Schema. Hier erfahren Sie, wie Sie ein [Speicherkonto erstellen](../storage/common/storage-account-create.md). |
    | BLOB-Container | In diesen Container können Sie Ihr Schema hochladen. Wenn Sie das Schema später Ihrem Integrationskonto hinzufügen, benötigen Sie diesen Inhalts-URI Ihres Containers ebenfalls. Hier erfahren Sie, wie Sie einen [Blobcontainer erstellen](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Mit diesem Tool lassen sich Speicherkonten und Blobcontainer einfacher verwalten. Um Storage-Explorer zu verwenden, wählen Sie einen Schritt aus: <p>- Wählen Sie im Azure-Portal Ihr Speicherkonto aus. Wählen Sie im Menü „Speicherkonto“ **Storage-Explorer** aus. <p>- Bei der Desktopversion [laden Sie den Azure Storage-Explorer herunter und installieren ihn](https://www.storageexplorer.com/). Verbinden Sie dann den Storage-Explorer mit Ihrem Speicherkonto, indem Sie die Schritte unter [Erste Schritte mit dem Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ausführen. Weitere Informationen finden Sie unter [Schnellstart: Verwenden von Azure Storage-Explorer zum Erstellen eines Blobs im Objektspeicher](../storage/blobs/quickstart-storage-explorer.md).  |
    |||

  Um größere Schemata für den Ressourcentyp **Logic App (Consumption)** hinzuzufügen, können Sie auch die [Azure Logic Apps REST API - Schemata](/rest/api/logic/schemas/create-or-update) verwenden. Für den Ressourcentyp **Logic App (Standard)** ist die Azure Logic Apps REST API jedoch derzeit nicht verfügbar.

## <a name="limits"></a>Grenzwerte

* Für **Logic App (Standard)** gibt es keine Größenbeschränkungen für Schemadateien.

* Für **Logic App (Consumption)** gibt es Grenzen für Integrationskonten und Artefakte wie Schemata. Weitere Informationen finden Sie unter [Grenzwerte und Konfigurationsinformationen für Azure Logic Apps](logic-apps-limits-and-config.md#integration-account-limits).

  Wenn Sie ein Integrationskonto mit Ihrem Workflow verwenden und XML überprüfen möchten, fügen Sie das Schema normalerweise diesem Konto hinzu oder laden es hoch. Wenn Sie auf ein Schema verweisen oder es importieren, das nicht in Ihrem Integrationskonto enthalten ist, erhalten Sie möglicherweise den folgenden Fehler, wenn Sie das Element `xsd:redefine` verwenden:

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  Um diesen Fehler zu beheben, müssen Sie das Element `xsd:import` oder `xsd:include` anstelle von `xsd:redefine` oder einen URI verwenden.

<a name="add-schema"></a>

## <a name="add-schemas"></a>Hinzufügen von Schemas

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit den Anmeldeinformationen Ihres Azure-Kontos an.

1. Geben Sie im Hauptsuchfeld in Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, dem Sie Ihr Schema hinzufügen möchten.

1. Wählen Sie im Menü Ihres Integrationskontos unter **Einstellungen** die Option **Schemas** aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Schemas** die Option **Hinzufügen** aus.

Je nach Größe Ihrer Schemadatei (.xsd) führen Sie die Schritte zum Upload eines Schemas aus, das entweder [maximal 2 MB](#smaller-schema) groß oder [größer als 2 MB (maximal 8 MB)](#larger-schema) ist.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Hinzufügen von Schemas bis maximal 2 MB

1. Geben Sie im Bereich **Schema hinzufügen** einen Namen für Ihr Schema ein. Lassen Sie **Kleine Datei** ausgewählt. Wählen Sie neben dem Feld **Schema** das Ordnersymbol aus. Suchen Sie das Schema für den Upload, und wählen Sie es aus.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihres Schemas abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Hinzufügen von Schemas mit mehr als 2 MB

Wenn Sie größere Schemas hinzufügen möchten, können Sie Ihr Schema in einen Azure-Blobcontainer in Ihrem Azure-Speicherkonto hochladen. Die Schritte zum Hinzufügen von Schemas unterscheiden sich abhängig davon, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt. Überprüfen Sie deshalb zuerst anhand der folgenden Schritte, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt: [Festlegen der öffentlichen Zugriffsebene für Blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Überprüfen der Containerzugriffsebene

1. Öffnen Sie den Azure Storage-Explorer. Klappen Sie im Explorer-Fenster Ihr Azure-Abonnement auf, sofern es noch nicht aufgeklappt ist.

1. Klappen Sie **Speicherkonten** > {*Ihr-Speicherkonto*} > **Blobcontainer** auf. Wählen Sie Ihren Blobcontainer aus.

1. Wählen Sie im Kontextmenü des Blobcontainers **Öffentliche Zugriffsebene festlegen** aus.

   * Wenn Ihr Blobcontainer mindestens über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container mit öffentlichem Zugriff](#public-access).

     ![Öffentlicher Zugriff](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Wenn Ihr Blobcontainer nicht über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container ohne öffentlichen Zugriff](#public-access).

     ![Kein öffentlicher Zugriff](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Hochladen in Container mit öffentlichem Zugriff

1. Laden Sie das Schema in Ihr Speicherkonto hoch. Wählen Sie im Fenster auf der rechten Seite **Hochladen** aus.

1. Wählen Sie das hochgeladene Schema aus, nachdem der Upload abgeschlossen ist. Wählen Sie auf der Symbolleiste **URL kopieren** aus, um die URL des Schemas zu kopieren.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Schema hinzufügen** geöffnet ist. Geben Sie einen Namen für Ihre Assembly ein. Wählen Sie **Große Datei (größer als 2 MB)** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Schema** angezeigt.

1. Fügen Sie die URL Ihres Schemas in das Feld **Inhalts-URI** ein. Schließen Sie das Hinzufügen Ihres Schemas ab.

Nachdem der Upload Ihres Schemas abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Artefakte** Ihr hochgeladenes Schema angezeigt.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Hochladen in Container ohne öffentlichen Zugriff

1. Laden Sie das Schema in Ihr Speicherkonto hoch. Wählen Sie im Fenster auf der rechten Seite **Hochladen** aus.

1. Nachdem der Upload abgeschlossen ist, generieren Sie eine Shared Access Signature (SAS) für Ihr Schema. Wählen Sie im Kontextmenü des Schemas **Shared Access Signature abrufen** aus.

1. Wählen Sie im Bereich **Shared Access Signature** die Option **URI für Shared Access Signature auf Containerebene generieren** > **Erstellen** aus. Nachdem die SAS-URL generiert wurde, wählen Sie neben dem Feld **URL** die Option **Kopieren** aus.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Schema hinzufügen** geöffnet ist. Wählen Sie **Große Datei** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Schema** angezeigt.

1. Fügen Sie im Feld **Inhalts-URI** den zuvor generierten SAS-URI ein. Schließen Sie das Hinzufügen Ihres Schemas ab.

Nachdem der Upload Ihres Schemas abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Artefakte** Ihr hochgeladenes Schema angezeigt.

### <a name="standard"></a>[Standard](#tab/standard)

#### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Schemas** aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Schemas** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Schema hinzufügen** einen eindeutigen Namen für Ihr Schema ein.

1. Wählen Sie neben dem Feld **Schema** das Ordnersymbol aus. Wählen Sie das Schema für den Upload aus.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Schemadatei abgeschlossen ist, wird das Schema in der Liste **Schemas** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Artefakte** ebenfalls Ihr hochgeladenes Schema angezeigt.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. Öffnen Sie in der Struktur Ihres Logik-App-Projekts den Ordner **Artefakte** und dann den Ordner **Schemas**.

1. Fügen Sie Ihr Schema im Ordner **Schemas** hinzu.

---

<a name="edit-schema"></a>

## <a name="edit-a-schema"></a>Bearbeiten eines Schemas

Wenn Sie ein vorhandenes Schema aktualisieren möchten, müssen Sie eine neue Schemadatei hochladen, die über die gewünschten Änderungen verfügt. Allerdings können Sie zuerst das vorhandene Schema zum Bearbeiten herunterladen.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto, falls es noch nicht geöffnet ist.

1. Wählen Sie im Menü Ihres Integrationskontos unter **Einstellungen** die Option **Schemas** aus.

1. Nachdem der Bereich **Schemas** geöffnet wurde, wählen Sie Ihr Schema aus. Um das Schema zunächst herunterzuladen und zu bearbeiten, wählen Sie im Bereich **Schemas** die Option **Herunterladen** aus und speichern das Schema.

1. Wenn Sie bereit für den Upload des aktualisierten Schemas sind, wählen Sie im Bereich **Schemas** das zu aktualisierende Schema aus. Wählen Sie auf der Symbolleiste des Bereichs **Schemas** die Option **Aktualisieren** aus.

1. Suchen und wählen Sie das aktualisierte Schema aus, das Sie hochladen möchten.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Schemadatei abgeschlossen ist, wird das aktualisierte Schema in der Liste **Schemas** angezeigt.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource, falls noch nicht geschehen.

1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Schemas** aus.

1. Nachdem der Bereich **Schemas** geöffnet wurde, wählen Sie Ihr Schema aus. Um das Schema zunächst herunterzuladen und zu bearbeiten, wählen Sie im Bereich **Schemas** die Option **Herunterladen** aus und speichern das Schema.

1. Wählen Sie auf der Symbolleiste des Bereichs **Schemas** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Schema hinzufügen** einen eindeutigen Namen für Ihr Schema ein.

1. Wählen Sie neben dem Feld **Schema** das Ordnersymbol aus. Wählen Sie das Schema für den Upload aus.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Schemadatei abgeschlossen ist, wird das aktualisierte Schema in der Liste **Schemas** angezeigt.

---

<a name="delete-schema"></a>

## <a name="delete-a-schema"></a>Löschen eines Schemas

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto, falls es noch nicht geöffnet ist.

1. Wählen Sie im Menü Ihres Integrationskontos unter **Einstellungen** die Option **Schemas** aus.

1. Nachdem der Bereich **Schemas** geöffnet wurde, wählen Sie Ihr Schema und dann **Löschen** aus.

1. Um zu bestätigen, dass Sie das Schema löschen möchten, wählen **Ja** aus.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource, falls noch nicht geschehen.

1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Schemas** aus.

1. Nachdem der Bereich **Schemas** geöffnet wurde, wählen Sie Ihr Schema und dann **Löschen** aus.

1. Um zu bestätigen, dass Sie das Schema löschen möchten, wählen **Ja** aus.

---

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen von XML für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
* [Transformieren von XML für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-transform.md)