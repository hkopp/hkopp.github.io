---
layout: post
category : brainteaser
tags : [math, set theory]
---
{% include math %}

##Einführung
Vor einer Weile hat jemand einen Ausdruck aus einem Mathebuch zu einem
Treffen meiner lokalen Hackergruppe gebracht. Es ging um
Hüllensysteme; tiefste Mengentheorie, von der ich noch nie gehört
habe. Seine Behauptung war, dass ein Teil davon nicht stimmen könnte.
Aus welchen Buch es genau war, wollte er nicht sagen.
Ich habe mich nun damit beschäftigt und die Korrektheit der
betreffenden Aussage bewiesen.

##Mittelteil
Ich habe ein Bild des Scans bekommen. Der Teil um den es geht ist die
unterstrichene Aussage im zweiten Bild.

<img src="{{ site.url }}/assets/huellensysteme/huellensystemeI.jpg" alt="Huellensysteme_I" style="width: 800px;"/>

<img src="{{ site.url }}/assets/huellensysteme/huellensystemeII.jpg" alt="Huellensysteme_II" style="width: 800px;"/>

Ich verwende im Folgenden für eine Menge $$E$$ die Notation $$P(E)$$ als
Potenzmenge von $$E$$.


###Definitionen
Sei $$E$$ eine Menge, ein Hüllensystem $$H\subseteq P(E)$$ heißt
_Hüllensystem_, falls gilt:

1. $$E\subseteq H$$ und
2. $$N\subseteq H$$, $$N\neq\emptyset\Rightarrow\bigcap N\in H$$


Sei $$F: P(E)\rightarrow P(E)$$ eine Abbildung. Eine Teilmenge
$$X\subseteq E$$ von $$E$$ heißt _$$F$$-abgeschlossen_, falls für alle
$$Y\subseteq X$$, $$Y\in \operatorname{def}(F)$$ gilt, dass
$$F(Y)\subseteq X$$.

##Ende
Hier nun der Beweis:

####Voraussetzung
Sei $$E$$ eine Menge, $$F: P(E)\rightarrow P(E)$$. Sei weiterhin
$$H:=\{X\subseteq E, X$$ ist $$F$$-abgeschlossen$$\}$$ die Menge der
$$F$$-abgeschlossen Teilmengen von $$E$$.

####Behauptung
Dann ist $$H$$ ein Hüllensystem auf $$E$$.

####Beweis

Dass $$H\subseteq P(E)$$ ist klar per definitionem. Es bleiben also die
anderen beiden Eigenschaften in der Definition eines Hüllensystems zu
zeigen.

1. z.z. $$E\in H$$. Da $$F(E)\subseteq E$$ aufgrund der Definition von
$$F$$ ist $$E$$ $$F$$-abgeschlossen und damit $$E\in H$$ w.z.b.w.

2. z.z. $$N\subseteq H$$, $$N\neq\emptyset\Rightarrow\bigcap N\in H$$  
Anschaulich bedeutet das, dass der Schnitt von $$F$$-abgeschlossenen
Teilmengen von $$E$$ wieder eine $$F$$-abgeschlossene Teilmenge von
$$E$$ ergibt. Sei also $$N\subseteq H$$, $$N\neq\emptyset$$ beliebig
aber fest. Sei $$N=\{n_i, i\in I\}$$, wobei $$I$$ eine Indexmenge ist.
Beachte, $$N$$ muss nicht endlich oder abzählbar sein. Dann sind die
$$n_i$$ $$F$$-abgeschlossen für alle $$i$$, da $$N\subseteq H$$.
Insbesondere, aufgrund der Definition von $$F$$-abgeschlossenheit gilt
für alle $$Y\subseteq n_i$$ mit $$Y\in \operatorname{def}(F)$$, dass
$$F(Y)\subseteq n_i$$.  
Wir müssen nun zeigen, dass $$Y\subseteq \bigcap N$$, $$Y\in
\mathtt{def}(F)\Rightarrow F(Y)\subseteq \bigcap N$$
Wähle also $$Y\subseteq\bigcap N$$ beliebig aber fest. Dann ist
$$Y\subseteq n_i$$ für alle $$i$$. Damit ist $$F(Y)\subseteq n_i$$ für
alle $$i$$, da alle $$n_i$$ $$F$$-abgeschlossen sind.
Damit ist $$F(X)\subseteq\bigcap N$$

$$\square$$
