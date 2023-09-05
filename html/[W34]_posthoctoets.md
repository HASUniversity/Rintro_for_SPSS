# Posthoctoets

Met een ANOVA kan je testen of er wel/geen effect is van een verklarende variabele.
Als er een **significant** effect is, de H~0~ wordt verworpen, dan weet je wel dat er ergens een verschil zit tussen de verschillende groepen van de verklarende variabele, maar nog niet welke groepen onderling van elkaar verschillen.
Daarvoor is een **posthoctoets** ontwikkeld.

Je voert  een posthoctoets uit om te kijken welke groepen onderling significant van elkaar verschillen.
Waarom niet gewoon een aantal t-toetsen uitvoeren (je vergelijkt immers telkens twee groepen onderling)? 
Twee redenen:

* Ten eerste omdat je op zoek gaat of en waar onderlinge verschillen zitten. En voor al die toetsen bij elkaar wil je de kans op een type-1-fout (ten onrechte H~0~ verwerpen) op 0,05 houden. 
* Ten tweede omdat je dezelfde data gebruikt om verschillende toetsen uit te voeren.

In de loop van de afgelopen eeuw zijn er heel wat verschillende posthoctoetsen ontwikkeld en is het moeilijk door de bomen het bos te zien.
We gebruiken een aantal simpele stelregels:

* Alle groepen onderling vergelijken:
    * Bij ongeveer gelijke groepsgroottes: **Tukey HSD**.
    * Bij ongelijke groepsgrootte:  **Bonferroni**.
* Alleen behandelingen ten opzichte van een controlegroep vergelijken:
    * De **Dunnet's** posthoctoets.

Hoe voeren we die uit (als voorbeeld met de melkgiftdata, waarbij `fit` het resultaat is van de functie `lm()`)?

Er zijn binnen R veel manieren om posthoctoetsen uit te voeren.
Wij gebruiken binnen deze cursus de package **emmeans**, omdat we met deze package alle mogelijke posthoctoetsen kunnen uitvoeren. Wel zo gemakkelijk.


### Tukey HSD

Als voorbeeld het effect van ras op melkgift:

<img src="[W34]_posthoctoets_files/figure-html/unnamed-chunk-1-1.png" width="672" />



Tukey HSD doe je op de volgende manier: 


```r
library(emmeans)
emmeans(fit, specs = pairwise ~ ras)
```

```
## $emmeans
##  ras emmean     SE df lower.CL upper.CL
##  HF    2.95 0.0459  9     2.85     3.06
##  MRY   3.21 0.0513  9     3.09     3.33
##  RHF   2.97 0.0592  9     2.83     3.10
## 
## Confidence level used: 0.95 
## 
## $contrasts
##  contrast  estimate     SE df t.ratio p.value
##  HF - MRY   -0.2580 0.0688  9  -3.748  0.0114
##  HF - RHF   -0.0147 0.0749  9  -0.196  0.9791
##  MRY - RHF   0.2433 0.0784  9   3.105  0.0307
## 
## P value adjustment: tukey method for comparing a family of 3 estimates
```

Het resultaat bestaat uit twee onderdelen:

* Het gemiddelde effect van iedere factor (officieel de *estimated marginal means* genoemd, weet je gelijk waar de naam van de package vandaan komt). Van ieder effect is ook de standaardfout en het betrouwbaarheidsinterval gegeven.
* Daaronder staan de *contrasts*. Dat zijn de onderlinge vergelijken. Het verschil wordt gegeven en er wordt een t-toets uitgevoerd waarbij de overschrijdingskans gecorrigeerd is. Standaard wordt de TukeyHSD-correctie gebruikt.

De bovenste en onderste vergelijking zijn significant (p<0.05).
Dus MRY verschilt significant van HF en RHF, maar HF en RHF verschillen onderling niet significant van elkaar.


### Bonferroni

Willen we nu een Bonferroni-posthoc uitvoeren, dan hoeven maar een argument toe te voegen:


```r
emmeans(fit, specs = pairwise ~ ras, adjust = "bonf")
```

