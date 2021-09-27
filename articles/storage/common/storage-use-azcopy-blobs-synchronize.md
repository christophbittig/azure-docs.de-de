---
title: Synchronisieren mit Azure Blob Storage mithilfe von AzCopy v10 | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Sammlung von AzCopy-Beispielbefehlen, die Sie bei der Synchronisierung mit Azure Blob Storage unterstützen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 84c8c1e0eeb402185b7451ce953581d23b39c860
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624821"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Synchronisieren mit Azure Blob Storage mithilfe von AzCopy

Sie können lokalen Speicher mit Azure Blob Storage synchronisieren, indem Sie das Befehlszeilenprogramm AzCopy v10 verwenden.

Sie können die Inhalte eines lokalen Dateisystems mit einem Blobcontainer synchronisieren. Sie können auch Container und virtuelle Verzeichnisse miteinander synchronisieren. Die Synchronisierung erfolgt unidirektional. Anders gesagt: Sie wählen aus, welcher der beiden Endpunkte die Quelle und welcher das Ziel ist. Bei der Synchronisierung werden auch Server-zu-Server-APIs verwendet. Die Beispiele in diesem Abschnitt können auch für Konten verwendet werden, die über einen hierarchischen Namespace verfügen.

> [!NOTE]
> Das aktuelle Release von AzCopy synchronisiert nicht zwischen anderen Quellen und Zielen (beispielsweise: Dateispeicher oder Amazon Web Services (AWS) S3-Buckets).

