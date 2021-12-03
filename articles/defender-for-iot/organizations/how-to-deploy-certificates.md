---
title: Bereitstellen von Zertifikaten
description: Es wird beschrieben, wie Sie Zertifikate für Defender für IoT einrichten und bereitstellen.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: df2b0484d9f9302443dc37870a6a97de31ded3b1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343703"
---
# <a name="about-certificates"></a>Informationen zu Zertifikaten

Dieser Abschnitt enthält Informationen, die Sie beim Erstellen und Bereitstellen von Zertifikaten für Microsoft Defender für IoT benötigen. Die Erstellung und Bereitstellung von Zertifikaten sollte von einer Person durchgeführt werden, die für Sicherheits-, PKI- oder andere Zertifikate qualifiziert ist.

Von Defender für IoT werden SSL/TLS-Zertifikate genutzt, um die Kommunikation zwischen den folgenden Systemkomponenten zu schützen: 

- Zwischen Benutzern und der Webkonsole der Appliance 
- Zwischen den Sensoren und einer lokalen Verwaltungskonsole 
- Zwischen einer Verwaltungskonsole und einer Hochverfügbarkeits-Verwaltungskonsole
- Zur REST-API auf dem Sensor und zur lokalen Verwaltungskonsole 

Administratorbenutzer von Defender für IoT können ein Zertifikat in Sensorkonsolen und in die zugehörige lokale Verwaltungskonsole hochladen, indem sie das Dialogfeld „SSL/TLS-Zertifikate“ verwenden.

:::image type="content" source="media/how-to-deploy-certificates/certificate-upload.png" alt-text="Dialogfeld für Zertifikatupload":::

## <a name="about-certificate-generation-methods"></a>Informationen zu Methoden für die Zertifikatgenerierung

Alle Methoden für die Zertifikatgenerierung mit den folgenden Vorgehensweisen werden unterstützt:  

- Private und Enterprise Key-Infrastrukturen (Private PKI) 
- Public Key-Infrastrukturen (Public PKI) 
- Lokal auf der Appliance generierte Zertifikate (lokal selbstsigniert) 

> [!Important]
> Wir raten Ihnen davon ab, lokal selbstsignierte Zertifikate zu verwenden. Diese Art der Verbindung ist nicht sicher und darf nur für Testumgebungen verwendet werden. Da der Besitzer des Zertifikats nicht überprüft und die Sicherheit Ihres Systems nicht aufrechterhalten werden kann, sollten selbstsignierte Zertifikate niemals für Produktionsnetzwerke verwendet werden.

## <a name="about-certificate-validation"></a>Informationen zur Zertifikatüberprüfung

Zusätzlich zum Schützen der Kommunikation zwischen Systemkomponenten können Benutzer auch die Zertifikatüberprüfung durchführen.  

Die Auswertung der Überprüfung erfolgt basierend auf den folgenden Elementen:

- Zertifikatssperrliste (Certificate Revocation List, CRL)
- Ablaufdatum des Zertifikats  
- Vertrauenskette des Zertifikats

Die Überprüfung wird zweimal durchgeführt:

1. Beim Hochladen des Zertifikats auf Sensoren und lokale Verwaltungskonsolen. Wenn die Überprüfung fehlschlägt, kann das Zertifikat nicht hochgeladen werden.
1. Beim Initiieren der verschlüsselten Kommunikation zwischen:

    - Defender für IoT-Systemkomponenten, z. B. einem Sensor und einer lokalen Verwaltungskonsole.

    - Defender für IoT und bestimmten Drittanbieterservern, die in Weiterleitungsregeln definiert sind.  Lesen Sie hierzu den Abschnitt [Informationen zu weitergeleiteten Warnungsinformationen](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information).  

Wenn bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen den relevanten Komponenten beendet, und in der Konsole wird ein Überprüfungsfehler angezeigt.

## <a name="about-certificate-upload-to-defender-for-iot"></a>Informationen zum Hochladen von Zertifikaten in Defender für IoT