```
## $emmeans
##  ras emmean     SE df lower.CL upper.CL
##  HF    2.95 0.0459  9     2.85     3.06
##  MRY   3.21 0.0513  9     3.09     3.33
##  RHF   2.97 0.0592  9     2.83     3.10
## 
## Confidence level used: 0.95 
## 
## $contrasts
##  contrast  estimate     SE df t.ratio p.value
##  HF - MRY   -0.2580 0.0688  9  -3.748  0.0137
##  HF - RHF   -0.0147 0.0749  9  -0.196  1.0000
##  MRY - RHF   0.2433 0.0784  9   3.105  0.0379
## 
## P value adjustment: bonferroni method for 3 tests
```

Het bovenste deel blijft precies hetzelfd, maar je ziet (kleine) verschillen in de p-waarde.


Deze test is iets conservatiever (voorzichtiger) dus de p-waardes zijn een fractie hoger.


### Dunnet's

Voor de Dunnet's Posthoctoets gebruiken we als *specs* niet de pairwise, maar trt.vs.ctrl (*treatment versus control):


```r
emmeans(fit, specs = trt.vs.ctrl ~ ras)
```

```
## $emmeans
##  ras emmean     SE df lower.CL upper.CL
##  HF    2.95 0.0459  9     2.85     3.06
##  MRY   3.21 0.0513  9     3.09     3.33
##  RHF   2.97 0.0592  9     2.83     3.10
## 
## Confidence level used: 0.95 
## 
## $contrasts
##  contrast estimate     SE df t.ratio p.value
##  MRY - HF   0.2580 0.0688  9   3.748  0.0087
##  RHF - HF   0.0147 0.0749  9   0.196  0.9637
## 
## P value adjustment: dunnettx method for 2 tests
```

De functie pakt automatisch de eerste factor (hier **HF**) als controle.

Met het argument `ref`` kan je aangeven welke factor je als controle wilt.
In onderstaand geval willen we de tweede factor als controle:


```r
emmeans(fit, specs = trt.vs.ctrl ~ ras, ref = 2)
```

```
## $emmeans
##  ras emmean     SE df lower.CL upper.CL
##  HF    2.95 0.0459  9     2.85     3.06
##  MRY   3.21 0.0513  9     3.09     3.33
##  RHF   2.97 0.0592  9     2.83     3.10
## 
## Confidence level used: 0.95 
## 
## $contrasts
##  contrast  estimate     SE df t.ratio p.value
##  HF - MRY    -0.258 0.0688  9  -3.748  0.0087
##  RHF - MRY   -0.243 0.0784  9  -3.105  0.0237
## 
## P value adjustment: dunnettx method for 2 tests
```

Er is nog een variant voor als je de laatste factor als controle wilt.
Dan moet je een k achter ctrl zetten:



```r
emmeans(fit, specs = trt.vs.ctrlk ~ ras)
```

```
## $emmeans
##  ras emmean     SE df lower.CL upper.CL
##  HF    2.95 0.0459  9     2.85     3.06
##  MRY   3.21 0.0513  9     3.09     3.33
##  RHF   2.97 0.0592  9     2.83     3.10
## 
## Confidence level used: 0.95 
## 
## $contrasts
##  contrast  estimate     SE df t.ratio p.value
##  HF - RHF   -0.0147 0.0749  9  -0.196  0.9637
##  MRY - RHF   0.2433 0.0784  9   3.105  0.0237
## 
## P value adjustment: dunnettx method for 2 tests
```



    
::: {.exercise #posthoc}
posthoctoets

* importeer de package emmeans (via `install.packages("emmeans")`)
* Voer de juiste posthoctoets uit bij voorgaande opgave over silicium.

:::


## Opgaven hoofdstuk 15


::: {.exercise #les5}

Gebruik uit onderstaande *Practice Problems* de datasets:

15.1, 15.4, 15.8

I.p.v. vragen uit het boek, met dezelfde dataset de volgende vragen beantwoorden:

* Data invoeren
* Scatterplot maken
* Hypotheses opstellen
* ANOVA uitvoeren, p-waarden opschrijven
* De juiste posthoc-toets uitvoeren
* Conclusies trekken

:::

