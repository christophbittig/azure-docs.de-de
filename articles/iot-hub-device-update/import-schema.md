---
title: Importieren von Updates in Device Update für IoT Hub – Schema und weitere Informationen | Microsoft-Dokumentation
description: Schema und andere verwandte Informationen (einschließlich Objekten), die beim Importieren von Updates in Device Update für IoT Hub verwendet werden.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 09dc0bd3afb2b9bfc99313ad38d5c7ad19086cb8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061038"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importieren von Updates in Device Update für IoT Hub – Schema und weitere Informationen
Wenn Sie ein Update in Device Update für IoT Hub importieren möchten, müssen Sie zuerst die [Konzepte](import-concepts.md) und die [Schrittanleitung](import-update.md) lesen. Wenn Sie an den Details des Schemas interessiert sind, das beim Erstellen eines Importmanifests verwendet wird, und etwas zu verknüpften Objekten erfahren möchten, informieren Sie sich weiter unten.

## <a name="import-manifest-schema"></a>Schema des Importmanifests

| Name | type | BESCHREIBUNG | Beschränkungen |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId`-Objekt | Update-Identität. |
| UpdateType | Zeichenfolge | Updatetyp: <br/><br/> * Geben Sie `microsoft/apt:1` an, wenn Sie ein paketbasiertes Update mit dem Referenz-Agent ausführen.<br/> * Geben Sie `microsoft/swupdate:1` an, wenn Sie ein imagebasiertes Update mit dem Referenz-Agent ausführen.<br/> Geben Sie `microsoft/simulator:1` an, wenn Sie den Beispiel-Agent-Simulator verwenden.<br/> * Geben Sie einen benutzerdefinierten Typ an, wenn Sie einen benutzerdefinierten Agent entwickeln. | Format: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Insgesamt maximal 32 Zeichen |
| InstalledCriteria | Zeichenfolge | Vom Agent interpretierte Zeichenfolge zur Ermittlung, ob das Update erfolgreich angewendet wurde:  <br/> * Geben Sie den **Wert** von „SWVersion“ für den Updatetyp `microsoft/swupdate:1` an.<br/> * Geben Sie `{name}-{version}` für den Updatetyp `microsoft/apt:1` an, dessen Name und Version aus der APT-Datei abgerufen werden.<br/> * Geben Sie eine benutzerdefinierte Zeichenfolge an, wenn Sie einen benutzerdefinierten Agent entwickeln.<br/> | Maximal 64 Zeichen |
| Kompatibilität | Array von `CompatibilityInfo`[-Objekten](#compatibilityinfo-object) | Kompatibilitätsinformationen des Geräts, das mit diesem Update kompatibel ist. | Maximal 10 Elemente |
| CreatedDateTime | date/time | Datum und Uhrzeit, an dem/zu der das Update erstellt wurde. | Durch Trennzeichen getrenntes ISO 8601-Datums- und Uhrzeitformat, in UTC |
| ManifestVersion | Zeichenfolge | Schemaversion des Importmanifests. Geben Sie den Wert `2.0` an, der mit der Schnittstelle `urn:azureiot:AzureDeviceUpdateCore:1` und der Schnittstelle `urn:azureiot:AzureDeviceUpdateCore:4` kompatibel ist. | Muss gleich `2.0` sein. |
| Dateien | Array von `File`-Objekten | Aktualisieren von Nutzlastdateien | Maximal fünf Dateien |

## <a name="updateid-object"></a>UpdateId-Objekt

| Name | type | BESCHREIBUNG | Beschränkungen |
| --------- | --------- | --------- | --------- |
| Anbieter | Zeichenfolge | Anbieterteil der Update-Identität. | 1–64 Zeichen, alphanumerisch, Punkt und Bindestrich. |
| Name | Zeichenfolge | Namensteil der Update-Identität. | 1–64 Zeichen, alphanumerisch, Punkt und Bindestrich. |
| Version | version | Versionsteil der Update-Identität. | 2 bis 4 Teile, durch Punkte getrennte Versionsnummer. Die Gesamtanzahl _jedes_ durch Punkte getrennten Teils kann zwischen „0“ und „2147483647“ betragen. Führende Nullen werden nicht unterstützt.

## <a name="file-object"></a>File-Objekt

| Name | type | BESCHREIBUNG | Beschränkungen |
| --------- | --------- | --------- | --------- |
| Dateiname | Zeichenfolge | Name der Datei | Darf nicht mehr als 255 Zeichen umfassen. Muss innerhalb eines Updates eindeutig sein |
| SizeInBytes | Int64 | Größe der Datei in Bytes. | Informationen zur maximalen Größe pro einzelner Datei und zusammen pro Update finden Sie unter [Grenzwerte für Geräteupdates](./device-update-limits.md). |
| Hashes | `Hashes`-Objekt | JSON-Objekt mit Hash(es) der Datei |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo-Objekt

| Name | type | BESCHREIBUNG | Beschränkungen |
| --- | --- | --- | --- |
| DeviceManufacturer | Zeichenfolge | Hersteller des Geräts, mit dem das Update kompatibel ist. | 1–64 Zeichen, alphanumerisch, Punkt und Bindestrich. |
| DeviceModel | Zeichenfolge | Modell des Geräts, mit dem das Update kompatibel ist. | 1–64 Zeichen, alphanumerisch, Punkt und Bindestrich. |

## <a name="hashes-object"></a>Hashes-Objekt

| Name | Erforderlich | type | BESCHREIBUNG |
| --------- | --------- | --------- | --------- |
| Sha256 | True | Zeichenfolge | Base64-codierter Hash der Datei mit dem SHA-256-Algorithmus. |

## <a name="example-import-request-body"></a>Beispiel für den Import-Anforderungstext

Wenn Sie die Beispielausgabe des Importmanifests von der Seite [Hinzufügen eines neuen Updates](./import-update.md#review-the-generated-import-manifest) verwenden und die [Rest-API](/rest/api/deviceupdate/updates) für Geräteupdates direkt aufrufen möchten, um den Import durchzuführen, sollte der entsprechende Anforderungstext wie folgt aussehen:

```json
{
  "importManifest": {
    "url": "http://<your Azure Storage location file path>/importManifest.json",
    "sizeInBytes": <size of import manifest file>,
    "hashes": {
      "sha256": "<hash of import manifest file>"
    }
  },
  "files": [
    {
      "filename": "file1.json",
      "url": "http://<your Azure Storage location file path>/file1.json"
    },
    {
          "filename": "file2.zip",
          "url": "http://<your Azure Storage location file path>/file2.zip"
    },
  ]
}
```

## <a name="oauth-authorization-when-calling-device-update-apis"></a>OAuth-Autorisierung beim Aufruf von Device Update-APIs

**azure_auth**

Azure Active Directory OAuth2 Flow Type: oauth2 Flow: any 

Autorisierungs-URL: https://login.microsoftonline.com/common/oauth2/authorize

**Bereiche**

| Name | BESCHREIBUNG |
| --- | --- |
| `https://api.adu.microsoft.com/user_impersonation` | Identitätswechsel Ihres Benutzerkontos |
| `https://api.adu.microsoft.com/.default`  | Flows für Clientanmeldeinformationen |


