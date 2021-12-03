---
title: Verwalten der Geräteregistrierungen für den Azure IoT Hub Device Provisioning-Dienst im Azure-Portal
description: Verwalten von Geräteregistrierungen für Ihren Gerätebereitstellungsdienst (Device Provisioning Service, DPS) im Azure-Portal
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/25/2021
ms.topic: how-to
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: 7f0fdcf8a3e51405861082b3c2ad8a6385f5a6e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843365"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Verwalten von Geräteregistrierungen mit dem Azure-Portal

Eine *Geräteregistrierung* erstellt einen Datensatz eines einzelnen Geräts oder einer Gruppe von Geräten, die sich zu einem beliebigen Zeitpunkt beim Azure IoT Hub Device Provisioning-Dienst (Device Provisioning Service, DPS) registrieren können. Der Registrierungsdatensatz enthält die Erstkonfiguration für das Gerät/die Geräte im Rahmen dieser Registrierung. In der Konfiguration enthalten ist entweder der IoT-Hub, dem ein Gerät zugewiesen wird, oder eine Zuweisungsrichtlinie, die den Hub aus einer Gruppe von Hubs konfiguriert. Dieser Artikel zeigt Ihnen die Verwaltung von Geräteregistrierungen für Ihren Bereitstellungsdienst.

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

