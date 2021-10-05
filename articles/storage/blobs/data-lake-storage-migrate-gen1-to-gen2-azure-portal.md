---
title: Migrieren über das Azure-Portal (Data Lake Storage Gen1 zu Gen2)
description: Sie können die Migration zwischen Azure Data Lake Storage Gen1 und Azure Data Lake Storage Gen2 vereinfachen, indem Sie das Azure-Portal verwenden.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3d78ba06112fce8dff64f2091e434d8f502634d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662740"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2-by-using-the-azure-portal-preview"></a>Migrieren von Azure Data Lake Storage von Gen1 zu Gen2 mithilfe des Azure-Portals (Vorschau)

Mithilfe des Azure-Portals können Sie die Anzahl der für eine Migration erforderlichen Schritte reduzieren. Dabei werden Daten und Metadaten (z. B. Zeitstempel und ACLs) automatisch auf Ihr Gen2-fähiges Konto übertragen. Wenn Sie eine vollständige Migration durchführen, müssen Sie Ihre Workloads nicht auf Gen2 umleiten, da Anforderungen automatisch umgeleitet werden.

> [!IMPORTANT]
> Die Migration von Gen1 zu Gen2 mithilfe des Azure-Portals befindet sich derzeit in der Vorschauphase.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

In diesem Artikel werden die folgenden Aufgaben beschrieben:

- Schritt 1: Registrieren für die Vorschauversion

- Schritt 2: Erstellen eines Speicherkontos mit Gen2-Funktionen

- Schritt 3: Überprüfen von RBAC-Rollenzuweisungen

- Schritt 4: Durchführen der Migration

- Schritt 5: Testen der Anwendungen

- Schritt 6: Abschließen der Migration

Lesen Sie unbedingt die allgemeine Anleitung zur Migration von Gen1 zu Gen2. Weitere Informationen finden Sie unter [Migrieren von Azure Data Lake Storage von Gen1 zu Gen2](data-lake-storage-migrate-gen1-to-gen2.md).

> [!NOTE]
> Zur besseren Lesbarkeit wird in diesem Artikel mit dem Begriff *Gen1* auf Azure Data Lake Storage Gen1 verwiesen und mit dem Begriff *Gen2* auf Azure Data Lake Storage Gen2.

## <a name="enroll-in-the-preview"></a>Registrieren für die Vorschauversion

Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4SeyCfCfrtBlHWFupvoz_BUMEFNQzBSQTE0OU1aM0hXMDlBNEwzVTYyRy4u&wdLOR=cBC075B83-9324-4399-B94E-05A919D007C9). Erstellen Sie nach der Registrierung ein Speicherkonto mit Gen2-Funktionen (siehe nächster Abschnitt).

Microsoft wird Sie in etwa sieben Tagen kontaktieren, um zu bestätigen, dass Ihr neues Konto für die Migration aktiviert ist.

## <a name="create-a-storage-account-with-gen2-capabilities"></a>Erstellen eines Speicherkontos mit Gen2-Funktionen

Azure Data Lake Storage Gen2 ist kein dedizierter Speicher- oder Dienstkontotyp. Vielmehr handelt es sich hierbei um verschiedene Funktionen, die Sie erwerben können, indem Sie das Feature **Hierarchischer Namespace** eines Azure-Speicherkontos aktivieren. Informationen zum Erstellen eines Kontos mit Gen2-Funktionen finden Sie unter [Erstellen eines Speicherkontos für die Verwendung mit Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

Konfigurieren Sie die Einstellungen beim Erstellen des Kontos mit den folgenden Werten.

| Einstellung | Wert |
|--|--|
| **Speicherkontoname** | Ein beliebiger Name Ihrer Wahl. Dieser Name muss nicht mit dem Namen Ihres Gen1-Kontos übereinstimmen und kann in einem beliebigen Abonnement Ihrer Wahl enthalten sein. |
| **Location** | Die Region, die vom Data Lake Storage Gen1-Konto verwendet wird |
| **Replikation** | LRS oder ZRS |
| **TLS-Mindestversion** | 1.0 |
| **NFS v3** | Disabled |
| **Hierarchischer Namespace** | Aktiviert |

> [!NOTE]
> Mit dem Migrationstool im Azure-Portal werden keine Kontoeinstellungen verschoben. Daher müssen Sie nach dem Erstellen des Kontos Einstellungen wie Verschlüsselung, Netzwerkfirewalls und Datenschutzeinstellungen manuell konfigurieren.

## <a name="verify-rbac-role-assignments"></a>Überprüfen von RBAC-Rollenzuweisungen

Stellen Sie bei Gen2 sicher, dass Ihrer Azure Active Directory-Benutzeridentität (Azure AD) im Bereich des Speicherkontos, der übergeordneten Ressourcengruppe oder des Abonnements die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde.

Stellen Sie bei Gen1 sicher, dass Ihrer Azure AD-Identität im Bereich des Gen1-Kontos, der übergeordneten Ressourcengruppe oder des Abonnements die Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) zugewiesen wurde.

