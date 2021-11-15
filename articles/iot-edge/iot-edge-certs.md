---
title: Zertifikate für Gerätesicherheit – Azure IoT Edge | Microsoft-Dokumentation
description: Azure IoT Edge verwendet Zertifikate, um Geräte, Module und untergeordnete Geräte zu validieren und sichere Verbindungen zwischen diesen herzustellen.
author: stevebus
ms.author: stevebus
ms.reviewer: kgremban
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: 65b8bd55763de286ceccc7acd720d521d98f09d1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473048"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Grundlegendes zur Verwendung von Zertifikaten durch Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge-Zertifikate werden von den Modulen und nachgeschalteten IoT-Geräten verwendet, um die Identität und Rechtmäßigkeit des [IoT Edge-Hub](iot-edge-runtime.md#iot-edge-hub)-Laufzeitmoduls zu überprüfen. Diese Überprüfungen ermöglichen eine sichere TLS-Verbindung (Transport Layer Security) zwischen Runtime, Modulen und IoT-Geräten. Wie IoT Hub selbst erfordert IoT Edge eine sichere und verschlüsselte Verbindung zwischen IoT-Downstreamgeräten (oder Blattgeräten) und IoT Edge-Modulen. Um eine sichere TLS-Verbindung herzustellen, bietet das IoT Edge-Hubmodul eine Serverzertifikatkette zur Verbindung von Clients, damit sie seine Identität bestätigen.

>[!NOTE]
>Dieser Artikel befasst sich mit den Zertifikaten, die zur Sicherung von Verbindungen zwischen den verschiedenen Komponenten auf einem IoT Edge-Gerät oder zwischen einem IoT Edge-Gerät und beliebigen Blattgeräten verwendet werden. Sie können auch Zertifikate verwenden, um Ihr IoT Edge-Gerät gegenüber dem IoT Hub zu authentifizieren. Diese Authentifizierungszertifikate sind anders und werden in diesem Artikel nicht behandelt. Weitere Informationen zur Authentifizierung Ihres Geräts mit Zertifikaten finden Sie unter [Erstellen und Bereitstellen eines IoT Edge-Geräts mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-x509.md).

In diesem Artikel wird erläutert, wie IoT Edge-Zertifikate in Produktions-, Entwicklungs- und Testszenarien funktionieren.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

## <a name="changes-in-version-12"></a>Änderungen in Version 1.2

* Das **Zertifikat der Gerätezertifizierungsstelle** wurde in **Zertifikat der Edgezertifizierungsstelle** umbenannt.
* Das **Zertifikat der Workloadzertifizierungsstelle** wurde als veraltet markiert. Der IoT Edge Security Manager generiert das IoT Edge-Hubserverzertifikat nun direkt aus dem Zertifikat der Edgezertifizierungsstelle, ohne dass das Zwischenzertifikat der Workloadzertifizierungsstelle zwischen diesen vorhanden sein muss.

:::moniker-end

## <a name="iot-edge-certificates"></a>IoT Edge-Zertifikate

Es gibt zwei allgemeine Szenarien für die Einrichtung von Zertifikaten auf einem IoT Edge-Gerät. Manchmal kauft der Endbenutzer oder Operator eines Geräts ein von einem Hersteller gefertigtes generisches Gerät und verwaltet dann die Zertifikate selbst. Ein anderes Mal arbeitet der Hersteller im Auftrag des Kunden an der Erstellung eines kundenspezifischen Geräts und signiert das Zertifikat vor der Übergabe des Geräts. Der Entwurf des IoT Edge-Zertifikats versucht, beide Szenarien in Betracht zu ziehen.

Die folgende Abbildung veranschaulicht die IoT Edge-Verwendung von Zertifikaten. Je nach der Anzahl der beteiligten Entitäten sind null, ein oder mehrere Zwischensignaturzertifikate zwischen dem Zertifikat der Stammzertifizierungsstelle und dem Zertifikat der Gerätezertifizierungsstelle möglich. Hier wird ein Fall gezeigt.

<!--1.1-->
:::moniker range="iotedge-2018-06"
![Diagramm typischer Zertifikatsbeziehungen](./media/iot-edge-certs/edgeCerts-general.png)