Beispiele für andere Arten von Aufgaben, z. B. das Hochladen von Dateien, das Herunterladen von Blobs oder das Kopieren von Blobs zwischen Konten, finden Sie unter den Links im Abschnitt [Nächste Schritte](#next-steps) dieses Artikels.

## <a name="get-started"></a>Erste Schritte

Lesen Sie den Artikel [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu erfahren, wie Sie dem Speicherdienst Autorisierungsanmeldeinformationen bereitstellen können.

> [!NOTE]
> In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie Anmeldeinformationen für Autorisierung mithilfe von Azure Active Directory (Azure AD) bereitgestellt haben.
>
> Wenn Sie lieber ein SAS-Token für die Autorisierung des Zugriffs auf Blobdaten verwenden möchten, können Sie dieses Token in jedem AzCopy-Befehl an die Ressourcen-URL anfügen. Beispiel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Richtlinien

- Mit dem Befehl [sync](storage-ref-azcopy-sync.md) werden Dateinamen und die Zeitstempel der letzten Änderung verglichen. Legen Sie das optionale Flag `--delete-destination` auf den Wert `true` oder `prompt` fest, um Dateien im Zielverzeichnis zu löschen, wenn diese im Quellverzeichnis nicht mehr vorhanden sind.

- Wenn Sie das Flag `--delete-destination` auf `true` festlegen, löscht AzCopy Dateien, ohne zur Bestätigung aufzufordern. Wenn eine Bestätigungsaufforderung angezeigt werden soll, bevor AzCopy eine Datei löscht, legen Sie das `--delete-destination`-Flag auf `prompt` fest.

- Wenn Sie das Flag `--delete-destination` auf `prompt` oder `false` festlegen möchten, sollten Sie den Befehl [copy](storage-ref-azcopy-copy.md) anstelle des Befehls [sync](storage-ref-azcopy-sync.md) verwenden und den Parameter `--overwrite` auf `ifSourceNewer` festlegen. Der Befehl [copy](storage-ref-azcopy-copy.md) verbraucht weniger Arbeitsspeicher und verursacht weniger Abrechnungskosten, weil die Quelle oder das Ziel bei einem Kopiervorgang vor dem Verschieben von Dateien nicht indiziert werden muss.

- Um ein versehentliches Löschen zu verhindern, aktivieren Sie das Feature [Vorläufiges Löschen](../blobs/soft-delete-blob-overview.md), bevor Sie das Flag `--delete-destination=prompt|true` verwenden.

- Der Computer, auf dem Sie den Synchronisierungsbefehl ausführen, sollte über eine genaue Systemuhr verfügen, weil die Zeiten der letzten Änderung maßgeblich sind, um über die Übertragung einer Datei zu entscheiden. Wenn die Zeit Ihres Systems erheblich abweicht, sollten Sie Dateien am Ziel nicht zu nahe an dem Zeitpunkt ändern, zu dem Sie einen Synchronisierungsbefehl ausführen möchten.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualisieren eines Containers mit Änderungen an einem lokalen Dateisystem

In diesem Fall ist der Container das Ziel und das lokale Dateisystem die Quelle.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualisieren eines lokalen Dateisystems mit Änderungen an einem Container

In diesem Fall ist das lokale Dateisystem das Ziel und der Container die Quelle.

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

**Syntax**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Aktualisieren eines Containers mit Änderungen in einem anderen Container

Der erste Container in diesem Befehl ist die Quelle. Das zweite ist das Ziel. Fügen Sie unbedingt an jede Quell-URL ein SAS-Token an.

Wenn Sie Autorisierungsanmeldeinformationen mithilfe von Azure Active Directory (Azure AD) angeben, können Sie das SAS-Token nur in der Ziel-URL weglassen. Stellen Sie sicher, dass Sie die richtigen Rollen in Ihrem Zielkonto eingerichtet haben. Weitere Informationen finden Sie unter [Option 1: Verwenden von Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Aktualisieren eines Verzeichnisses mit Änderungen in einem Verzeichnis in einem anderen Container

Das erste Verzeichnis in diesem Befehl ist die Quelle. Das zweite ist das Ziel. Fügen Sie unbedingt an jede Quell-URL ein SAS-Token an.

Wenn Sie Autorisierungsanmeldeinformationen mithilfe von Azure Active Directory (Azure AD) angeben, können Sie das SAS-Token nur in der Ziel-URL weglassen. Stellen Sie sicher, dass Sie die richtigen Rollen in Ihrem Zielkonto eingerichtet haben. Weitere Informationen finden Sie unter [Option 1: Verwenden von Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Beispiel**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Synchronisieren mit optionalen Flags

Sie können den Synchronisierungsvorgang mit optionalen Flags optimieren. Hier sind einige Beispiele angegeben.

|Szenario|Flag|
|---|---|
|Angeben, wie streng MD5-Hashes beim Herunterladen überprüft werden sollen|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
|Ausschließen von Dateien basierend auf einem Muster|**--exclude-path**|
|Angeben, wie detailliert die synchronisierungsbezogenen Protokolleinträge sein sollen|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|

Eine vollständige Liste der Flags finden Sie unter [Optionen](storage-ref-azcopy-sync.md#options).

> [!NOTE]
> Standardmäßig ist das Flag `--recursive` auf `true` festgelegt. Die Flags `--exclude-pattern` und `--include-pattern` gelten nur für Dateinamen und nicht für andere Teile des Dateipfads.

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in diesen Artikeln:

- [Beispiele: Hochladen](storage-use-azcopy-blobs-upload.md)
- [Beispiele: Herunterladen](storage-use-azcopy-blobs-download.md)
- [Beispiele: Kopieren zwischen Konten](storage-use-azcopy-blobs-copy.md)
- [Beispiele: Amazon S3-Buckets](storage-use-azcopy-s3.md)
- [Beispiele: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Beispiele: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Lesen Sie diese Artikel, um Einstellungen zu konfigurieren, die Leistung zu optimieren und Probleme zu beheben:

- [AzCopy v10-Konfigurationseinstellungen (Azure Storage)](storage-ref-azcopy-configuration-settings.md)
- [Optimieren der Leistung von AzCopy mit Azure Storage](storage-use-azcopy-optimize.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)
