# Verbesserung der Screenreader-Unterstützung in Vuetify: Eine komponentenbasierte Analyse und Optimierung der Accessibility-Implementierung nach WCAG 2.1

## Einleitung

In diesem Repository befindet sich das Resultat der Bachelorarbeit von Meike Jungilligens. Das Werk der Arbeit stellen drei Pull-Requests in das [Vuetify-Repository](https://github.com/vuetifyjs/vuetify) dar, die die Screenreader-Kompatibilität von drei ausgewählten Komponenten verbessern.

Der Schwerpunkt der Arbeit liegt auf der Analyse und Verbesserung der Screenreader-Kompatibilität durch gezielte Anpassungen im Vuetify-Quellcode. Dabei werden insbesondere HTML-Markup, ARIA-Rollen und -Attribute sowie deren Zusammenspiel im Kontext von Screenreadern betrachtet und optimiert. Ziel ist es, eine barriereärmere Nutzung der Komponenten mit assistiven Technologien zu ermöglichen.

Dieses Repository dient der Ergebnisdokumentation und Herleitung der einzelnen Implementierungen. Die schriftliche Ausarbeitung ist zur ausführlicheren Erklärung ebenfalls hinterlegt.

Den Implementierungen voraus ging die Erstellung eines Entscheidungsbaumes, der als Orientierung für die Implementierung genutzt wurde. Dieser stützt sich auf verschiedene Quellen wie die [WAI-ARIA](https://www.w3.org/TR/wai-aria-1.2/) oder den [Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/), die auch als mögliche Referenzwerke im Baum vermerkt sind. Der Baum setzt den in diesen Quellen verfolgten HTML-first-Ansatz um. Er sieht den Einsatz von ARIA-Attributen nur als Ergänzung vor oder als Ersatz, falls kein geeignetes HTML-Element vorhanden ist. Der HTML-first-Ansatz beruht darauf, dass native HTML-Elemente von assistiven Technologien zuverlässiger und konsistenter interpretiert werden als ARIA-Attribute allein.

Der Inhalt dieses Repos soll die Implementierungsentscheidungen pro Komponente und Pull-Request darlegen und verfolgt dabei das folgende Schema, welches alle Schritte aus Kapitel 3 der schriftlichen Ausarbeitung zu einer Implementierungsstrategie verbindet. Unter Miteinbezug der benutzten Issues und Tests, mit Benutzung des entwickelten Entscheidungsbaums und mit Bezug auf die relevanten Quellen wird so eine Implementierung herbeigeführt:

1. Analyse des Issues und der Tests (vgl. Kapitel 3.1 und 3.2 in der Ausarbeitung) mit dem Ziel, die konkrete Ursache der eingeschränkten Screenreader-Kompatibilität zu identifizieren und die dafür verantwortliche Haupt- oder Teilkomponente einzugrenzen. Diese Fokussierung dient der Reduktion der Komplexität und vermeidet unnötige Eingriffe in funktional unbeteiligte Komponenten.
2. Bewertung der identifizierten (Teil-)Komponente entlang des Entscheidungsbaums, um zu bestimmen, ob eine native HTML-Semantik vorliegt und ob diese die funktionalen Anforderungen der Komponente aus Sicht der Screenreader vollständig abdeckt.
3. Abgleich des Ist-Zustands der Komponente mit dem Soll-Zustand gemäß der vorhandenen Quellen mit Ableitung der notwendigen Anpassungen.
4. Umsetzung der identifizierten Anpassungen im Vuetify-Quellcode, zum Beispiel durch das gezielte Setzen oder Ergänzen von ARIA-Attributen an bestehenden Elementen.

## Implementierung pro Komponente

Die Implementierungen basieren auf den entsprechenden Issues aus dem Vuetify-Repo, selbst durchgeführten Tests sowie dem oben beschriebenen Entscheidungsbaum (s. folgende Abbildung). Zur besseren Nachvollziehbarkeit sind all diese Artefakte ebenfalls in diesem Repository hinterlegt (im Ordner Artefakte). Die Darlegung der Strategie erfolgt nun pro Komponente nach dem in der Einleitung aufgeführten Schema. 

![Entscheidungsbaum](/artefakte/entscheidungsbaum.jpg)

Ein [Glossar](https://github.com/mjung2605/screenreader-a11y-in-vuetify/blob/main/README.md#kurzes-aria-glossar) mit einer Auflistung und Definition aller verwendeten Elemente (z.B. ARIA-Attribute) befindet sich am Ende dieser README.

### VDataTable

1. Das Issue (bestätigt durch die Tests) bezieht sich auf die unzureichende Accessibility der Sortierinformation in der Kopfzeile der Tabelle, da sich die Buttons zum Sortieren dort befinden. In Vuetify ist dies die Teilkomponente ```VDataTableHeaders```.
2. Pfad im Entscheidungsbaum: **JA** (```<th>``` in ```<table>```), **NEIN** (keine nativ eingebaute Sortierinformation/-Funktion). Die Implementierung erfolgt also mit nativem HTML + erweiterndem ARIA.
3. Es ist ein [APG zu sortierbaren Tabellen](https://www.w3.org/WAI/ARIA/apg/patterns/table/examples/sortable-table/) vorhanden. Dies empfiehlt, das native ```<th>```-HTML-Element zu verwenden und zusätzlich das ARIA-Attribut ```aria-sort``` sowie eine ergänzende Ansage, dass entsprechende Stellen sortierbar sind, zu setzen, was zur abgeleiteten Strategie in Punkt 2 passt.
4. Der Wert von ```aria-sort``` wird im Code aus dem vorhandenen Wert ```item-order``` für die entsprechende Zelle (bzw. die dazugehörige Spalte) abgeleitet. ```aria-sort``` kann zwei Werte annehmen: *ascending* oder *descending*. Um dem Nutzer vor dem Betätigen der Buttons schon mitzuteilen, dass eine Spalte sortierbar ist, wurde diese Information in einem ```aria-label``` ergänzt. Das ```aria-label``` gibt jetzt den Titel des Elements (also hier die Spaltenüberschrift) sowie die Sortierbarkeit/Sortierung aus.

[Link zur Data Table Pull Request](https://github.com/vuetifyjs/vuetify/pull/22575)

### VTreeview

1. Die Issues (bestätigt durch die Tests) betreffen die eingeschränkte Tastaturnavigation  und die nichterkennbaren notwendigen Informationen zu *Name, Rolle und Wert* der Teilelemente. In den Tests wurde außerdem festgestellt, dass Beziehungen zwischen Einträgen sowie deren Zustand (auf- oder zugeklappt) nicht oder nur unvollständig angesagt werden. Betroffen ist also die gesamte Baumstruktur mit den Teilkomponenten `VTreeview`, `VTreeviewChildren` und `VTreeviewItem`.
2. Pfad im Entscheidungsbaum: **NEIN** (kein passendes natives HTML-Element für Baumstrukturen), **JA** (passende ARIA-Rollen vorhanden). Die Implementierung erfolgt über ARIA.
3. Es ist ein [APG für Treeviews](https://www.w3.org/WAI/ARIA/apg/patterns/treeview/) vorhanden. Dieses empfiehlt u. a. die Rollen `tree` und `treeitem` sowie die Attribute `aria-expanded`, `aria-checked` und eine eindeutige Beschriftung. In Vuetify war davon nur `aria-checked` bereits vorhanden.
4. In `VTreeview` wurde die Rolle `tree` ergänzt. In `VTreeviewItem` wurden `role="treeitem"` und `aria-expanded` gesetzt. Zusätzlich wurde in `VTreeviewChildren` ein `aria-label` ergänzt, um den jeweiligen Teilbaum zu beschreiben. Dadurch werden Struktur und Zustand der Treeview für Screenreader nachvollziehbar.

Link zur Treeview Pull Request

### VTimepicker

1. Das Issue (bestätigt durch die Tests) betrifft die fehlende Ansage von deaktivierten bzw. ungültigen Zeitwerten durch Screenreader. In den Tests fiel außerdem auf, dass die Input-Felder nicht durch ein Label beschrieben werden, deren Zweck und Funktion also für Screenreader-Nutzende unzugänglich bleibt. Dies betrifft die für die Zeiteingabe zuständigen Input-Felder innerhalb der Komponente `VTimepicker`. Die eigentliche Uhr der Komponente wurde hier nach den WCAG als "entbehrlich" (d.h. Komponente kann auch ohne diese vollständig bedient werden) und somit nicht kritisch für die Barrierefreihiet der Komponente eingestuft und deswegen in der Optimierung nicht berücksichtigt.
2. Pfad im Entscheidungsbaum: **JA** (`<input>`), **NEIN** (ungültige Zustände werden nicht angesagt). Die Implementierung erfolgt also mit nativem HTML + erweiterndem ARIA.
3. Es existiert kein spezifisches APG für Timepicker-Komponenten. In der WAI-ARIA-Spezifikation ist jedoch das Attribut `aria-invalid` vorgesehen und auch (bestätigt nach Sichtung des ARIA in HTML-Dokuments) für HTML-`<input>`-Elemente zulässig.
4. Das native Element wird in Vuetify bereits verwendet und folglich durch ARIA-Attribute ergänzt: Bei deaktivierten oder ungültigen Zeitwerten wird `aria-invalid` auf den entsprechenden Input-Feldern gesetzt. Zusätzlich wurde der Status im `aria-label` ergänzt, sodass Screenreader den ungültigen Zustand zusammen mit der Bezeichnung des Feldes ansagen.

[Link zur Time Picker Pull Request](https://github.com/vuetifyjs/vuetify/pull/22576)

### VAutocomplete

Das zugrundegelegene Issue wurde zur Projektlaufzeit kurz vor Implementierungsbeginn bereits gelöst und deswegen nicht weiter bearbeitet.

## Fazit

Die in diesem Repository dokumentierten Implementierungen zeigen, dass sich die Screenreader-Kompatibilität von Vuetify-Komponenten mit den durchgeführten Anpassungen deutlich verbessern lies. Durch die systematische Analyse der Issues, die Nutzung des Entscheidungsbaums und den Abgleich mit bestehenden Standards konnten konkrete Ursachen identifiziert und behoben werden.

Besonders deutlich wurde, dass native HTML-Semantik eine Grundlage für Barrierefreiheit bietet, jedoch in vielen Fällen durch ergänzende ARIA-Rollen und -Attribute vervollständigt werden muss. Gleichzeitig zeigen die Beispiele, dass fehlende oder unvollständige Semantik erhebliche Auswirkungen auf die Nutzbarkeit mit Screenreadern haben kann.

Die Pull-Requests sollen nicht nur die behandelten Komponenten verbessern, sondern auch als Orientierung für zukünftige Accessibility-Anpassungen in Vuetify dienen. Der entwickelte Entscheidungsbaum hat sich dabei als praktisches Hilfsmittel erwiesen, um konsistente und nachvollziehbare Implementierungsentscheidungen zu treffen.

## Kontaktinformationen der Erstellerin

Meike Jungilligens (Studentin Medieninformatik B.Sc.)

E-Mail: mjungilligens@gmail.com

GitHub: [mjung2605](https://github.com/mjung2605)

## Kurzes ARIA-Glossar

[Referenz für ARIA-Definitionen](https://www.w3.org/TR/wai-aria-1.2/)

```aria-label```:  
Stellt eine für Screenreader zugängliche Beschriftung für ein Element bereit, wenn kein sichtbares oder korrekt zugeordnetes Label vorhanden ist. Wird genutzt, um Zweck oder Zustand eines Elements eindeutig anzusagen.

```aria-sort```:  
Gibt die aktuelle Sortierreihenfolge einer Tabellenspalte an. Zulässige Werte sind `ascending` und `descending`. Wird typischerweise auf `<th>`-Elementen verwendet.

```aria-expanded```:  
Kennzeichnet, ob ein ausklappbares Element (z. B. ein Knoten in einer Treeview) aktuell ein- oder ausgeklappt ist. Mögliche Werte sind `true` oder `false`.

```aria-checked```:  
Gibt den Auswahlzustand eines auswählbaren Elements an (z. B. Checkboxen oder auswählbare Treeview-Einträge). Wird von Screenreadern angesagt, um den aktuellen Zustand zu vermitteln.

```aria-invalid```:  
Markiert ein Eingabefeld als ungültig. Wird von Screenreadern angesagt, um auf fehlerhafte oder nicht zulässige Eingaben hinzuweisen. Das Attribut ist auch für native HTML-Input-Elemente zulässig.

```role="tree"```:  
Kennzeichnet ein Element als übergeordnete Baumstruktur. Ermöglicht Screenreadern, die enthaltenen Elemente als hierarchisch zusammengehörig zu interpretieren.

```role="treeitem"```:  
Kennzeichnet ein einzelnes Element innerhalb einer Treeview. Wird verwendet, um Einträge als Teil einer Baumstruktur auszuzeichnen.

[Referenz für HTML-Definitionen](https://html.spec.whatwg.org/multipage/)

```<th>```:  
Tabellenkopfzelle in HTML. 

```<input>```:  
Natives HTML-Formularelement zur Dateneingabe. 