* [Registrierungsgruppen:](concepts-service.md#enrollment-group) Für die Registrierung mehrerer verbundener Geräte
* [Individuelle Registrierung:](concepts-service.md#individual-enrollment) Für die Registrierung eines einzelnen Geräts

## <a name="create-an-enrollment-group"></a>Erstellen einer Registrierungsgruppe

 Eine Registrierungsgruppe ist ein Eintrag für eine Gruppe von Geräten, die einen allgemeinen Nachweismechanismus gemeinsam nutzen. Wir empfehlen, dass Sie eine Registrierungsgruppe für eine große Anzahl von Geräten, die eine Erstkonfiguration gemeinsam nutzen, oder für Geräte zu verwenden, die demselben Mandanten zugeordnet sind. Geräte, die entweder einen [symmetrischen Schlüssel](concepts-symmetric-key-attestation.md) oder einen Nachweis für [X.509-Zertifikate](concepts-x509-attestation.md) verwenden, werden unterstützt.

### <a name="create-a-symmetric-key-enrollment-group"></a>Erstellen einer Registrierungsgruppe mit symmetrischem Schlüssel

Informationen zum Erstellen und Verwenden von Registrierungsgruppen mit symmetrischen Schlüsseln finden Sie im Tutorial [Bereitstellen von Geräten mit symmetrischen Schlüsseln](how-to-legacy-device-symm-key.md).

So erstellen Sie eine Registrierungsgruppe mit symmetrischem Schlüssel:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite **+ Registrierungsgruppe hinzufügen** aus.

6. Geben Sie auf der Seite **Registrierungsgruppe hinzufügen** die folgenden Informationen ein:

   | Feld | Beschreibung |
    | :--- | :--- |
    | **Gruppenname** | Der Name der Gerätegruppe.|
    | **Nachweistyp** |Wählen Sie **Symmetrischer Schlüssel**.|
    | **Schlüssel automatisch generieren** |Aktivieren Sie dieses Kontrollkästchen.|
    | **Wählen Sie aus, wie Geräte den Hubs zugewiesen werden sollen** |Wählen Sie *Statische Konfiguration* aus, damit Sie sie einem bestimmten Hub zuweisen können.|
    | **Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann** |Wählen Sie einen Ihrer Hubs aus.|

    Übernehmen Sie bei den verbleibenden Feldern die Standardwerte.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-symm-key.png" alt-text="Fügen Sie eine Registrierungsgruppe für den Nachweis des symmetrischen Schlüssels hinzu.":::

7. Wählen Sie **Speichern** aus.

### <a name="create-a-x509-certificate-enrollment-group"></a>Erstellen einer X.509-Zertifikatregistrierungsgruppe

So erstellen Sie eine X.509-Zertifikatregistrierungsgruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite **+ Registrierungsgruppe hinzufügen** aus.

6. Geben Sie auf der Seite **Registrierungsgruppe hinzufügen** die folgenden Informationen ein:

    | Feld | Beschreibung |
    | :--- | :--- |
    | **Gruppenname** | Der Name der Gerätegruppe.|
    | **Nachweistyp** |Wählen Sie **Certificate** aus.|
    | **Zertifikattyp** | Wählen Sie **Zertifizierungsstellenzertifikat** oder **Zwischenzertifikat** aus – abhängig davon, mit welchem Zertifikat Ihre Gerätezertifikate signiert wurden.|
    | **Primäres Zertifikat**| Wenn Sie Ihre Gerätezertifikate mit einem Zertifizierungsstellenzertifikat signieren, muss für das Zertifikat der Stammzertifizierungsstelle der [Eigentumsnachweis](how-to-verify-certificates.md) abgeschlossen sein. Wenn Sie Ihre Gerätezertifikate mit einem Zwischenzertifikat signieren, steht Ihnen eine Schaltfläche zum Hochladen zur Verfügung, über die Sie dieses Zertifikat hochladen können. Für das Zertifikat, mit dem das Zwischenzertifikat signiert wurde, muss der [Eigentumsnachweis](how-to-verify-certificates.md) ebenfalls abgeschlossen sein.

    Übernehmen Sie bei den verbleibenden Feldern die Standardwerte.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-cert.png" alt-text="Fügen Sie die Registrierungsgruppe für den Nachweis des Zertifizierungsstellenzertifikats hinzu.":::

7. Wählen Sie **Speichern** aus.

## <a name="create-an-individual-enrollment"></a>Erstellen einer individuellen Registrierung

Eine individuelle Registrierung ist ein Eintrag für ein einzelnes Gerät, das einem IoT-Hub zugewiesen werden kann. Geräte, die den [symmetrischen Schlüssel](concepts-symmetric-key-attestation.md), [X.509-Zertifikate](concepts-x509-attestation.md) und den [TPM-Nachweis](concepts-tpm-attestation.md) verwenden, werden unterstützt.

### <a name="create-a-symmetric-key-individual-enrollment"></a>Erstellen einer individuellen Registrierung mit symmetrischem Schlüssel

>[!TIP]
>Ausführlichere Anleitungen zum Erstellen und Verwenden individueller Registrierungen mit symmetrischen Schlüsseln finden Sie im [Schnellstart: Bereitstellen eines simulierten Geräts mit symmetrischem Schlüssel](quick-create-simulated-device-symm-key.md#create-a-device-enrollment).

So erstellen Sie eine individuelle Registrierung mit symmetrischem Schlüssel:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite die Option **+ Individuelle Registrierung hinzufügen** aus.

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    | Feld | Beschreibung |
    | :--- | :--- |
    | **Mechanismus** | Wählen Sie *Symmetrischer Schlüssel* aus. |
    | **Schlüssel automatisch generieren** |Aktivieren Sie dieses Kontrollkästchen. |
    | **Registrierungs-ID** | Geben Sie eine eindeutige Registrierungs-ID ein.|
    | **IoT Hub-Geräte-ID** |  Diese ID steht für Ihr Gerät. Sie muss den Regeln für eine Geräte-ID entsprechen. Weitere Informationen finden Sie unter „[Eigenschaften der Geräteidentität](../iot-hub/iot-hub-devguide-identity-registry“. Wenn die Geräte-ID nicht angegeben wurde, wird die Registrierungs-ID verwendet.|
    | **Wählen Sie aus, wie Geräte den Hubs zugewiesen werden sollen** |Wählen Sie *Statische Konfiguration* aus, damit Sie sie einem bestimmten Hub zuweisen können.|
    | **Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann** |Wählen Sie einen Ihrer Hubs aus.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-symm-key.png" alt-text="Fügen Sie eine individuelle Registrierung für den Nachweis des symmetrischen Schlüssels hinzu.":::

7. Wählen Sie **Speichern** aus.

### <a name="create-a-x509-certificate-individual-enrollment"></a>Erstellen einer individuellen Registrierung mit X.509-Zertifikat

>[!TIP]
>Ausführlichere Anleitungen zum Erstellen und Verwenden individueller Registrierungen mit X.509-Zertifikaten finden Sie im [Schnellstart: Bereitstellen eines Geräts mit X.509-Zertifikat](quick-create-simulated-device-x509.md#create-a-device-enrollment).

So erstellen Sie eine individuelle Registrierung mit X.509-Zertifikat:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite die Option **+ Individuelle Registrierung hinzufügen** aus.

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    | Feld | Beschreibung |
    | :--- | :--- |
    | **Mechanismus** | Wählen Sie *X.509* aus. |
    | **Primäres Zertifikat (PEM- oder CER-Datei)** | Laden Sie ein Zertifikat hoch, aus dem Sie untergeordnete Zertifikate generieren können. Wenn Sie die CER-Datei auswählen, wird nur das Base64-codierte Zertifikat akzeptiert. |
    | **IoT Hub-Geräte-ID** |  Diese ID steht für Ihr Gerät. Sie muss den Regeln für eine Geräte-ID entsprechen. Weitere Informationen finden Sie unter „[Eigenschaften der Geräteidentität](../iot-hub/iot-hub-devguide-identity-registry“. Die Geräte-ID muss der Antragstellername auf dem Gerätezertifikat sein, das Sie für die Registrierung hochladen. Dieser Antragstellername muss den Regeln für eine Geräte-ID entsprechen.|
    | **Wählen Sie aus, wie Geräte den Hubs zugewiesen werden sollen** |Wählen Sie *Statische Konfiguration* aus, damit Sie sie einem bestimmten Hub zuweisen können.|
    | **Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann** |Wählen Sie einen Ihrer Hubs aus.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-cert.png" alt-text="Fügen Sie eine individuelle Registrierung für den X.509-Zertifikatnachweis hinzu.":::

7. Wählen Sie **Speichern** aus.

### <a name="create-a-tpm-individual-enrollment"></a>Erstellen einer individuellen TPM-Registrierung

>[!TIP]
>Ausführlichere Anleitungen zum Erstellen und Verwenden von individuellen Registrierungen mithilfe des TPM-Nachweises finden Sie in einem der Beispiele für [Bereitstellen eines simulierten TPM-Geräts](quick-create-simulated-device-tpm.md#create-a-device-enrollment-entry).

So erstellen Sie eine individuelle TPM-Registrierung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite die Option **+ Individuelle Registrierung hinzufügen** aus.

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    | Feld | Beschreibung |
    | :--- | :--- |
    | **Mechanismus** | Wählen Sie *TPM* aus. |
    | **Endorsement Key** | Der eindeutige Endorsement Key des TPM-Geräts. |
    | **Registrierungs-ID** | Geben Sie eine eindeutige Registrierungs-ID ein.|
    | **IoT Hub-Geräte-ID** |  Diese ID steht für Ihr Gerät. Sie muss den Regeln für eine Geräte-ID entsprechen. Weitere Informationen finden Sie unter „[Eigenschaften der Geräteidentität](../iot-hub/iot-hub-devguide-identity-registry“. Wenn die Geräte-ID nicht angegeben wurde, wird die Registrierungs-ID verwendet.|
    | **Wählen Sie aus, wie Geräte den Hubs zugewiesen werden sollen** |Wählen Sie *Statische Konfiguration* aus, damit Sie sie einem bestimmten Hub zuweisen können.|
    | **Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann** |Wählen Sie einen Ihrer Hubs aus.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-tpm.png" alt-text="Fügen Sie eine individuelle Registrierung für den TPM-Nachweis hinzu.":::

7. Wählen Sie **Speichern** aus.

## <a name="update-an-enrollment-entry"></a>Aktualisieren eines Registrierungseintrags

So aktualisieren Sie einen vorhandenen Registrierungseintrag:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie den Registrierungseintrag aus, den Sie ändern möchten.

6. Auf der Seite mit Details zum Registrierungseintrag können Sie alle Elemente  – außer dem Sicherheitstyp und den Anmeldeinformationen – aktualisieren.

7. Wählen Sie anschließend **Speichern** aus.

## <a name="remove-a-device-enrollment"></a>Entfernen einer Geräteregistrierung

So entfernen Sie einen Registrierungseintrag:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den Device Provisioning-Dienst aus, für den Sie Ihr Gerät registrieren möchten.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie den Registrierungseintrag aus, den Sie entfernen möchten. 

6. Wählen Sie oben auf der Seite **Löschen** aus.

7. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

8. Nach Abschluss der Aktion sehen Sie, dass Ihr Eintrag aus der Liste der Geräteregistrierungen entfernt wurde.
