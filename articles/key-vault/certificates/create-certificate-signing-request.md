---
title: Erstellen und Zusammenführen einer Zertifikatsignieranforderung in Azure Key Vault
description: Hier erfahren Sie, wie Sie eine Zertifikatsignieranforderung in Azure Key Vault erstellen und zusammenführen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 54b387892e3ad7d813fb97fb8e9ab399ccdb2a1c
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515106"
---
# <a name="create-and-merge-a-certificate-signing-request-in-key-vault"></a>Erstellen und Zusammenführen einer Zertifikatsignieranforderung in Key Vault

Azure Key Vault unterstützt die Speicherung digitaler Zertifikate, die von einer beliebigen Zertifizierungsstelle (ZS) ausgestellt wurden. Key Vault unterstützt die Erstellung einer Zertifikatsignieranforderung (Certificate Signing Request, CSR) mit einem Schlüsselpaar mit einem privaten und einem öffentlichen Schlüssel. Die CSR kann von jeder Zertifizierungsstelle (interne Unternehmenszertifizierungsstelle oder externe öffentliche Zertifizierungsstelle) signiert werden. Bei einer Zertifikatsignieranforderung handelt es sich um eine Nachricht, die Sie zum Anfordern eines digitalen Zertifikats an eine Zertifizierungsstelle senden.