Nach der Installation des Sensors und der lokalen Verwaltungskonsole wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf den Sensor und die lokale Verwaltungskonsolen-Webanwendung verwendet.

Bei der erstmaligen Anmeldung beim Sensor und der lokalen Verwaltungskonsole werden Administratorbenutzer aufgefordert, ein SSL/TLS-Zertifikat hochzuladen. Die Verwendung von SSL/TLS-Zertifikaten wird dringend empfohlen.

Wenn das Zertifikat von der zuständigen Person nicht richtig erstellt wird oder dafür Verbindungsprobleme bestehen, kann das Zertifikat nicht hochgeladen werden. Die Benutzer sind dann gezwungen, ein lokal signiertes Zertifikat zu nutzen.  

Die Option zum Überprüfen des hochgeladenen Zertifikats und der Drittanbieterzertifikate ist automatisch aktiviert, aber sie kann auch deaktiviert werden. Falls diese Option deaktiviert ist, wird die verschlüsselte Kommunikation zwischen Komponenten auch dann fortgesetzt, wenn ein Zertifikat ungültig ist.

## <a name="certificate-deployment-tasks"></a>Aufgaben für die Zertifikatbereitstellung

In diesem Abschnitt werden die erforderlichen Schritte beschrieben, mit denen Sie sicherstellen können, dass die Zertifikatbereitstellung reibungslos durchgeführt wird.

**Überprüfen Sie für die Bereitstellung von Zertifikaten Folgendes:**

