---
title: Bereitstellen des IoT-Connectors im Azure-Portal – Azure Healthcare-APIs
description: In diesem Artikel erfahren Sie, wie Sie den IoT-Connector im Azure-Portal bereitstellen.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/10/2021
ms.author: jasteppe
ms.custom: mode-portal
ms.openlocfilehash: 8323d2026bc45c3c357ad0055cc7da37c5a345b8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061589"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Bereitstellen des IoT-Connectors im Azure-Portal

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schnellstartanleitung erfahren Sie, wie Sie den IoT-Connector im Azure-Portal bereitstellen. Durch das Konfigurieren eines IoT-Connectors können Sie Daten aus Internet der Dinge (IoT) in Ihrem Fast Healthcare Interoperability Resources-Dienst (FHIR&#174;) erfassen, indem Sie einen Azure Event Hub für Gerätenachrichten verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Es ist wichtig, dass Sie die folgenden Voraussetzungen erfüllt haben, bevor Sie mit den Schritten zum Erstellen einer IoT-Connectorinstanz in Azure Healthcare-APIs beginnen.

* [Azure-Konto](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Im Azure-Portal bereitgestellte Ressourcengruppe](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Event Hubs Namespace und Event Hub, die im Azure-Portal bereitgestellt werden](../../event-hubs/event-hubs-create.md)
* [In Azure Healthcare-APIs bereitgestellter Arbeitsbereich](../healthcare-apis-quickstart.md)  
* [In Azure Healthcare-APIs bereitgestellter FHIR-Dienst](../fhir/fhir-portal-quickstart.md) 

## <a name="deploy-iot-connector"></a>Bereitstellen von IoT-Konnektor 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an, und geben Sie dann den Ressourcennamen Ihres Arbeitsbereichs für die Gesundheits-APIs in das Feld **Suchleiste** ein.
 
   ![Screenshot der Eingabe des Ressourcennamens des Arbeitsbereichs in das Suchleistenfeld](media/select-workspace-resource-group.png#lightbox)

2. Wählen Sie **Deploy IoT connectors (IoT-Connectors bereitstellen) aus.**

   ![Screenshot des Blatts "IoT-Connectors"](media/iot-connector-blade.png#lightbox)

3. Wählen Sie als Nächstes **IoT-Connector hinzufügen** aus.

   ![Screenshot: Hinzufügen von IoT-Connectors](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>Konfigurieren des IoT-Connectors zum Erfassen von Daten

Füllen Sie auf der Registerkarte **Grundlagen** die erforderlichen Felder unter **Instanzdetails** aus.

![Screenshot: Details zur IoT-Konfiguration der Instanz](media/basics-instance-details.png#lightbox)

1. Geben Sie den **IoT-Connectornamen ein.**

   Der **Name des IoT-Connectors** ist ein Anzeigename für den IoT-Connector. Geben Sie einen eindeutigen Namen für Ihre IoT Connector ein. Beispielsweise können Sie ihm den Namen `healthdemo-iot` geben.

2. Geben Sie den **Event Hub-Namen** ein.

   Der Event Hub-Name ist der Name der **Event Hubs Instanz,** die Sie bereitgestellt haben. 

   Informationen zu Azure Event Hubs finden Sie unter [Schnellstart: Erstellen eines Event Hubs mit Azure-Portal](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace).

3. Geben Sie die **Consumergruppe ein.**

   Der Name der Consumergruppe befindet sich über die **Suchleiste,** um zu der bereitgestellten Event Hubs Instanz zu wechseln, und indem Sie das Blatt  **Consumergruppen** auswählen.

   ![Screenshot: Name der Consumergruppe](media/consumer-group-name.png#lightbox)

   Informationen zu Consumergruppen finden Sie unter [Features und Terminologie in Azure Event Hubs](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers).

4. Geben Sie den Namen des **vollqualifizierten Namespace** ein.

    Der **vollqualifizierte Namespace** ist der **Hostname,** der sich auf der **Übersichtsseite** Ihres Event Hubs Namespace befindet.

    ![Screenshot: Vollqualifizierte Namespaces](media/event-hub-hostname.png#lightbox)  

    Weitere Informationen zu Event Hubs Namespaces finden Sie unter [Namespace](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) im Dokument Features und Terminologie in Azure Event Hubs.

5. Wählen Sie **Weiter: Gerätezuordnung aus.** 
  
## <a name="configure-device-mapping-properties"></a>Konfigurieren von Gerätezuordnungseigenschaften

> [!TIP]
> Der IoMT Connector Data Mapper ist ein Open Source-Tool zum Visualisieren der Zuordnungskonfiguration für die Normalisierung der Eingabedaten eines Geräts und deren Transformation in FHIR-Ressourcen. Entwickler können dieses Tool verwenden, um Geräte- und FHIR-Zielzuordnungen zu bearbeiten und zu testen und die Daten zum Hochladen in einen IoT-Connector im Azure-Portal zu exportieren. Dieses Tool hilft Entwicklern auch, die Geräte- und FHIR-Zielzuordnungskonfigurationen ihres Geräts zu verstehen.
>
> Weitere Informationen finden Sie in der Open Source-Dokumentation:
>
> [IoMT-Connector-Datenzuordnung](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [Zuordnung von Geräteinhalten](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. Geben Sie auf der Registerkarte **Gerätezuordnung** den JSON-Code für die Gerätezuordnung ein, der Ihrem IoT-Connector zugeordnet ist.

   ![Screenshot: Konfigurieren der Gerätezuordnung](media/configure-device-mapping.png#lightbox)

2. Wählen Sie **Weiter: Ziel >** aus, um die Zieleigenschaften zu konfigurieren, die Ihrem IoT-Connector zugeordnet sind.

## <a name="configure-fhir-destination-mapping-properties"></a>Konfigurieren von FHIR-Zielzuordnungseigenschaften

Geben Sie auf der Registerkarte **Ziel** die Zieleigenschaften ein, die dem IoT-Connector zugeordnet sind.

   ![Screenshot: Konfigurieren von Zieleigenschaften](media/configure-destination-properties.png#lightbox)

1. Geben Sie die Azure-Ressourcen-ID des **FHIR-Diensts** ein.

   Der **FHIR-Servername** (auch als **FHIR-Dienst** bezeichnet) befindet sich über die **Suchleiste,** um zum bereitgestellten FHIR-Dienst zu wechseln, und indem Sie das Blatt **Eigenschaften** auswählen. Kopieren Sie die **Ressourcen-ID-Zeichenfolge,** und fügen Sie sie in das Textfeld **FHIR-Server** ein.

    ![Screenshot: Eingeben des FHIR-Servernamens](media/fhir-service-resource-id.png#lightbox) 

2. Geben Sie den **Zielnamen ein.**

   **Der Zielname** ist ein Anzeigename für das Ziel. Geben Sie einen eindeutigen Namen für Ihr Ziel ein. Beispielsweise können Sie ihm den Namen `iotmedicdevice` geben.

3. Wählen **Sie Erstellen** oder **Suchen** als **Auflösungstyp aus.**

    > [!NOTE]
    > Damit das IoT-Connectorziel eine gültige Beobachtungsressource im FHIR-Dienst erstellen kann, müssen eine Geräteressource und eine Patientenressource auf dem FHIR-Server vorhanden **sein,** damit die Beobachtung ordnungsgemäß auf das Gerät verweisen kann, das die Daten erstellt hat, und auf den Patienten, von dem die Daten gemessen wurden. Es gibt zwei Modi, die der IoT-Connector verwenden kann, um die Geräte- und Patientenressourcen aufzulösen.

   **Erstellen**

     Das IoT-Connectorziel versucht, eine Geräteressource vom FHIR-Server mithilfe des in der Event Hub-Nachricht enthaltenen Gerätebezeichners abzurufen. Außerdem wird versucht, eine Patientenressource vom FHIR-Server abzurufen, indem der in der Event Hub-Nachricht enthaltene Patientenbezeichner verwendet wird. Wenn eine ressource nicht gefunden wird, werden neue Ressourcen (Gerät, Patient oder beides) erstellt, die nur den in der Event Hub-Nachricht enthaltenen Bezeichner enthalten. Wenn Sie die Option **Erstellen** verwenden, können sowohl ein Gerätebezeichner als auch ein Patientenbezeichner in der Gerätezuordnung konfiguriert werden. Anders ausgedrückt: Wenn sich das IoT Connector Ziel im **Erstellungsmodus** befindet, kann es normal funktionieren, **ohne** dem FHIR-Server Geräte- und Patientenressourcen hinzuzufügen.

   **Suche**

     Das IoT-Connectorziel versucht, eine Geräteressource vom FHIR-Server mithilfe des in der Event Hub-Nachricht enthaltenen Gerätebezeichners abzurufen. Wenn die Geräteressource nicht gefunden wird, verursacht dies einen Fehler, und die Daten werden nicht verarbeitet. Damit **Lookup** ordnungsgemäß funktioniert, muss eine Geräteressource mit einem Bezeichner vorhanden **sein,** der mit dem in der Event Hub-Nachricht enthaltenen Gerätebezeichner übereinstimmt, und die Geräteressource **muss** einen Verweis auf eine Patientenressource aufweisen, die ebenfalls vorhanden ist. Anders ausgedrückt: Wenn sich das IoT-Connectorziel im Suchmodus befindet, **müssen** dem FHIR-Server Geräte- und Patientenressourcen hinzugefügt werden, bevor Daten verarbeitet werden können.

   Weitere Informationen finden Sie in der Open Source-Dokumentation [FHIR-Zielzuordnung.](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)

4. Geben Sie unter **Zielzuordnung** den JSON-Code im Code-Editor ein.

   Informationen zum Mapper-Tool finden Sie unter [IoMT-Connector-Datenzuordnungstool.](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)

5. Sie können **Überprüfen + erstellen** auswählen, oder Sie können **Weiter: Tags >** auswählen, wenn Sie Tags konfigurieren möchten.  

## <a name="optional-configure-tags"></a>(Optional) Konfigurieren von Tags

Tags sind Name-Wert-Paare, die zum Kategorisieren von Ressourcen verwendet werden. Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren Ihrer Azure-Ressourcen und Verwaltungshierarchie.](../../azure-resource-manager/management/tag-resources.md)

Geben Sie auf der Registerkarte **Tags** die Tageigenschaften ein, die dem IoT-Connector zugeordnet sind.

   ![Screenshot der Tageigenschaften.](media/tag-properties.png#lightbox)
 
1. Geben Sie einen **Namen** ein.
2. Geben Sie einen **Wert** ein.
3. Klicken Sie auf **Überprüfen + erstellen**.

   Sie sollten eine **Erfolgreiche Überprüfungsmeldung** wie in der folgenden Abbildung sehen. 

   ![Screenshot: Meldung zum Erfolgreichen Überprüfen](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > Wenn Ihr IoT-Connector die Überprüfung nicht überprüft hat, überprüfen Sie die Fehlermeldung zur Überprüfung, und beheben Sie das Problem. Es wird empfohlen, die Eigenschaften auf jeder Registerkarte des IoT-Connectors zu überprüfen, die Sie konfiguriert haben.

4. Wählen Sie als Nächstes die Option **Erstellen**.

   Der neu bereitgestellte IoT-Connector wird auf der Seite Azure-Ressourcengruppen angezeigt.

   ![Screenshot des bereitgestellten IoT-Connectors, der in der Liste Zuletzt verwendeter Azure-Ressourcen aufgeführt ist](media/azure-resources-iot-connector-deployed.png#lightbox)  

    Nachdem Ihr IoT-Connector bereitgestellt wurde, werden die Schritte zum Zuweisen von Berechtigungen für den Zugriff auf den Event Hub und den FHIR-Dienst beschrieben. 

## <a name="granting-iot-connector-access"></a>Gewähren des Zugriffs auf den IoT-Connector

Um sicherzustellen, dass Ihr IoT-Connector ordnungsgemäß funktioniert, muss er Zugriffsberechtigungen für den Event Hub und den FHIR-Dienst erteilt haben. 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>Zugreifen auf den IoT-Connector über den Event Hub

1. Wählen Sie in der Liste **Azure-Ressourcengruppe** den Namen Ihres **Event Hubs Namespace** aus.

2. Wählen Sie das Blatt **Zugriffssteuerung (IAM)** und dann **+ Hinzufügen** aus.   

   ![Screenshot der Zugriffssteuerung des Event Hubs Namespace](media/access-control-blade-add.png#lightbox)

3. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   ![Screenshot: Hinzufügen einer Rollenzuweisung](media/event-hub-add-role-assignment.png#lightbox)
 
4. Wählen Sie **die Rolle** und dann Azure Event Hubs **Datenempfänger aus.**

   ![Screenshot: Felder zum Hinzufügen erforderlicher Rollenzuweisungen](media/event-hub-add-role-assignment-fields.png#lightbox)

   Mit Azure Event Hubs Rolle "Datenempfänger" kann der IoT-Connector, dem diese Rolle zugewiesen wird, Daten von diesem Event Hub empfangen.

   Weitere Informationen zu Anwendungsrollen finden Sie unter [Authentication & Authorization for the Healthcare APIs (preview) (Authentifizierung & Autorisierung für die Gesundheits-APIs (Vorschauversion)).](.././authentication-authorization.md)

5. Wählen **Sie Zugriff zuweisen zu aus,** und behalten Sie die Standardoption **Benutzer, Gruppe oder Dienstprinzipal bei.**

6. Geben Sie **im Feld** Auswählen den Sicherheitsprinzipal für Ihren IoT-Connector ein.  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   Wenn Sie einen IoT-Connector bereitstellen, wird eine verwaltete Identität erstellt. Der name der verwalteten Identifizierung ist eine Verkettung des Arbeitsbereichsnamens, des Ressourcentyps (d. h. des IoT-Connectors) und des Namens des IoT-Connectors.

7. Wählen Sie **Speichern** aus.

   Nachdem die Rollenzuweisung erfolgreich zum Event Hub hinzugefügt wurde, wird in einer Benachrichtigung ein grünes Häkchen mit dem Text "Rollenzuweisung hinzufügen" angezeigt.  Diese Meldung gibt an, dass der IoT-Connector jetzt aus dem Event Hub lesen kann.

   ![Screenshot: Meldung zu hinzugefügter Rollenzuweisung](media/event-hub-added-role-assignment.png#lightbox)

Weitere Informationen zum Erstellen des Zugriffs auf Event Hubs Ressourcen finden Sie unter [Autorisieren des Zugriffs mit Azure Active Directory](../../event-hubs/authorize-access-azure-active-directory.md).  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>Zugreifen auf den IoT-Connector über den FHIR-Dienst

1. Wählen Sie **in der Liste Azure-Ressourcengruppe** den Namen Ihres **FHIR-Diensts aus.**
 
2. Wählen Sie **das Blatt Zugriffssteuerung (IAM)** und dann **+ Hinzufügen aus.** 

3. Wählen Sie **Rollenzuweisung hinzufügen** aus.

  ![Screenshot: Hinzufügen einer Rollenzuweisung für den FHIR-Dienst](media/fhir-service-add-role-assignment.png#lightbox)

4. Wählen Sie **die Rolle** und dann **FHIR Data Writer aus.**

   Die FHIR-Datenschreiberrolle bietet Lese- und Schreibzugriff, den der IoT-Connector verwendet, um zu funktionieren. Da der IoT-Connector als separate Ressource bereitgestellt wird, erhält der FHIR-Dienst Anforderungen vom IoT-Connector. Wenn der FHIR-Dienst nicht weiß, wer die Anforderung gestellt hat, oder wenn er nicht über die zugewiesene Rolle verfügen soll, wird die Anforderung als nicht autorisiert verweigert.

   Weitere Informationen zu Anwendungsrollen finden Sie unter [Authentication & Authorization for the Healthcare APIs (preview) (Authentifizierung & Autorisierung für die Gesundheits-APIs (Vorschauversion)).](.././authentication-authorization.md)

5. Geben Sie **im Feld** Auswählen den Sicherheitsprinzipal für Ihren IoT-Connector ein.  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. Wählen Sie **Speichern** aus.

   ![Screenshot: Meldung zur Hinzugefügten Rollenzuweisung des FHIR-Diensts](media/fhir-service-added-role-assignment.png#lightbox)

   Weitere Informationen zum Zuweisen von Rollen zum FHIR-Dienst finden Sie unter [Konfigurieren von Azure RBAC.](.././configure-azure-rbac.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen IoT-Connector in der Azure-Portal. Eine Übersicht über den IoT-Connector finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
