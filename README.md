# Zusammenfassung und Ausblick des Forschungsprojekts
Die folgenden Passagen beziehen sich auf die schriftliche Ausarbeitung des Masterprojekts "Machine Learning unterstützte Kanalschätzung für Mehrantennensysteme"

Die gesteigerte Mobilität der Endgeräte sorgt für sich verändernde Kanäle. Eine leistungsfähige, zuverlässige und dennoch ressourcenschonende Kanalschätzung ist zur Realisierung konstant hoher Datenraten mit geringen Fehlerraten nötig.


Im Kontext der Mehrantennensysteme soll die regularisierte LS-Kanalschätzung mittels neuronaler Netzwerke an die rauschoptimale MMSE-Kanalschätzung herangeführt
werden. Zum Einsatz kommt ein lineares Systemmodell. Das LS-Verfahren ist einfach berechenbar, kann jedoch das vorliegende additive Rauschen verstärken. Die LMMSEKanalschätzung stellt für lineare Systemmodelle die rauschoptimale Lösung dar, setzt
jedoch die Kenntnis der Kanalstatistiken voraus. Als Nebenbedingung soll der Einsatz
von Pilotsignalen nach Möglichkeit reduziert werden.


Anhand von zwei Versuchsaufbauten werden Resultate zur Beantwortung der Forschungsfrage erhoben. Der Unterschied der Aufbauten besteht in der Komplex- bzw. Reellwertigkeit der generierten Matrizen. Beide Versuchsaufbauten bilden simulativ ein lineares
Kanalmodell mit klassischen Kanalschätzern ab. Die geschätzte Kanalmatrix des regularisierten LS-Verfahrens kann als Eingang in das neuronale Netzwerk eingegeben werden.
Die Schätzung des neuronalen Netzwerks wird im Anschluss mit den klassischen Kanalschätzern anhand des MSEs verglichen. Eine ausführliche Beschreibung der Aufbauten
kann Kapitel 3 entnommen werden. Problematiken, die während der Implementierung
der finalen Lösung aufgetreten sind, können in Kapitel 5.2 eingesehen werden.


Das Forschungsziel, die Heranführung der LS- an die LMMSE-Lösung, konnte für wenige
Pilotsignale und für den niedrigen SNR-Bereich erreicht werden. Es ist trotz Unkenntnis
der Kanalstatistik für bestimmte Konfigurationen möglich, ein besseres Ergebnis als die
einfache LS-Schätzung zu erreichen. Im höheren SNR-Bereich schneidet die verbesserte
Schätzung den ursprünglichen LS-Schätzer und ist im Vergleich somit schlechter. Bei
Verwendung eines angepassten Trainingsdatensatzes kann der MSE des verbesserten LS-Schätzers auch im hohen SNR-Bereich wieder auf oder sogar unter den ursprünglichen
LS-Schätzer fallen. In diesem Fall ist der Zugewinn an Performance durch das neuronale Netzwerk jedoch vernachlässigbar gering. Die Verbesserung im hohen SNR-Bereich,
könnte Teil weiterer Nachforschungen werden. Dem Kapitel 4 können alle Beobachtungen entnommen werden. Weiterführende Interpretationen sind im Kapitel 5 einsehbar.


Das neuronale Netzwerk muss bei der Kanalschätzung für jedes einzelne Element der
Kanalmatrix H eine Schätzung herausgeben. Die Schätzung vieler kontinuierlicher Werte
ist jedoch fehleranfälliger als die Schätzung von lediglich einem Wert. Fortführend könnte
ein neuronales Netzwerk speziell zum Zwecke der Varianzschätzung aufgebaut werden.
Die geschätzte Varianz bestände aus nur einem kontinuierlichen Wert. Sie würde sich
entsprechend als weniger aufwendig gestalten. Der erhaltene Wert kann in die klassischen
Schätzer eingegeben und anschließend mit den Ergebnissen dieses Projekts verglichen
werden. 


Aufbauend auf dem DeEQ Algorithmus aus [MH] ist der Einbezug von Modellwissen in
das neuronale Netzwerk denkbar. Hierbei könnten die Ansätze der klassischen Kanalschätzung um die Vorzüge des maschinellen Lernens erweitert werden. Die Anwendung
der Erkenntnisse aus [MH] in der Kanalschätzung erscheint attraktiv.

# Verwendete Bibliotheken
Die Tabelle stellt eine Übersicht über alle verwendeten Bibliotheken
dar. Um einen reibungslosen Ablauf zu gewährleisten, wird die Anwendung der genannten
Bibliotheken empfohlen. Da die Bibliotheken aktualisiert werden, können bestimmte Funktionen in
anderen Versionen eventuell nicht mehr vorhanden sein.

| Bibliothek  | Version |
| ------------- | ------------- |
| Python  | 3.8.3  |
| Numpy  | 1.18.5  |
| Pandas  | 1.0.3  |
| Tensorflow  | 2.3.1 |
| tensorflow.keras  | 2.4.0  |
| matplotlib  | 3.2.2 |

# Licence

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