- Ein Spezialist für Sicherheits-, PKI- oder andere Zertifikate führt die Zertifikaterstellung selbst durch oder überwacht sie. 
- Sie erstellen ein eindeutiges Zertifikat für jeden Sensor, jede Verwaltungskonsole und jeden Hochverfügbarkeitscomputer.
- Sie erfüllen die Anforderungen an die Zertifikaterstellung. Weitere Informationen finden Sie unter  [Anforderungen an die Zertifikaterstellung](#certificate-creation-requirements).
- Für Administratorbenutzer, die sich bei den einzelnen Defender für IoT-Sensoren anmelden, und die lokale Verwaltungskonsole und den Hochverfügbarkeitscomputer besteht Zugriff auf das Zertifikat.

## <a name="certificate-creation-requirements"></a>Anforderungen an die Zertifikaterstellung

In diesem Abschnitt werden die Anforderungen an die Zertifikaterstellung beschrieben, z. B.:

- [Anforderungen an den Portzugriff für die Zertifikatüberprüfung](#port-access-requirements-for-certificate-validation)

- [Anforderungen an den Dateityp](#file-type-requirements)

- [Anforderungen an die Schlüsseldatei](#key-file-requirements)

- [Anforderungen an die Zertifikatkettendatei (bei Verwendung von PEM)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="port-access-requirements-for-certificate-validation"></a>Anforderungen an den Portzugriff für die Zertifikatüberprüfung

Bei Nutzung der Zertifikatüberprüfung sollten Sie sicherstellen, dass der Zugriff auf Port 80 verfügbar ist.

Die Zertifikatüberprüfung wird anhand einer Zertifikatssperrliste und des Ablaufdatums des Zertifikats ausgewertet. Dies bedeutet, dass es für die Appliance möglich sein sollte, eine Verbindung mit dem vom Zertifikat definierten Zertifikatssperrlisten-Server herzustellen. Standardmäßig verweist das Zertifikat auf die URL der Zertifikatssperrliste (CRL-URL) an HTTP-Port 80. 

Einige Sicherheitsrichtlinien der Organisation blockieren möglicherweise den Zugriff auf diesen Port. Wenn Ihre Organisation auf Port 80 nicht zugreifen kann, haben Sie folgende Möglichkeiten: 

1. Definieren Sie eine andere URL und einen bestimmten Port im Zertifikat.

    - Die URL sollte als „http://“ und nicht als „https://“ definiert werden.

    - Überprüfen Sie, ob der CRL-Zielserver an dem von Ihnen definierten Port lauschen kann. 

1. Verwenden Sie einen Proxyserver, der auf die Zertifikatssperrliste an Port 80 zugreift.

### <a name="file-type-requirements"></a>Anforderungen an den Dateityp

Für Defender für IoT ist es erforderlich, dass jedes von der Zertifizierungsstelle signierte Zertifikat eine KEY- und eine CRT-Datei enthält. Diese Dateien werden nach der Anmeldung auf den Sensor und in die lokale Verwaltungskonsole hochgeladen. Einige Organisationen benötigen hierfür unter Umständen eine PEM-Datei. Für Defender für IoT ist dieser Dateityp nicht erforderlich.

**CRT: Zertifikatcontainerdatei**

Eine Datei im PEM- oder DER-Format mit einer anderen Erweiterung. Die Datei wird in Windows-Explorer als Zertifikat erkannt. Die PEM-Datei wird von Windows-Explorer nicht erkannt.

**KEY: Datei mit dem privaten Schlüssel**

Eine KEY-Datei hat das gleiche Format wie eine PEM-Datei, aber eine andere Erweiterung.

**.pem: Zertifikatcontainerdatei (optional)**

Eine PEM-Datei ist eine Textdatei, die die Base64-Codierung des Zertifikattexts, eine Nur-Text-Kopfzeile und eine Fußzeile zur Kennzeichnung des Zertifikatanfangs und -endes enthält.

Unter Umständen müssen Sie vorhandene Dateitypen in unterstützte Typen konvertieren. Ausführlichere Informationen finden Sie unter [Konvertieren vorhandener Dateien in unterstützte Dateien](#convert-existing-files-to-supported-files).

### <a name="certificate-file-parameter-requirements"></a>Anforderungen an Zertifikatsdateiparameter

Vergewissern Sie sich, dass Sie die folgenden Parameteranforderungen erfüllt haben, bevor Sie ein Zertifikat erstellen:

- [Anforderungen an die CRT-Datei](#crt-file-requirements)
- [Anforderungen an die Schlüsseldatei](#key-file-requirements)
- [Anforderungen an die Zertifikatkettendatei (bei Verwendung von PEM)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="crt-file-requirements"></a>Anforderungen an die CRT-Datei

In diesem Abschnitt werden die Anforderungen an CRT-Felder beschrieben.

- Signaturalgorithmus = SHA256RSA 
- Signaturhashalgorithmus = SHA256 
- Gültig von = gültiges Datum in der Vergangenheit 
- Gültig bis = gültiges Datum in der Zukunft 
- Öffentlicher Schlüssel = RSA 2.048 Bits (mindestens) oder 4.096 Bits 
- CRL-Verteilungspunkt = URL zu CRL-Datei 
- Allgemeiner Name des Antragstellers = Domänenname der Appliance, z. B. „Sensor.contoso.com“ oder „*.contoso.com“.  
- Land des Antragsstellers = definiert, z. B. USA 
- Organisationseinheit des Antragsstellers = definiert, z. B. Contoso Labs 
- Organisationseinheit des Antragsstellers = definiert, z. B. Contoso Inc. 

Zertifikate mit anderen Parametern funktionieren möglicherweise, werden aber von Microsoft nicht unterstützt.  

### <a name="key-file-requirements"></a>Anforderungen an die Schlüsseldatei

Verwenden Sie RSA mit 2.048 oder 4.096 Bit.

Bei Verwendung einer Schlüssellänge von 4.096 Bit dauert der SSL-Handshake zu Beginn jeder Verbindung länger. Darüber hinaus erhöht sich die CPU-Auslastung bei Handshakes.

### <a name="certificate-chain-file-requirements-if-pem-is-used"></a>Anforderungen an die Zertifikatkettendatei (bei Verwendung von PEM)

Eine PEM-Datei, die die Zertifikate aller Zertifizierungsstellen der Vertrauenskette enthält, die zu Ihrem Zertifikat geführt hat.  

In der Zertifikatkettendatei werden Bag-Attribute unterstützt.

## <a name="create-certificates"></a>Erstellen von Zertifikaten

Verwenden Sie eine Zertifikatverwaltungsplattform, um ein Zertifikat zu erstellen, z. B. eine automatisierte PKI-Verwaltungsplattform. Überprüfen Sie, ob die Zertifikate die Anforderungen an Zertifikatsdateien erfüllen. Informationen zum Testen der von Ihnen erstellten Dateien finden Sie im Abschnitt zum Testen von Zertifikaten.

Falls Sie keine Zertifikatüberprüfung durchführen, sollten Sie den Verweis auf die Zertifikatssperrlisten-URL im Zertifikat entfernen. Informationen zu diesem Parameter finden Sie unter [Anforderungen an die CRT-Datei](#crt-file-requirements).

Wenden Sie sich an eine zuständige qualifizierte Person für Sicherheits-, PKI- oder andere Zertifikate, falls Sie nicht über eine Anwendung verfügen, mit der automatisch Zertifikate erstellt werden können.

Sie können [die von Ihnen erstellten Zertifikate testen](#test-certificates-you-create).  

Darüber hinaus können Sie auch vorhandene Zertifikatsdateien konvertieren, falls Sie keine neuen erstellen möchten. Ausführlichere Informationen finden Sie unter [Konvertieren vorhandener Dateien in unterstützte Dateien](#convert-existing-files-to-supported-files).

### <a name="sample-certificate"></a>Beispielzertifikat

Sie können Ihr Zertifikat mit dem unten angegebenen Beispielzertifikat vergleichen. Vergewissern Sie sich, dass die gleichen Felder vorhanden sind und deren Reihenfolge identisch ist.

:::image type="content" source="media/how-to-deploy-certificates/sample-certificate.png" alt-text="Beispielzertifikat":::

## <a name="test-certificates-you-create"></a>Testen der von Ihnen erstellten Zertifikate

Sie können Zertifikate testen, bevor Sie sie auf Ihren Sensoren bzw. in Ihren lokalen Verwaltungskonsolen bereitstellen. Verwenden Sie die folgenden Befehle, wenn Sie die Informationen in der CSR-Zertifikatsdatei oder der Datei mit dem privaten Schlüssel überprüfen möchten:

| **Test** | **CLI-Befehl** |
|--|--|
| Überprüfen einer Zertifikatsignieranforderung (CSR) | openssl req -text -noout -verify -in CSR.csr |
| Überprüfen eines privaten Schlüssels  | openssl rsa -in privateKey.key -check  |
| Überprüfen eines Zertifikats  | openssl x509 -in certificate.crt -text -noout |

Falls diese Tests nicht erfolgreich sind, können Sie anhand der Informationen unter [Anforderungen an Zertifikatsdateiparameter](#certificate-file-parameter-requirements) überprüfen, ob die Dateiparameter korrekt sind, oder sich an Ihre zuständige Person für Zertifikate wenden.

## <a name="convert-existing-files-to-supported-files"></a>Konvertieren vorhandener Dateien in unterstützte Dateien 

In diesem Abschnitt wird beschrieben, wie Sie vorhandene Zertifikatsdateien in unterstützte Formate konvertieren.

|**Beschreibung** | **CLI-Befehl** |
|--|--|
| Konvertieren einer CRT-Datei in eine PEM-Datei   | `openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt`  | 
| Konvertieren einer PEM-Datei in eine CRT-Datei   | `openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt` |  
| Konvertieren einer PKCS#12-Datei (.pfx, .p12) mit einem privaten Schlüssel und Zertifikaten in PEM   | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes`. Sie können „-nocerts“ hinzufügen, um nur den privaten Schlüssel auszugeben, oder „-nokeys“, um nur die Zertifikate auszugeben.  |  

## <a name="troubleshooting"></a>Problembehandlung  

In diesem Abschnitt werden verschiedene Probleme, die bei einem Upload oder der Überprüfung von Zertifikaten ggf. auftreten können, sowie die Schritte zur Behebung der Probleme beschrieben.

### <a name="troubleshoot-ca-certificate-upload"></a>Problembehandlung für den Upload von Zertifizierungsstellenzertifikaten  

Administratorbenutzer, die sich zum ersten Mal beim Sensor oder bei der lokalen Verwaltungskonsole anmelden möchten, können das von der Zertifizierungsstelle signierte Zertifikat nicht hochladen, wenn es nicht richtig erstellt wurde oder ungültig ist. Falls beim Hochladen des Zertifikats ein Fehler auftritt, wird mindestens eine Meldung zu den folgenden Fehlern angezeigt:

| **Fehler bei Zertifikatüberprüfung** | **Empfehlung** |
|--|--|
| Passphrase stimmt nicht mit dem Schlüssel überein. | Überprüfen Sie, ob Sie die richtige Passphrase eingegeben haben. Falls das Problem weiterhin besteht, sollten Sie versuchen, das Zertifikat mit der richtigen Passphrase neu zu erstellen. |
| Die Vertrauenskette kann nicht überprüft werden. Das bereitgestellte Zertifikat und die Stammzertifizierungsstelle stimmen nicht überein.  | Vergewissern Sie sich, dass die PEM-Datei zur CRT-Datei passt. Falls das Problem weiterhin besteht, sollten Sie versuchen, das Zertifikat mit der richtigen Vertrauenskette (per PEM-Datei definiert) neu zu erstellen. |
| Dieses SSL-Zertifikat ist abgelaufen und wird nicht als gültig angesehen.  | Erstellen Sie ein neues Zertifikat mit gültigen Datumsangaben.|
| Dieses SSL-Zertifikat ist abgelaufen und wird nicht als gültig angesehen.  | Erstellen Sie ein neues Zertifikat mit gültigen Datumsangaben.|
|Dieses Zertifikat wurde von der Zertifikatssperrliste widerrufen und kann nicht als vertrauenswürdig für eine sichere Verbindung eingestuft werden. | Erstellen Sie ein neues Zertifikat, das nicht widerrufen wird. |
|Der Speicherort der Zertifikatssperrliste ist nicht erreichbar. Überprüfen Sie, ob von dieser Appliance aus auf die URL zugegriffen werden kann. | Vergewissern Sie sich, dass in Ihrer Netzwerkkonfiguration für die Appliance das Erreichen des Zertifikatssperrlisten-Servers, der im Zertifikat definiert ist, zulässig ist. Sie können einen Proxyserver verwenden, falls für die Herstellung einer direkten Verbindung Einschränkungen bestehen.  
|Fehler bei der Zertifikatüberprüfung.  | Dies ist ein Hinweis auf einen allgemeinen Fehler in der Appliance. Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).|

### <a name="troubleshoot-file-conversions"></a>Problembehandlung bei Dateikonvertierungen  

Bei Ihrer Dateikonvertierung wird unter Umständen kein gültiges Zertifikat erstellt. Es kann beispielsweise sein, dass die Dateistruktur Fehler aufweist.

Gehen Sie wie folgt vor, falls die Konvertierung nicht erfolgreich ist:  

- Verwenden Sie die Konvertierungsbefehle, die unter [Konvertieren vorhandener Dateien in unterstützte Dateien](#convert-existing-files-to-supported-files) beschrieben sind.
- Stellen Sie sicher, dass die Dateiparameter korrekt sind. Ausführlichere Informationen finden Sie unter [Anforderungen an den Dateityp](#file-type-requirements) und [Anforderungen an Zertifikatsdateiparameter](#certificate-file-parameter-requirements).  
- Wenden Sie sich an die Person, die bei Ihnen für Zertifikate zuständig ist.
