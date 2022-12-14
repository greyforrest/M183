# Lern-Bericht
Melanie Völlmin

## Einleitung

Im Modul 183 haben wir uns mit der Sicherheit von Applikationen und Benutzerdaten beschäftigt.

## Was habe ich gelernt?

Ich habe gelernt, wie Passwörter sicherer in Datenbanken gespeichert werden können.

## Beschreibung

Um Passwörter sicher in Datenbanken zu speichern, hashed und salted man sie davor. Doch was genau heisst das?

Beim Hashen wird ein Algorithmus verwendet, um Daten, in diesem Fall Passwörter, unlesbar zu verschlüsseln. Die Passwörter können nicht mehr zurückverschlüsselt werden und mit dem Hash kann man sich nicht anmelden. Um das vom Benutzer eingegebene Passwort mit dem richtigen zu vergleichen, wird die Eingabe des Benutzers gleich wie das andere Passwort gehashed und wenn die beiden Hashes übereinstimmen, sind auch die Passwörter die selben. 

Das Problem dabei? Es gibt im Internet ganze "Dictionaries" mit den Hashes von Standartpasswörtern wie "123456" oder "Passwort". Und was macht man dagegen? Salten!

Ein Salt ist eine zufällige Zeichenfolge, die auch im Klartext in der Datenbank gespeichert werden kann. Die Salts werden vor die Passwörter gesetzt und dann so gehashed. So sind die Passwörter etwas sicherer, denn die Chance das genau dieser Hash, der aus einer zufälligen Zeichenfolge (dem Salt) und einem Passwort entstanden ist, irgendwo wieder zu finden ist, ist mehr als nur sehr unwahrscheinlich.

Momentan gibt es 2 besonders sichere Algorithmen zum hashen: bcrypt und PBKDF2. Ich habe mich mit bcrypt beschäftigt. Bei diesem Algorithmus wird beim encoden des Passworts einiges an den Anfang gehängt: Zuerst den hashing-Algorithmus zum Beispiel "$2a$". Danach die Anzahl Runden, die gemacht wurden, um das Passwort zu hashen. Dann folgt der Salt und anschliessend das gehashte Passwort. 

Hier ein Beispiel für das Salten und Hashen mit dem BCryptPasswordEncoder aus dem Springframework:
``
private BCryptPasswordEncoder saltAndHash = new BCryptPasswordEncoder(10, new SecureRandom()); 
//10 ist dabei die Anzahl Runden, die beim Hashen gemacht werden, umso mehr Runden desto sicherer, dafür ladet es länger
//Der SecureRandom ist der Salt, der dem Encoder direkt mitgegeben wird. Das hat in diesem Fall einen Nachteil: 
//Alle Passwörter, die mit diesem BCryptPasswordEncoder gehashed und gesalted werden, haben den selben Salt.
//Das ist nicht allzu schlimm, aber es ist immer sicherer, bei jedem Passwort einen anderen Salt zu verwenden.

public String saltAndHashPassword(String passwort){
    return saltAndHash.encode(passwort)
}

public boolean comparePassword(String eingegebenesPasswort, String hashAusDerDatenbank){
    //Das eingegebene Passwort wird auf dem selben Weg wie das andere Passwort gehasht und die Hashes werden dann verglichen.
    //Das bereits gehashte Passwort wird nie decoded.
    return saltAndHash.matches(eingegebenesPasswort, hashAusDerDatenbank)
}
``
In einem eigenen Projekt aus dem Lernatelier, in dem Benutzerdaten in Datenbanken gespeichert werden, habe ich die Passwörter mittels BCryptPasswortEncoder gehashed und gesalted und so in einer Datenbank gespeichert. Dabei kann man schön sehen, wie sich alles verändert. Das Passwort, das ich gewählt habe um mich auf meiner Website anzumelden, ist *"aVm8Y78cazuF&&"*. In der Datenbank sieht das ganze aber so aus:

![Screenshot aus der Datenbank, wo das Passwort nicht mehr gleich aussieht](https://user-images.githubusercontent.com/69569613/207620239-752a1d77-4562-48f3-950d-988f20335667.png)

`$2a` steht für die Verschlüsselungsart.

`$10` sind die Anzahl Runden.

Der Rest ist der Salt und das Passwort, gehashed.

***Da es in diesem Modul um Datensicherheit geht, habe ich das Passwort natürlich sofort nach dem Screenshot geändert.***

Mit den heutigen technologischen Fortschritten gibt es jedoch schon Grafikkarten, die sehr schnell im hashen sind. Deswegen sind Passwörter auch nicht die 100% sicher, wenn sie hashed und salted. Dagegen kann man leider nicht viel machen ausser extrem vorsichtig vorgehen. Es ist zu empfehlen, Passwörter 10'000 mal zu hashen, nicht so wie in unserem Beispiel nur 10 mal. Zusätzlich sollten Salts immer mindestens 32 Bit gross sein, am besten noch länger.

## Verifikation

Ich habe im Text beschrieben, was hashen und salten ist und für was es gebraucht wird. Im Codeausschnitt habe ich gezeigt, dass ich das gelernte auch anwenden kann. Zum Schluss habe ich noch beschrieben und gezeigt, dass ich das ganze nun auch schon in einem eigenen Projekt angewendet habe.

# Reflektion zum Arbeitsprozess

Wir haben bereits im ersten Jahr mal ganz kurz über Hashen gesprochen, das Thema war für mich also nicht 100% Neuland. Ich habe das Thema in diesem Modul schnell begriffen. Ich dachte zuerst immer, dass die gehashten Passworte wieder zurückübersetzt werden, um es mit dem eingegebenen Passwort abzugleichen. Zum Glück habe ich nun verstanden, dass das nicht so ist, es wäre auch viel zu unsicher, wenn die Passworte decoded werden könnten. 

Bei unserem Lernatelierprojekt habe ich mich in den BCryptPasswordEncoder eingelesen. Das meiste habe ich auch verstanden, aber ich bin mir nicht sicher was die Runden sind. Werden die Passwörter wirklich nur 10 Runden gehashed? Denn laut Dokumentation kann als "Strength" einen Wert zwischen 4 und 31 angeben, was für mich nach etwas komischen Zahlen klingt, vorallem wenn man bedenkt, dass das ja eigentlich gar nicht sicher ist und diese Klasse aus dem Security-Package des Springframeworks kommt, was anscheinend extrem gut ist und immer empfohlen wird.

**VBV**: Ich werde mir noch einige Videos zum hashen und salten mit dem BCryptEncoder anschauen, um das Ganze noch ein wenig besser zu verstehen.