> [!NOTE]
> Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am bzw. nach dem 1. Januar 2038 ablaufen. Diese Einschränkung gilt für das Zertifikat der Gerätezertifizierungsstelle, alle Zertifikate in der Vertrauenssammlung und die Geräte-ID-Zertifikate, die für X.509-Bereitstellungsmethoden verwendet werden.

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

:::image type="content" source="./media/iot-edge-certs/iot-edge-certs-general-1-2.png" alt-text="Diagramm typischer IoT Edge-Zertifikatsbeziehungen":::

:::moniker-end

### <a name="certificate-authority"></a>Zertifizierungsstelle

Die Zertifizierungsstelle, kurz „ZS“ genannt (Certificate Authority, CA), ist eine Entität, die digitale Zertifikate ausstellt. Eine Zertifizierungsstelle fungiert als vertrauenswürdige dritte Partei zwischen Besitzer und Empfänger des Zertifikats. Ein digitales Zertifikat zertifiziert den Empfänger des Zertifikats als Besitzer eines öffentlichen Schlüssels. Die Zertifikatsvertrauenskette stellt zunächst ein Stammzertifikat aus, das in allen von der Zertifizierungsstelle ausgestellten Zertifikaten als Grundlage der Vertrauensstellung gilt. Danach kann der Besitzer mit dem Stammzertifikat zusätzliche Zwischenzertifikate („untergeordnete Zertifikate“) ausgeben.

### <a name="root-ca-certificate"></a>Zertifikat der Stammzertifizierungsstelle

Ein Zertifikat der Stammzertifizierungsstelle ist der Vertrauensanker des gesamten Prozesses. In Produktionsszenarien wird dieses ZS-Zertifikat normalerweise bei einer vertrauenswürdigen kommerziellen Zertifizierungsstelle wie Baltimore, Verisign oder DigiCert erworben. Wenn Sie vollständige Kontrolle über die Geräte haben, die mit Ihren IoT Edge-Geräten verbunden sind, kann eine Zertifizierungsstelle auf Unternehmensebene verwendet werden. In jedem Fall ist die gesamte Zertifikatkette ab dem IoT Edge Hub ununterbrochen, sodass die IoT-Blattgeräte dem Stammzertifikat vertrauen müssen. Sie können das Zertifikat der Stammzertifizierungsstelle im Speicher der vertrauenswürdigen Stammzertifizierungsstelle speichern oder die Details des Zertifikats in Ihrem Anwendungscode bereitstellen.

### <a name="intermediate-certificates"></a>Zwischenzertifikate

In einem typischen Fertigungsprozess zum Erstellen sicherer Geräte werden die Zertifikate der Stammzertifizierungsstelle selten direkt verwendet – in erster Linie wg. des Risikos von Datenlecks oder Offenlegung. Das Zertifikat der Stammzertifizierungsstelle erstellt ein oder mehrere Zertifizierungsstellen-Zwischenzertifikate und signiert diese digital. Es kann nur ein einzelnes Zwischenzertifikat oder eine Kette dieser Zwischenzertifikate vorhanden sein. Folgende Szenarien erfordern z.B. eine Kette von Zwischenzertifikaten:

* Eine Hierarchie von Abteilungen innerhalb eines Herstellers.

* Mehrere Unternehmen sind nacheinander an der Produktion eines Geräts beteiligt.

* Ein Kunde erwirbt ein Zertifikat einer Stammzertifizierungsstelle und leitet ein Signaturzertifikat für den Hersteller ab, womit der die Geräte signiert, die er im Auftrag des Kunden herstellt.

In jedem Fall verwendet der Hersteller am Ende dieser Kette ein Zertifikat der Zwischenzertifizierungsstelle, um das am Endgerät platzierte Zertifikat der Gerätezertifizierungsstelle zu signieren. Im Allgemeinen werden diese Zwischenzertifikate in der Fertigungsanlage streng geheim gehalten. Zur Verwendung durchlaufen sie strenge, sowohl physische als auch elektronische Prozesse.

<!--1.1-->
:::moniker range="iotedge-2018-06"
### <a name="device-ca-certificate"></a>Zertifikat der Gerätezertifizierungsstelle

