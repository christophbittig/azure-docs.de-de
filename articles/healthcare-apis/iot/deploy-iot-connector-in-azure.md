---
title: Bereitstellen des IoT-Connectors im Azure-Portal – Azure Healthcare-APIs
description: In diesem Artikel erfahren Sie, wie Sie den IoT-Connector in der Azure-Portal.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/13/2021
ms.author: jasteppe
ms.openlocfilehash: 064c904b33317d72adbef771353a98a947438252
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003210"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Bereitstellen des IoT-Connectors im Azure-Portal

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schnellstartanleitung erfahren Sie, wie Sie den IoT-Connector im Azure-Portal. Wenn Sie einen IoT-Connector konfigurieren, können Sie Daten aus Internet der Dinge (IoT) mithilfe eines Azure Event Hubs für Gerätenachrichten in Ihrem Fast Healthcare Interoperability Resources-Dienst (FHIR&#174;) aufnehmen.

## <a name="prerequisites"></a>Voraussetzungen

Es ist wichtig, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie mit dem Erstellen einer IoT-Connectorinstanz in Azure Healthcare-APIs beginnen.

* [Azure-Konto](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Ressourcengruppe, die in der Azure-Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Event Hubs namespace and Event Hub deployed in the Azure-Portal](../../event-hubs/event-hubs-create.md)
* [In Azure Healthcare-APIs bereitgestellter Arbeitsbereich](../workspace-overview.md)  
* [In Azure Healthcare-APIs bereitgestellter FHIR-Dienst](../fhir/fhir-portal-quickstart.md) 


## <a name="deploy-iot-connector"></a>Bereitstellen des IoT-Connectors 

1. Melden Sie sich [beim Azure-Portal](https://portal.azure.com)an, und geben Sie dann den Ressourcennamen Ihres Arbeitsbereichs für die Gesundheits-APIs in **das** Feld Suchleiste ein.
 
   ![Screenshot: Eingeben des Namens der Arbeitsbereichsressource in das Suchleistenfeld](media/select-workspace-resource-group.png#lightbox)

2. Wählen Sie das **Blatt IoT-Connectors** aus.

   ![Screenshot des Blatts "IoT-Connectors"](media/iot-connector-blade.png#lightbox)

3. Wählen Sie als Nächstes **IoT-Connector hinzufügen aus.**

   ![Screenshot: Hinzufügen von IoT-Connectors](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>Konfigurieren des IoT-Connectors zum Aufnehmen von Daten

Füllen Sie **auf der Registerkarte** Grundlagen die erforderlichen Felder unter **Instanzdetails aus.**

![Screenshot: Details zur IoT-Konfiguration der Instanz](media/basics-instance-details.png#lightbox)

1. Geben Sie den **Namen des IoT-Connectors ein.**

   Der **Name des IoT-Connectors** ist ein Benutzername für den IoT-Connector. Geben Sie einen eindeutigen Namen für Ihre IoT Connector. Als Beispiel können Sie ihm den Namen `healthdemo-iot` geben.

2. Geben Sie den **Event Hub-Namen ein.**

   Der Event Hub-Name ist der Name der Event Hubs **Instanz,** die Sie bereitgestellt haben. 

   Weitere Informationen zu Azure Event Hubs Finden Sie unter [Schnellstart: Erstellen eines Event Hubs mithilfe Azure-Portal](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace).

3. Geben Sie die **Consumergruppe ein.**

   Der Name der Consumergruppe  befindet sich in der Suchleiste, um zu der Event Hubs-Instanz zu wechseln, die Sie bereitgestellt haben, und indem Sie das **Blatt Consumergruppen** auswählen.

   ![Screenshot: Name der Consumergruppe](media/consumer-group-name.png#lightbox)

   Informationen zu Consumergruppen finden Sie unter [Features und Terminologie in Azure Event Hubs](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers).

4. Geben Sie den Namen des vollqualifizierten **Namespace ein.**

    Der **vollqualifizierte Namespace** ist **der Hostname,** der sich auf Event Hubs Der Namespace befindet sich auf der Seite **Übersicht.**

    ![Screenshot: Vollqualifizierter Namespace](media/event-hub-hostname.png#lightbox)  

    Weitere Informationen zu Event Hubs Namespaces finden Sie unter [Namespace](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) im Dokument Features und Terminologie in Azure Event Hubs Namespaces.

5. Wählen Sie **Weiter: Gerätezuordnung aus.** 
  
## <a name="configure-device-mapping-properties"></a>Konfigurieren von Gerätezuordnungseigenschaften

> [!TIP]
> Der IoMT Connector Data Mapper ist ein Open Source-Tool zum Visualisieren der Zuordnungskonfiguration zum Normalisieren der Eingabedaten eines Geräts und zum anschließenden Transformieren in FHIR-Ressourcen. Entwickler können dieses Tool verwenden, um Geräte- und FHIR-Zielzuordnungen zu bearbeiten und zu testen und die Daten zum Hochladen in einen IoT-Connector im Azure-Portal. Dieses Tool hilft Entwicklern auch dabei, die Geräte- und FHIR-Zielzuordnungskonfigurationen ihres Geräts zu verstehen.
>
> Weitere Informationen finden Sie in der Open-Source-Dokumentation:
>
> [Datenzuordnung des IoMT-Connectors](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [Zuordnung von Geräteinhalten](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. Geben Sie auf **der Registerkarte** Gerätezuordnung den JSON-Code für die Gerätezuordnung ein, der Ihrem IoT-Connector zugeordnet ist.

   ![Screenshot: Konfigurieren der Gerätezuordnung](media/configure-device-mapping.png#lightbox)

2. Wählen **Sie Weiter: Ziel >,** um die Zieleigenschaften zu konfigurieren, die Ihrem IoT-Connector zugeordnet sind.

## <a name="configure-fhir-destination-mapping-properties"></a>Konfigurieren von FHIR-Zielzuordnungseigenschaften

Geben Sie **auf der** Registerkarte Ziel die Zieleigenschaften ein, die dem IoT-Connector zugeordnet sind.

   ![Screenshot: Konfigurieren von Zieleigenschaften](media/configure-destination-properties.png#lightbox)

1. Geben Sie die Azure-Ressourcen-ID des **FHIR-Diensts ein.**

   Der **FHIR-Servername** (auch als **FHIR-Dienst** bezeichnet) befindet sich in der Suchleiste, um zum bereitgestellten FHIR-Dienst zu wechseln und das Blatt **Eigenschaften** auszuwählen.  Kopieren Sie die **Ressourcen-ID-Zeichenfolge,** und fügen Sie sie in das **Textfeld FHIR-Server** ein.

    ![Screenshot: FHIR-Servernamen eingeben](media/fhir-service-resource-id.png#lightbox) 

2. Geben Sie den **Zielnamen ein.**

   Der **Zielname** ist ein Benutzerfreundlich für das Ziel. Geben Sie einen eindeutigen Namen für Ihr Ziel ein. Als Beispiel können Sie ihm den Namen `iotmedicdevice` geben.

3. Wählen **Sie erstellen** oder suchen **als** **Auflösungstyp aus.**

    > [!NOTE]
    > Damit das IoT-Connectorziel eine gültige Beobachtungsressource im FHIR-Dienst erstellen kann, müssen im FHIR-Server eine Geräteressource und eine Patientenressource vorhanden sein, damit die Beobachtung ordnungsgemäß auf das Gerät verweisen kann, von dem die Daten erstellt wurden, und der Patient, von dem die Daten gemessen wurden.  Der IoT-Connector kann zwei Modi verwenden, um das Gerät und die Patientenressourcen aufzulösen.

   **Erstellen**

     Das IoT-Connectorziel versucht, eine Geräteressource vom FHIR-Server mithilfe des in der Event Hub-Nachricht enthaltenen Gerätebezeichners abzurufen. Außerdem wird versucht, eine Patientenressource vom FHIR-Server mithilfe des in der Event Hub-Nachricht enthaltenen Patientenbezeichners abzurufen. Wenn keine der Ressourcen gefunden wird, werden neue Ressourcen erstellt (Gerät, Patient oder beides), die nur den in der Event Hub-Nachricht enthaltenen Bezeichner enthalten. Wenn Sie die Option **Erstellen** verwenden, können sowohl eine Geräte-ID als auch eine Patienten-ID in der Gerätezuordnung konfiguriert werden. Anders ausgedrückt: Wenn sich IoT Connector Ziel  im Erstellungsmodus befindet, kann es normal funktionieren, ohne dem FHIR-Server Geräte- und Patientenressourcen hinzufügen zu müssen. 

   **Suche**

     Das IoT-Connectorziel versucht, eine Geräteressource vom FHIR-Server mithilfe des In der Event Hub-Nachricht enthaltenen Gerätebezeichners abzurufen. Wenn die Geräteressource nicht gefunden wird, verursacht dies einen Fehler, und die Daten werden nicht verarbeitet. Damit **Lookup** ordnungsgemäß funktioniert, muss eine Geräteressource mit einem Bezeichner vorhanden sein,  der mit der in der Event Hub-Nachricht enthaltenen Geräte-ID übereinstimmen, und die Geräteressource muss über einen Verweis auf eine Patientenressource verfügen, die ebenfalls vorhanden ist.  Anders ausgedrückt: Wenn sich das IoT-Connectorziel im Suchmodus  befindet, müssen dem FHIR-Server Geräte- und Patientenressourcen hinzugefügt werden, bevor Daten verarbeitet werden können.

   Weitere Informationen finden Sie in der Open Source-Dokumentation [zur FHIR-Zielzuordnung.](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)

4. Geben **Sie unter Zielzuordnung** den JSON-Code im Code-Editor ein.

   Informationen zum Mapper-Tool finden Sie unter [IoMT Connector Data Mapper Tool](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper).

5. Sie können Überprüfen **+ erstellen** auswählen, oder Sie können **Weiter: Tags** >, wenn Sie Tags konfigurieren möchten.  

## <a name="optional-configure-tags"></a>(Optional) Konfigurieren von Tags

Tags sind Name-Wert-Paare, die zum Kategorisieren von Ressourcen verwendet werden. Weitere Informationen zu Tags finden Sie unter Verwenden [von Tags zum Organisieren ihrer Azure-Ressourcen und Verwaltungshierarchie.](../../azure-resource-manager/management/tag-resources.md)

Geben Sie **auf der** Registerkarte Tags die Dem IoT-Connector zugeordneten Tageigenschaften ein.

   ![Screenshot: Tageigenschaften](media/tag-properties.png#lightbox)
 
1. Geben Sie einen **Namen** ein.
2. Geben Sie einen **Wert ein.**
3. Klicken Sie auf **Überprüfen + erstellen**.

   Sie sollten eine **Erfolgsmeldung zur Überprüfung wie** in der folgenden Abbildung sehen. 

   ![Screenshot der Erfolgsmeldung zur Überprüfung.](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > Wenn Ihr IoT-Connector nicht überprüft wurde, überprüfen Sie die Überprüfungsfehlermeldung, und beheben Sie das Problem. Es wird empfohlen, die Eigenschaften auf jeder ioT-Connectorregisterkarte zu überprüfen, die Sie konfiguriert haben.

4. Wählen Sie als Nächstes die Option **Erstellen**.

   Der neu bereitgestellte IoT-Connector wird in Ihrer Azure-Ressourcengruppe angezeigt.

   ![Screenshot des bereitgestellten IoT-Connectors, der in der Liste Der letzte Azure-Ressourcen aufgeführt ist.](media/azure-resources-iot-connector-deployed.png#lightbox)  

    Nachdem Ihr IoT-Connector bereitgestellt wurde, werden wir die Schritte zum Zuweisen von Berechtigungen für den Zugriff auf den Event Hub und den FHIR-Dienst ausführen. 

## <a name="granting-iot-connector-access"></a>Gewähren des Zugriffs auf den IoT-Connector

Um sicherzustellen, dass Ihr IoT-Connector ordnungsgemäß funktioniert, muss er Zugriffsberechtigungen für den Event Hub- und FHIR-Dienst erteilt haben. 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>Zugreifen auf den IoT-Connector über den Event Hub

1. Wählen Sie **in der Liste Azure-Ressourcengruppe** den Namen Ihres Event Hubs Namespace **aus.**

2. Wählen Sie das **Blatt Zugriffssteuerung (IAM)** und dann **+ Hinzufügen aus.**   

   ![Screenshot: Zugriffssteuerung für Event Hubs Namespace](media/access-control-blade-add.png#lightbox)

3. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   ![Screenshot: Hinzufügen der Rollenzuweisung](media/event-hub-add-role-assignment.png#lightbox)
 
4. Wählen Sie die **Rolle** und dann **Azure Event Hubs Datenempfänger** aus.

   ![Screenshot: Hinzufügen von Pflichtfeldern für die Rollenzuweisung](media/event-hub-add-role-assignment-fields.png#lightbox)

   Mit der Rolle Azure Event Hubs Datenempfängers kann der IoT-Connector, dem diese Rolle zugewiesen wird, Daten von diesem Event Hub empfangen.

   Weitere Informationen zu Anwendungsrollen finden Sie unter [Authentifizierung & Autorisierung für die Gesundheits-APIs (Vorschau).](.././authentication-authorization.md)

5. Wählen Sie **Zugriff zuweisen zu** aus, und behalten Sie die Standardoption **Benutzer, Gruppe oder Dienstprinzipal** bei.

6. Geben **Sie** im Feld Auswählen den Sicherheitsprinzipal für Ihren IoT-Connector ein.  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   Wenn Sie einen IoT-Connector bereitstellen, wird eine verwaltete Identität erstellt. Der verwaltete Identifizierungsname ist eine Verkettung des Arbeitsbereichsnamens, des Ressourcentyps (d. h. des IoT-Connectors) und des Namens des IoT-Connectors.

7. Wählen Sie **Speichern** aus.

   Nachdem die Rollenzuweisung dem Event Hub erfolgreich hinzugefügt wurde, wird in einer Benachrichtigung ein grünes Häkchen mit dem Text "Rollenzuweisung hinzufügen" angezeigt.  Diese Meldung gibt an, dass der IoT-Connector jetzt aus dem Event Hub lesen kann.

   ![Screenshot der hinzugefügten Meldung zur Rollenzuweisung](media/event-hub-added-role-assignment.png#lightbox)

Weitere Informationen zum Erstellen des Zugriffs auf Event Hubs Ressourcen finden Sie unter [Autorisieren](../../event-hubs/authorize-access-azure-active-directory.md)des Zugriffs mit Azure Active Directory .  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>Zugreifen auf den IoT-Connector über den FHIR-Dienst

1. Wählen Sie in der **Liste Azure-Ressourcengruppe** den Namen Ihres  **FHIR-Diensts** aus.
 
2. Wählen Sie das Blatt **Zugriffssteuerung (IAM)** und dann **+ Hinzufügen** aus. 

3. Wählen Sie **Rollenzuweisung hinzufügen** aus.

  ![Screenshot: Hinzufügen der Rollenzuweisung für den FHIR-Dienst](media/fhir-service-add-role-assignment.png#lightbox)

4. Wählen Sie die **Rolle** und dann **FHIR Data Writer** aus.

   Die FHIR Data Writer-Rolle bietet Lese- und Schreibzugriff, den der IoT-Connector für die Funktion verwendet. Da der IoT-Connector als separate Ressource bereitgestellt wird, empfängt der FHIR-Dienst Anforderungen vom IoT-Connector. Wenn der FHIR-Dienst nicht weiß, wer die Anforderung stellt, oder wenn ihm die Rolle nicht zugewiesen ist, wird die Anforderung als nicht autorisiert abgelehnt.

   Weitere Informationen zu Anwendungsrollen finden Sie unter [Authentifizierung & Autorisierung für die Gesundheits-APIs (Vorschau).](.././authentication-authorization.md)

5. Geben **Sie** im Feld Auswählen den Sicherheitsprinzipal für Ihren IoT-Connector ein.  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. Wählen Sie **Speichern** aus.

   ![Screenshot: Meldung zum Hinzufügen der Rollenzuweisung durch den FHIR-Dienst](media/fhir-service-added-role-assignment.png#lightbox)

   Weitere Informationen zum Zuweisen von Rollen zum FHIR-Dienst finden Sie unter [Konfigurieren von Azure RBAC für den FHIR-Dienst.](../fhir/configure-azure-rbac-for-fhir.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen IoT-Connector im Azure-Portal bereitstellen. Eine Übersicht über den IoT-Connector finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.