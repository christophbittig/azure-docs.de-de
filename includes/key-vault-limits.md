---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 05/28/2021
ms.author: ambapat
ms.openlocfilehash: 2918ae1a7fcca250de91e61ecc9b2c64a2d9a0aa
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019134"
---
Der Azure Key Vault-Dienst unterstützt zwei Arten von Ressourcen: Tresore und verwaltete HSMs. In den beiden folgenden Abschnitten werden jeweils die entsprechenden Diensteinschränkungen beschrieben.

### <a name="resource-type-vault"></a>Ressourcentyp: Tresor

In diesem Abschnitt werden die Diensteinschränkungen für den Ressourcentyp `vaults` beschrieben.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Schlüsseltransaktionen (maximale Transaktionen innerhalb von 10 Sekunden pro Tresor und Region<sup>1</sup>):

|Schlüsseltyp|HSM-Schlüssel<br>Erstellungsschlüssel|HSM-Schlüssel<br>Alle anderen Transaktionen|Softwareschlüssel<br>Erstellungsschlüssel|Softwareschlüssel<br>Alle anderen Transaktionen|
|:---|---:|---:|---:|---:|
|RSA 2.048 Bit|5|1\.000|10|2\.000|
|RSA 3.072 Bit|5|250|10|500|
|RSA 4.096 Bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2\.000|
|ECC P-384|5|1\.000|10|2\.000|
|ECC P-521|5|1\.000|10|2\.000|
|ECC SECP256K1|5|1\.000|10|2\.000|
||||||

> [!NOTE]
> In der vorstehenden Tabelle ist zu erkennen, dass für RSA-Softwareschlüssel mit 2.048 Bit 2.000 GET-Transaktionen pro 10 Sekunden zulässig sind. Für RSA-HSM-Schlüssel mit 2.048 Bit sind 1.000 GET-Transaktionen pro 10 Sekunden zulässig.
>
> Die genannten Schwellenwerte für die Drosselung sind gewichtet, und ihre Summe wird erzwungen. Wie in der vorherigen Tabelle gezeigt fällt für das Ausführen von GET-Vorgängen mit RSA-HSM-Schlüsseln bei Verwendung eines 4.096-Bit-Schlüssels ein achtmal größerer Aufwand als mit einem 2.048-Bit-Schlüssel an. Der Grund dafür ist: 1.000/125 = 8.
>
> Innerhalb eines 10-Sekunden-Intervalls kann ein Azure Key Vault-Client *nur einen* der folgenden Vorgänge ausführen, bevor der HTTP-Statuscode `429` zur Drosselung auftritt:
> - 2\.000 GET-Transaktionen für RSA 2.048-Bit-Softwareschlüssel
> - 1\.000 GET-Transaktionen für RSA 2.048-Bit-HSM-Schlüssel
> - 125 GET-Transaktionen für RSA 4.096-Bit-HSM-Schlüssel
> - 124 GET-Transaktionen für RSA 4.096-Bit-HSM-Schlüssel und 8 GET-Transaktionen für RSA 2.048-Bit-HSM-Schlüssel

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimnisse, Schlüssel für verwaltete Speicherkonten und Tresortransaktionen:

| Transaktionstyp | Maximal zulässige Transaktionen innerhalb von 10 Sekunden pro Tresor und Region<sup>1</sup> |
| --- | --- |
| Alle Transaktionen |2\.000 |

