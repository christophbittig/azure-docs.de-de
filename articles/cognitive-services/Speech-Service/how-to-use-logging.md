---
title: Speech SDK-Protokollierung – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Anmeldung im Speech SDK (C++, C#, Python, Objective-C, Java) aktivieren.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ba3f5234c3f11cdd5c2e302679bbb6a79ec0c4f6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550209"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Aktivieren der Protokollierung im Speech SDK

Die Protokollierung in eine Datei ist ein optionales Feature für das Speech SDK. Während der Entwicklung bietet die Protokollierung zusätzliche Informationen und Diagnosen aus den Hauptkomponenten des Speech SDK. Dies kann aktiviert werden, indem die Eigenschaft `Speech_LogFilename` für ein Sprachkonfigurationsobjekt auf den Speicherort und den Namen der Protokolldatei festgelegt wird. Die Protokollierung wird von einer statischen Klasse in der nativen Bibliothek des Speech SDK behandelt. Sie können die Protokollierung für jede Erkennungsmodul- oder Synthesizer-Instanz des Speech SDK aktivieren. Alle Instanzen im gleichen Prozess schreiben Protokolleinträge in dieselbe Protokolldatei.

> [!NOTE]
> Die Protokollierung ist seit Version 1.4.0 des Speech SDK in allen unterstützten Programmiersprachen verfügbar, mit Ausnahme von JavaScript.

## <a name="sample"></a>Beispiel

Der Name der Protokolldatei wird über ein Konfigurationsobjekt angegeben. Wenn Sie das `SpeechConfig` als Beispiel nehmen und davon ausgehen, dass Sie eine Instanz namens `config` erstellt haben:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

```go
import ("github.com/Microsoft/cognitive-services-speech-sdk-go/common")

config.SetProperty(common.SpeechLogFilename, "LogfilePathAndName")
```

Sie können über das Konfigurationsobjekt eine Erkennung erstellen. Dies aktiviert die Protokollierung für alle Erkennungen.

> [!NOTE]
> Wenn Sie ein `SpeechSynthesizer` aus dem Konfigurationsobjekt erstellen, wird die Protokollierung nicht aktiviert. Wenn die Protokollierung jedoch aktiviert ist, erhalten Sie auch eine Diagnose von `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Erstellen einer Protokolldatei auf verschiedenen Plattformen

Unter Windows oder Linux kann die Protokolldatei in einem beliebigen Pfad gespeichert werden, für den der Benutzer über die Schreibberechtigung verfügt. Schreibberechtigungen für Dateisystemspeicherorte in anderen Betriebssystemen können standardmäßig eingeschränkt oder begrenzt sein.

### <a name="universal-windows-platform-uwp"></a>Universelle Windows-Plattform (UWP)

UWP-Anwendungen müssen Protokolldateien an einem der Speicherorte für Anwendungsdaten (lokal, Roaming oder temporär) speichern. Eine Protokolldatei kann im lokalen Anwendungsordner erstellt werden:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Innerhalb einer Unity-UWP-Anwendung kann eine Protokolldatei wie folgt mithilfe des Ordners für den persistenten Datenpfad der Anwendung erstellt werden:

```csharp
#if ENABLE_WINMD_SUPPORT
    string logFile = Application.persistentDataPath + "/logFile.txt";
    config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```
Weitere Informationen zu Dateizugriffsberechtigungen in UWP-Anwendungen finden Sie unter [Dateizugriffsberechtigungen](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Sie können eine Protokolldatei entweder im internen Speicher, im externen Speicher oder im Cacheverzeichnis speichern. Dateien, die im internen Speicher oder im Cacheverzeichnis erstellt werden, sind für die Anwendung privat. Es ist ratsam, eine Protokolldatei im externen Speicher zu erstellen.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Der obige Code speichert eine Protokolldatei im externen Speicher im Stammverzeichnis eines anwendungsspezifischen Verzeichnisses. Ein Benutzer kann mit dem Dateimanager auf die Datei zugreifen (normalerweise in `Android/data/ApplicationName/logfile.txt`). Die Datei wird gelöscht, wenn die Anwendung deinstalliert wird.

Sie müssen auch die Berechtigung `WRITE_EXTERNAL_STORAGE` in der Manifestdatei anfordern:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Innerhalb einer Android-UWP-Anwendung kann die Protokolldatei wie folgt mithilfe des Ordners für den persistenten Datenpfad der Anwendung erstellt werden:

```csharp
string logFile = Application.persistentDataPath + "/logFile.txt";
config.SetProperty(PropertyId.Speech_LogFilename, logFile);
```
Darüber hinaus müssen Sie auch die Schreibberechtigung in Ihren Unity Player-Einstellungen für Android auf „External (SDCard)“ (Extern (SDCard)) festlegen. Das Protokoll wird in ein Verzeichnis geschrieben, das Sie mithilfe eines Tools wie AndroidStudio Device File Explorer abrufen können. Der genaue Verzeichnispfad kann zwischen Android-Geräten variieren. Der Speicherort ist in der Regel das Verzeichnis `sdcard/Android/data/your-app-packagename/files`.

Weitere Informationen zur Daten- und Dateispeicherung für Android-Anwendungen finden Sie [hier](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Nur Verzeichnisse innerhalb der Sandbox der Anwendung sind zugänglich. Dateien können in den Verzeichnissen für Dokumente, Bibliotheken und temporäre Daten erstellt werden. Dateien im Dokumentenverzeichnis können einem Benutzer zur Verfügung gestellt werden 

Wenn Sie Objective-C unter iOS verwenden, erstellen Sie mithilfe des folgenden Codeausschnitts eine Protokolldatei im Dokumentverzeichnis der Anwendung:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Um auf eine erstellte Datei zuzugreifen, fügen Sie die folgenden Eigenschaften der Eigenschaftsliste `Info.plist` der Anwendung hinzu:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Wenn Sie Swift unter iOS verwenden, verwenden Sie den folgenden Codeausschnitt, um Protokolle zu aktivieren:
```swift
let documentsDirectoryPathString = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).first!
let documentsDirectoryPath = NSURL(string: documentsDirectoryPathString)!
let logFilePath = documentsDirectoryPath.appendingPathComponent("swift.log")
self.speechConfig!.setPropertyTo(logFilePath!.absoluteString, by: SPXPropertyId.speechLogFilename)
```

Weitere Informationen zum iOS-Dateisystem finden Sie [hier](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="logging-with-multiple-recognizers"></a>Protokollierung mit mehreren Erkennungsmodulen

Obwohl ein Protokolldatei-Ausgabepfad als Konfigurationseigenschaft in einem `SpeechRecognizer` oder einem anderen SDK-Objekt angegeben wird, ist die SDK-Protokollierung eine *prozessweite* Singleton-Einrichtung ohne Konzept einzelner Instanzen. Sie können dies als `SpeechRecognizer`-Konstruktor (oder ähnlich) einordnen, der implizit eine statische und interne „Configure Global Logging“-Routine mit den in der entsprechenden verfügbaren `SpeechConfig` aufruft.

Dies bedeutet, dass Sie beispielsweise nicht sechs parallele Erkennungsmodule für die gleichzeitige Ausgabe in sechs separate Dateien konfigurieren können. Stattdessen konfiguriert das neueste erstellte Erkennungsmodul die globale Protokollierungsinstanz für die Ausgabe in der Datei, die in ihren Konfigurationseigenschaften angegeben ist, und die ganze SDK-Protokollierung wird in diese Datei ausgegeben.

Dies bedeutet auch, dass die Lebensdauer des Objekts, für das die Protokollierung konfiguriert wurde, nicht an die Dauer der Protokollierung gebunden ist. Die Protokollierung wird als Reaktion auf die Veröffentlichung eines SDK-Objekts nicht angehalten und fortgesetzt, solange keine neue Protokollierungskonfiguration bereitgestellt wird. Nach dem Start kann die prozessweite Protokollierung beendet werden, indem der Protokolldateipfad beim Erstellen eines neuen Objekts auf eine leere Zeichenfolge festgelegt wird.

Um potenzielle Verwirrung bei der Konfiguration der Protokollierung für mehrere Instanzen zu reduzieren, kann es hilfreich sein, die Steuerung der Protokollierung von Objekten zu abstrahieren, die wirkliche Arbeit verrichten. Ein Beispielpaar von Hilfsroutinen:

```cpp
void EnableSpeechSdkLogging(const char* relativePath)
{
    auto configForLogging = SpeechConfig::FromSubscription("unused_key", "unused_region");
    configForLogging->SetProperty(PropertyId::Speech_LogFilename, relativePath);
    auto emptyAudioConfig = AudioConfig::FromStreamInput(AudioInputStream::CreatePushStream());
    auto temporaryRecognizer = SpeechRecognizer::FromConfig(configForLogging, emptyAudioConfig);
}

void DisableSpeechSdkLogging()
{
    EnableSpeechSdkLogging("");
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkunden unserer Beispiele auf GitHub](https://aka.ms/csspeech/samples)
