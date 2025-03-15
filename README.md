# HGAuth

Ein Opensim-Authentifizierungsmodul, das eine Webformularübermittlung erzwingen kann, bevor eingehende HG-Teleports erlaubt werden.

Version 1.0.4, 17. Dezember 2024

-----
**Zusammenfassung**

Dies ist eine Neufassung von Projekt Sasha, das seit 2018 nicht mehr entwickelt wurde.

Es handelt sich um eine Reihe von PHP-Skripten, die einen Weg bieten, eingehende HG-Teleports von Avataren (aus anderen Grids) zu erzwingen, um den auf einer Webseite präsentierten Bedingungen zuzustimmen, bevor sie eingelassen werden.

Dezember 2024: Ich habe dies neu geschrieben, um mit den neuesten Viewern und OS-Serverversionen kompatibel zu sein. Es verwendet nun UUIDs zur Authentifizierung.

Obwohl mir keine Probleme bekannt sind, verwenden Sie es bitte auf eigenes Risiko.

-----
**Funktionsweise**

Avatare, die versuchen, zum ersten Mal mit diesem Paket zu einem Grid zu teleportieren, erhalten einen Ablehnungsdialog im Viewer mit einer anpassbaren Nachricht und einem externen Link. Durch Klicken auf diesen Link gelangen sie zu einer externen Webseite mit einer Nachricht auf der Seite und einem Formular. Durch Klicken auf die Bestätigungs-/Ja-Schaltfläche im Formular werden sie für zukünftige eingehende HG-Teleports autorisiert.

Die aktuelle Implementierung authentifiziert basierend auf der UUID des Avatars im Viewer und verhindert, dass dies geändert werden kann. Das Webformular verfügt über Sicherheitsfunktionen, um verschiedene Missbräuche zu verhindern.

Die Formulierungen auf der Webseite können geändert oder angepasst werden, um Ihren Bedürfnissen zu entsprechen. Projekt Sasha wurde ursprünglich entwickelt, um die rechtlichen Anforderungen der DSGVO für Bewohner der EU durchzusetzen. Das Formular kann jedoch für die Durchsetzung von AGB oder anderen Anforderungen verwendet werden.

-----
**Neueste Änderungen**

Version 1.0.4 entfernt Benutzer@Grid-Daten aus dem Authentifizierungsprozess aufgrund ungelöster Probleme in der Sequenz von HTTP-Anforderungen der XML-Payloads. Diese Version behebt Instabilitäten im Viewer aufgrund dieser Fehler; jedoch sind Benutzer@Grid-Informationen nicht mehr für die Anzeige auf Webformularen oder die Authentifizierung verfügbar. Die Authentifizierung basiert nun auf UUID. Diese Änderung ermöglicht fehlerfreie HG-TPs nach Unterzeichnung des Formulars.

Die hgauth-Datenbanktabelle in Version 1.0.4 hat eine Änderung seit 1.0.3. Daten aus früheren Versionen sind kompatibel, jedoch MÜSSEN bestehende Tabellen bei der Migration auf Version 1.0.4 den UNIQUE-Schlüssel für 'avatarname' entfernen/löschen. Sie können optional einen INDEX-Schlüssel für 'avatarname' hinzufügen, um Abfragen zu beschleunigen.

Version 1.0.3 fügte eine Workaround hinzu, um Benutzer@Grid-Daten für Anzeige und Authentifizierung verfügbar zu halten. Neuere Viewer-Versionen entwickelten jedoch eine Inkompatibilität damit, die zu einem erfolgreichen 2. Teleport einen Viewer-Neustart erfordert.

Version 1.0.2 entfernte Entwicklungscodes und fügte kleine UI-Verbesserungen hinzu.

Version 1.0.1 enthält geringfügige Fehlerbehebungen und Dokumentationsupdates.

-----
**Änderungen gegenüber Projekt Sasha**

- Übertragung des Benutzernamens und der UUID in der URL-Namensraum eliminiert
- Beseitigung der Verwundbarkeit des Formularprozessors, die zu Missbrauch der Datenbank führen könnte
- Korrektes Handling der Formulareinreichung über jQuery und AJAX
- PHP-Code repariert, nicht kompatibel mit PHP 8.x
- Konsolidierung der PHP-Vorlagen zur Vereinfachung von UI-Änderungen
- Hinzufügen von Entwicklerinformationen zur Fehlerberichterstattung und für Feature-Anfragen