## <a name="perform-the-migration"></a>Durchführen der Migration

Überlegen Sie vor Beginn, ob Sie nur Daten kopieren oder eine vollständige Migration durchführen möchten.

Wenn Sie nur Daten kopieren, bleiben beide Konten nach Abschluss der Migration aktiv. Während der Migration ist Ihr Gen1-Konto schreibgeschützt. Anschließend können Sie Ihre Computeworkloads zur Verwendung Ihres neuen Gen2-fähigen Speicherkontos aktualisieren. Nachdem Sie sichergestellt haben, dass Ihre Anwendungen und Workloads erwartungsgemäß funktionieren, können Sie das Gen1-Konto auflösen. Diese Option wird von Microsoft empfohlen.

Wenn Sie eine vollständige Migration durchführen, werden Daten von Gen1 in Gen2 kopiert. Anschließend wird Ihr Gen1-URI an Ihren Gen2-URI umgeleitet. Nach Abschluss der Migration haben Sie keinen Zugriff mehr auf Ihr Gen1-Konto, und alle Gen1-Anforderungen werden an Ihr Gen2-fähiges Konto umgeleitet. Ihr Gen1-Konto ist nicht mehr für Datenvorgänge verfügbar. Darüber hinaus werden Ihnen die Daten im Gen1-Konto nicht mehr in Rechnung gestellt. Sie können Ihr Gen1-Konto löschen, sobald die Pipelines in Ihrem Gen2-fähigen Konto ausgeführt werden.

> [!NOTE]
> Azure Data Lake Analytics-Anwendungen werden von Gen2 nicht unterstützt. Wenn Sie über eine dieser Workloads verfügen, verschieben Sie sie vor der Migration von Gen1 zu Gen2 in Azure Synapse Analytics oder eine andere unterstützte Workload.

### <a name="option-1-copy-data-from-gen1-to-gen2"></a>Option 1: Kopieren von Daten von Gen1 in Gen2

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie Ihr Data Lake Storage Gen1-Konto, und rufen Sie die Kontoübersicht auf.