Das Zertifikat der Gerätezertifizierungsstelle wird aus dem letzten Zertifikat der Zwischenzertifizierungsstelle im Prozess generiert und davon signiert. Dieses Zertifikat wird auf dem IoT Edge-Gerät selbst installiert, vorzugsweise an einem sicheren Speicherort wie einem Hardwaresicherheitsmodul (HSM). Darüber hinaus identifiziert ein Zertifikat der Gerätezertifizierungsstelle eindeutig ein IoT Edge-Gerät. Das Zertifikat der Gerätezertifizierungsstelle kann andere Zertifikate signieren.

### <a name="iot-edge-workload-ca"></a>IoT Edge-Workloadzertifizierungsstelle

Der [IoT Edge-Sicherheits-Manager](iot-edge-security-manager.md) erstellt das Zertifikat der Workloadzertifizierungsstelle, das erste auf der „Operator“-Seite des Prozesses, beim ersten Start von IoT Edge. Dieses Zertifikat wird aus dem Zertifikat der Gerätezertifizierungsstelle generiert und davon signiert. Mit diesem Zertifikat, das nur ein anderes Zwischensignaturzertifikat ist, werden alle anderen von der IoT Edge-Runtime verwendeten Zertifikate generiert und signiert. Zurzeit ist dies in erster Linie das im folgenden Abschnitt erläuterte IoT Edge-Hubserverzertifikat, aber in Zukunft könnten auch andere Zertifikate für die Authentifizierung von IoT Edge-Komponenten infrage kommen.

Dieses „Workload“-Zwischenzertifikat dient zur Abgrenzung zwischen den Interessen des Herstellers des Geräts und des Operators des Geräts. Stellen Sie sich ein Szenario vor, in dem ein IoT Edge-Gerät verkauft oder von einem Kunden an einen anderen übertragen wird. Sie wünschen wahrscheinlich, dass das vom Hersteller bereitgestellte Zertifikat der Gerätezertifizierungsstelle unveränderlich ist. Die für den Betrieb des Geräts spezifischen „Workload“-Zertifikate sollten jedoch gelöscht und für die neue Bereitstellung neu erstellt werden.

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
### <a name="edge-ca-certificate"></a>Zertifikat der Edgezertifizierungsstelle

Das Zertifikat der Edgezertifizierungsstelle wird aus dem letzten Zertifikat der Zwischenzertifizierungsstelle im Prozess generiert und damit signiert. Dieses Zertifikat wird auf dem IoT Edge-Gerät selbst installiert, vorzugsweise an einem sicheren Speicherort wie einem Hardwaresicherheitsmodul (HSM). Darüber hinaus identifiziert ein Zertifikat der Edgezertifizierungsstelle ein IoT Edge-Gerät eindeutig. Das Zertifikat der Edgezertifizierungsstelle kann andere Zertifikate signieren.

:::moniker-end

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge-Hubserverzertifikat

Das IoT Edge-Hubserverzertifikat ist das eigentliche Zertifikat, das Blattknotengeräten und Modulen während des Herstellens der von IoT Edge geforderten TLS-Verbindung zur Identitätsüberprüfung präsentiert wird. Dieses Zertifikat stellt die vollständige Kette der zu seinem Generieren verwendeten Signaturzertifikate dar – bis zum Zertifikat der Stammzertifizierungsstelle, dem das IoT-Blattgerät vertrauen muss. Beim Generieren durch den IoT Edge wird als allgemeiner Name (Common Name, CN) dieses IoT Edge-Hubzertifikats die Eigenschaft „hostname“ in der Konfigurationsdatei nach der Konvertierung in Kleinbuchstaben festgelegt.

>[!Tip]
>Da das IoT Edge-Hubserverzertifikat die hostname-Eigenschaft des Geräts als allgemeinen Namen verwendet, sollten keine anderen Zertifikate in der Kette denselben allgemeinen Namen verwenden.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Der [IoT Edge Security Manager](iot-edge-security-manager.md) generiert das Zertifikat des IoT Edge-Hubs, das erste auf der Operatorseite des Prozesses, beim ersten Start von IoT Edge. Dieses Zertifikat wird aus dem Zertifikat der Edgezertifizierungsstelle generiert und damit signiert.
:::moniker-end

## <a name="production-implications"></a>Auswirkungen auf die Produktion

