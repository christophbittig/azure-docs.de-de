---
title: azcopy login | Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzinformationen zum Befehl „azcopy login“.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f5c0532f194aad27f99752c9c5312f30dfe0a21d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600207"
---
# <a name="azcopy-login"></a>azcopy login

Dient zur Anmeldung bei Azure Active Directory, um den Zugriff auf Azure Storage-Ressourcen zu ermöglichen.

## <a name="synopsis"></a>Zusammenfassung

Melden Sie sich bei Azure Active Directory an, um auf Azure Storage-Ressourcen zugreifen zu können.

Zur Autorisierung für Ihr Azure Storage-Konto müssen Sie Ihrem Benutzerkonto die Rolle **Mitwirkender an Storage-Blobdaten** im Kontext des Speicherkontos, der übergeordneten Ressourcengruppe oder des übergeordneten Abonnements zuweisen.

Dieser Befehl speichert verschlüsselte Anmeldeinformationen für den aktuellen Benutzer mithilfe der integrierten Betriebssystemmechanismen zwischen.

> [!IMPORTANT]
> Wenn Sie eine Umgebungsvariable über die Befehlszeile festlegen, kann diese Variable in Ihrem Befehlszeilenverlauf gelesen werden. Es empfiehlt sich gegebenenfalls, Variablen mit Anmeldeinformationen aus Ihrem Befehlszeilenverlauf zu löschen. Wenn Sie verhindern möchten, dass Variablen in Ihrem Verlauf angezeigt werden, können Sie den Benutzer mit einem Skript zur Eingabe seiner Anmeldeinformationen auffordern und die Umgebungsvariable festlegen.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Verwandte konzeptionelle Artikel

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Tutorial: Migrieren von lokalen Daten zu Cloudspeicher mit AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Übertragen von Daten mit AzCopy und Blobspeicher](./storage-use-azcopy-v10.md#transfer-data)
- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

## <a name="examples"></a>Beispiele

Interaktive Anmeldung mit allgemeiner standardmäßiger AAD-Mandanten-ID:

```azcopy
azcopy login
```

Interaktive Anmeldung mit einer spezifischen Mandanten-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Anmeldung mit der systemseitig zugewiesenen Identität einer VM:

```azcopy
azcopy login --identity
```

Anmeldung mit der benutzerseitig zugewiesenen Identität einer VM und einer Client-ID der Dienstidentität:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Anmeldung mit der benutzerseitig zugewiesenen Identität einer VM und einer Objekt-ID der Dienstidentität:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Anmeldung mit der benutzerseitig zugewiesenen Identität einer VM und einer Ressourcen-ID der Dienstidentität:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Anmeldung als Dienstprinzipal unter Verwendung eines geheimen Clientschlüssels: Legen Sie die Umgebungsvariable „AZCOPY_SPA_CLIENT_SECRET“ auf den geheimen Clientschlüssel für die geheimnisbasierte Dienstprinzipalautorisierung fest.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Anmeldung als Dienstprinzipal unter Verwendung eines Zertifikats und seines Kennworts:

Legen Sie die Umgebungsvariable „AZCOPY_SPA_CERT_PASSWORD“ auf das Kennwort des Zertifikats für die zertifikatbasierte Dienstprinzipalautorisierung fest:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Behandeln Sie `/path/to/my/cert` als Pfad zu einer PEM- oder PKCS12-Datei. AzCopy ruft Ihr Zertifikat nicht aus dem Zertifikatspeicher des Systems ab.

`--certificate-path` ist bei der zertifikatbasierten Dienstprinzipalautorisierung zwingend erforderlich.

## <a name="options"></a>Optionen

**--aad-endpoint**-Zeichenfolge: Der zu verwendende Azure Active Directory-Endpunkt. Für die globale Azure-Cloud gilt der Standardwert (https://login.microsoftonline.com) ). Legen Sie diesen Parameter bei der Authentifizierung in einer nationalen Cloud fest. Für die verwaltete Dienstidentität nicht erforderlich.

**--application-id**-Zeichenfolge: Anwendungs-ID der benutzerseitig zugewiesenen Identität. Erforderlich für die Dienstprinzipalautorisierung.

**--certificate-path**-Zeichenfolge: Pfad zum Zertifikat für die SPN-Authentifizierung. Erforderlich für die zertifikatbasierte Dienstprinzipalautorisierung.

**--help**: Hilfe zum Befehl `azcopy login`.

**--identity**: Melden Sie sich mit der Identität des virtuellen Computers an. Diese wird auch als verwaltete Dienstidentität (Managed Service Identity, MSI) bezeichnet.

**--identity-client-id**-Zeichenfolge: Client-ID der benutzerseitig zugewiesenen Identität.

**--identity-object-id**-Zeichenfolge: Objekt-ID der benutzerseitig zugewiesenen Identität.

**--identity-resource-id**-Zeichenfolge: Ressourcen-ID der benutzerseitig zugewiesenen Identität.

**--service-principal**: Melden Sie sich per Dienstprinzipalname (Service Principal Name, SPN) unter Verwendung eines Zertifikats oder Geheimnisses an. Der geheime Clientschlüssel oder das Zertifikatkennwort muss in der entsprechenden Umgebungsvariablen platziert werden. Geben Sie „AzCopy env“ ein, um Namen und Beschreibungen von Umgebungsvariablen anzuzeigen.

**--tenant-id**-Zeichenfolge: Die Azure Active Directory-Mandanten-ID, die für die interaktive OAuth-Geräteanmeldung verwendet werden soll.

## <a name="options-inherited-from-parent-commands"></a>Von übergeordneten Befehlen geerbte Optionen

|Option|BESCHREIBUNG|
|---|---|
|--cap-mbps float|Begrenzt die Übertragungsrate (in Megabit pro Sekunde). Der Schritt-für-Schritt-Durchsatz kann von der Obergrenze geringfügig abweichen. Wenn diese Option auf „null“ festgelegt oder weggelassen wird, ist der Durchsatz nicht begrenzt.|
|–output-type string|Format der Befehlsausgabe. Folgende Optionen sind verfügbar: „text“ und „json“. Der Standardwert lautet „text“.|
|--trusted-microsoft-suffixes-Zeichenfolge   |Gibt zusätzliche Domänensuffixe an, an die Azure Active Directory-Anmeldetoken gesendet werden können.  Der Standardwert ist ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'. Alle hier aufgelisteten Werte werden zum Standardwert hinzugefügt. Aus Sicherheitsgründen sollten Sie hier nur Microsoft Azure-Domänen platzieren. Trennen Sie mehrere E-Mail-Adressen durch Semikolons voneinander.|

## <a name="see-also"></a>Weitere Informationen

- [azcopy](storage-ref-azcopy.md)
