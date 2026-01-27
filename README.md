# Verbesserung der Screenreader-Unterstützung in Vuetify: Eine komponentenbasierte Analyse und Optimierung der Accessibility-Implementierung nach WCAG 2.1

## Einleitung

In diesem Repository befindet sich das Resultat der Bachelorarbeit von Meike Jungilligens. Das Werk der Arbeit stellen drei Pull-Requests in das [Vuetify-Repository](https://github.com/vuetifyjs/vuetify) dar, die die Screenreader-Kompatibilität von drei ausgewählten Komponenten verbessern.

Der Schwerpunkt der Arbeit liegt auf der Analyse und Verbesserung der Screenreader-Kompatibilität durch gezielte Anpassungen im Vuetify-Quellcode. Dabei werden insbesondere HTML-Markup, ARIA-Rollen und -Attribute sowie deren Zusammenspiel im Kontext von Screenreadern betrachtet und optimiert. Ziel ist es, eine barriereärmere Nutzung der Komponenten mit assistiven Technologien zu ermöglichen.

Dieses Repository dient der Ergebnisdokumentation und Herleitung der einzelnen Implementierungen. Die schriftliche Ausarbeitung ist zur ausführlicheren Erklärung ebenfalls hinterlegt.

Den Implementierungen voraus ging die Erstellung eines Entscheidungsbaumes, der als Orientierung für die Implementierung genutzt wurde. Dieser stützt sich auf verschiedene Quellen wie die [WAI-ARIA](https://www.w3.org/TR/wai-aria-1.2/) oder den [Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/), die auch als möglioche Referenzwerke im Baum vermerkt sind. Der Baum setzt den in diesen Quellen verfolgten HTML-first-Ansatz um. XXX Er sieht den Einsatz von ARIA-Attributen nur als Ergänzung vor oder als Ersatz, falls kein geeignetes HTML-Element vorhanden ist. Der HTML-first-Ansatz beruht darauf, dass native HTML-Elemente von assistiven Technologien zuverlässiger und konsistenter interpretiert werden als ARIA-Attribute allein. 

Der Inhalt dieses Repos soll die Implementierungsentscheidungen pro Komponente und Pull-Request darlegen und verfolgt dabei das folgende Schema:

1. Analyse des Issues und der Tests (vgl. Kapitel 3.1 und 3.2 in der Ausarbeitung) mit dem Ziel, die konkrete Ursache der eingeschränkten Screenreader-Kompatibilität zu identifizieren und die dafür verantwortliche Haupt- oder Teilkomponente einzugrenzen. Diese Fokussierung dient der Reduktion der Komplexität und vermeidet unnötige Eingriffe in funktional unbeteiligte Komponenten.
2. Bewertung der identifizierten (Teil-)Komponente entlang des Entscheidungsbaums, um zu bestimmen, ob eine native HTML-Semantik vorliegt und ob diese die funktionalen Anforderungen der Komponente aus Sicht der Screenreader vollständig abdeckt.
3. Abgleich des Ist-Zustands der Komponente mit dem Soll-Zustand gemäß der vorhandenen Quellen mit Ableitung der notwendigen Anpassungen.
4. Umsetzung der identifizierten Anpassungen im Vuetify-Quellcode, zum Beispiel durch das gezielte Setzen oder Ergänzen von ARIA-Attributen an bestehenden Elementen.

## Implementierung pro Komponente

Die Implementierungen basieern auf den entsprechenden Issues aus dem Vuetify-Repo, selbst durchgeführten Tests sowie dem oben beschriebenen Entscheidungsbaum. Zur besseren Nachvollziehbarkeit sind all diese Artefakte ebenfalls in diesem Repository hinterlegt (im Ordner Artefakte). Die Darlegung der Strategie erfolgt nun pro Komponente nach dem in der Einleitung aufgeführten Schema.


### VDataTable

### VTreeview

### VTimepicker

### VAutocomplete

Das zugrundegelegene Isue wurde zur Projektlaufzeit kurz vor Implementierungsbeginn bereits gelöst und wird deswegen nicht weiter bearbeitet.

## Fazit