Da die Prozesse für Fertigung und Betrieb getrennt sind, berücksichtigen Sie beim Vorbereiten von Produktionsgeräten die folgenden Auswirkungen:

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Mit einem beliebigen zertifikatbasierten Prozess sollten das Zertifikat der Stammzertifizierungsstelle und alle Zertifikate der Zwischenzertifizierungsstelle während des gesamten Prozesses des Veröffentlichens auf einem IoT Edge-Gerät geschützt und überwacht werden. Der Hersteller des IoT Edge-Geräts sollten über sichere Prozesse für richtige Speicherung und Nutzung ihrer Zwischenzertifikate verfügen. Darüber hinaus sollte das Zertifikat der Gerätezertifizierungsstelle in so sicherem Speicher wie auf dem Gerät selbst möglich gespeichert werden, vorzugsweise in einem Hardwaresicherheitsmodul.

* Das IoT Edge-Hubserverzertifikat wird den Clientgeräten und Modulen, die eine Verbindung herstellen, durch IoT Edge-Hub präsentiert. Der allgemeine Name (Common Name, CN) des Zertifikats der Gerätezertifizierungsstelle **darf nicht** identisch mit dem Wert für „hostname“ sein, der in der Konfigurationsdatei auf dem IoT Edge-Gerät verwendet wird. Der Name, der von Clients zum Herstellen der Verbindung mit IoT Edge (z. B. über den GatewayHostName-Parameter der Verbindungszeichenfolge oder den Befehl CONNECT in MQTT) verwendet wird, **darf nicht** mit dem im Zertifikat der Gerätezertifizierungsstelle verwendeten allgemeinen Namen identisch sein. Diese Einschränkung ist darauf zurückzuführen, dass IoT Edge Hub die gesamte Vertrauenskette für die Überprüfung durch Clients präsentiert. Wenn das IoT Edge-Hubserverzertifikat und das Zertifikat der Gerätezertifizierungsstelle über denselben CN verfügen, geraten Sie in eine Überprüfungsschleife, und das Zertifikat wird ungültig.

* Da das Zertifikat der Gerätezertifizierungsstelle vom IoT Edge-Sicherheits-Daemon zum Generieren der endgültigen IoT Edge-Zertifikate verwendet wird, muss es selbst ein Signaturzertifikat sein, also Funktionen für die Zertifikatsignatur besitzen. Das Anwenden von „V3 Basic constraints CA:True“ auf das Zertifikat der Gerätezertifizierungsstelle richtet automatisch die erforderlichen Schlüsselverwendungseigenschaften ein.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Mit einem beliebigen zertifikatbasierten Prozess sollten das Zertifikat der Stammzertifizierungsstelle und alle Zertifikate der Zwischenzertifizierungsstelle während des gesamten Prozesses des Veröffentlichens auf einem IoT Edge-Gerät geschützt und überwacht werden. Der Hersteller des IoT Edge-Geräts sollten über sichere Prozesse für richtige Speicherung und Nutzung ihrer Zwischenzertifikate verfügen. Darüber hinaus sollte das Zertifikat der Edgezertifizierungsstelle in so sicherem Speicher wie auf dem Gerät selbst möglich gespeichert werden, vorzugsweise in einem Hardwaresicherheitsmodul.

* Das IoT Edge-Hubserverzertifikat wird den Clientgeräten und Modulen, die eine Verbindung herstellen, durch IoT Edge-Hub präsentiert. Der allgemeine Name (Common Name, CN) des Zertifikats der Edgezertifizierungsstelle **darf nicht** identisch mit dem Wert von „hostname“ sein, der in der Konfigurationsdatei auf dem IoT Edge-Gerät verwendet wird. Der Name, der von Clients zum Herstellen der Verbindung mit IoT Edge (z. B. über den GatewayHostName-Parameter der Verbindungszeichenfolge oder den Befehl CONNECT in MQTT) verwendet wird, **darf nicht** mit dem im Zertifikat der Edgezertifizierungsstelle verwendeten allgemeinen Namen identisch sein. Diese Einschränkung ist darauf zurückzuführen, dass IoT Edge Hub die gesamte Vertrauenskette für die Überprüfung durch Clients präsentiert. Wenn das IoT Edge-Hubserverzertifikat und das Zertifikat der Edgezertifizierungsstelle über denselben CN verfügen, geraten Sie in eine Überprüfungsschleife, und das Zertifikat wird ungültig.

