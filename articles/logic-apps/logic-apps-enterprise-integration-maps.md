---
title: Hinzufügen von XSLT-Zuordnungen für XML-Transformationen in Workflows
description: Erstellen und Hinzufügen von XSLT-Zuordnungen zum Transformieren von XML für Workflows in Azure Logic Apps mit dem Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: 71083bf7eaddb04f322245ca5e33971ff2d79c53
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549869"
---
# <a name="add-xslt-maps-for-xml-transformation-in-azure-logic-apps"></a>Hinzufügen von XSLT-Zuordnungen für XML-Transformationen in Azure Logic Apps

Um XML zwischen Formaten zu konvertieren, kann Ihr Logic-App-Workflow Zuordnungen mit der **Transform XML**-Aktion verwenden. Eine Zuordnung ist ein XML-Dokument, das die Extensible Stylesheet Language Transformation (XSLT) Sprache verwendet, um zu beschreiben, wie Daten von XML in ein anderes Format transformiert werden können. Die Zuordnung besteht aus einem XML-Quellschema als Eingabe und einem XML-Zielschema als Ausgabe. Sie können eine einfache Transformation definieren, z.B. das Kopieren eines Namens und einer Adresse von einem Dokument in ein anderes. Oder Sie können mithilfe der standardmäßigen Zuordnungsvorgänge komplexere Transformationen erstellen. Sie können Daten mit Hilfe verschiedener integrierter Funktionen manipulieren oder steuern, z.B. mit String-Manipulationen, bedingten Zuweisungen, arithmetischen Ausdrücken, Datums- und Zeitformatierungen und sogar Schleifenkonstrukten.

Stellen Sie sich beispielsweise vor, dass Sie regelmäßige B2B-Aufträge oder -Rechnungen von einem Kunden erhalten, der das Format „YearMonthDay“ (JahrMonatTag, JJJJMMTT) für Datumsangaben verwendet. Ihre Organisation verwendet jedoch das Datumsformat „MonthDayYear“ (MonatTagJahr,MMTTJJJJ). Mithilfe einer Zuordnung können Sie das Format JJJJMMTT in das Format MMTTJJJJ transformieren, bevor Auftrags- oder Rechnungsdetails in Ihrer Kundenaktivitätsdatenbank gespeichert werden.

