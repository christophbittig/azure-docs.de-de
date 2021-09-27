---
title: Autorisieren des Zugriffs auf Blobs mit AzCopy und Azure Active Directory | Microsoft-Dokumentation
description: Sie können Autorisierungsanmeldeinformationen für AzCopy-Vorgänge mit Azure Active Directory (Azure AD) bereitstellen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/01/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: d5aced875863f892ec9dda0022f4571ce15349c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638233"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Autorisieren des Zugriffs auf Blobs mit AzCopy und Azure Active Directory (Azure AD)

Sie können AzCopy mit Autorisierungsanmeldeinformationen bereitstellen, indem Sie Azure AD verwenden. Auf diese Weise müssen Sie nicht jedem Befehl ein Shared Access Signature-Token (SAS) anfügen.

Überprüfen Sie zunächst Ihre Rollenzuweisungen. Wählen Sie dann aus, welchen Typ von *Sicherheitsprinzipal* Sie autorisieren möchten. Eine [Benutzeridentität](../../active-directory/fundamentals/add-users-azure-active-directory.md), eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) und ein [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) sind jeweils Sicherheitsprinzipaltypen.

Eine Benutzeridentität ist ein beliebiger Benutzer, der über eine Identität in Azure AD verfügt. Dieser Sicherheitsprinzipal lässt sich am einfachsten autorisieren. Verwaltete Identitäten und Dienstprinzipale sind eine gute Option, wenn Sie AzCopy in einem Skript verwenden möchten, das ohne Benutzerinteraktion ausgeführt wird. Eine verwaltete Identität eignet sich besser für Skripts, die auf einem virtuellen Azure-Computer (VM) ausgeführt werden, und ein Dienstprinzipal eignet sich besser für Skripts, die lokal ausgeführt werden.

Weitere Informationen zu AzCopy finden Sie unter [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md).

## <a name="verify-role-assignments"></a>Überprüfen von Rollenzuweisungen

Die Ebene der Autorisierung, die Sie benötigen, hängt davon ab, ob Sie planen, Dateien hoch- oder nur herunterzuladen.

