---
title: Datenkonvertierung für Azure Healthcare-APIs
description: Verwenden des $convert-Datenendpunkts und der Anpassungskonvertervorlagen zum Konvertieren von Daten in den Gesundheits-APIs
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: ranku
ms.openlocfilehash: a40a8f44ebb8e4297544ccd49c75acedfeabf1d1
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2021
ms.locfileid: "132902101"
---
# <a name="converting-your-data-to-fhir"></a>Konvertieren Ihrer Daten in FHIR

> [!IMPORTANT]
> Diese Funktion befindet sich in der öffentlichen Vorschau und wird ohne Vereinbarung zum Servicelevel bereitgestellt. Für Produktionsworkloads wird sie nicht empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Der benutzerdefinierte $convert-Data-Endpunkt im FHIR-Dienst ist für die Datenkonvertierung von verschiedenen Datentypen in FHIR vorgesehen. Er verwendet die Liquid-Vorlagen-Engine und die Vorlagen aus dem Projekt [FHIR Converter](https://github.com/microsoft/FHIR-Converter) als Standardvorlagen. Diese Konvertierungsvorlagen können nach Bedarf angepasst werden. Derzeit werden zwei Konvertierungstypen unterstützt: **C-CDA in FHIR** und **HL7v2 in FHIR-Konvertierung.**

## <a name="use-the-convert-data-endpoint"></a>Verwenden des Endpunkts „$convert-data“

Der `$convert-data` Vorgang ist in den FHIR-Dienst integriert, um als Teil des Diensts ausgeführt zu werden. Nachdem Sie in Ihrem Server aktiviert `$convert-data` haben, können Sie API-Aufrufe an den Server durchführen, um Ihre Daten in FHIR zu konvertieren:

`https://<<FHIR service base URL>>/$convert-data`

### <a name="parameter-resource"></a>Parameterressource

$convert-data verwendet eine [Parameter-Ressource](http://hl7.org/fhir/parameters.html) im Anforderungstext, wie in der folgenden Tabelle beschrieben. Im Anforderungstext des API-Aufrufs würden Sie die folgenden Parameter einschließen:

| Parametername      | Beschreibung | Zulässige Werte |
| ----------- | ----------- | ----------- |
| inputData      | Die zu konvertierenden Daten. | Für `Hl7v2` : Zeichenfolge <br> Für `Ccda` : XML|
| inputDataType   | Der Datentyp der Eingabe. | ```HL7v2```, ``Ccda`` |
| templateCollectionReference | Verweis auf eine [OCI-Imagevorlagenauflistung](https://github.com/opencontainers/image-spec) auf [Azure Container Registry (ACR).](https://azure.microsoft.com/services/container-registry/) Es ist das Bild, das Liquid-Vorlagen enthält, die für die Konvertierung verwendet werden sollen. Dies kann ein Verweis auf die Standardvorlagen oder ein benutzerdefiniertes Vorlagenimage sein, das im FHIR-Dienst registriert ist. Im Folgenden erfahren Sie, wie Sie die Vorlagen anpassen, diese in ACR hosten und sich beim FHIR-Dienst registrieren. | Für Standardvorlagen: <br> Für **HL7v2-Standardvorlagen:** <br>```microsofthealth/fhirconverter:default``` <br>``microsofthealth/hl7v2templates:default``<br>Für **C-CDA-Standardvorlagen:**``microsofthealth/ccdatemplates:default`` <br><br>Für benutzerdefinierte Vorlagen: <br> \<RegistryServer\>/\<imageName\>@\<imageDigest\>, \<RegistryServer\>/\<imageName\>:\<imageTag\> |
| rootTemplate | Die Stammvorlage, die beim Transformieren der Daten verwendet werden soll. | Für **HL7v2:**<br>```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04```<br><br> Für **C-CDA:**<br>```CCD```, `ConsultationNote`, `DischargeSummary`, `HistoryandPhysical`, `OperativeNote`, `ProcedureNote`, `ProgressNote`, `ReferralNote`, `TransferSummary` |

> [!WARNING]
> Standardvorlagen werden unter der MIT-Lizenz veröffentlicht und von Microsoft-Support **nicht** unterstützt.
>
> Standardvorlagen werden nur bereitgestellt, um Ihnen den schnellen Einstieg zu erleichtern. Sie werden möglicherweise aktualisiert, wenn versionen der Azure API for FHIR aktualisiert werden. Daher müssen Sie das Konvertierungsverhalten überprüfen und **Ihre eigene Kopie von Vorlagen** auf einem Azure Container Registry hosten, diese beim Azure API for FHIR registrieren und in Ihren API-Aufrufen verwenden, um ein konsistentes Datenkonvertierungsverhalten für die verschiedenen Versionen von Azure API for FHIR zu erhalten.

#### <a name="sample-request"></a>Beispiel für eine Anforderung

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

#### <a name="sample-response"></a>Beispiel für eine Antwort

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Anpassen von Vorlagen

Sie können die [Erweiterung „FHIR Converter“](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) für Visual Studio Code verwenden, um die Vorlagen gemäß Ihren Anforderungen anzupassen. Die Erweiterung bietet eine interaktive Bearbeitungsumgebung und ermöglicht das mühelose Herunterladen von Vorlagen und Beispieldaten, die von Microsoft veröffentlicht wurden. Weitere Informationen finden Sie in der Dokumentation in der Erweiterung.

## <a name="host-and-use-templates"></a>Hosten und Verwenden von Vorlagen

Es wird dringend empfohlen, ihre eigene Kopie von Vorlagen in ACR zu hosten. Es sind vier Schritte erforderlich, um Ihre eigene Kopie von Vorlagen zu hosten und diejenigen im vorgang $convert-data zu verwenden:

1. Pushen Sie die Vorlagen in Ihre Azure Container Registry-Instanz.
1. Aktivieren Sie die verwaltete Identität in Ihrer FHIR-Dienstinstanz.
1. Bereitstellen des Zugriffs auf die verwaltete Identität des FHIR-Diensts für die ACR
1. Registrieren Sie die ACR-Server im FHIR-Dienst.
1. Konfigurieren Sie optional die ACR-Firewall für den sicheren Zugriff.

### <a name="push-templates-to-azure-container-registry"></a>Pushen von Vorlagen an Azure Container Registry

Nachdem Sie eine ACR-Instanz erstellt haben, können Sie den Befehl _FHIR Converter: Push Templates_ in der [Erweiterung „FHIR Converter“](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) verwenden, um die angepassten Vorlagen an ACR zu pushen. Alternativ können Sie auch das [CLI-Tool für die Vorlagenverwaltung](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) verwenden.

### <a name="enable-managed-identity-on-fhir-service"></a>Aktivieren der verwalteten Identität für den FHIR-Dienst

Navigieren Sie im Azure-Portal zu Ihrer Instanz des FHIR-Dienstdiensts, und wählen Sie dann das Blatt **Identität** aus.
Ändern Sie den Status in **Ein,** um die verwaltete Identität im FHIR-Dienst zu aktivieren.

![Aktivieren der verwalteten Identität](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-fhir-service"></a>Bereitstellen des Zugriffs auf den FHIR-Dienst von ACR

1. Navigieren Sie zum Blatt **Zugriffssteuerung (IAM).**

1. Wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus, um die Seite Rollenzuweisung hinzufügen zu öffnen.

1. Weisen Sie die [Rolle AcrPull](../../role-based-access-control/built-in-roles.md#acrpull) zu. 

   ![Seite „Rollenzuweisung hinzufügen“](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

Weitere Informationen zum Zuweisen von Rollen im Azure-Portal finden Sie unter [Integrierte Azure-Rollen.](../../role-based-access-control/role-assignments-portal.md)

### <a name="register-the-acr-servers-in-fhir-service"></a>Registrieren der ACR-Server im FHIR-Dienst

Sie können den ACR-Server mithilfe der Azure-Portal oder mithilfe der CLI registrieren.

#### <a name="registering-the-acr-server-using-azure-portal"></a>Registrieren des ACR-Servers mit Azure-Portal
Navigieren Sie in Ihrer FHIR-Dienstinstanz unter **Datentransformation** zum Blatt **Artifacts.** Die Liste der derzeit registrierten ACR-Server wird angezeigt. Wählen Sie **Hinzufügen** und dann im Dropdownmenü Ihren Registrierungsserver aus. Sie müssen **Speichern** auswählen, damit die Registrierung wirksam wird. Es kann einige Minuten dauern, bis die Änderung angewendet und Ihre Instanz neu gestartet wird.

#### <a name="registering-the-acr-server-using-cli"></a>Registrieren des ACR-Servers über die Befehlszeilenschnittstelle
Sie können bis zu 20 ACR-Server im FHIR-Dienst registrieren.

Installieren Sie die CLI für die Gesundheits-APIs bei Bedarf über Azure PowerShell:

```powershell
az extension add -n healthcareapis
```

Registrieren Sie die ACR-Server im FHIR-Dienst gemäß den folgenden Beispielen:

##### <a name="register-a-single-acr-server"></a>Registrieren eines einzelnen ACR-Servers

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>Registrieren mehrerer ACR-Server

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```
### <a name="configure-acr-firewall"></a>Konfigurieren der ACR-Firewall

Wählen  Sie im Portal Netzwerk des Azure-Speicherkontos aus.

![Konfigurieren der ACR-Firewall](media/convert-data/networking-container-registry.png)

Klicken Sie auf **Ausgewählte Netzwerke**. 

Geben Sie im Abschnitt **Firewall** die IP-Adresse im Feld **Adressbereich an.** Fügen Sie IP-Adressbereiche hinzu, um den Zugriff über das Internet oder Ihre lokalen Netzwerke zuzulassen. 

In der folgenden Tabelle finden Sie die IP-Adresse für die Azure-Region, in der der FHIR-Dienst bereitgestellt wird.

|**Azure-Region**         |**Öffentliche IP-Adresse** |
|:----------------------|:-------------------|
| Australien (Osten)       | 20.53.44.80       |
| Kanada, Mitte       | 20.48.192.84      |
| USA (Mitte)           | 52.182.208.31     |
| East US              | 20.62.128.148     |
| USA (Ost) 2            | 20.49.102.228     |
| USA, Osten 2 (EUAP)       | 20.39.26.254      |
| Deutschland, Norden        | 51.116.51.33      |
| Deutschland, Westen-Mitte | 51.116.146.216    |
| Japan, Osten           | 20.191.160.26     |
| Korea, Mitte        | 20.41.69.51       |
| USA Nord Mitte     | 20.49.114.188     |
| Nordeuropa         | 52.146.131.52     |
| Südafrika, Norden   | 102.133.220.197   |
| USA Süd Mitte     | 13.73.254.220     |
| Asien, Südosten       | 23.98.108.42      |
| Schweiz, Norden    | 51.107.60.95      |
| UK, Süden             | 51.104.30.170     |
| UK, Westen              | 51.137.164.94     |
| USA, Westen-Mitte      | 52.150.156.44     |
| Europa, Westen          | 20.61.98.66       |
| USA, Westen 2            | 40.64.135.77      |


> [!NOTE]
> Die obigen Schritte ähneln den Konfigurationsschritten, die im Dokument Exportieren von FHIR-Daten beschrieben sind. Weitere Informationen finden Sie unter [Sicherer Export in Azure Storage](./export-data.md#secure-export-to-azure-storage)

Für den Zugriff auf ein privates Netzwerk (d. h. Private Link) können Sie auch den öffentlichen Netzwerkzugriff von ACR deaktivieren.
* Wählen Sie im Portal das Blatt Netzwerk des Azure-Speicherkontos aus.
* Wählen Sie `Disabled`aus.
* Wählen Sie Firewallausnahme: Vertrauenswürdigen Microsoft-Dienste den Zugriff auf diese Containerregistrierung gestatten.

![private link for ACR (private link für ACR)](media/convert-data/configure-private-network-container-registry.png)

### <a name="verify"></a>Überprüfung

Rufen Sie die API „$convert-data“ auf, und geben Sie dabei im Parameter „templateCollectionReference“ Ihren Vorlagenverweis an.

`<RegistryServer>/<imageName>@<imageDigest>`
