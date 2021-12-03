---
title: Leitfaden und Anleitung zur Problembehandlung für Den IoT-Connector – Azure Healthcare-APIs
description: Dieser Artikel hilft Benutzern bei der Problembehandlung von häufigen Fehlermeldungen, Bedingungen und dem Kopieren von Zuordnungsdateien für den IoT-Connector.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 6a3af00eaca90c2ecdbbdcc4f6cce5231ffefcc0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719445"
---
# <a name="iot-connector-troubleshooting-guide"></a>Leitfaden zur Problembehandlung für Den IoT-Connector

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind. 

> [!TIP]
> Wenn Sie ein [Azure Technical Support-Ticket](https://azure.microsoft.com/support/create-ticket/) für den IoT-Connector öffnen, fügen Sie Kopien Ihrer Geräte- und FHIR-Zielzuordnungen ein, um Sie bei der Problembehandlung zu unterstützen.

Dieser Artikel enthält Schritte zur Problembehandlung für häufige Fehlermeldungen und Bedingungen des IoT-Connectors. Außerdem erfahren Sie, wie Sie Kopien der Zielzuordnungen Device and Fast Healthcare Interoperability Resources (FHIR&#174;) des IoT-Connectors erstellen. Außerdem können Sie die Geräte- und FHIR-Zielzuordnungskopien zum Bearbeiten und Archivieren außerhalb der Azure-Portal. 

## <a name="device-and-fhir-destination-mappings-validations"></a>Überprüfungen von Geräte- und FHIR-Zielzuordnungen

In diesem Abschnitt wird der Überprüfungsprozess des IoT-Connectors beschrieben. Der Überprüfungsprozess überprüft die Zielzuordnungen "Gerät" und "FHIR", bevor sie zur Verwendung gespeichert werden können. Diese Elemente sind in den Zielzuordnungen Gerät und FHIR erforderlich.

> [!TIP]
> Sehen Sie sich das [IoMT Connector Data Mapper-Tool zum](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) Bearbeiten, Testen und Behandeln von Problemen bei Der IoT-Connector geräte- und FHIR-Zielzuordnungen an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version des](https://github.com/microsoft/iomt-fhir) IoT-Connectors.

**Gerätezuordnungen**

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
>Beispiel:
> 
>Wenn ein mobiles IoMT-Gerät ein- oder entfernt wird, haben die Elemente keine Werte außer einem Namen, den der IoT-Connector abspricht und aus gibt. Bei der FHIR-Konvertierung ordnet der IoT-Connector es einem codeorientierten Konzept basierend auf dem semantischen Typ zu. Dies bedeutet, dass keine tatsächlichen Werte aufgefüllt werden.

**FHIR-Zielzuordnungen**

|Element|Erforderlich|
|:------|:-------|
|TypName|True|

> [!NOTE]
> Dies ist das einzige zu diesem Zeitpunkt überprüfte FHIR-Zielzuordnungselement.

## <a name="error-messages-and-fixes"></a>Fehlermeldungen und Fehlerbehebungen

### <a name="iot-connector-resource"></a>IoT-Connectorressource

|`Message`|Angezeigt wird|Bedingung|Fix| 
|-------|---------|---------|---|
|Die maximale Anzahl von Ressourcentypen `iotconnectors` wurde erreicht.|API und Azure-Portal|Das IoT-Connector-Abonnementkontingent ist erreicht (Der Standardwert ist 25 pro Abonnement).|Löschen Sie eine der vorhandenen Instanzen des IoT-Connectors. Verwenden Sie ein anderes Abonnement, bei dem das Abonnementkontingent noch nicht erreicht wurde. Fordern Sie eine Erhöhung des Abonnementkontingents an.
|Ungültige `deviceMapping` Zuordnung. Validierungsfehler: {Fehlerliste}|API und Azure-Portal|Die `properties.deviceMapping` in der Ressourcenbereitstellungsanforderung des IoT-Connectors bereitgestellte ist ungültig.|Korrigieren Sie die Fehler im JSON-Zuordnungs-JSON-Code, der in der -Eigenschaft `properties.deviceMapping` bereitgestellt wird.
|`fullyQualifiedEventHubNamespace` ist NULL, leer oder falsch formatiert.|API und Azure-Portal|Die Bereitstellungsanforderung für den IoT-Connector `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` ist ungültig.|Aktualisieren Sie den IoT-Connector `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` auf das richtige Format. Sollte `{YOUR_NAMESPACE}.servicebus.windows.net` sein.
|Vorgängerressourcen müssen vollständig bereitgestellt werden, bevor eine untergeordnete Ressource bereitgestellt werden kann.|API|Der übergeordnete Arbeitsbereich wird weiterhin bereitgestellt.|Warten Sie, bis die übergeordnete Arbeitsbereichsbereitstellung abgeschlossen ist, und senden Sie die Bereitstellungsanforderung erneut.
|`Location` -Eigenschaft untergeordneter Ressourcen muss mit der `Location` -Eigenschaft der übergeordneten Ressourcen übereinstimmen.|API|Die Anforderungseigenschaft für die IoT-Connectorbereitstellung `location` ist anders als die übergeordnete Eigenschaft `location` Arbeitsbereich.|Legen Sie `location` die -Eigenschaft des IoT-Connectors in der Bereitstellungsanforderung auf den gleichen Wert wie die übergeordnete Workspace-Eigenschaft `location` fest.

### <a name="destination-resource"></a>Zielressource

|`Message`|Angezeigt wird|Bedingung|Fix| 
|-------|---------|---------|---|
|Die maximale Anzahl von Ressourcentypen `iotconnectors/destinations` wurde erreicht.|API und Azure-Portal|Das Zielressourcenkontingent des IoT-Connectors ist erreicht, und der Standardwert ist 1 pro IoT-Connector.|Löschen Sie die vorhandene Instanz der Zielressource des IoT-Connectors. Pro IoT-Connector ist nur eine Zielressource zulässig.
|Die `fhirServiceResourceId` bereitgestellte ist ungültig.|API und Azure-Portal|Die in der Bereitstellungsanforderung für Zielressourcen bereitgestellte ist keine gültige Ressourcen-ID für eine Instanz des `properties.fhirServiceResourceId` Azure Healthcare-APIs-FHIR-Diensts.|Stellen Sie sicher, dass die Ressourcen-ID ordnungsgemäß formatiert ist, und stellen Sie sicher, dass die Ressourcen-ID für eine Azure Healthcare-APIs-FHIR-Instanz gilt. Das Format sollte wie im folgenden Format sein: `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`
|Vorgängerressourcen müssen vollständig bereitgestellt werden, bevor eine untergeordnete Ressource bereitgestellt werden kann.|API|Der übergeordnete Arbeitsbereich oder der übergeordnete IoT-Connector wird weiterhin bereitgestellt.|Warten Sie, bis die Bereitstellung des übergeordneten Arbeitsbereichs oder des übergeordneten IoT-Connectors abgeschlossen ist, und senden Sie die Bereitstellungsanforderung erneut.
|`Location` -Eigenschaft untergeordneter Ressourcen muss mit der `Location` -Eigenschaft der übergeordneten Ressourcen übereinstimmen.|API|Die Anforderungseigenschaft Destination provisioning `location` (Zielbereitstellung) ist anders als die übergeordnete IoT-Connectoreigenschaft. `location`|Legen Sie die -Eigenschaft des Ziels in der Bereitstellungsanforderung auf den gleichen Wert wie die übergeordnete `location` IoT-Connectoreigenschaft `location` fest.

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>Warum werden IoT-Connectordaten nicht im FHIR-Dienst angezeigt?

|Mögliche Probleme|Fehlerbehebungen|
|----------------|-----|
|Die Daten werden noch verarbeitet.|Die Daten werden in Batches (alle ca. 15 Minuten) an den FHIR-Dienst ausgehend.  Es ist möglich, dass die Daten noch verarbeitet werden und zusätzliche Zeit benötigt wird, damit die Daten im FHIR-Dienst beibehalten werden.|
|Die Gerätezuordnung wurde nicht konfiguriert.|Konfigurieren und speichern Sie die konforme Gerätezuordnung.|
|Die FHIR-Zielzuordnung wurde nicht konfiguriert.|Konfigurieren und speichern Sie die konforme FHIR-Zielzuordnung.|
|Die Gerätenachricht enthält keinen erwarteten Ausdruck, der in der Gerätezuordnung definiert ist.|Überprüfen `JsonPath` Sie, ob ausdrücke, die in der Gerätezuordnung definiert sind, mit Token übereinstimmen, die in der Gerätenachricht definiert sind.|
|Eine Geräteressource wurde nicht im FHIR-Dienst erstellt (Auflösungstyp: nur Suche)*.|Erstellen Sie eine gültige Geräteressource im FHIR-Dienst. Stellen Sie sicher, dass die Geräteressource einen Bezeichner enthält, der mit dem in der eingehenden Nachricht angegebenen Gerätebezeichner entspricht.|
|Im FHIR-Dienst wurde keine Patient-Ressource erstellt (Auflösungstyp: nur Suche)*.|Erstellen Sie eine gültige Patient-Ressource im FHIR-Dienst.|
|Der Verweis ist nicht festgelegt, oder der Verweis `Device.patient` ist ungültig (Auflösungstyp: Nur Suche)*.|Stellen Sie sicher, dass die Geräteressource einen gültigen [Verweis](https://www.hl7.org/fhir/device-definitions.html#Device.patient) auf eine Patientenressource enthält.| 

[*Referenzschnellstart: Bereitstellen](deploy-iot-connector-in-azure.md) des IoT-Connectors mit Azure-Portal für eine funktionale Beschreibung der IoT-Connector-Auflösungstypen (z. B. Suchen oder Erstellen).

### <a name="the-operation-performed-by-iot-connector"></a>Der vom IoT-Connector ausgeführte Vorgang

Diese Eigenschaft stellt den Vorgang dar, der vom IoT-Connector ausgeführt wird, wenn der Fehler aufgetreten ist. Ein Vorgang stellt in der Regel die Datenflussphase während der Verarbeitung einer Gerätenachricht dar. Im Folgenden finden Sie eine Liste der möglichen Werte für diese Eigenschaft.

> [!NOTE]
> Informationen zu den verschiedenen Phasen des Datenflusses im IoT-Connector finden Sie unter [IoT-Connector-Datenfluss.](iot-data-flow.md)

|Datenflussphase|BESCHREIBUNG|
|---------------|-----------|
|Setup|Die Setupdatenflussphase ist der Vorgang, der speziell für die Einrichtung Ihrer Instanz des IoT-Connectors gilt.|
|Normalisierung|Normalisierung ist die Datenflussphase, in der die Gerätedaten normalisiert werden.|
|Gruppierung|Die Gruppierungsdatenflussphase, in der die normalisierten Daten gruppiert werden.|
|FHIRConversion|FHIRConversion ist die Datenflussphase, in der die ge gruppierten normalisierten Daten in eine FHIR-Ressource transformiert werden.|
|Unbekannt|Unbekannt ist der Vorgangstyp, der unbekannt ist, wenn ein Fehler auftritt.|

### <a name="the-severity-of-the-error"></a>Der Schweregrad des Fehlers

Diese Eigenschaft stellt den Schweregrad des aufgetretenen Fehlers dar. Im Folgenden finden Sie eine Liste der möglichen Werte für diese Eigenschaft.

|severity|BESCHREIBUNG|
|---------------|-----------|
|Warnung|Es liegt ein kleineres Problem im Datenflussprozess vor, aber die Verarbeitung der Gerätenachricht wird nicht stoppt.|
|Fehler|Diese Meldung tritt auf, wenn bei der Verarbeitung einer bestimmten Gerätenachricht ein Fehler aufgetreten ist und andere Nachrichten möglicherweise weiterhin wie erwartet ausgeführt werden.|
|Kritisch|Dieser Fehler tritt auf, wenn ein Problem auf Systemebene mit dem IoT-Connector vorliegt und keine Nachrichten zu verarbeiten sind.|

### <a name="the-type-of-error"></a>Der Typ des Fehlers

Diese Eigenschaft gibt eine Kategorie für einen bestimmten Fehler an, die im Grunde eine logische Gruppierung für ähnliche Fehlertypen darstellt. Im Folgenden finden Sie eine Liste der möglichen Werte für diese Eigenschaft.

|Fehlertyp|BESCHREIBUNG|
|----------|-----------|
|`DeviceTemplateError`|Dieser Fehlertyp bezieht sich auf die Gerätezuordnung.|
|`DeviceMessageError`|Dieser Fehlertyp tritt auf, wenn eine bestimmte Gerätenachricht verarbeitet wird.|
|`FHIRTemplateError`|Dieser Fehlertyp bezieht sich auf die FHIR-Zielzuordnung.|
|`FHIRConversionError`|Dieser Fehlertyp tritt auf, wenn eine Nachricht in eine FHIR-Ressource transformiert wird.|
|`FHIRResourceError`|Dieser Fehlertyp bezieht sich auf vorhandene Ressourcen im FHIR-Dienst, auf die vom IoT-Connector verwiesen wird.|
|`FHIRServerError`|Dieser Fehlertyp tritt bei der Kommunikation mit dem FHIR-Dienst auf.|
|`GeneralError`|Bei diesem Fehlertyp handelt es sich um alle anderen Fehlertypen.|

### <a name="the-name-of-the-error"></a>Der Name des Fehlers

Diese Eigenschaft stellt den Namen für einen bestimmten Fehler bereit. Im Folgenden finden Sie die Liste aller Fehlernamen mit ihrer Beschreibung und den zugehörigen Fehlertypen, dem Schweregrad und den Datenflussphasen.

|Fehlerbezeichnung|BESCHREIBUNG|Fehlertyp(en)|Schweregrad des Fehlers|Datenflussphase(n)|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|Dieser Fehler tritt auf, wenn mehrere Patienten- oder Geräteressourcen im FHIR-Dienst für die entsprechenden Bezeichner in der Gerätenachricht gefunden werden.|`FHIRResourceError`|Fehler|`FHIRConversion`|
|`TemplateNotFoundException`|Eine Geräte- oder FHIR-Zielzuordnung, die nicht mit der Instanz des IoT-Connectors konfiguriert ist.|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|Die Korrelations-ID wird in der Gerätezuordnung nicht angegeben. `CorrelationIdNotDefinedException` ist ein bedingter Fehler, der nur auftritt, wenn die FHIR-Beobachtung Gerätemessungen mithilfe einer Korrelations-ID gruppieren muss, da sie nicht ordnungsgemäß konfiguriert ist.|`DeviceMessageError`|Fehler|Normalisierung|
|`PatientDeviceMismatchException`|Dieser Fehler tritt auf, wenn die Geräteressource im FHIR-Dienst über einen Verweis auf eine Patientenressource verfügt. Dieser Fehlertyp bedeutet, dass er nicht mit dem in der Nachricht angegebenen Patientenbezeichner übereinstimmen kann.|`FHIRResourceError`|Fehler|`FHIRConversionError`|
|`PatientNotFoundException`|Die Geräte-FHIR-Ressource, die dem Gerätebezeichner in der Gerätenachricht zugeordnet ist, verweist auf keine Patienten-FHIR-Ressource. Beachten Sie, dass dieser Fehler nur auftritt, wenn  die IoT-Connectorinstanz mit dem Suchauflösungstyp konfiguriert ist.|`FHIRConversionError`|Fehler|`FHIRConversion`|
|`DeviceNotFoundException`|Im FHIR-Dienst, der der in der Gerätenachricht angegebenen Geräte-ID zugeordnet ist, ist keine Geräteressource vorhanden.|`DeviceMessageError`|Fehler|Normalisierung|
|`PatientIdentityNotDefinedException`|Dieser Fehler tritt auf, wenn der Ausdruck zum Analysieren des Patientenbezeichners aus der Gerätenachricht nicht in der Gerätezuordnung konfiguriert ist oder der Bezeichner des Patienten nicht in der Gerätenachricht vorhanden ist. Beachten Sie, dass dieser Fehler nur auftritt, wenn der Auflösungstyp des IoT-Connectors auf Erstellen *festgelegt ist.*|`DeviceTemplateError`|Kritisch|Normalisierung|
|`DeviceIdentityNotDefinedException`|Dieser Fehler tritt auf, wenn der Ausdruck zum Analysieren des Gerätebezeichners aus der Gerätenachricht nicht in der Gerätezuordnung konfiguriert ist oder der Gerätebezeichner nicht in der Gerätenachricht vorhanden ist.|`DeviceTemplateError`|Kritisch|Normalisierung|
|`NotSupportedException`|Fehler beim Empfangen einer Gerätenachricht mit nicht unterstützten Formaten.|`DeviceMessageError`|Fehler|Normalisierung|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>Erstellen von Kopien der Geräte- und FHIR-Zielzuordnungen des IoT-Connectors

Das Kopieren von IoT-Connectorzuordnungen kann zum Bearbeiten und Archivieren außerhalb der Website Azure-Portal werden.

Die Zuordnungskopien sollten beim Öffnen eines Supporttickets für den technischen Azure-Support bereitgestellt werden, um den Problembehandlungsprozess zu unterstützen.

> [!NOTE]
> JSON ist derzeit das einzige unterstützte Format für Geräte- und FHIR-Zielzuordnungen.

> [!TIP]
> Weitere Informationen zu [IoT-Connector-Gerätezuordnungen](how-to-use-device-mappings.md) und [FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

1. Wählen Sie links im Arbeitsbereich "Gesundheits-APIs" die Option **"IoT-Connectors"** aus.

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="Wählen Sie IoT-Connectors aus." lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. Wählen Sie den Namen des **IoT-Connectors** aus, aus dem Sie die Geräte- und FHIR-Zielzuordnungen kopieren möchten.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > Dieser Prozess kann auch zum Kopieren und Speichern  des Inhalts der Ziel-FHIR-Zielzuordnung verwendet werden.

3. Wählen Sie den Inhalt des JSON-Code aus, und führen Sie einen Kopiervorgang aus (z. B.: Drücken Sie **STRG+C**). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. Führen Sie einen Einfügevorgang (z. B. DRÜCKEN SIE **STRG+V**) in eine neue Datei in einem Editor wie code Microsoft Visual Studio oder Editor. Stellen Sie sicher, dass Sie die Datei mit der **Erweiterung .json** speichern.

> [!TIP]
> Wenn Sie ein [Azure Technical Support-Ticket](https://azure.microsoft.com/support/create-ticket/) für den IoT-Connector öffnen, stellen Sie sicher, dass Sie Kopien Ihrer Geräte- und FHIR-Zielzuordnungen hinzufügen, um sie bei der Problembehandlung zu unterstützen.

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