**Berechtigungen**

Wenn zum Anmelden des Benutzers eine Azure AD-Anwendung verwendet wird, muss der Bereich „/user_impersonation“ umfassen. 

Sie müssen Ihrer Azure AD-App Berechtigungen hinzufügen (auf der Registerkarte der API-Berechtigungen in der Ansicht der Azure AD-Anwendung), um die Azure Device Update-API zu verwenden. Fordern Sie die API-Berechtigung für Azure Device Update an (unter „Von meiner Organisation verwendete APIs“), und erteilen Sie die delegierte Berechtigung „user_impersonation“.

ADU akzeptiert Token, die Token mit einem beliebigen von Azure AD unterstützten Flow für Benutzer, Anwendungen oder verwaltete Identitäten erwerben. Einige Flows erfordern jedoch ein zusätzliches Setup der Azure AD-Anwendung: 

* Sorgen Sie bei öffentlichen Clientflows dafür, dass Mobil- und Desktopflows aktiviert werden.
* Fügen Sie bei impliziten Flows unbedingt eine Webplattform hinzu, und wählen Sie „Zugriffstoken“ für den Autorisierungsendpunkt aus.

**Beispiel für die Verwendung der Azure CLI**:

```azurecli
az login

az account get-access-token --resource 'https://api.adu.microsoft.com/'
```

**Beispiele für das Erwerben eines Tokens mit der PowerShell MSAL**:

_Verwenden von Benutzeranmeldeinformationen_ 

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope
```

_Verwenden von Benutzeranmeldeinformationen mit Gerätecode_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -Interactive -DeviceCode
```

_Verwenden von App-Anmeldeinformationen_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$cert = '<client_certificate>'
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/.default'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -ClientCertificate $cert
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Konzepte zum Importieren](./import-concepts.md).

Wenn Sie bereit sind, probieren Sie die [Schrittanleitung für den Import](./import-update.md) aus, in dem Sie Schritt für Schritt durch den Importvorgang geführt werden.