* Da das Zertifikat der Edgezertifizierungsstelle vom IoT Edge-Sicherheitsdaemon zum Generieren der endgültigen IoT Edge-Zertifikate verwendet wird, muss es selbst ein Signaturzertifikat sein, also Funktionen für die Zertifikatsignatur besitzen. Das Anwenden von „V3 Basic constraints CA:True“ auf das Zertifikat der Edgezertifizierungsstelle richtet automatisch die erforderlichen Schlüsselverwendungseigenschaften ein.
:::moniker-end

## <a name="devtest-implications"></a>Auswirkungen auf Entwicklung/Test

Um Entwicklungs- und Testszenarien zu vereinfachen, bietet Microsoft eine Reihe von [Komfortskripts](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) zum Generieren von nicht zur Produktion bestimmten, für IoT Edge im transparenten Gatewayszenario geeignete Zertifikate an. Beispiele zur Funktionsweise der Skripts finden Sie unter [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md).

>[!Tip]
> Um Verbindungen Ihrer Geräte-IoT-„Blattgeräte“ und Anwendungen herzustellen, die unser IoT-Geräte-SDK über IoT Edge verwenden, müssen Sie den optionalen GatewayHostName-Parameter dem Ende der Verbindungszeichenfolge des Geräts hinzufügen. Wenn das IoT Edge-Hubserverzertifikat generiert wird, basiert es auf einer Kleinbuchstabenversion des Hostnamens aus der Konfigurationsdatei. Damit die Namen übereinstimmen und die Überprüfung des TLS-Zertifikats erfolgreich durchgeführt werden kann, sollten Sie deshalb den Parameter „GatewayHostName“ in Kleinbuchstaben eingeben.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Beispiel für IoT Edge-Zertifikathierarchie

Ein Beispiel dieses Zertifikatspfads veranschaulicht der folgende Screenshot eines ausgeführten IoT Edge-Geräts, das als transparentes Gateway eingerichtet ist. OpenSSL wird verwendet, um eine Verbindung mit IoT Edge Hub herzustellen und die Zertifikate zu überprüfen und zu sichern.

<!--1.1-->
:::moniker range="iotedge-2018-06"
![Screenshot der Zertifikatshierarchie auf jeder Ebene](./media/iot-edge-certs/iotedge-cert-chain.png)

Sie sehen die Hierarchie der Zertifikatstiefe im Screenshot dargestellt:

| Zertifikattyp | Zertifikatsname|
|--|--|
| Zertifikat der Stammzertifizierungsstelle | Zertifikat der Azure IoT Hub-Zertifizierungsstelle – nur Test |
| Zertifikat der Zwischenzertifizierungsstelle | Zertifikat der Azure IoT Hub-Zwischenzertifizierungsstelle – nur Test |
| Zertifikat der Gerätezertifizierungsstelle | iotgateway.ca („iotgateway“ wurde als Zertifikatname der Zertifizierungsstelle an die Komfortskripts übergeben) |
| Zertifikat der Workloadzertifizierungsstelle | IoT Edge-Workloadzertifizierungsstelle |
| IoT Edge-Hubserverzertifikat | iotedgegw.local (entspricht dem Wert für „hostname“ aus der Konfigurationsdatei) |
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

![Screenshot der Zertifikatshierarchie auf jeder Ebene](./media/iot-edge-certs/iot-edge-cert-chain-1-2.png)

Sie sehen die Hierarchie der Zertifikatstiefe im Screenshot dargestellt:

| Zertifikattyp | Zertifikatsname |
|--|--|
| Zertifikat der Stammzertifizierungsstelle | Zertifikat der Azure IoT Hub-Zertifizierungsstelle – nur Test |
| Zertifikat der Zwischenzertifizierungsstelle | Zertifikat der Azure IoT Hub-Zwischenzertifizierungsstelle – nur Test |
| Zertifikat der Gerätezertifizierungsstelle | iotgateway.ca („iotgateway“ wurde als Zertifikatname der Zertifizierungsstelle an die Komfortskripts übergeben) |
| IoT Edge-Hubserverzertifikat | iotedgegw.local (entspricht dem Wert für „hostname“ aus der Konfigurationsdatei) |
:::moniker-end

## <a name="next-steps"></a>Nächste Schritte

[Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md)

[Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md)
