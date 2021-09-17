---
title: So signieren Sie Pakete für Gastkonfigurationen
description: Sie können optional Gastkonfigurationsinhaltspakete signieren und erzwingen, dass der Agent nur signierten Inhalt zulässt
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 7c7d120f17e32b3ea9accb6697ac66f4afcbb879
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772933"
---
# <a name="how-to-sign-guest-configuration-packages"></a>So signieren Sie Pakete für Gastkonfigurationen

Benutzerdefinierte Richtlinien für Gastkonfigurationen verwenden SHA256-Hash, um zu überprüfen, ob sich das Richtlinienpaket nicht geändert hat. Optional können Kunden auch ein Zertifikat nutzen, um Pakete zu signieren und für die Gastkonfigurationserweiterung zu erzwingen, dass nur signierte Inhalte zulässig sind.

Für dieses Szenario müssen Sie zwei Schritte ausführen. Führen Sie das Cmdlet zum Signieren des Inhaltspakets aus, und fügen Sie ein Tag an die Computer an, auf denen das Signieren von Code erzwungen werden soll.

## <a name="signature-validation-using-a-code-signing-certificate"></a>Signaturüberprüfung mithilfe eines Codesignaturzertifikats

Führen Sie zum Verwenden des Features für die Signaturüberprüfung das Cmdlet `Protect-GuestConfigurationPackage` aus, um das Paket vor der Veröffentlichung zu signieren. Für dieses Cmdlet ist ein Zertifikat für das „Codesignieren“ erforderlich.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parameter des Cmdlets `Protect-GuestConfigurationPackage`:

- **Pfad**: Vollständiger Pfad des Pakets mit der Gastkonfiguration.
- **Zertifikat**: Codesignaturzertifikat zum Signieren des Pakets. Dieser Parameter wird nur beim Signieren von Inhalt für Windows unterstützt.

## <a name="certificate-requirements"></a>Zertifikatanforderungen

Vom GuestConfiguration-Agent wird erwartet, dass der öffentliche Schlüssel des Zertifikats auf Windows-Computern unter „Vertrauenswürdige Stammzertifizierungsstellen“ und auf Linux-Computern unter dem Pfad `/usr/local/share/ca-certificates/extra` vorhanden ist. Damit auf dem Knoten signierter Inhalt überprüft werden kann, müssen Sie den öffentlichen Schlüssel des Zertifikats auf dem Computer installieren, bevor Sie die benutzerdefinierte Richtlinie anwenden. Dieser Prozess kann mit einem beliebigen Verfahren auf der VM oder mit Azure Policy durchgeführt werden. Eine Beispielvorlage ist verfügbar, [um ein Zertifikat für einen Computer bereitzustellen](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows).
In der Key Vault-Zugriffsrichtlinie muss es für den Computeressourcenanbieter zulässig sein, bei Bereitstellungen auf Zertifikate zuzugreifen. Informationen zu den ausführlichen Schritten finden Sie unter [Einrichten des Schlüsseltresors für virtuelle Computer in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Hier ist ein Beispiel für den Export des öffentlichen Schlüssels aus einem Signaturzertifikat angegeben, um ihn dann auf dem Computer importieren zu können.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## <a name="tag-requirements"></a>Tag-Anforderungen

Fügen Sie nach dem Veröffentlichen Ihres Inhalts ein Tag mit dem Namen `GuestConfigPolicyCertificateValidation` und dem Wert `enabled` an alle virtuellen Computer an, für die das Codesignieren erforderlich ist. Informationen darüber, wie Tags mithilfe von Azure Policy im großen Stil bereitgestellt werden können, finden Sie in den [Tagbeispielen](../samples/built-in-policies.md#tags). Wenn dieses Tag vorhanden ist, ermöglicht die Richtliniendefinition, die mit dem Cmdlet `New-GuestConfigurationPolicy` generiert wurde, die Anforderung über die Gastkonfigurationserweiterung.

## <a name="next-steps"></a>Nächste Schritte

- [Testen Sie das Paketartefakt](./guest-configuration-create-test.md) aus Ihrer Entwicklungsumgebung.
- [Veröffentlichen Sie das Paketartefakt](./guest-configuration-create-publish.md), damit es für Ihre Computer zugänglich ist.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy Definition](./guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie die [Konformitätsdetails für die Zuweisung von Gastkonfigurationsrichtlinien](./determine-non-compliance.md#compliance-details-for-guest-configuration) ansehen können.
