---
title: Verschlüsselung in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Mit der Verschlüsselung in Azure Data Lake Storage Gen1 können Sie Ihre Daten schützen, Sicherheitsrichtlinien für Unternehmen implementieren und gesetzliche Vorschriften einhalten. Dieser Artikel enthält eine Übersicht über das Design und behandelt einige technische Aspekte der Implementierung.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: f0b24fd25a0fa95d6ed8561349987f9cb5fc5ee3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596123"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Verschlüsselung von Daten in Azure Data Lake Storage Gen1

Mit der Verschlüsselung in Azure Data Lake Storage Gen1 können Sie Ihre Daten schützen, Sicherheitsrichtlinien für Unternehmen implementieren und gesetzliche Vorschriften einhalten. Dieser Artikel enthält eine Übersicht über das Design und behandelt einige technische Aspekte der Implementierung.

Data Lake Storage Gen1 unterstützt die Verschlüsselung von ruhenden Daten und Daten im Übergang. Für ruhende Daten unterstützt Data Lake Storage Gen1 die standardmäßig aktivierte, transparente Verschlüsselung. Das bedeutet Folgendes:

* **Standardmäßig aktiviert**: Beim Erstellen eines neuen Data Lake Storage Gen1-Kontos wird die Verschlüsselung standardmäßig aktiviert. In Data Lake Storage Gen1 gespeicherte Daten werden daraufhin immer verschlüsselt, bevor sie auf persistenten Medien gespeichert werden. Das gilt für alle Daten und kann nach der Kontoerstellung nicht mehr geändert werden.
* **Transparent**: Daten werden von Data Lake Storage Gen1 vor der persistenten Speicherung automatisch verschlüsselt und vor dem Abrufen entschlüsselt. Die Verschlüsselung wird auf der Data Lake Storage Gen1-Ebene von einem Administrator konfiguriert und verwaltet. Die Datenzugriffs-APIs werden nicht geändert. Folglich sind auch keine verschlüsselungsbedingten Änderungen in Anwendungen und Diensten erforderlich, die mit Data Lake Storage Gen1 interagieren.

Daten im Übergang (auch als „Daten in Bewegung“ bezeichnet) werden in Data Lake Storage Gen1 ebenfalls immer verschlüsselt. Zusätzlich zur Verschlüsselung von Daten vor dem Speichern auf persistenten Medien werden Daten auch während der Übertragung immer über HTTPS geschützt. Für die Data Lake Storage Gen1-REST-Schnittstellen wird ausschließlich das HTTPS-Protokoll unterstützt. Die folgende Abbildung zeigt, wie Daten in Data Lake Storage Gen1 verschlüsselt werden:

![Abbildung der Datenverschlüsselung in Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Einrichten der Verschlüsselung mit Data Lake Storage Gen1

Die Verschlüsselung für Data Lake Storage Gen1 wird während der Kontoerstellung eingerichtet und ist standardmäßig immer aktiviert. Sie können die Schlüssel entweder selbst verwalten oder die Verwaltung durch Data Lake Storage Gen1 zulassen (dies ist die Standardeinstellung).

Weitere Informationen finden Sie in den [Ersten Schritten](./data-lake-store-get-started-portal.md).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Funktionsweise der Verschlüsselung in Data Lake Storage Gen1

Im Folgenden wird beschrieben, wie Sie Hauptverschlüsselungsschlüssel (Master Encryption Keys, MEKs) verwalten. Außerdem erhalten Sie eine Beschreibung der drei unterschiedlichen Schlüsseltypen, die Sie bei der Datenverschlüsselung für Data Lake Storage Gen1 verwenden können.

### <a name="master-encryption-keys"></a>Masterverschlüsselungsschlüssel

Data Lake Storage Gen1 bietet zwei Verwaltungsmodi für Hauptverschlüsselungsschlüssel (Master Encryption Keys, MEKs). Stellen Sie sich den Masterverschlüsselungsschlüssel vorerst einfach als Schlüssel der obersten Ebene vor. Zugriff auf den Hauptverschlüsselungsschlüssel ist erforderlich, um Data Lake Storage Gen1 gespeicherte Daten entschlüsseln zu können.

Zur Verwaltung des Masterverschlüsselungsschlüssels stehen die beiden folgenden Modi zur Verfügung:

*   Vom Dienst verwaltete Schlüssel
*   Vom Kunden verwaltete Schlüssel

In beiden Modi wird der Masterverschlüsselungsschlüssel geschützt, indem er Azure Key Vault gespeichert wird. Key Vault ist ein vollständig verwalteter, äußerst sicherer Dienst in Azure, der zur sicheren Aufbewahrung kryptografischer Schlüssel verwendet werden kann. Weitere Informationen finden Sie unter [Key Vault](https://azure.microsoft.com/services/key-vault).

Im Anschluss folgt eine kurze Gegenüberstellung der Funktionen, die in den beiden MEK-Verwaltungsmodi zur Verfügung stehen:

| Frage | Vom Dienst verwaltete Schlüssel | Vom Kunden verwaltete Schlüssel |
| -------- | -------------------- | --------------------- |
|Wie werden die Daten gespeichert?|Immer verschlüsselt (vor dem Speichern)|Immer verschlüsselt (vor dem Speichern)|
|Wo wird der Masterverschlüsselungsschlüssel gespeichert?|Key Vault|Key Vault|
|Werden Verschlüsselungsschlüssel unverschlüsselt außerhalb von Key Vault gespeichert? |Nein|Nein|
|Kann der MEK aus Key Vault abgerufen werden?|Nein. Wenn der MEK in Key Vault gespeichert wurde, kann er ausschließlich zur Ver- und Entschlüsselung verwendet werden.|Nein. Wenn der MEK in Key Vault gespeichert wurde, kann er ausschließlich zur Ver- und Entschlüsselung verwendet werden.|
|Wer ist der Eigentümer der Key Vault-Instanz und des MEK?|Der Data Lake Storage Gen1-Dienst|Sie sind Eigentümer der Key Vault-Instanz, die zu Ihrem Azure-Abonnement gehört. Der MEK in Key Vault kann per Software oder Hardware verwaltet werden.|
|Können Sie den Zugriff auf den MEK für den Data Lake Storage Gen1-Dienst sperren?|Nein|Ja. Sie können die Zugriffssteuerungslisten in Key Vault verwalten und Zugriffssteuerungseinträge für die Dienstidentität für den Data Lake Storage Gen1-Dienst entfernen.|
|Können Sie den MEK endgültig löschen?|Nein|Ja. Wenn Sie den MEK aus Key Vault löschen, können die Daten im Data Lake Storage Gen1-Konto von niemandem mehr entschlüsselt werden (auch nicht vom Data Lake Storage Gen1-Dienst). <br><br> Wenn Sie den MEK vor dem Löschen aus Key Vault explizit gesichert haben, ist eine Wiederherstellung des MEK und der Daten möglich. Wenn Sie den MEK allerdings vor dem Löschen aus Key Vault nicht gesichert haben, können die Daten im Data Lake Storage Gen1-Konto anschließend nicht mehr entschlüsselt werden.|


Abgesehen von dem Unterschied, wer den MEK und die Key Vault-Instanz verwaltet, in der er sich befindet, ist das Design bei beiden Modi gleich.

Beachten Sie beim Auswählen des Modus für die Masterverschlüsselungsschlüssel Folgendes:

*   Beim Bereitstellen eines Data Lake Storage Gen1-Kontos haben Sie die Wahl zwischen vom Kunden und vom Dienst verwalteten Schlüsseln.
*   Nach der Bereitstellung eines Data Lake Storage Gen1-Kontos kann der Modus nicht mehr geändert werden.

### <a name="encryption-and-decryption-of-data"></a>Ver- und Entschlüsselung von Daten

Im Rahmen der Datenverschlüsselung gibt es drei Arten von Schlüsseln. In der folgenden Tabelle erhalten Sie einen Überblick:

| Schlüssel                   | Abkürzung | Zugeordnet zu | Speicherort                             | type       | Hinweise                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Masterverschlüsselungsschlüssel | MEK          | Ein Data Lake Storage Gen1-Konto | Key Vault                              | Asymmetrisch | Kann von Data Lake Storage Gen1 oder von Ihnen verwaltet werden.                                                              |
| Datenverschlüsselungsschlüssel   | DEK          | Ein Data Lake Storage Gen1-Konto | Persistenter Speicher (vom Data Lake Storage Gen1-Dienst verwaltet) | Symmetrisch  | Der DEK wird vom MEK verschlüsselt. Der verschlüsselte DEK ist das, was auf einem persistenten Medium gespeichert wird. |
| Blockverschlüsselungsschlüssel  | BEK          | Einem Datenblock | Keine                                         | Symmetrisch  | Der BEK wird vom DEK und dem Datenblock abgeleitet.                                                      |

Das folgende Diagramm veranschaulicht dieses Konzepte:

![Schlüssel bei der Datenverschlüsselung](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgorithmus, wenn eine Datei entschlüsselt werden soll:
1.  Überprüfen Sie, ob der DEK für das Data Lake Storage Gen1-Konto zwischengespeichert und zur Verwendung bereit ist.
    - Falls nicht, lesen Sie den verschlüsselten DEK aus dem persistenten Speicher, und senden Sie ihn zur Entschlüsselung an Key Vault. Der verschlüsselte DEK wird im Arbeitsspeicher zwischengespeichert. Er kann nun verwendet werden.
2.  Gehen Sie für jeden Datenblock in der Datei wie folgt vor:
    - Lesen Sie den verschlüsselten Datenblock aus dem persistenten Speicher.
    - Generieren Sie den BEK auf der Grundlage des DEK und des verschlüsselten Datenblocks.
    - Entschlüsseln Sie die Daten mithilfe des BEK.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgorithmus, wenn ein Datenblock verschlüsselt werden soll:
1.  Überprüfen Sie, ob der DEK für das Data Lake Storage Gen1-Konto zwischengespeichert und zur Verwendung bereit ist.
    - Falls nicht, lesen Sie den verschlüsselten DEK aus dem persistenten Speicher, und senden Sie ihn zur Entschlüsselung an Key Vault. Der verschlüsselte DEK wird im Arbeitsspeicher zwischengespeichert. Er kann nun verwendet werden.
2.  Generieren Sie auf der Grundlage des DEKs einen eindeutigen BEK für den Datenblock.
3.  Verschlüsseln Sie den Datenblock mit dem BEK mithilfe der AES-256-Verschlüsselung.
4.  Speichern Sie den verschlüsselten Datenblock im persistenten Speicher.

> [!NOTE] 
> Der DEK wird immer durch den MEK verschlüsselt und entweder auf einem persistenten Medium gespeichert oder im Arbeitsspeicher zwischengespeichert.

## <a name="key-rotation"></a>Schlüsselrotation

Wenn Sie vom Kunden verwaltete Schlüssel verwenden, ist eine Rotation des MEK möglich. Informationen zum Einrichten eines Data Lake Storage Gen1-Kontos mit vom Kunden verwalteten Schlüsseln finden Sie in den [Ersten Schritten](./data-lake-store-get-started-portal.md).

### <a name="prerequisites"></a>Voraussetzungen

Beim Einrichten des Data Lake Storage Gen1-Kontos haben Sie ausgewählt, dass Sie Ihre eigenen Schlüssel verwenden. Diese Option kann nach der Kontoerstellung nicht mehr geändert werden. Bei den folgenden Schritten wird davon ausgegangen, dass Sie vom Kunden verwaltete Schlüssel verwenden (d.h. Sie haben Ihre eigenen Schlüssel aus Key Vault ausgewählt).

Wenn Sie die Standardverschlüsselungsoptionen verwenden, werden Ihre Daten immer durch von Data Lake Storage Gen1 verwaltete Schlüssel verschlüsselt. Bei dieser Option besteht die Möglichkeit der Schlüsselrotation nicht, da diese von Data Lake Storage Gen1 verwaltet werden.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Rotieren des MEK in Data Lake Storage Gen1

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zu der Key Vault-Instanz mit den Schlüsseln, die Ihrem Data Lake Storage Gen1-Konto zugeordnet sind. Wählen Sie **Schlüssel** aus.

    ![Screenshot von Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Wählen Sie den Schlüssel aus, der Ihrem Data Lake Storage Gen1-Konto zugeordnet ist, und erstellen Sie eine neue Version dieses Schlüssels. Data Lake Storage Gen1 unterstützt zurzeit nur die Schlüsselrotation zu einer neuen Version eines Schlüssels. Die Rotation zu einem anderen Schlüssel wird nicht unterstützt.

   ![Screenshot des Fensters „Schlüssel“, „Neue Version“ hervorgehoben](./media/data-lake-store-encryption/keynewversion.png)

4. Navigieren Sie zum Data Lake Storage Gen1-Konto, und wählen Sie **Verschlüsselung** aus.

   ![Screenshot des Fensters des Data Lake Storage Gen1-Speicherkontos, Option „Verschlüsselung“ hervorgehoben](./media/data-lake-store-encryption/select-encryption.png)

5. Sie werden darüber benachrichtigt, dass eine neue Version des Schlüssels verfügbar ist. Klicken Sie auf **Schlüssel rotieren**, um den Schlüssel auf die neue Version zu aktualisieren.

   ![Screenshot des Data Lake Storage Gen1-Fensters mit der Benachrichtigung, Option „Schlüssel rotieren“ hervorgehoben](./media/data-lake-store-encryption/rotatekey.png)

Der Vorgang dauert in der Regel weniger als zwei Minuten, und bei der Schlüsselrotation sind keine Ausfallzeiten zu erwarten. Nach Abschluss des Vorgangs wird die neue Version des Schlüssels verwendet.

> [!IMPORTANT]
> Nach Abschluss der Schlüsselrotation wird die alte Version des Schlüssels nicht mehr aktiv zum Verschlüsseln neuer Daten verwendet. Es kann jedoch Fälle geben, in denen für den Zugriff auf ältere Daten der alte Schlüssel benötigt wird. Löschen Sie den alten Schlüssel nicht, damit solche älteren Daten gelesen werden können.