Wenn Sie nur Dateien herunterladen möchten, überprüfen Sie, ob die Rolle [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Ihrer Benutzeridentität, der verwalteten Identität oder dem Dienstprinzipal zugewiesen wurde.

Wenn Sie Dateien hochladen möchten, dann überprüfen Sie, ob Ihrem Sicherheitsprinzipal eine dieser Rollen zugewiesen wurde:

- [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Diese Rollen können Ihrem Sicherheitsprinzipal in jedem dieser Bereiche zugewiesen werden:

- Container (Dateisystem)
- Speicherkonto
- Resource group
- Subscription

Informationen zum Überprüfen und Zuweisen von Rollen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](../blobs/assign-azure-role-data-access.md).

> [!NOTE]
> Denken Sie daran, dass die Weitergabe von Azure-Rollenzuweisungen bis zu fünf Minuten dauern kann.

Ihrem Sicherheitsprinzipal muss keine dieser Rollen zugewiesen sein, wenn Ihr Sicherheitsprinzipal der Zugriffssteuerungsliste (ACL) des Zielcontainers oder des Verzeichnisses hinzugefügt wurde. In der Zugriffssteuerungsliste benötigt Ihr Sicherheitsprinzipal Schreibberechtigungen für das Zielverzeichnis und Ausführungsberechtigungen für den Container und jedes übergeordnete Verzeichnis.

Weitere Informationen finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Autorisieren einer Benutzeridentität

Nachdem Sie überprüft haben, ob Ihrer Benutzeridentität die notwendige Berechtigungsstufe gewährt wurde, öffnen Sie eine Eingabeaufforderung, geben Sie den folgenden Befehl ein, und drücken Sie dann die EINGABETASTE.

```azcopy
azcopy login
```

Wenn Sie einen Fehler angezeigt bekommen, fügen Sie die Mandanten-ID der Organisation ein, zu der das Speicherkonto gehört.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Ersetzen Sie den Platzhalter `<tenant-id>` mit der Mandanten-ID der Organisation, zu der das Speicherkonto gehört. Wählen Sie **Azure Active Directory > Eigenschaften > Verzeichnis-ID** im Azure-Portal aus, um die Mandanten-ID zu finden.

Dieser Befehl gibt einen Authentifizierungscode und die URL einer Website zurück. Öffnen Sie die Website, geben Sie den Code ein, und wählen Sie dann die Schaltfläche **Weiter** aus.

![Erstellen eines Containers](media/storage-use-azcopy-v10/azcopy-login.png)

Daraufhin wird ein Anmeldefenster geöffnet. Melden Sie sich in diesem Fenster mit Ihren Azure-Kontoanmeldeinformationen bei Ihrem Azure-Konto an. Wenn Sie sich erfolgreich angemeldet haben, können Sie das Browserfenster schließen und mit der Verwendung von AzCopy beginnen.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Erstellen und Autorisieren einer verwalteten Identität

Dies ist eine gute Option, wenn Sie AzCopy in einem Skript verwenden möchten, das ohne Benutzerinteraktion und über einen virtuellen Azure-Computer ausgeführt wird. Bei Verwendung dieser Option müssen Sie keine Anmeldeinformationen auf dem virtuellen Computer speichern.

Sie können sich bei Ihrem Konto anmelden, indem Sie eine systemweite verwaltete Identität verwenden, die Sie auf dem virtuellen Computer aktiviert haben, oder indem Sie die Client-ID, Objekt-ID oder Ressourcen-ID einer vom Benutzer zugewiesenen verwalteten Identität verwenden, die Sie dem virtuellen Computer zugewiesen haben.

Weitere Informationen zum Aktivieren einer systemweiten verwalteten Identität oder zum Erstellen einer vom Benutzer zugewiesenen verwalteten Identität finden unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorisieren mithilfe einer systemweiten verwalteten Identität

Stellen Sie zunächst sicher, dass Sie eine systemweite verwaltete Identität auf Ihrem virtuellen Computer aktiviert haben. Siehe [Systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Geben Sie dann an der Befehlskonsole den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorisieren mithilfe einer benutzerseitig zugewiesenen verwalteten Identität

Stellen Sie zunächst sicher, dass Sie eine vom Benutzer zugewiesene verwaltete Identität auf Ihrem virtuellen Computer aktiviert haben. Siehe [Benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Geben Sie dann an der Befehlskonsole einen der folgenden Befehle ein, und drücken Sie die EINGABETASTE.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Ersetzen Sie den Platzhalter `<client-id>` durch die Client-ID der benutzerseitig zugewiesenen verwalteten Identität.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Ersetzen Sie den Platzhalter `<object-id>` durch die Objekt-ID der benutzerseitig zugewiesenen verwalteten Identität.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Ersetzen Sie den Platzhalter `<resource-id>` durch die Ressourcen-ID der benutzerseitig zugewiesenen verwalteten Identität.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Autorisieren eines Dienstprinzipals

Dies ist eine gute Option, wenn Sie AzCopy in einem Skript verwenden möchten, das ohne Benutzerinteraktion ausgeführt wird, vor allem bei der lokalen Ausführung. Wenn Sie AzCopy auf virtuellen Computern ausführen möchten, die in Azure ausgeführt werden, lässt sich eine verwaltete Dienstidentität einfacher verwalten. Weitere Informationen finden Sie im Abschnitt [Autorisieren einer verwalteten Identität](#authorize-a-managed-identity) in diesem Artikel.

Sie müssen sich interaktiv mindestens ein Mal anmelden, bevor Sie ein Skript ausführen, damit Sie in AzCopy die Anmeldeinformationen Ihres Dienstprinzipals bereitstellen können.  Diese Anmeldedaten werden in einer gesicherten, verschlüsseln Datei gespeichert, damit Ihr Skript diese vertraulichen Daten nicht weitergeben muss.

Sie können sich mit einem geheimen Clientschlüssel oder mit dem Kennwort eines Zertifikats, das für die App-Registrierung Ihres Dienstprinzipals verwendet wird, bei Ihrem Konto anmelden.

Weitere Informationen zum Erstellen eines Dienstprinzipals finden Sie unter [Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

Weitere Informationen zu Dienstprinzipalen im Allgemeinen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorisieren eines Dienstprinzipals unter Verwendung eines geheimen Clientschlüssels

Beginnen Sie, indem Sie die `AZCOPY_SPA_CLIENT_SECRET`-Umgebungsvarible auf den geheimen Clientschlüssel der App-Registrierung Ihres Dienstprinzipals festlegen.

> [!NOTE]
> Stellen Sie sicher, dass Sie diesen Wert in Ihrer Eingabeaufforderung einrichten und nicht in den Einstellungen der Umgebungsvariable Ihres Betriebssystems. So ist der Wert nur für die aktuelle Sitzung verfügbar.

Dieses Beispiel zeigt Ihnen, wie dies in PowerShell geht.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Erwägen Sie, eine Eingabeaufforderung wie in diesem Beispiel zu verwenden. So erscheint das Kennwort nicht im Befehlsverlauf Ihrer Konsole.

Geben Sie dann den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Ersetzen Sie den Platzhalter `<application-id>` mit der Anwendungs-ID der App-Registrierung Ihres Dienstprinzipals. Ersetzen Sie den Platzhalter `<tenant-id>` mit der Mandanten-ID der Organisation, zu der das Speicherkonto gehört. Wählen Sie **Azure Active Directory > Eigenschaften > Verzeichnis-ID** im Azure-Portal aus, um die Mandanten-ID zu finden.

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorisieren eines Dienstprinzipals mithilfe eines Zertifikats

Wenn Sie lieber Ihre eigenen Anmeldedaten für die Autorisierung verwenden möchten, dann können Sie ein Zertifikat in Ihre App-Registrierung hochladen und dieses zum Anmelden verwenden.

Sie müssen nicht nur Ihr Zertifikat in Ihre App-Registrierung hochladen, sondern benötigen außerdem eine Kopie des Zertifikat, die auf dem Computer oder dem virtuellen Computer gespeichert ist, auf dem AzCopy ausgeführt wird. Diese Kopie des Zertifikats sollte das .PFX oder .PEM-Format haben und den privaten Schlüssel enthalten. Der private Schlüssel sollte mit einem Kennwort geschützt sein. Wenn Sie Windows verwenden und Ihre Zertifikat nur in einem Zertifikatspeicher ist, dann stellen Sie sicher, dass Sie das Zertifikat als PFX-Datei exportieren (einschließlich des privaten Schlüssels). Eine Anleitung dazu finden Sie unter [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate).

Richten Sie jetzt die `AZCOPY_SPA_CERT_PASSWORD`-Umgebungsvariable mit dem Zertifikatkennwort ein.

> [!NOTE]
> Stellen Sie sicher, dass Sie diesen Wert in Ihrer Eingabeaufforderung einrichten und nicht in den Einstellungen der Umgebungsvariable Ihres Betriebssystems. So ist der Wert nur für die aktuelle Sitzung verfügbar.

Dieses Beispiel zeigt Ihnen, wie diese Aufgabe in PowerShell ausgeführt wird.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Geben Sie dann den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Ersetzen Sie den Platzhalter `<path-to-certificate-file>` mit einem relativen oder vollqualifizierten Pfad zur Zertifikatdatei. AzCopy speichert den Pfad zu diesem Zertifikat, speichert jedoch keine Kopie des Zertifikats. Achten Sie darauf, dass das Zertifikat dort bleibt, wo es ist. Ersetzen Sie den Platzhalter `<tenant-id>` mit der Mandanten-ID der Organisation, zu der das Speicherkonto gehört. Wählen Sie **Azure Active Directory > Eigenschaften > Verzeichnis-ID** im Azure-Portal aus, um die Mandanten-ID zu finden.

> [!NOTE]
> Erwägen Sie, eine Eingabeaufforderung wie in diesem Beispiel zu verwenden. So erscheint das Kennwort nicht im Befehlsverlauf Ihrer Konsole.

## <a name="authorize-without-a-secret-store"></a>Autorisieren ohne Geheimnisspeicher

Mit dem Befehl `azcopy login` wird ein OAuth-Token abgerufen und in einem Geheimnisspeicher auf Ihrem System gespeichert. Wenn Ihr Betriebssystem über keinen Geheimnisspeicher verfügt (z. B. *keyring* unter Linux`azcopy login`), funktioniert der Befehl nicht, da es keinen Speicherort für das Token gibt.

Anstatt den Befehl `azcopy login` zu verwenden, können Sie In-Memory-Umgebungsvariablen festlegen. Führen Sie dann einen beliebigen AzCopy-Befehl aus. Der AzCopy-Befehl ruft das erforderliche Authentifizierungstoken für den Vorgang ab. Nach Abschluss des Vorgangs wird das Token aus dem Arbeitsspeicher gelöscht.

### <a name="authorize-a-user-identity"></a>Autorisieren einer Benutzeridentität

Nachdem Sie überprüft haben, ob Ihrer Benutzeridentität die erforderliche Berechtigungsstufe gewährt wurde, geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Führen Sie dann einen beliebigen azcopy-Befehl aus (beispielsweise: `azcopy list https://contoso.blob.core.windows.net`).

Dieser Befehl gibt einen Authentifizierungscode und die URL einer Website zurück. Öffnen Sie die Website, geben Sie den Code ein, und wählen Sie dann die Schaltfläche **Weiter** aus.

![Erstellen eines Containers](media/storage-use-azcopy-v10/azcopy-login.png)

Daraufhin wird ein Anmeldefenster geöffnet. Melden Sie sich in diesem Fenster mit Ihren Azure-Kontoanmeldeinformationen bei Ihrem Azure-Konto an. Nachdem Sie sich erfolgreich angemeldet haben, kann der Vorgang abgeschlossen werden.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorisieren mithilfe einer systemweiten verwalteten Identität

Stellen Sie zunächst sicher, dass Sie eine systemweite verwaltete Identität auf Ihrem virtuellen Computer aktiviert haben. Siehe [Systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Führen Sie dann einen beliebigen azcopy-Befehl aus (beispielsweise: `azcopy list https://contoso.blob.core.windows.net`).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorisieren mithilfe einer benutzerseitig zugewiesenen verwalteten Identität

Stellen Sie zunächst sicher, dass Sie eine vom Benutzer zugewiesene verwaltete Identität auf Ihrem virtuellen Computer aktiviert haben. Siehe [Benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Geben Sie dann einen der folgenden Befehle ein, und drücken Sie die EINGABETASTE.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

Ersetzen Sie den Platzhalter `<client-id>` durch die Client-ID der benutzerseitig zugewiesenen verwalteten Identität.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

Ersetzen Sie den Platzhalter `<object-id>` durch die Objekt-ID der benutzerseitig zugewiesenen verwalteten Identität.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

Ersetzen Sie den Platzhalter `<resource-id>` durch die Ressourcen-ID der benutzerseitig zugewiesenen verwalteten Identität.

Nachdem Sie diese Variablen festgelegt haben, können Sie einen beliebigen azcopy-Befehl ausführen (beispielsweise: `azcopy list https://contoso.blob.core.windows.net`).

### <a name="authorize-a-service-principal"></a>Autorisieren eines Dienstprinzipals

Sie können sich mit einem geheimen Clientschlüssel oder mit dem Kennwort eines Zertifikats, das für die App-Registrierung Ihres Dienstprinzipals verwendet wird, bei Ihrem Konto anmelden.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorisieren eines Dienstprinzipals unter Verwendung eines geheimen Clientschlüssels

Geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
export AZCOPY_TENANT_ID=<tenant-id>
```

Ersetzen Sie den Platzhalter `<application-id>` mit der Anwendungs-ID der App-Registrierung Ihres Dienstprinzipals. Ersetzen Sie den Platzhalter `<client-secret>` durch den geheimen Clientschlüssel. Ersetzen Sie den Platzhalter `<tenant-id>` mit der Mandanten-ID der Organisation, zu der das Speicherkonto gehört. Wählen Sie **Azure Active Directory > Eigenschaften > Verzeichnis-ID** im Azure-Portal aus, um die Mandanten-ID zu finden.

> [!NOTE]
> Verwenden Sie ggf. eine Eingabeaufforderung, um das Kennwort vom Benutzer zu erfassen. Auf diese Weise wird Ihr Kennwort in Ihrem Befehlsverlauf nicht angezeigt.

Führen Sie dann einen beliebigen azcopy-Befehl aus (beispielsweise: `azcopy list https://contoso.blob.core.windows.net`).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorisieren eines Dienstprinzipals mithilfe eines Zertifikats

Wenn Sie lieber Ihre eigenen Anmeldedaten für die Autorisierung verwenden möchten, dann können Sie ein Zertifikat in Ihre App-Registrierung hochladen und dieses zum Anmelden verwenden.

Sie müssen nicht nur Ihr Zertifikat in Ihre App-Registrierung hochladen, sondern benötigen außerdem eine Kopie des Zertifikat, die auf dem Computer oder dem virtuellen Computer gespeichert ist, auf dem AzCopy ausgeführt wird. Diese Kopie des Zertifikats sollte das .PFX oder .PEM-Format haben und den privaten Schlüssel enthalten. Der private Schlüssel sollte mit einem Kennwort geschützt sein.

Geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
export AZCOPY_TENANT_ID=<tenant-id>
```

Ersetzen Sie den Platzhalter `<path-to-certificate-file>` mit einem relativen oder vollqualifizierten Pfad zur Zertifikatdatei. AzCopy speichert den Pfad zu diesem Zertifikat, speichert jedoch keine Kopie des Zertifikats. Achten Sie darauf, dass das Zertifikat dort bleibt, wo es ist. Ersetzen Sie den Platzhalter `<certificate-password>` durch das Kennwort des Zertifikats. Ersetzen Sie den Platzhalter `<tenant-id>` mit der Mandanten-ID der Organisation, zu der das Speicherkonto gehört. Wählen Sie **Azure Active Directory > Eigenschaften > Verzeichnis-ID** im Azure-Portal aus, um die Mandanten-ID zu finden.

> [!NOTE]
> Verwenden Sie ggf. eine Eingabeaufforderung, um das Kennwort vom Benutzer zu erfassen. Auf diese Weise wird Ihr Kennwort in Ihrem Befehlsverlauf nicht angezeigt.

Führen Sie dann einen beliebigen azcopy-Befehl aus (beispielsweise: `azcopy list https://contoso.blob.core.windows.net`).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu AzCopy finden Sie unter [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md).

- Wenn Sie Fragen haben, Probleme melden oder allgemeines Feedback bereitstellen möchten, können Sie dies [auf der GitHub-Seite](https://github.com/Azure/azure-storage-azcopy) tun.
