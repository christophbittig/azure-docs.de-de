---
title: Leitfaden zur Problembehandlung für IoT-Connectors und Anleitungen – Azure Healthcare-APIs
description: Dieser Artikel hilft Benutzern bei der Problembehandlung von häufigen Fehlermeldungen, Bedingungen und dem Kopieren von Zuordnungsdateien für den IoT-Connector.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jasteppe
ms.openlocfilehash: 07f1ed78abe90b2967335322f0eea17e721a44bc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005407"
---
# <a name="iot-connector-troubleshooting-guide"></a>Leitfaden zur Problembehandlung für Den IoT-Connector

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieser Artikel enthält Schritte zur Problembehandlung allgemeiner IoT-Connector-Fehlermeldungen und -Bedingungen. Außerdem erfahren Sie, wie Sie Kopien der Zielzuordnungen Gerät und Fast Healthcare Interoperability Resources (FHIR&#174;) des IoT-Connectors erstellen. Darüber hinaus können Sie die Geräte- und FHIR-Zielzuordnungskopien für die Bearbeitung und Archivierung außerhalb der Azure-Portal verwenden.  

> [!TIP]
> Wenn Sie ein [Azure Technical Support-Ticket](https://azure.microsoft.com/support/create-ticket/) für den IoT-Connector öffnen, fügen Sie Kopien Ihrer Geräte- und FHIR-Zielzuordnungen ein, um die Problembehandlung zu unterstützen.

## <a name="device-and-fhir-destination-mapping-validations"></a>Überprüfungen der Geräte- und FHIR-Zielzuordnung

In diesem Abschnitt wird der Überprüfungsprozess beschrieben, den der IoT-Connector führt. Der Überprüfungsprozess überprüft die Geräte- und FHIR-Zielzuordnungen, bevor sie zur Verwendung gespeichert werden können. Diese Elemente sind in den Geräte- und FHIR-Zielzuordnungen erforderlich.

**Gerätezuordnung**

|Element|Erforderlich|
|:-------|:------|
|TypName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|True|
|Values[].ValueExpression|True|

> [!NOTE]
> `Values[].ValueName and Values[].ValueExpression` -Elemente sind nur erforderlich, wenn sie über einen Werteintrag im Array verfügen. Es ist gültig, dass keine Werte zugeordnet sind. Dies wird verwendet, wenn die gesendete Telemetrie ein Ereignis ist. 
>
>Zum Beispiel:
> 
>Wenn ein wearable IoMT-Gerät eingeschaltet oder entfernt wird, haben die Elemente keine Werte außer einem Namen, mit dem der IoT-Connector übereinstimmt und ausgibt. Bei der FHIR-Konvertierung ordnet der IoT-Connector ihn einem codefähigen Konzept zu, das auf dem semantischen Typ basiert. Dies bedeutet, dass keine tatsächlichen Werte aufgefüllt werden.

**FHIR-Zielzuordnung**

|Element|Erforderlich|
|:------|:-------|
|TypName|True|

> [!NOTE]
> Dies ist das einzige erforderliche FHIR-Zielzuordnungselement, das derzeit überprüft wurde.

## <a name="error-messages-and-fixes"></a>Fehlermeldungen und Fehlerbehebungen

### <a name="iot-connector-resource"></a>IoT-Connectorressource

|`Message`|Angezeigt wird|Bedingung|Fix| 
|-------|---------|---------|---|
|Die maximale Anzahl von Ressourcentypen `iotconnectors` wurde erreicht.|API und Azure-Portal|Das Abonnementkontingent für den IoT-Connector wird erreicht (der Standardwert ist 25 pro Abonnement).|Löschen Sie eine der vorhandenen Instanzen des IoT-Connectors. Verwenden Sie ein anderes Abonnement, bei dem das Abonnementkontingent noch nicht erreicht wurde. Fordern Sie eine Erhöhung des Abonnementkontingents an.
|Ungültige `deviceMapping` Zuordnung. Validierungsfehler: {Liste der Fehler}|API und Azure-Portal|Die `properties.deviceMapping` in der Ressourcenbereitstellungsanforderung des IoT-Connectors bereitgestellte ist ungültig.|Beheben Sie die Fehler im zuordnungs-JSON-Code, der in der -Eigenschaft bereitgestellt `properties.deviceMapping` wird.
|`fullyQualifiedEventHubNamespace` ist NULL, leer oder falsch formatiert.|API und Azure-Portal|Die IoT-Connectorbereitstellungsanforderung `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` ist ungültig.|Aktualisieren Sie den IoT-Connector `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` auf das richtige Format. Sollte `{YOUR_NAMESPACE}.servicebus.windows.net` sein.
|Vorgängerressourcen müssen vollständig bereitgestellt werden, bevor eine untergeordnete Ressource bereitgestellt werden kann.|API|Der übergeordnete Arbeitsbereich wird weiterhin bereitgestellt.|Warten Sie, bis die übergeordnete Arbeitsbereichsbereitstellung abgeschlossen ist, und übermitteln Sie die Bereitstellungsanforderung erneut.
|`Location` -Eigenschaft von untergeordneten Ressourcen muss mit der `Location` -Eigenschaft übergeordneter Ressourcen übereinstimmen.|API|Die Anforderungseigenschaft für die IoT-Connectorbereitstellung `location` unterscheidet sich von der übergeordneten `location` Workspace-Eigenschaft.|Legen Sie die `location` -Eigenschaft des IoT-Connectors in der Bereitstellungsanforderung auf den gleichen Wert wie die übergeordnete `location` Workspace-Eigenschaft fest.

### <a name="destination-resource"></a>Zielressource

|`Message`|Angezeigt wird|Bedingung|Fix| 
|-------|---------|---------|---|
|Die maximale Anzahl von Ressourcentypen `iotconnectors/destinations` wurde erreicht.|API und Azure-Portal|Das Zielressourcenkontingent des IoT-Connectors wird erreicht, und der Standardwert ist 1 pro IoT-Connector.|Löschen Sie die vorhandene Instanz der IoT-Connectorzielressource. Pro IoT-Connector ist nur eine Zielressource zulässig.
|Der `fhirServiceResourceId` bereitgestellte ist ungültig.|API und Azure-Portal|Der in der Anforderung zur `properties.fhirServiceResourceId` Bereitstellung von Zielressourcen bereitgestellte ist keine gültige Ressourcen-ID für eine Instanz des FHIR-Diensts der Azure Healthcare-APIs.|Stellen Sie sicher, dass die Ressourcen-ID ordnungsgemäß formatiert ist, und stellen Sie sicher, dass die Ressourcen-ID für eine Azure Healthcare APIs-FHIR-Instanz bestimmt ist. Das Format sollte wie hier dargestellt sein: `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`
|Vorgängerressourcen müssen vollständig bereitgestellt werden, bevor eine untergeordnete Ressource bereitgestellt werden kann.|API|Der übergeordnete Arbeitsbereich oder der übergeordnete IoT-Connector wird weiterhin bereitgestellt.|Warten Sie, bis die Bereitstellung des übergeordneten Arbeitsbereichs oder des übergeordneten IoT-Connectors abgeschlossen ist, und senden Sie dann die Bereitstellungsanforderung erneut.
|`Location` -Eigenschaft von untergeordneten Ressourcen muss mit der `Location` -Eigenschaft übergeordneter Ressourcen übereinstimmen.|API|Die Eigenschaft Zielbereitstellungsanforderung `location` unterscheidet sich von der übergeordneten IoT-Connectoreigenschaft. `location`|Legen Sie die `location` -Eigenschaft des Ziels in der Bereitstellungsanforderung auf den gleichen Wert wie die übergeordnete IoT-Connectoreigenschaft `location` fest.

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>Warum werden keine IoT-Connectordaten im FHIR-Dienst angezeigt?

|Mögliche Probleme|Fehlerbehebungen|
|----------------|-----|
|Die Daten werden noch verarbeitet.|Daten werden in Batches an den FHIR-Dienst übermittelt (alle ca. 15 Minuten).  Es ist möglich, dass die Daten noch verarbeitet werden und zusätzliche Zeit benötigt wird, damit die Daten im FHIR-Dienst beibehalten werden.|
|Die Gerätezuordnung wurde nicht konfiguriert.|Konfigurieren und speichern Sie die entsprechende Gerätezuordnung.|
|Die FHIR-Zielzuordnung wurde nicht konfiguriert.|Konfigurieren und speichern Sie die entsprechende FHIR-Zielzuordnung.|
|Die Gerätenachricht enthält keinen erwarteten Ausdruck, der in der Gerätezuordnung definiert ist.|Überprüfen Sie, ob `JsonPath` die in der Gerätezuordnung definierten Ausdrücke mit token übereinstimmen, die in der Gerätenachricht definiert sind.|
|Im FHIR-Dienst wurde keine Geräteressource erstellt (Auflösungstyp: Nur Suche)*.|Erstellen Sie eine gültige Geräteressource im FHIR-Dienst. Stellen Sie sicher, dass die Geräteressource einen Bezeichner enthält, der mit dem in der eingehenden Nachricht angegebenen Gerätebezeichner übereinstimmt.|
|Im FHIR-Dienst wurde keine Patientenressource erstellt (Auflösungstyp: Nur Suche)*.|Erstellen Sie eine gültige Patientenressource im FHIR-Dienst.|
|Der `Device.patient` Verweis ist nicht festgelegt, oder der Verweis ist ungültig (Auflösungstyp: Nur Suche)*.|Stellen Sie sicher, dass die Geräteressource einen gültigen [Verweis](https://www.hl7.org/fhir/device-definitions.html#Device.patient) auf eine Patientenressource enthält.| 

[*Referenzschnellstart: Bereitstellen des IoT-Connectors mit Azure-Portal](deploy-iot-connector-in-azure.md) für eine funktionale Beschreibung der Auflösungstypen des IoT-Connectors (z. B. Suchen oder Erstellen).

### <a name="the-operation-performed-by-iot-connector"></a>Der vom IoT-Connector ausgeführte Vorgang

Diese Eigenschaft stellt den Vorgang dar, der vom IoT-Connector ausgeführt wird, wenn der Fehler aufgetreten ist. Ein Vorgang stellt in der Regel die Datenflussphase während der Verarbeitung einer Gerätenachricht dar. Im Folgenden finden Sie eine Liste der möglichen Werte für diese Eigenschaft.

> [!NOTE]
> Informationen zu den verschiedenen Phasen des Datenflusses im IoT-Connector finden Sie unter [IoT-Connectordatenfluss.](iot-data-flow.md)

|Datenflussphase|BESCHREIBUNG|
|---------------|-----------|
|Setup|Die Setup-Datenflussphase ist der vorgangsspezifische Vorgang zum Einrichten Ihrer Instanz des IoT-Connectors.|
|Normalisierung|Normalisierung ist die Datenflussphase, in der die Gerätedaten normalisiert werden.|
|Gruppierung|Die Gruppierungsdatenflussphase, in der die normalisierten Daten gruppiert werden.|
|FHIRConversion|FHIRConversion ist die Datenflussphase, in der die gruppiert normalisierten Daten in eine FHIR-Ressource transformiert werden.|
|Unbekannt|Unbekannt ist der Vorgangstyp, der unbekannt ist, wenn ein Fehler auftritt.|

### <a name="the-severity-of-the-error"></a>Der Schweregrad des Fehlers

Diese Eigenschaft stellt den Schweregrad des aufgetretenen Fehlers dar. Im Folgenden finden Sie eine Liste der möglichen Werte für diese Eigenschaft.

|severity|BESCHREIBUNG|
|---------------|-----------|
|Warnung|Im Datenflussprozess besteht ein kleineres Problem, aber die Verarbeitung der Gerätenachricht wird nicht beendet.|
|Fehler|Diese Meldung tritt auf, wenn bei der Verarbeitung einer bestimmten Gerätenachricht ein Fehler aufgetreten ist und andere Meldungen möglicherweise weiterhin wie erwartet ausgeführt werden.|
|Kritisch|Dieser Fehler tritt auf, wenn ein Problem auf Systemebene mit dem IoT-Connector vorliegt und keine Nachrichten verarbeitet werden sollen.|

### <a name="the-type-of-error"></a>Der Fehlertyp

Diese Eigenschaft gibt eine Kategorie für einen bestimmten Fehler an, die im Grunde eine logische Gruppierung für ähnliche Fehlertypen darstellt. Im Folgenden finden Sie eine Liste der möglichen Werte für diese Eigenschaft.

|Fehlertyp|BESCHREIBUNG|
|----------|-----------|
|`DeviceTemplateError`|Dieser Fehlertyp bezieht sich auf die Gerätezuordnung.|
|`DeviceMessageError`|Dieser Fehlertyp tritt auf, wenn eine bestimmte Gerätenachricht verarbeitet wird.|
|`FHIRTemplateError`|Dieser Fehlertyp bezieht sich auf die FHIR-Zielzuordnung.|
|`FHIRConversionError`|Dieser Fehlertyp tritt auf, wenn eine Nachricht in eine FHIR-Ressource transformiert wird.|
|`FHIRResourceError`|Dieser Fehlertyp bezieht sich auf vorhandene Ressourcen im FHIR-Dienst, auf die vom IoT-Connector verwiesen wird.|
|`FHIRServerError`|Dieser Fehlertyp tritt bei der Kommunikation mit dem FHIR-Dienst auf.|
|`GeneralError`|Bei diesem Fehlertyp geht es um alle anderen Arten von Fehlern.|

### <a name="the-name-of-the-error"></a>Der Name des Fehlers

Diese Eigenschaft stellt den Namen für einen bestimmten Fehler bereit. Im Folgenden finden Sie die Liste aller Fehlernamen mit ihrer Beschreibung und den zugeordneten Fehlertypen, dem Schweregrad und den Datenflussphasen.

|Fehlerbezeichnung|BESCHREIBUNG|Fehlertyp(en)|Schweregrad des Fehlers|Datenflussphase(n)|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|Dieser Fehler tritt auf, wenn mehrere Patienten- oder Geräteressourcen im FHIR-Dienst für die entsprechenden Bezeichner gefunden werden, die in der Gerätenachricht vorhanden sind.|`FHIRResourceError`|Fehler|`FHIRConversion`|
|`TemplateNotFoundException`|Eine Geräte- oder FHIR-Zielzuordnung, die nicht mit der Instanz des IoT-Connectors konfiguriert ist.|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|Die Korrelations-ID wird in der Gerätezuordnung nicht angegeben. `CorrelationIdNotDefinedException` ist ein bedingter Fehler, der nur auftritt, wenn die FHIR-Beobachtung Gerätemessungen mithilfe einer Korrelations-ID gruppieren muss, da sie nicht ordnungsgemäß konfiguriert ist.|`DeviceMessageError`|Fehler|Normalisierung|
|`PatientDeviceMismatchException`|Dieser Fehler tritt auf, wenn die Geräteressource im FHIR-Dienst über einen Verweis auf eine Patientenressource verfügt. Dieser Fehlertyp bedeutet, dass er nicht mit dem in der Nachricht vorhandenen Patientenbezeichner übereinstimmt.|`FHIRResourceError`|Fehler|`FHIRConversionError`|
|`PatientNotFoundException`|Die Geräte-FHIR-Ressource, die dem Gerätebezeichner in der Gerätenachricht zugeordnet ist, verweist auf keine Patienten-FHIR-Ressource. Beachten Sie, dass dieser Fehler nur auftritt, wenn die IoT-Connectorinstanz mit dem *Auflösungstyp Suche* konfiguriert ist.|`FHIRConversionError`|Fehler|`FHIRConversion`|
|`DeviceNotFoundException`|Im FHIR-Dienst ist keine Geräteressource vorhanden, die dem in der Gerätenachricht vorhandenen Gerätebezeichner zugeordnet ist.|`DeviceMessageError`|Fehler|Normalisierung|
|`PatientIdentityNotDefinedException`|Dieser Fehler tritt auf, wenn der Ausdruck zum Analysieren des Patientenbezeichners aus der Gerätenachricht nicht in der Gerätezuordnung konfiguriert ist oder der Patient identifer in der Gerätenachricht nicht vorhanden ist. Beachten Sie, dass dieser Fehler nur auftritt, wenn der Auflösungstyp des IoT-Connectors auf *Erstellen* festgelegt ist.|`DeviceTemplateError`|Kritisch|Normalisierung|
|`DeviceIdentityNotDefinedException`|Dieser Fehler tritt auf, wenn der Ausdruck zum Analysieren der Geräte-ID aus der Gerätenachricht nicht für die Gerätezuordnung konfiguriert ist oder der Gerätebezeichner in der Gerätemeldung nicht vorhanden ist.|`DeviceTemplateError`|Kritisch|Normalisierung|
|`NotSupportedException`|Fehler beim Empfang einer Gerätemeldung mit nicht unterstützten Formaten.|`DeviceMessageError`|Fehler|Normalisierung|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>Erstellen von Kopien von IoT-Connector-Geräte- und FHIR-Zielzuordnungen

Das Kopieren von IoT-Connectorzuordnungen kann für die Bearbeitung und Archivierung außerhalb der Azure-Portal Website nützlich sein.

Die Zuordnungskopien sollten dem technischen Azure-Support beim Öffnen eines Supporttickets zur Verfügung gestellt werden, um die Problembehandlung zu unterstützen.

> [!NOTE]
> JSON ist derzeit das einzige unterstützte Format für Geräte- und FHIR-Zielzuordnungen.

> [!TIP]
> Weitere Informationen zu [Geräte- und FHIR-Zielzuordnungen](how-to-use-fhir-mapping-iot.md) für IoT-Connectors

1. Wählen Sie links im Arbeitsbereich für die Gesundheits-APIs die Option **"IoT-Connectors"** aus.

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="Wählen Sie IoT-Connectors aus." lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. Wählen Sie den Namen des **IoT-Connectors** aus, aus dem Sie die Geräte- und FHIR-Zielzuordnungen kopieren möchten.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > Dieser Prozess kann auch zum Kopieren und Speichern des Inhalts der **FHIR-Zielzuordnung "Destination"** verwendet werden.

3. Wählen Sie den Inhalt des JSON-Code aus, und erstellen Sie einen Kopiervorgang (z. B. **Drücken Sie STRG+C).** 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. Fügen Sie einen Einfügevorgang (z. B. **STRG+V)** in eine neue Datei in einem Editor wie Microsoft Visual Studio Code oder Editor ein. Stellen Sie sicher, dass Sie die Datei mit der Erweiterung **.json** speichern.

> [!TIP]
> Wenn Sie ein [Azure Technical Support-Ticket](https://azure.microsoft.com/support/create-ticket/) für den IoT-Connector öffnen, stellen Sie sicher, dass Sie Kopien Ihrer Geräte- und FHIR-Zielzuordnungen als Hilfe bei der Problembehandlung einschließen.

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.