-----
**Installation**

Die Datei hgauth.sql liefert Ihnen die benötigte Tabellenstruktur
Importieren Sie sie in Ihren MySQL-Server in die gewählte Datenbank.

Die Datei authconfig.php enthält Datenbankanmeldeinformationen und Konfigurationen für
die anderen Skripte. Sie wird nur als Include-Datei verwendet und kann außerhalb
des Dokumentenstamms platziert werden.

Die Datei hgauth.php wird verwendet, um HG-Teleport-Autorisierungsanfragen von einem Opensim Authorization Service Connector über HTTP zu empfangen und Antworten an diesen Dienst zurückzusenden. Diese Datei enthält auch eine Nachricht, die im Dialogfeld des Viewers erscheint, wenn der anfängliche eingehende Teleport-Antrag abgelehnt wird. Sie können die Nachricht aktualisieren.

Die Datei index.php wird verwendet, um auf einem Webbrowser ein Autorisierungsformular für einen Benutzer darzustellen, der auf den Link im HG TP-Ablehnungsdialog des Viewers geklickt hat. Sie können die Bildschirmnachricht nach Ihren Wünschen ändern.

Nachdem die Dateien konfiguriert und in die entsprechenden Webserver-Verzeichnisse platziert wurden, müssen Sie einige Änderungen an Ihren Opensim-Konfigurationsdateien vornehmen, damit diese wirksam werden.

In der Datei:
config-include/GridCommon.ini (Grid-Modus)
oder
config-include/StandaloneCommon.ini (Standalone-Modus)

1) Fügen Sie Folgendes im Abschnitt [Modules] hinzu:

   AuthorizationServices = "RemoteAuthorizationServicesConnector"

2) Fügen Sie Folgendes im Abschnitt [AuthorizationService] hinzu:

   AuthorizationServerURI = "http://yourwebserver/path/to/hgauth.php"

Aus Sicherheitsgründen wird empfohlen, den Webzugriff auf diese Dateien einzuschränken. Eine Beispieltdatei htaccess.txt für Apache (Apache 2.4-Syntax) ist enthalten. Diese sollte in .htaccess umbenannt und im Verzeichnis mit den PHP-Skripten platziert werden. Möglicherweise muss Apache so konfiguriert werden, dass die .htaccess-Datei gelesen wird.

- authconfig.php - sollte nicht direkt zugegriffen werden, sondern nur als Include-Datei verwendet werden - empfohlen wird, dies außerhalb des Dokumentenstamms zu platzieren oder .htaccess zu verwenden, um den Zugriff zu verhindern.
- hgauth.php - Zugriff sollte auf die IP der eingehenden HTTP-Verbindung des Hosting-Opensim-Servers beschränkt sein.
- index.php - uneingeschränkte Webseite

Obwohl nicht kritisch, stellen Sie sicher, dass Ihre date.timezone in Ihrer php.ini festgelegt ist. Andernfalls können Datensätze in der Datenbank eine Mischung aus lokalen und GMT-Zeiten enthalten. Die Erstellungszeit wird durch die interne Uhr von MySQL geschrieben, während die Bestätigungszeit von PHP geschrieben wird.

-----
**Anforderungen**

- Webserver, getestet auf Apache 2.4.x
- PHP, getestet und entwickelt auf PHP 8.x
- MySQL-Server, sollte auf allen Versionen von MySQL >= 7.x oder MariaDB >= 10.x funktionieren.

-----
**Nutzungsbedingungen**

Durch die Verwendung dieser Dateien aus diesem Paket stimmen Sie Folgendem zu:
- Die Installation ist nur für Evaluierungszwecke gedacht.
- Der Service wird nicht von den Entwicklern der Opensim-Server-Software unterstützt.
- Die Autoren haften nicht für etwaige Schäden. Verwenden Sie es auf eigenes Risiko.

-----
**Credits**

Teile des Codes stammen aus Projekt Sasha. Folgende Personen (Avatare) werden aus Projekt Sasha genannt: Foto50, Hack13, FreakyTech und Leighton Marjoram.

-----

Wenn Ihnen dieses Projekt gefällt, geben Sie mir bitte einen Stern.

Bitte informieren Sie mich über etwaige Fehler oder Feature-Anfragen.

Cuga Rajal (Second Life und Opensim)

cuga@rajal.org