3. Wählen Sie die Schaltfläche **Daten migrieren** aus.

   > [!div class="mx-imgBorder"]
   > ![Schaltfläche zum Migrieren](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. Wählen Sie **Daten in ein neues Gen2-Konto kopieren** aus.

   > [!div class="mx-imgBorder"]
   > ![Option zum Kopieren von Daten](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-data-option.png)

5. Erteilen Sie Microsoft die Zustimmung zum Durchführen der Datenmigration, indem Sie das Kontrollkästchen aktivieren. Klicken Sie anschließend auf die Schaltfläche **Anwenden**.

   > [!div class="mx-imgBorder"]
   > ![Kontrollkästchen zum Erteilen der Zustimmung](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   Während Ihre Daten migriert werden, ist Ihr Gen1-Konto schreibgeschützt und Ihr Gen2-fähiges Konto deaktiviert. Nach Abschluss der Migration können Sie in beiden Konten lesen und schreiben.

   Sie können die Migration jederzeit beenden, indem Sie die Schaltfläche **Migration beenden** auswählen.

   > [!div class="mx-imgBorder"]
   > ![Option zum Beenden der Migration](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

### <a name="option-2-perform-a-complete-migration"></a>Option 2: Durchführen einer vollständigen Migration

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie Ihr Data Lake Storage Gen1-Konto, und rufen Sie die Kontoübersicht auf.

3. Wählen Sie die Schaltfläche **Daten migrieren** aus.

   > [!div class="mx-imgBorder"]
   > ![Schaltfläche „Migrieren“](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. Wählen Sie **Migration zu einem neuen Gen2-Konto abschließen** aus.

   > [!div class="mx-imgBorder"]
   > ![Option zum Abschließen der Migration](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-complete-option.png)

5. Erteilen Sie Microsoft die Zustimmung zum Durchführen der Datenmigration, indem Sie das Kontrollkästchen aktivieren. Klicken Sie anschließend auf die Schaltfläche **Anwenden**.

   > [!div class="mx-imgBorder"]
   > ![Kontrollkästchen zum Erteilen der Zustimmung](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   - Während Ihre Daten migriert werden, ist Ihr Gen1-Konto schreibgeschützt und Ihr Gen2-fähiges Konto deaktiviert.
   - Während der Gen1-URI umgeleitet wird, sind beide Konten deaktiviert.
   - Nach Abschluss der Migration ist Ihr Gen1-Konto deaktiviert. In Ihrem Gen2-fähigen Konto können Sie lesen und schreiben.

   Sie können die Migration vor der Umleitung des URI jederzeit beenden, indem Sie die Schaltfläche **Migration beenden** auswählen.

   > [!div class="mx-imgBorder"]
   > ![Schaltfläche „Migration beenden“](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

## <a name="migrate-workloads-and-applications"></a>Migrieren von Workloads und Anwendungen

1. Konfigurieren Sie [Dienste in Ihren Workloads](./data-lake-storage-supported-azure-services.md), sodass sie auf Ihren Gen2-Endpunkt verweisen.

2. Aktualisieren Sie Anwendungen für die Verwendung von Gen2-APIs. Weitere Informationen finden Sie in diesen Leitfäden:

   | Environment | Artikel |
   |--------|-----------|
   |Azure Storage-Explorer |[Verwenden von Azure Storage-Explorer zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
   |.NET |[Verwenden von .NET zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
   |Java|[Verwenden von Java zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
   |Python|[Verwenden von Python zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
   |JavaScript (Node.js)|[Verwenden des JavaScript-SDK in Node.js zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-javascript.md)|
   |REST-API |[Azure Data Lake Store-REST-API](/rest/api/storageservices/data-lake-storage-gen2)|

3. Aktualisieren Sie Skripts für die Verwendung der [PowerShell-Cmdlets](data-lake-storage-directory-file-acl-powershell.md) und [Azure CLI-Befehle](data-lake-storage-directory-file-acl-cli.md) für Data Lake Storage Gen2.

4. Suchen Sie nach URI-Verweisen, die die Zeichenfolge `adl://` in Codedateien oder in Databricks-Notebooks, Apache Hive-HQL-Dateien oder anderen Dateien in Ihren Workloads enthalten. Ersetzen Sie diese Verweise durch den [für Gen2 formatierten URI](data-lake-storage-introduction-abfs-uri.md) Ihres neuen Speicherkontos. Beispiel: Der Gen1-URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` könnte in `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` geändert werden.

## <a name="leverage-the-gen1-compatibility-layer-optional"></a>Nutzen der Gen1-Kompatibilitätsebene (optional)

Microsoft bietet Anwendungskompatibilität mit eingeschränkter Funktionalität, sodass Ihre Anwendungen weiterhin Gen1-APIs für die Interaktion mit Daten in Ihrem Gen2-fähigen Konto verwenden können. Die Kompatibilitätsebene wird auf dem Server ausgeführt, sodass keine Installation erforderlich ist.

> [!IMPORTANT]
> Microsoft empfiehlt diese Funktion nicht als Ersatz für die Migration von Workloads und Anwendungen. Die Unterstützung für die Gen1-Kompatibilitätsebene endet, wenn Gen1 am 29. Februar 2024 eingestellt wird.

Damit die Kompatibilitätsebene möglichst wenige Probleme bereitet, verwenden Sie die Gen1-SDKs der folgenden Versionen (oder höher).

   | Sprache | SDK-Version |
   |--|--|
   | **.NET** | [2.3.9](https://github.com/Azure/azure-data-lake-store-net/blob/master/CHANGELOG.md) |
   | **Java** | [1.1.21](https://github.com/Azure/azure-data-lake-store-java/blob/master/CHANGES.md) |
   | **Python** | [0.0 51](https://github.com/Azure/azure-data-lake-store-python/blob/master/HISTORY.rst) |

Die folgenden Funktionen werden in Gen2 und somit auch in der Kompatibilitätsebene nicht unterstützt.

- ListStatus-API-Option für  ListBefore für einen Eintrag.

- ListStatus-API mit über 4000 Dateien ohne Fortsetzungstoken.

- Blockcodierung für Anfügevorgänge.

- Alle API-Aufrufe, bei denen https://management.azure.com/   als Azure AD-Tokenzielgruppe (Azure Active Directory) verwendet wird.

- Datei- oder Verzeichnisnamen, die nur Leerzeichen oder Tabstoppzeichen enthalten, mit einem Punkt (`.`) enden, einen Doppelpunkt (`:`) oder mehrere aufeinander folgende Schrägstriche (`//`) enthalten.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="how-much-does-the-data-migration-cost"></a>Wie viel kostet die Datenmigration?

Während der Datenmigration werden Ihnen die Datenspeicherung und die Transaktionen des Gen1-Kontos in Rechnung gestellt. Wenn Sie die Option wählen, bei der nur Daten kopiert werden, werden Ihnen nach Abschluss der Migration die Datenspeicherung und die Transaktionen für beide Konten in Rechnung gestellt. Wenn Sie vermeiden möchten, dass Ihnen das Gen1-Konto in Rechnung gestellt wird, müssen Sie das Gen1-Konto löschen. Löschen Sie das Gen1-Konto, nachdem Sie die Aktualisierung Ihrer Anwendungen abgeschlossen haben. Wenn Sie sich für eine vollständige Migration entscheiden, werden Ihnen nach der Migration nur die Datenspeicherung und die Transaktionen des Gen2-fähigen Kontos in Rechnung gestellt.

#### <a name="after-the-migration-completes-can-i-choose-to-go-back-to-using-the-gen1-account"></a>Kann ich nach Abschluss der Migration das Gen1-Konto noch verwenden?

Dies wird nicht unterstützt, da nach Abschluss der Migration ein Zugriff auf die Daten in Ihrem Gen1-Konto nicht mehr möglich ist. Sie können das Gen1-Konto weiterhin im Azure-Portal anzeigen. Wenn Sie so weit sind, können Sie das Konto löschen.

#### <a name="i-would-like-to-enable-geo-redundant-storage-grs-on-the-gen2-account-how-do-i-do-that"></a>Ich möchte georedundanten Speicher (GRS) auf dem Gen2-Konto aktivieren.Wie geht das?

Nach Abschluss der Migration können Sie sowohl bei der Option „Daten kopieren“ als auch bei der Option „Migration abschließen“ die Redundanzoption in GRS ändern, sofern Sie nicht vorhaben, die Anwendungskompatibilitätsebene zu verwenden. Bei Konten, für die die GRS-Redundanz verwendet wird, funktioniert die Anwendungskompatibilität nicht.

#### <a name="gen1-doesnt-have-containers-and-gen2-has-them---what-should-i-expect"></a>Gen1 hat keine Container, aber Gen2. Was erwartet mich?

Wenn wir die Daten in Ihr Gen2-fähiges Konto kopieren, erstellen wir automatisch einen Container mit dem Namen `Gen1`. Wenn Sie nur Daten kopieren möchten, können Sie diesen Container nach dem Kopieren der Daten umbenennen. Wenn Sie eine vollständige Migration durchführen und die Anwendungskompatibilitätsebene verwenden möchten, sollten Sie den Namen des Containers dagegen nicht ändern. Wenn Sie die Kompatibilitätsebene nicht mehr verwenden möchten, können Sie den Namen des Containers ändern.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Migration im Allgemeinen. Weitere Informationen finden Sie unter [Migrieren von Azure Data Lake Storage von Gen1 zu Gen2](data-lake-storage-migrate-gen1-to-gen2.md).