Weitere allgemeine Informationen zu Zertifikaten finden Sie unter [Informationen zu Azure Key Vault-Zertifikaten](./about-certificates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Hinzufügen der von Partnerzertifizierungsstellen ausgestellten Zertifikate in Key Vault

Key Vault ist eine Partnerschaft mit den folgenden Zertifizierungsstellen eingegangen, um die Zertifikaterstellung zu vereinfachen:

|Anbieter|Zertifikattyp|Konfigurationssetup  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault bietet OV- oder EV-SSL-Zertifikate mit DigiCert| [Integrationsleitfaden](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault bietet OV- oder EV-SSL-Zertifikate mit GlobalSign| [Integrationsleitfaden](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Hinzufügen von Zertifikaten in Key Vault, die von Zertifizierungsstellen ausgestellt wurden, die keine Partner sind

Führen Sie die folgenden Schritte aus, um ein Zertifikat von Zertifizierungsstellen hinzuzufügen, die keine Partnerschaft mit Key Vault eingegangen sind. (GoDaddy ist beispielsweise keine vertrauenswürdige Key Vault-Zertifizierungsstelle.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie zu dem Schlüsseltresor, dem Sie das Zertifikat hinzufügen möchten.
1. Wählen Sie auf der Eigenschaftenseite die Option **Zertifikate** aus.
1. Wählen Sie die Registerkarte **Generieren/importieren** aus.
1. Wählen Sie auf dem Bildschirm **Zertifikat erstellen** die folgenden Werte aus:
    - **Methode der Zertifikaterstellung**: Generieren.
    - **Zertifikatname**: ContosoManualCSRCertificate.
    - **Typ der Zertifizierungsstelle (ZS):** Zertifikat wurde durch eine nicht integrierte Zertifizierungsstelle ausgestellt.
    - **Antragsteller:** `"CN=www.contosoHRApp.com"`
     > [!NOTE]
     > Wenn Sie einen RDN (Relative Distinguished Name) nutzen, dessen Wert ein Komma (,) enthält, setzen Sie den Wert, der das Sonderzeichen enthält, in doppelte Anführungszeichen. 
     >
     > Beispieleintrag für **Antragsteller**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > In diesem Beispiel enthält der `OU`-Wert des RDN einen Wert mit einem Komma im Namen. Die resultierende Ausgabe für `OU` lautet **Docs, Contoso**.
1. Wählen Sie die anderen Werte wie gewünscht und dann **Erstellen** aus, um das Zertifikat der Liste **Zertifikate** hinzuzufügen.

    ![Screenshot: Zertifikateigenschaften](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Wählen Sie in der Liste **Zertifikate** das neue Zertifikat aus. Der aktuelle Status des Zertifikats lautet **Deaktiviert**, da es noch nicht von der Zertifizierungsstelle ausgestellt wurde.
1. Wählen Sie auf der Registerkarte **Zertifikatvorgang** die Option **CSR herunterladen** aus.

   ![Screenshot: Hervorgehobene Schaltfläche „CSR herunterladen“](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Lassen Sie die CSR (.csr) von der Zertifizierungsstelle signieren.
1. Wählen Sie nach dem Signieren der Anforderung auf der Registerkarte **Zertifikatvorgang** die Option **Signierte Anforderung zusammenführen** aus, um das signierte Zertifikat zu Key Vault hinzuzufügen.

Die Zertifikatanforderung wurde nun erfolgreich zusammengeführt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Erstellen Sie eine Zertifikatrichtlinie. Da die in diesem Szenario ausgewählte Zertifizierungsstelle über keine Partnerschaft verfügt, wird **IssuerName** auf **Unknown** festgelegt, und das Zertifikat wird von Key Vault nicht registriert oder erneuert.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Wenn Sie einen RDN (Relative Distinguished Name) nutzen, dessen Wert ein Komma (,) enthält, verwenden Sie einfache Anführungszeichen für den vollständigen Wert oder Wertsatz, und setzen Sie den Wert, der das Sonderzeichen enthält, in doppelte Anführungszeichen. 
     >
     >Beispieleintrag für **SubjectName**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. In diesem Beispiel ist **Docs, Contoso** der Wert für `OU`. Dieses Format funktioniert für alle Werte, die ein Komma enthalten.
     > 
     > In diesem Beispiel enthält der `OU`-Wert des RDN einen Wert mit einem Komma im Namen. Die resultierende Ausgabe für `OU` lautet **Docs, Contoso**.

1. Erstellen Sie die CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Lassen Sie die CSR von der Zertifizierungsstelle signieren. `$csr.CertificateSigningRequest` ist die Base-codierte CSR für das Zertifikat. Sie können dieses Blob in die Website für Zertifikatanforderungen des Ausstellers übernehmen. Dieser Schritt ist je nach Zertifizierungsstelle unterschiedlich. Sehen Sie in den Richtlinien der Zertifizierungsstelle nach, wie dieser Schritt auszuführen ist. Sie können auch Tools wie Certreq oder OpenSSL verwenden, um die CSR signieren zu lassen und den Vorgang zum Generieren eines Zertifikats abzuschließen.

1. Führen Sie die signierte Anforderung in Key Vault zusammen. Nachdem die Zertifikatanforderung signiert wurde, können Sie sie mit dem anfänglichen Schlüsselpaar mit einem privaten und einem öffentlichen Schlüssel zusammenführen, das Sie in Azure Key Vault erstellt haben.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Die Zertifikatanforderung wurde nun erfolgreich zusammengeführt.

---

## <a name="add-more-information-to-the-csr"></a>Hinzufügen von weiteren Informationen zur CSR

Wenn Sie beim Erstellen der CSR weitere Informationen hinzufügen möchten, definieren Sie diese in **SubjectName**. Sie können beispielsweise folgende Informationen hinzufügen:
- Land
- Ort/Standort
- Bundesland/Kanton
- Organization
- Organisationseinheit

Beispiel

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Wenn Sie ein domänenvalidiertes Zertifikat (DV-Zertifikat) mit zusätzlichen Informationen anfordern, lehnt die Zertifizierungsstelle die Anforderung unter Umständen ab, wenn nicht alle Informationen in der Anforderung validiert werden können. Die zusätzlichen Informationen eignen sich unter Umständen besser für die Anforderung eines organisationsvalidierten Zertifikats (OV-Zertifikat).

## <a name="faqs"></a>Häufig gestellte Fragen

- Wie überwache oder verwalte ich meine CSR?

     Entsprechende Informationen finden Sie unter [Überwachen und Verwalten der Zertifikaterstellung](./create-certificate-scenarios.md).

- Was ist zu tun, wenn mir Folgendes angezeigt wird: **Fehlertyp „Der öffentliche Schlüssel des Endentitätszertifikats im angegebenen X.509-Zertifikatinhalt stimmt nicht mit dem öffentlichen Teil des angegebenen privaten Schlüssels überein. Überprüfen Sie die Gültigkeit des Zertifikats.“** ?

     Dieser Fehler tritt auf, wenn Sie die signierte CSR nicht mit der gleichen initialisierten CSR-Anforderung zusammenführen. Jede von Ihnen erstellte neue CSR verfügt über einen privaten Schlüssel, der beim Zusammenführen der signierten Anforderung abgeglichen werden muss.

- Wird beim Zusammenführen einer CSR die gesamte Kette zusammengeführt?

     Ja, die gesamte Kette wird zusammengeführt, vorausgesetzt, der Benutzer hat eine P7B-Datei zum Zusammenführen zurückgegeben.

- Was geschieht, wenn das ausgestellte Zertifikat im Azure-Portal den Status „deaktiviert“ hat?

     Überprüfen Sie auf der Registerkarte **Zertifikatvorgang** die Fehlermeldung für dieses Zertifikat.

- Was ist zu tun, wenn mir Folgendes angezeigt wird: **Fehlertyp „Beim angegebenen Antragstellernamen handelt es sich nicht um einen gültigen X.500-Namen.“** ?

     Dieser Fehler kann auftreten, wenn **SubjectName** Sonderzeichen enthält. Weitere Informationen finden Sie in den Hinweisen in den Anweisungen zum Azure-Portal und zu PowerShell.

- Fehlertyp  **Die zum Abrufen Ihres Zertifikats verwendete CSR wurde bereits verwendet. Versuchen Sie, ein neues Zertifikat mit einer neuen CSR zu generieren**.
     Navigieren Sie zum Abschnitt „Erweiterte Richtlinie“ des Zertifikats, und überprüfen Sie, ob die Option Schlüssel beim Erneuern wiederverwenden? deaktiviert ist.
---

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
- [Referenz für die Azure Key Vault-REST-API](/rest/api/keyvault)
- [Tresore: Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate)
- [Tresore: Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy)
