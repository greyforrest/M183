# Lern-Bericht
Melanie Völlmin

## Einleitung

Im Modul 183 haben wir uns mit der Sicherheit von Applikationen und Benutzerdaten beschäftigt.

## Was habe ich gelernt?

Ich habe gelernt, wie Passwörter sicher in Datenbanken gespeichert werden können.

## Beschreibung

Um Passwörter sicher in Datenbanken zu speichern, hasht und salted man sie davor. Doch was genau heisst das?

Beim Hashen wird ein Algorithmus verwendet, um Daten, in diesem Fall Passwörter, unlesbar zu verschlüsseln. Die Passwörter können nicht mehr zurückverschlüsselt werden und mit dem Hash kann man sich nicht anmelden. Um das vom Benutzer eingegebene Passwort mit dem richtigen zu vergleichen, wird die Eingabe des Benutzers gleich wie das andere Passwort gehasht und wenn die beiden Hashes übereinstimmen, sind auch die Passwörter die selben. 

Das Problem dabei? Es gibt im Internet ganze "Dictionaries" mit den Hashes von Standartpasswörtern wie "123456" oder "Passwort". Und was macht man dagegen? Salten!

Ein Salt ist eine zufällige Zeichenfolge, die auch im Klartext in der Datenbank gespeichert werden kann. Die Salts werden vor die Passwörter gesetzt und dann so gehasht. So werden die Passwörter nochmals sicherer gespeichert und die Chance das genau dieser Hash, der aus einer zufälligen Zeichenfolge und einem Passwort entstanden ist, irgendwo wieder zu finden ist mehr als nur sehr unwahrscheinlich.

bcrypt ist ein salt und hashing Algorithmus. Dabei wird beim encoden des Passworts einiges an den Anfang gehängt: Zuerst den hashing-Algorithmus zum Beispiel "$2a$". Danach die Anzahl Runden, die gemacht wurden, um das Passwort zu hashen. Dann folgt der Salt und anschliessend das gehashte Passwort. 

In einem eigenen Projekt aus dem Lernatelier, in dem Benutzerdaten in Datenbanken gespeichert werden, habe ich die Passwörter mittels BCrypt gehashed und gesalted und so in der Datenbank gespeichert.

Hier ein Beispiel für das Salten und Hashen mit der Javaklasse BCryptPasswordEncoder:
```
private BCryptPasswordEncoder hashAndSalt = new BCryptPasswordEncoder(10, new SecureRandom()); 
//10 ist dabei die Anzahl Runden, die beim Hashen gemacht werden, umso mehr Runden desto sicherer, dafür braucht es aber auch länger
//Der SecureRandom ist der Salt, der dem Encoder direkt mitgegeben wird. Das hat in diesem Fall einen Nachteil: 
//Alle Passwörter, die mit diesem BCryptPasswordEncoder gehashed und gesalted werden, haben den selben Salt.

public String saltAndHashPassword(String passwort){
    return hashAndSalt.encode(passwort)
}

public boolean comparePassword(String eingegebenesPasswort, String hashAusDerDatenbank){
    //Das eingegebene Passwort wird auf dem selben Weg wie das andere Passwort gehasht und die Hashes werden dann verglichen.
    //Das bereits gehashte Passwort wird nie decoded.
    return hashAndSalt.matches(eingegebenesPasswort, hashAusDerDatenbank)
}
```

Wie bereits erwähnt, habe ich das selbst in einem eigenen Projekt angewendet. Dabei ist das Passwort, das ich gewählt habe um mich auf meiner Website anzumelden, "aVm8Y78cazuF&&". In der Datenbank sieht das ganze aber so aus: 
![Screenshot aus der Datenbank, wo das Passwort nicht mehr gleich aussieht](https://user-images.githubusercontent.com/69569613/207620239-752a1d77-4562-48f3-950d-988f20335667.png)

Da es in diesem Modul um Datensicherheit geht, habe ich das Passwort natürlich sofort geändert.

## Verifikation

✍️ Erklären Sie kurz und bündig, inwiefern die von Ihnen verwendeten Medien zeigen, was Sie gelernt haben.

# Reflektion zum Arbeitsprozess

👍 Überlegen Sie sich jeweils etwas, was gut an Ihrer Arbeit lief; 

👎 und etwas, was nicht gut lief.

**VBV**: ✍️ Formulieren Sie davon ausgehend einen *handelbaren* Verbesserungsvorschlag.
