---
title: Clientbibliotheken für Azure Key Vault | Microsoft-Dokumentation
description: Clientbibliotheken für Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 5b4057a15493581dfd6216022a280d742d9561e0
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480766"
---
# <a name="client-libraries-for-azure-key-vault"></a>Clientbibliotheken für Azure Key Vault

Die Clientbibliotheken für Azure Key Vault ermöglichen den programmgesteuerten Zugriff auf Key Vault-Funktionen mit verschiedenen Sprachen, z. B. .NET, Python, Java und JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Clientbibliotheken pro Sprache und Objekt

Jedes SDK verfügt über separate Clientbibliotheken für Schlüsseltresore, Geheimnisse, Schlüssel und Zertifikate (siehe Tabelle unten).

| Sprache | Geheimnisse | Schlüssel | Zertifikate | Schlüsseltresor (Verwaltungsebene) |
|--|--|--|--|--|
| .NET | - [API-Referenz](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet-Paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Schnellstart](../secrets/quick-create-net.md) | - [API-Referenz](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet-Paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Schnellstart](../keys/quick-create-net.md) | - [API-Referenz](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet-Paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Schnellstart](../certificates/quick-create-net.md) | - [API-Referenz](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API-Referenz](/python/api/overview/azure/keyvault-secrets-readme)<br>- [PyPI-Paket](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Schnellstart](../secrets/quick-create-python.md) |- [API-Referenz](/python/api/overview/azure/keyvault-keys-readme)<br>- [PyPI-Paket](https://pypi.org/project/azure-keyvault-keys/)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Schnellstart](../keys/quick-create-python.md) | - [API-Referenz](/python/api/overview/azure/keyvault-certificates-readme)<br>- [PyPI-Paket](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Schnellstart](../certificates/quick-create-python.md) | - [API-Referenz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [PyPI-Paket](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API-Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Schnellstart](../secrets/quick-create-java.md) |- [API-Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Schnellstart](../keys/quick-create-java.md) | - [API-Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Schnellstart](../certificates/quick-create-java.md) |- [API-Referenz](/java/api/com.microsoft.azure.management.keyvault)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/keyvault/microsoft-azure-keyvault)|
| Node.js | - [API-Referenz](/javascript/api/@azure/keyvault-secrets/)<br>- [npm-Paket](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Schnellstart](../secrets/quick-create-node.md) |- [API-Referenz](/javascript/api/@azure/keyvault-keys/)<br>- [npm-Paket](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Schnellstart](../keys/quick-create-node.md)| - [API-Referenz](/javascript/api/@azure/keyvault-certificates/)<br>- [npm-Paket](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Schnellstart](../certificates/quick-create-node.md) |  - [API-Referenz](/javascript/api/@azure/arm-keyvault/)<br>- [npm-Paket](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Nächste Schritte

- [Entwicklerhandbuch zu Azure-Schlüsseltresor](developers-guide.md)
- Weitere Informationen zum [Authentifizieren bei einer Key Vault-Instanz](authentication.md)
- Weitere Informationen zum [Sichern des Zugriffs auf eine Key Vault-Instanz](security-features.md)
