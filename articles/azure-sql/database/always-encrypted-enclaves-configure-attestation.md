---
title: Konfigurieren des Nachweises für Always Encrypted mithilfe von Azure Attestation
description: Konfigurieren von Azure Attestation für Always Encrypted mit Secure Enclaves in Azure SQL-Datenbank
keywords: Verschlüsseln von Daten, SQL-Verschlüsselung, Datenbankverschlüsselung, vertrauliche Daten, Always Encrypted, Secure Enclaves, SGX, Nachweis
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6a27acf96b42a4a963b88e281fd692a91616b7e4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727199"
---
# <a name="configure-attestation-for-always-encrypted-using-azure-attestation"></a>Konfigurieren des Nachweises für Always Encrypted mithilfe von Azure Attestation

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Microsoft Azure Attestation](../../attestation/overview.md) ist eine Lösung für Trusted Execution Environment-Nachweise (TEE), einschließlich Intel Software Guard Extensions-Enclaves (Intel SGX). 

Um Azure Attestation für Nachweise von Intel SGX-Enclaves, die für [Always Encrypted mit Secure Enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) verwendet werden, in Azure SQL-Datenbank zu verwenden, führen Sie folgende Schritte aus:

1. Erstellen Sie einen [Nachweisanbieter](../../attestation/basic-concepts.md#attestation-provider), und konfigurieren Sie ihn mit der empfohlenen Nachweisrichtlinie.

2. Bestimmen Sie die Nachweis-URL, und geben Sie die URL für Anwendungsadministratoren frei.

> [!NOTE]
> Für die Konfiguration von Nachweisen ist der Nachweisadministrator verantwortlich. Weitere Informationen finden Sie unter [Rollen und Verantwortlichkeiten beim Konfigurieren von SGX-Enclaves und von Attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="create-and-configure-an-attestation-provider"></a>Erstellen und Konfigurieren eines Nachweisanbieters

Bei einem [Nachweisanbieter](../../attestation/basic-concepts.md#attestation-provider) handelt es sich um eine Ressource in Azure Attestation, mit deren Hilfe [Nachweisanforderungen](../../attestation/basic-concepts.md#attestation-request) anhand von [Nachweisrichtlinien](../../attestation/basic-concepts.md#attestation-request) ausgewertet und [Nachweistoken](../../attestation/basic-concepts.md#attestation-token) ausgestellt werden. 

Nachweisrichtlinien werden mithilfe der [Anspruchsregelgrammatik](../../attestation/claim-rule-grammar.md) angegeben.

> [!IMPORTANT]
> Ein Nachweisanbieter wird mit der Standardrichtlinie für Intel SGX-Enclaves erstellt, wodurch der innerhalb der Enclave ausgeführte Code nicht überprüft wird. Microsoft empfiehlt dringend, für Always Encrypted mit Secure Enclaves die unten empfohlene Richtlinie festzulegen und nicht die Standardrichtlinie zu verwenden.

Microsoft empfiehlt die folgende Richtlinie zum Nachweisen von Intel SGX-Enclaves, die für Always Encrypted in Azure SQL-Datenbank verwendet werden:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

Mit der oben genannten Richtlinie wird Folgendes überprüft:

- Die Enclave in Azure SQL-Datenbank unterstützt das Debuggen nicht. 
  > Enclaves können mit deaktiviertem oder aktiviertem Debuggen geladen werden. Die Debugunterstützung ermöglicht Entwicklern, Probleme mit dem Code zu beheben, der in einer Enclave ausgeführt wird. In einem Produktionssystem könnte das Debuggen einem Administrator ermöglichen, den Inhalt der Enclave zu untersuchen, was die von der Enclave gebotene Schutzebene verringern würde. Die empfohlene Richtlinie deaktiviert das Debuggen, um sicherzustellen, dass der Nachweis nicht erfolgreich ist, wenn ein böswilliger Administrator versucht, die Debugunterstützung durch Übernahme des Enclave-Computers zu aktivieren. 
- Die Produkt-ID der Enclave entspricht der Produkt-ID, die Always Encrypted mit Secure Enclaves zugewiesen ist.
  > Jede Enclave verfügt über eine eindeutige Produkt-ID, die die Enclave von anderen Enclaves unterscheidet. Der Always Encrypted-Enclave ist die Produkt-ID 4639 zugewiesen. 
- Die Sicherheitsversionsnummer (SVN) der Bibliothek ist größer als 0 (null).
  > Die SVN ermöglicht Microsoft, auf potenzielle Sicherheitsfehler zu reagieren, die im Enclave-Code identifiziert werden. Falls ein Sicherheitsproblem entdeckt und behoben wird, stellt Microsoft eine neue Version der Enclave mit einer neuen (inkrementierten) SVN bereit. Die oben genannte empfohlene Richtlinie wird mit der neuen SVN aktualisiert. Mit der Aktualisierung auf die empfohlene Richtlinie können Sie sicherstellen, dass der Nachweis nicht erfolgreich ist, wenn ein böswilliger Administrator versucht, eine ältere und unsichere Enclave zu laden.
- Die Bibliothek in der Enclave wurde mit dem Microsoft-Signaturschlüssel signiert (der Wert des x-ms-sgx-mrsigner-Anspruchs entspricht dem Hash des Signaturschlüssels).
  > Eines der Hauptziele des Nachweises ist, Clients zu überzeugen, dass die in der Enclave ausgeführte Binärdatei die Binärdatei ist, die ausgeführt werden soll. Nachweisrichtlinien stellen zwei Mechanismen für diesen Zweck bereit. Einer ist der **mrenclave**-Anspruch, bei dem es sich um den Hash der Binärdatei handelt, die in einer Enclave ausgeführt werden soll. Bei **mrenclave** besteht das Problem, dass sich der binäre Hash selbst bei geringfügigen Änderungen des Codes ändert, wodurch es schwierig wird, den Code, der in der Enclave ausgeführt wird, zu überarbeiten. Daher sollte **mrsigner** verwendet werden, der Hash eines Schlüssels, der zum Signieren der Enclave-Binärdatei verwendet wird. Wenn Microsoft die Enclave überarbeitet, bleibt **mrsigner** unverändert, solange der Signaturschlüssel nicht geändert wird. Auf diese Weise wird es möglich, aktualisierte Binärdateien bereitzustellen, ohne die Anwendungen der Kunden zu zerstören. 

> [!IMPORTANT]
> Microsoft muss möglicherweise den Schlüssel rotieren, der zum Signieren der Binärdatei für die Always Encrypted-Enclave verwendet wird. Dies kommt jedoch nur selten vor. Bevor eine neue Version der Binärdatei für die Enclave, die mit einem neuen Schlüssel signiert ist, in Azure SQL-Datenbank bereitgestellt wird, wird dieser Artikel aktualisiert, um die neue empfohlene Nachweisrichtlinie sowie Anweisungen dazu bereitzustellen, wie Sie die Richtlinie in Ihren Nachweisanbietern aktualisieren sollten, damit Sie sicherstellen können, dass Ihre Anwendungen weiterhin unterbrechungsfrei funktionieren.

Anweisungen zum Erstellen eines Nachweisanbieters und Konfigurieren des Anbieters mit einer Nachweisrichtlinie unter Verwendung folgender Optionen:

- [Schnellstart: Einrichten von Azure Attestation über das Azure-Portal](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Wenn Sie die Nachweisrichtlinie über das Azure-Portal konfigurieren, legen Sie den Nachweistyp auf `SGX-IntelSDK` fest.
- [Schnellstart: Einrichten von Azure Attestation mithilfe von Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Wenn Sie die Nachweisrichtlinie mit Azure PowerShell konfigurieren, legen Sie den Parameter `Tee` auf `SgxEnclave` fest.
- [Schnellstart: Einrichten von Azure Attestation mithilfe der Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Wenn Sie die Nachweisrichtlinie mit der Azure CLI konfigurieren, legen Sie den Parameter `attestation-type` auf `SGX-IntelSDK` fest.


## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Ermitteln der Nachweis-URL für die Nachweisrichtlinie

Nachdem Sie eine Nachweisrichtlinie konfiguriert haben, müssen Sie die Nachweis-URL für Administratoren von Anwendungen freigeben, die Always Encrypted mit Secure Enclaves in Azure SQL-Datenbank verwenden. Die Nachweis-URL ist die `Attest URI` des Nachweisanbieters, der die Nachweisrichtlinie enthält, die wie folgt aussieht: `https://MyAttestationProvider.wus.attest.azure.net`.

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Ermitteln der Nachweis-URL über das Azure-Portal

Kopieren Sie im Bereich „Übersicht“ Ihres Nachweisanbieters den Wert der `Attest URI`-Eigenschaft in die Zwischenablage. 

### <a name="use-powershell-to-determine-the-attestation-url"></a>Ermitteln der Nachweis-URL mithilfe von PowerShell

Verwenden Sie das `Get-AzAttestation`-Cmdlet, um die Eigenschaften des Nachweisanbieters, einschließlich AttestURI, abzurufen.

```powershell
Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName
```

Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Nachweisanbieters](../../attestation/quickstart-powershell.md#create-and-manage-an-attestation-provider).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Schlüsseln für Always Encrypted mit Secure Enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Weitere Informationen

- [Tutorial: Erste Schritte mit Always Encrypted mit Secure Enclaves in Azure SQL-Datenbank](always-encrypted-enclaves-getting-started.md)