Informationen zur Handhabung der Drosselung bei Überschreiten dieser Grenzwerte finden Sie unter [Anleitung zur Drosselung von Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> Für alle Transaktionsarten gilt als abonnementweiter Grenzwert das Fünffache des Schlüsseltresorlimits. „HSM – andere Transaktionen“ pro Abonnement ist beispielsweise auf 5.000 Transaktionen in 10 Sekunden pro Abonnent beschränkt.

#### <a name="backup-keys-secrets-certificates"></a>Sichern von Schlüsseln, Geheimnissen, Zertifikaten

Wenn Sie ein Schlüsseltresorobjekt (Geheimnis, Schlüssel oder Zertifikat) sichern, wird das Objekt beim Sicherungsvorgang als verschlüsseltes Blob heruntergeladen. Dieses Blob kann außerhalb von Azure nicht entschlüsselt werden. Um verwendbare Daten aus diesem Blob zu erhalten, müssen Sie das Blob in einem Schlüsseltresor innerhalb desselben Azure-Abonnements und derselben Azure-Geografie wiederherstellen.

| Transaktionstyp | Maximal zulässige Schlüsseltresor-Objektversionen |
| --- | --- |
| Sichern einzelner Schlüssel, Geheimnisse, Zertifikate |500 |

> [!NOTE]
> Wenn Sie beim Sichern eines Schlüssels, eines Geheimnisses oder eines Zertifikatobjekts die maximal mögliche Anzahl von Versionen überschreiten, tritt ein Fehler auf. Es ist nicht möglich, frühere Versionen eines Schlüssels, Geheimnisses oder Zertifikats zu löschen. 

### <a name="limits-on-count-of-keys-secrets-and-certificates"></a>Grenzwerte für Anzahl von Schlüsseln, Geheimnissen und Zertifikaten:

Für Key Vault ist die Anzahl von Schlüsseln, Geheimnissen oder Zertifikaten, die in einem Tresor gespeichert werden können, nicht beschränkt. Die Transaktionsgrenzwerte für den Tresor sollten berücksichtigt werden, um sicherzustellen, dass Vorgänge nicht gedrosselt werden.

Von Key Vault wird die Anzahl von Versionen eines Geheimnisses, Schlüssels oder Zertifikats nicht beschränkt, aber die Speicherung einer großen Anzahl von Versionen (mehr als 500) kann die Leistung von Sicherungsvorgängen beeinträchtigen. Weitere Informationen finden Sie unter [Sicherung in Azure Key Vault](../articles/key-vault/general/backup.md).

#### <a name="azure-private-link-integration"></a>Azure Private Link-Integration

> [!NOTE]
> Die Anzahl der Schlüsseltresore mit den pro Abonnement aktivierten privaten Endpunkten ist ein anpassbarer Grenzwert. Der gezeigte Grenzwert ist der Standardwert. Wenn Sie eine Erhöhung des Grenzwerts für Ihren Dienst anfordern möchten, können Sie eine Supportanfrage erstellen. Diese wird dann von uns für Ihren speziellen Fall geprüft.

| Resource | Begrenzung |
| -------- | -----:|
| Private Endpunkte pro Schlüsseltresor | 64 |
| Schlüsseltresore mit privaten Endpunkten pro Abonnement | 400 |

### <a name="resource-type-managed-hsm"></a>Ressourcentyp: verwaltetes HSM

In diesem Abschnitt werden die Diensteinschränkungen für den Ressourcentyp `managed HSM` beschrieben.

#### <a name="object-limits"></a>Objektgrenzwerte

|Element|Grenzwerte|
|----|------:|
Anzahl von HSM-Instanzen pro Abonnement und Region|5 
Anzahl von Schlüsseln pro HSM-Pool|5.000
Anzahl von Versionen pro Schlüssel|100
Anzahl benutzerdefinierter Rollendefinitionen pro HSM|50
Anzahl von Rollenzuweisungen im HSM-Bereich|50
Anzahl von Rollenzuweisungen in jedem einzelnen Schlüsselbereich|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Transaktionsgrenzwerte für administrative Vorgänge (Anzahl von Vorgängen pro Sekunde und HSM-Instanz)
|Vorgang |Anzahl von Vorgängen pro Sekunde|
|----|------:|
Alle RBAC-Vorgänge<br/>(einschließlich aller CRUD-Vorgänge für Rollendefinitionen und -zuweisungen)|5
Vollständige HSM-Sicherung/-Wiederherstellung<br/>(nur ein gleichzeitiger Sicherungs- oder Wiederherstellungsvorgang pro HSM-Instanz wird unterstützt)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Transaktionsgrenzwerte für kryptografische Vorgänge (Anzahl von Vorgängen pro Sekunde und HSM-Instanz)

- Jede verwaltete HSM-Instanz umfasst drei HSM-Partitionen mit Lastenausgleich. Bei den Durchsatzgrenzwerten handelt es sich um eine Funktion der zugrunde liegenden Hardwarekapazität, die der jeweiligen Partition zugeordnet ist. Die folgenden Tabellen geben Aufschluss über den maximalen Durchsatz mit mindestens einer verfügbaren Partition. Der tatsächliche Durchsatz kann bis zu dreimal höher sein, wenn alle drei Partitionen verfügbar sind.
- Bei den angegebenen Durchsatzgrenzwerten wird von der Verwendung eines einzelnen Schlüssels ausgegangen, um maximalen Durchsatz zu erzielen. Bei Verwendung eines einzelnen RSA-2048-Schlüssels liegt der maximale Durchsatz beispielsweise bei 1.100 Signierungsvorgängen. Wenn Sie 1.100 unterschiedliche Schlüssel mit jeweils einer Transaktion pro Sekunde verwenden, kann dieser Durchsatz nicht erreicht werden.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-Schlüsselvorgänge (Anzahl von Vorgängen pro Sekunde und HSM-Instanz)

|Vorgang|2\.048 Bit|3\.072 Bit|4\.096 Bit|
|--|--:|--:|--:|
Schlüssel erstellen|1| 1| 1
Schlüssel löschen (vorläufiges Löschen)|10|10|10 
Schlüssel endgültig löschen|10|10|10 
Sichern eines Schlüssels|10|10|10 
Schlüssel wiederherstellen|10|10|10 
Schlüsselinformationen abrufen|1100|1100|1100
Verschlüsseln|10000|10000|6000
Entschlüsseln|1100|360|160
Umschließen|10000|10000|6000
Aufheben der Umschließung|1100|360|160
Signieren|1100|360|160
Überprüfung|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC-Schlüsselvorgänge (Anzahl von Vorgängen pro Sekunde und HSM-Instanz)

Die folgende Tabelle gibt Aufschluss über die Anzahl von Vorgängen pro Sekunde für die einzelnen Kurventypen:

|Vorgang|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Schlüssel erstellen| 1| 1| 1| 1
Schlüssel löschen (vorläufiges Löschen)|10|10|10|10
Schlüssel endgültig löschen|10|10|10|10
Sichern eines Schlüssels|10|10|10|10
Schlüssel wiederherstellen|10|10|10|10
Schlüsselinformationen abrufen|1100|1100|1100|1100
Signieren|260|260|165|56
Überprüfung|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-Schlüsselvorgänge (Anzahl von Vorgängen pro Sekunde und HSM-Instanz)
- Bei Verschlüsselungs- und Entschlüsselungsvorgängen wird von einer Paketgröße von 4 KB ausgegangen.
- Durchsatzgrenzwerte für Verschlüsseln/Entschlüsseln gelten für AES-CBC- und AES-GCM-Algorithmen.
- Durchsatzgrenzwerte für Umschließen/Aufheben der Umschließung gelten für den AES-KW-Algorithmus.

|Vorgang|128 Bit|192 Bit|256 Bit|
|--|--:|--:|--:|
Schlüssel erstellen|1| 1| 1
Schlüssel löschen (vorläufiges Löschen)|10|10|10
Schlüssel endgültig löschen|10|10|10
Sichern eines Schlüssels|10|10|10
Schlüssel wiederherstellen|10|10|10
Schlüsselinformationen abrufen|1100|1100|1100
Verschlüsseln|8.000|8.000 |8.000 
Entschlüsseln|8.000|8.000|8.000
Umschließen|9000|9000|9000
Aufheben der Umschließung|9000|9000|9000