> [!NOTE]
> Azure Logic Apps ordnet begrenzten Arbeitsspeicher für die Verarbeitung von XML-Transformationen zu. Wenn Sie logische Anwendungen auf der Grundlage des [**Logic App (Verbrauch)** Ressourcentyps](logic-apps-overview.md#resource-type-and-host-environment-differences) erstellen und Ihre Zuordnungs- oder Nutzdaten-Transformationen einen hohen Speicherverbrauch haben, können solche Transformationen fehlschlagen, was zu Fehlern aufgrund von Speichermangel führt. Um dieses Szenario zu vermeiden, sollten Sie die folgenden Optionen in Betracht ziehen:
>
> * Bearbeiten Sie Ihre Zuordnungen oder Nutzdaten, um den Arbeitsspeicherverbrauch zu senken.
>
> * Erstellen Sie Ihre Logikanwendungen stattdessen mit dem [**Logic App (Standard)** Ressourcentyp](logic-apps-overview.md#resource-type-and-host-environment-differences).
>
>   Diese Workflows werden in einer Azure Logic Apps-Instanz für nur einen Mandanten ausgeführt, die dedizierte und flexible Optionen für Compute- und Arbeitsspeicherressourcen bietet. 
>   Der Ressourcentyp „Logik-App (Standard)“ unterstützt derzeit jedoch nicht das Verweisen auf externe Assemblys aus Zuordnungen. Außerdem wird derzeit nur XSLT 1.0 (Extensible Stylesheet Language Transformation) unterstützt.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie in [B2B-Unternehmensintegrations-Workflows mit Azure Logic Apps und Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Um Zuordnungen zu erstellen, können Sie die folgenden Tools verwenden:

  * Visual Studio 2019 und die [Microsoft Azure Logic Apps Unternehmensintegration Tools Extension](https://aka.ms/vsenterpriseintegrationtools).

  * Visual Studio 2015 und die [Microsoft Azure Logic Apps Unternehmensintegration Tools für Visual Studio 2015 2.0 Erweiterung](https://aka.ms/vsmapsandschemas).

   > [!IMPORTANT]
   > Installieren Sie die Erweiterung nicht zusammen mit der BizTalk Server-Erweiterung. Sind beide Erweiterungen installiert, kann dies zu unerwartetem Verhalten führen. Stellen Sie sicher, dass nur eine dieser Erweiterungen installiert ist.

   > [!NOTE]
   > Auf Monitoren mit hoher Auflösung kann ein [Anzeigeproblem des Zuordnungs-Designers](/visualstudio/designers/disable-dpi-awareness) in Visual Studio auftreten. Um dieses Anzeigeproblem zu beheben, [starten Sie Visual Studio im nicht DPI-fähigen Modus neu](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process), oder fügen Sie den [DPIUNAWARE-Registrierungswert](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry) hinzu.

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. zur Verwendung in Ihren Unternehmensintegrations- und B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden, für die Sie die Aktion **XML transformieren** verwenden möchten.

  * Wenn Sie den [**Logic App (Verbrauch)** Ressourcentyp](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, müssen Sie Ihr Integrationskonto [verknüpfen mit Ihrer Logic-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account), bevor Sie Ihre Artefakte in Ihrem Workflow verwenden können.

    Um Zuordnungen zur Verwendung in **Logic App (Verbrauch)** -Workflows zu erstellen und hinzuzufügen, benötigen Sie noch keine Logic App-Ressource. Wenn Sie jedoch bereit sind, diese Zuordnungen in Ihren Workflows zu verwenden, benötigt Ihre Logik-App-Ressource ein verknüpftes Integrationskonto, das diese Zuordnungen speichert.

  * Wenn Sie die Ressource [**Logic App (Standard)** vom Typ ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigen Sie eine vorhandene Logic-App-Ressource, da Sie keine Zuordnungen in Ihrem Integrationskonto speichern. Stattdessen können Sie Ihrer Logic-App-Ressource Zuordnungen entweder über das Azure-Portal oder über Visual Studio Code direkt hinzufügen. Derzeit wird nur XSLT 1.0 unterstützt. Sie können diese Zuordnungen dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden.

    Sie benötigen weiterhin ein Integrationskonto, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zu speichern und um die [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgänge zu verwenden. Allerdings brauchen Sie Ihre Logic-App-Ressource nicht mit Ihrem Integrationskonto verknüpfen, so dass die Verknüpfungsfunktionalität nicht besteht. Ihr Integrationskonto muss noch andere Anforderungen erfüllen, z.B. dass es dasselbe Azure-Abonnement verwendet und sich am selben Ort befindet wie Ihre Logic-App-Ressource.

    > [!NOTE]
    > Derzeit unterstützt nur der **Logic App (Verbrauch)** Ressourcentyp [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logic App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

* Während **Logik-App (Verbrauch)** das Verweisen auf externe Assemblys aus Zuordnungen unterstützt, unterstützt **Logik-App (Standard)** diese Funktion derzeit nicht. Das Verweisen auf eine Assembly ermöglicht direkte Aufrufe von benutzerdefiniertem .NET-Code aus XSLT-Zuordnungen.

  * Sie benötigen eine 64-Bit-Assembly. Der Transformationsdienst führt einen 64-Bit-Prozess aus, deshalb werden 32-Bit-Assemblys nicht unterstützt. Wenn Ihr Quellcode für eine 32-Bit-Assembly vorgesehen ist, kompilieren Sie den Code erneut in eine 64-Bit-Assembly. Wenn Sie nicht über den Quellcode verfügen, aber die Binärdatei von einem Drittanbieter erhalten haben, wenden Sie sich für die 64-Bit-Version an diesen Anbieter. Einige Anbieter stellen beispielsweise Assemblys in Paketen bereit, die sowohl 32-Bit- als auch 64-Bit-Versionen enthalten. Wenn es die Option gibt, verwenden Sie stattdessen die 64-Bit-Version.

  * Sie müssen *sowohl die Assembly als auch die Zuordnung* in einer bestimmten Reihenfolge in Ihr Integrationskonto hochladen. Sie müssen die [*Assembly zuerst hochladen*](#add-assembly), bevor Sie den Upload für die Zuordnung durchführen, in der auf die Assembly verwiesen wird.

  * Wenn Ihre Assembly [2 MB oder kleiner](#smaller-map) ist, können Sie Ihre Assembly und Zuordnung Ihrem Integrationskonto *direkt* über das Azure-Portal hinzufügen. Wenn Ihre Assembly oder Zuordnung jedoch größer als 2 MB, aber nicht größer als die [Größenbeschränkung für Assemblys oder Zuordnungen](logic-apps-limits-and-config.md#artifact-capacity-limits) ist, können Sie einen Azure-Blobcontainer verwenden, in den Sie Ihre Assembly und den Speicherort dieses Containers hochladen können. Auf diese Weise können Sie diesen Speicherort später bereitstellen, wenn Sie die Assembly Ihrem Integrationskonto hinzufügen. Für diese Aufgabe benötigen Sie folgende Elemente:

    | Element | BESCHREIBUNG |
    |------|-------------|
    | [Azure-Speicherkonto](../storage/common/storage-account-overview.md) | Unter diesem Konto erstellen Sie einen Azure-Blobcontainer für Ihre Assembly. Hier erfahren Sie, wie Sie ein [Speicherkonto erstellen](../storage/common/storage-account-create.md). |
    | BLOB-Container | In diesen Container können Sie Ihre Assembly hochladen. Außerdem benötigen Sie den Inhalts-URI und Speicherort dieses Containers, wenn Sie die Assembly Ihrem Integrationskonto hinzufügen. Hier erfahren Sie, wie Sie einen [Blobcontainer erstellen](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Mit diesem Tool lassen sich Speicherkonten und Blobcontainer einfacher verwalten. Eine Möglichkeit zur Verwendung des Storage-Explorers besteht darin, [den Azure Storage-Explorer herunterzuladen und zu installieren](https://www.storageexplorer.com/). Verbinden Sie dann den Storage-Explorer mit Ihrem Speicherkonto, indem Sie die Schritte unter [Erste Schritte mit dem Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ausführen. Weitere Informationen finden Sie unter [Schnellstart: Verwenden von Azure Storage-Explorer zum Erstellen eines Blobs im Objektspeicher](../storage/blobs/quickstart-storage-explorer.md). <p>Sie können auch im Azure-Portal Ihr Speicherkonto auswählen. Wählen Sie im Menü „Speicherkonto“ **Storage-Explorer** aus. |
    |||

  * Um größere Zuordnungen für den Ressourcentyp **Logic App (Verbrauch)** hinzuzufügen, können Sie auch die [Azure Logic Apps REST API-Zuordnungen](/rest/api/logic/maps/createorupdate) verwenden. Für den Ressourcentyp **Logic App (Standard)** ist die Azure Logic Apps REST API jedoch derzeit nicht verfügbar.

## <a name="limits"></a>Grenzwerte

* Mit **Logic App (Standard)** gibt es keine Beschränkungen für die Größen von Zuordnungsdateien.

* Mit **Logic App (Verbrauch)** gibt es Beschränkungen für Integrationskonten und Artefakte wie Zuordnungen. Weitere Informationen finden Sie unter [Grenzwerte und Konfigurationsinformationen für Azure Logic Apps](logic-apps-limits-and-config.md#integration-account-limits).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies-consumption-resource-only"></a>Hinzufügen von Assemblys, auf die verwiesen wird (nur Verbrauchsressource)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit den Anmeldeinformationen Ihres Azure-Kontos an.

1. Geben Sie im Hauptsuchfeld in Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, dem Sie Ihre Assembly hinzufügen möchten.

1. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus. Wählen Sie unter **Einstellungen** die Option **Assemblys** aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Assemblys** die Option **Hinzufügen** aus.

Je nach Größe Ihrer Assemblydatei führen Sie die Schritte zum Upload einer Assembly aus, die entweder [maximal 2 MB](#smaller-assembly) groß oder [größer als 2 MB (maximal 8 MB)](#larger-assembly) ist. Die Grenzwerte für die Anzahl von Assemblys in Integrationskonten finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Wenn Sie Ihre Assembly ändern, müssen Sie auch Ihre Zuordnung aktualisieren, und zwar unabhängig davon, ob die Zuordnung Änderungen enthält.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Hinzufügen von Assemblys bis zu 2 MB

1. Geben Sie unter **Assembly hinzufügen** einen Namen für Ihre Assembly ein. Lassen Sie **Kleine Datei** ausgewählt. Wählen Sie als Nächstes neben dem Feld **Assembly** das Ordnersymbol aus. Suchen Sie die Assembly für den Upload, und wählen Sie sie aus.

   Nachdem Sie die Assembly ausgewählt haben, wird als **Assemblyname**-Eigenschaft automatisch der Dateiname der Assembly angezeigt.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Assemblydatei abgeschlossen ist, wird die Assembly in der Liste **Assemblys** angezeigt. Im Bereich **Übersicht** Ihres Integrationskontos wird unter **Artefakte** ebenfalls Ihre hochgeladene Assembly angezeigt.

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Hinzufügen von Assemblys mit mehr als 2 MB

Wenn Sie größere Assemblys hinzufügen möchten, können Sie Ihre Assembly in einen Azure-Blobcontainer in Ihrem Azure-Speicherkonto hochladen. Die Schritte zum Hinzufügen von Assemblys unterscheiden sich abhängig davon, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt. Überprüfen Sie deshalb zuerst anhand der folgenden Schritte, ob Ihr Blobcontainer über öffentlichen Lesezugriff verfügt: [Festlegen der öffentlichen Zugriffsebene für Blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Überprüfen der Containerzugriffsebene

1. Öffnen Sie den Azure Storage-Explorer. Klappen Sie im Explorer-Fenster Ihr Azure-Abonnement auf, sofern es noch nicht aufgeklappt ist.

1. Klappen Sie **Speicherkonten** > {*Ihr-Speicherkonto*} > **Blobcontainer** auf. Wählen Sie Ihren Blobcontainer aus.

1. Wählen Sie im Kontextmenü des Blobcontainers **Öffentliche Zugriffsebene festlegen** aus.

   * Wenn Ihr Blobcontainer mindestens über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container mit öffentlichem Zugriff](#public-access-assemblies).

     ![Öffentlicher Zugriff](media/logic-apps-enterprise-integration-maps/azure-blob-container-public-access.png)

   * Wenn Ihr Blobcontainer nicht über öffentlichen Zugriff verfügt, wählen Sie **Abbrechen** aus, und folgen Sie den Schritten unten auf dieser Seite: [Hochladen in Container ohne öffentlichen Zugriff](#no-public-access-assemblies).

     ![Kein öffentlicher Zugriff](media/logic-apps-enterprise-integration-maps/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Hochladen in Container mit öffentlichem Zugriff

1. Laden Sie die Assembly in Ihr Speicherkonto hoch. Wählen Sie im Fenster auf der rechten Seite **Hochladen** aus.

1. Wählen Sie die hochgeladene Assembly aus, nachdem der Upload abgeschlossen ist. Wählen Sie auf der Symbolleiste **URL kopieren** aus, um die URL der Assembly zu kopieren.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Assembly hinzufügen** geöffnet ist. Geben Sie einen Namen für Ihre Assembly ein. Wählen Sie **Große Datei (größer als 2 MB)** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Assembly** angezeigt.

1. Fügen Sie die URL Ihrer Assembly in das Feld **Inhalts-URI** ein. Schließen Sie das Hinzufügen Ihrer Assembly ab.

   Nachdem das Hochladen Ihrer Assemblydatei abgeschlossen ist, wird das Assembly in der Liste **Assemblys** angezeigt. Im Bereich **Übersicht** Ihres Integrationskontos wird unter **Artefakte** ebenfalls Ihre hochgeladene Assembly angezeigt.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Hochladen in Container ohne öffentlichen Zugriff

1. Laden Sie die Assembly in Ihr Speicherkonto hoch. Wählen Sie im Fenster auf der rechten Seite **Hochladen** aus.

1. Nachdem der Upload abgeschlossen ist, generieren Sie eine Shared Access Signature (SAS) für Ihre Assembly. Wählen Sie im Kontextmenü der Assembly **Shared Access Signature abrufen** aus.

1. Wählen Sie im Bereich **Shared Access Signature** die Option **URI für Shared Access Signature auf Containerebene generieren** > **Erstellen** aus. Nachdem die SAS-URL generiert wurde, wählen Sie neben dem Feld **URL** die Option **Kopieren** aus.

1. Kehren Sie zum Azure-Portal zurück, in dem der Bereich **Assembly hinzufügen** geöffnet ist. Geben Sie einen Namen für Ihre Assembly ein. Wählen Sie **Große Datei (größer als 2 MB)** aus.

   Das Feld **Inhalts-URI** wird jetzt anstelle des Felds **Assembly** angezeigt.

1. Fügen Sie im Feld **Inhalts-URI** den zuvor generierten SAS-URI ein. Schließen Sie das Hinzufügen Ihrer Assembly ab.

Nachdem das Hochladen Ihrer Assemblydatei abgeschlossen ist, wird das Assembly in der Liste **Assemblys** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Artefakte** ebenfalls Ihre hochgeladene Assembly angezeigt.

<a name="create-maps"></a>

## <a name="create-maps"></a>Erstellen von Zuordnungen

Sie können zum Erstellen eines XSLT-Dokuments (Extensible Stylesheet Language Transformation), das Sie als Zuordnung verwenden können, Visual Studio 2015 oder 2019 verwenden und mit dem [Unternehmensintegrations-SDK](https://aka.ms/vsmapsandschemas) ein Integrationsprojekt erstellen. In diesem Projekt können Sie eine Integrationszuordnungsdatei erstellen, mit deren Hilfe Sie Elemente zwischen zwei XML-Schemadateien visuell zuordnen können. Nachdem Sie dieses Projekt erstellt haben, verfügen Sie über ein XSLT-Dokument. Die Grenzwerte für die Anzahl von Zuordnungen in Integrationskonten finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](logic-apps-limits-and-config.md#artifact-number-limits).

Ihre Zuordnung muss über die folgenden Attribute und einen `CDATA`-Abschnitt verfügen, der den Aufruf des Assemblycodes enthält:

* `name` ist der Name der benutzerdefinierten Assembly.

* `namespace` ist der Namespace in Ihrer Assembly, der den benutzerdefinierten Code enthält.

Das folgende Beispiel zeigt eine Zuordnung, in der auf eine Assembly mit dem Namen `XslUtilitiesLib` verwiesen und die `circumference`-Methode aus der Assembly aufgerufen wird.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
<msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
</msxsl:script>
<xsl:template match="data">
<circles>
    <xsl:for-each select="circle">
        <circle>
            <xsl:copy-of select="node()"/>
                <circumference>
                    <xsl:value-of select="user:circumference(radius)"/>
                </circumference>
        </circle>
    </xsl:for-each>
</circles>
</xsl:template>
</xsl:stylesheet>
```

## <a name="tools-and-capabilities-for-maps"></a>Tools und Funktionen für Zuordnungen

* Wenn Sie eine Zuordnung mit Visual Studio und dem [Unternehmensintegrations-SDK](https://aka.ms/vsmapsandschemas) erstellen, arbeiten Sie mit einer grafischen Darstellung der Zuordnung, die alle Beziehungen und Verknüpfungen zeigt, die Sie erstellen.

* Sie können eine direkte Datenkopie zwischen den XML-Schemata erstellen, die Sie zur Erstellung der Zuordnung verwenden. Das [Unternehmensintegrations-SDK](https://aka.ms/vsmapsandschemas) für Visual Studio enthält einen Mapper, der diese Aufgabe so einfach macht wie das Zeichnen einer Linie, die die Elemente im XML-Quellschema mit ihren Gegenstücken im XML-Zielschema verbindet.

* Vorgänge oder Funktionen für mehrere Zuordnungen sind verfügbar, einschließlich Zeichenfolgenfunktionen, Datums-/Uhrzeitfunktionen usw.

* Um eine XML-Beispielnachricht hinzuzufügen, können Sie die Zuordnungstestfunktion verwenden. Mit nur einem Klick können Sie die erstellte Zuordnung testen und die erstellte Ausgabe überprüfen.

<a name="add-map"></a>

## <a name="add-maps"></a>Hinzufügen von Zuordnungen

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Nachdem Sie alle Assemblys hochgeladen haben, auf die in Ihrer Zuordnung verwiesen wird, können Sie nun Ihre Zuordnung hochladen.

1. Falls Ihr Integrationskonto im Azure-Portal nicht bereits geöffnet ist, geben Sie im Hauptsuchfeld von Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie das Integrationskonto aus, dem Sie Ihre Zuordnung hinzufügen möchten.

1. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus. Wählen Sie unter **Einstellungen** die Option **Zuordnungen** aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Zuordnungen** die Option **Hinzufügen** aus.

1. Fahren Sie damit fort, entweder eine Zuordnung [von bis zu 2 MB](#smaller-map) oder [mehr als 2 MB](#larger-map) hinzuzufügen.

<a name="smaller-map"></a>

#### <a name="add-maps-up-to-2-mb"></a>Hinzufügen von Zuordnungen bis zu 2 MB

1. Geben Sie im Bereich **Zuordnung hinzufügen** einen eindeutigen Namen für Ihre Zuordnung ein.

1. Wählen Sie unter **Zuordnungstyp** den Typ aus. Beispiel: **Liquid**, **XSLT**, **XSLT 2.0** oder **XSLT 3.0**.

1. Wählen Sie neben dem Feld **Zuordnung** das Ordnersymbol aus. Wählen Sie die hochzuladende Zuordnung aus.

   Wenn die **Name**-Eigenschaft nicht angegeben wurde, wird der Dateiname der Zuordnung als Eigenschaftswert angezeigt, nachdem Sie die Zuordnungsdatei ausgewählt haben.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Zuordnungsdatei abgeschlossen ist, wird die Zuordnung in der Liste **Zuordnungen** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Artefakte** ebenfalls Ihre hochgeladene Zuordnung angezeigt.

<a name="larger-map"></a>

#### <a name="add-maps-more-than-2-mb"></a>Hinzufügen von Zuordnungen mit mehr als 2 MB

Größere Zuordnungen lassen sich derzeit mit [Azure Logic Apps REST API – Zuordnungen](/rest/api/logic/maps/createorupdate) hinzufügen.

### <a name="standard"></a>[Standard](#tab/standard)

#### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Zuordnungen** aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Zuordnungen** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Zuordnung hinzufügen** einen eindeutigen Namen für Ihre Zuordnung ein, und schließen Sie den Erweiterungsnamen `.xslt` ein.

1. Wählen Sie neben dem Feld **Zuordnung** das Ordnersymbol aus. Wählen Sie die hochzuladende Zuordnung aus.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Zuordnungsdatei abgeschlossen ist, wird die Zuordnung in der Liste **Zuordnungen** angezeigt. Auf der Seite **Übersicht** Ihres Integrationskontos wird unter **Artefakte** ebenfalls Ihre hochgeladene Zuordnung angezeigt.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. Öffnen Sie in der Struktur Ihres Logik-App-Projekts den Ordner **Artefakte** und dann den Ordner **Zuordnungen**.

1. Fügen Sie im Ordner **Zuordnungen** Ihre Zuordnung hinzu.

---

<a name="edit-map"></a>

## <a name="edit-a-map"></a>Bearbeiten von einer Zuordnung

Wenn Sie eine vorhandene Zuordnung aktualisieren möchten, müssen Sie eine neue Zuordnungsdatei hochladen, die über die gewünschten Änderungen verfügt. Allerdings können Sie zuerst die vorhandene Zuordnung zum Bearbeiten herunterladen.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto, falls es noch nicht geöffnet ist.

1. Wählen Sie im Menü Ihres Integrationskontos unter **Einstellungen** die Option **Zuordnungen** aus.

1. Nachdem der Bereich **Zuordnungen** geöffnet wurde, wählen Sie Ihre Zuordnung aus. Um die Zuordnung zunächst herunterzuladen und zu bearbeiten, wählen Sie im Bereich **Zuordnungen** die Option **Herunterladen** aus und speichern die Zuordnung.

1. Wenn Sie die aktualisierte Zuordnung wieder hochladen möchten, wählen Sie im Bereich **Zuordnungen** die zu aktualisierende Zuordnung aus. Wählen Sie auf der Symbolleiste des Bereichs **Zuordnungen** die Option **Aktualisieren** aus.

1. Suchen und wählen Sie die aktualisierte Zuordnung aus, die Sie hochladen möchten.

   Nachdem der Upload Ihrer Zuordnungsdatei abgeschlossen ist, wird die aktualisierte Zuordnung in der Liste **Zuordnungen** angezeigt.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource, falls noch nicht geschehen.

1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Zuordnungen** aus.

1. Nachdem der Bereich **Zuordnungen** geöffnet wurde, wählen Sie Ihre Zuordnung aus. Um die Zuordnung zunächst herunterzuladen und zu bearbeiten, wählen Sie im Bereich **Zuordnungen** die Option **Herunterladen** aus und speichern die Zuordnung.

1. Wählen Sie auf der Symbolleiste des Bereichs **Zuordnungen** die Option **Hinzufügen** aus.

1. Geben Sie unter **Zuordnung hinzufügen** einen eindeutigen Namen für Ihre Zuordnung ein, und schließen Sie den Erweiterungsnamen `.xslt` ein.

1. Wählen Sie neben dem Feld **Zuordnung** das Ordnersymbol aus. Wählen Sie die hochzuladende Zuordnung aus.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Nachdem der Upload Ihrer Zuordnungsdatei abgeschlossen ist, wird die aktualisierte Zuordnung in der Liste **Zuordnungen** angezeigt.

---

<a name="delete-map"></a>

## <a name="delete-a-map"></a>Löschen von einer Zuordnung

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto, falls es noch nicht geöffnet ist.

1. Wählen Sie im Menü Ihres Integrationskontos unter **Einstellungen** die Option **Zuordnungen** aus.

1. Nachdem der Bereich **Zuordnungen** geöffnet wurde, wählen Sie Ihre Zuordnung und dann **Löschen** aus.

1. Um zu bestätigen, dass Sie die Zuordnung löschen möchten, wählen **Ja** aus.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource, falls noch nicht geschehen.

1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Zuordnungen** aus.

1. Nachdem der Bereich **Zuordnungen** geöffnet wurde, wählen Sie Ihre Zuordnung und dann **Löschen** aus.

1. Um zu bestätigen, dass Sie die Zuordnung löschen möchten, wählen **Ja** aus.

---

## <a name="next-steps"></a>Nächste Schritte

* [Transformieren von XML für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-transform.md)
* [Überprüfen von XML für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
