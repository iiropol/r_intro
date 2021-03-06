
Datan sisään lukeminen
======================

Tässä osiossa tutustutaan datan sisään lukemiseen ja sisäänluetun datan tarkistamiseen. Tähän mennessä kaikki kurssilla käsitelty data on luotu R:ssä. Useimmiten R:llä käsiteltävä data on kuitenkin tallennettu tiedostoon, joka on luotu automaattisesti jollain ohjelmalla tai kirjattu esim. Excelissä.

Tässä esitellyt funktiot lukevat erilaisia tiedostoja, mutta kaikki palauttavat datan data frame-muodossa (voit kerrata data framen toimintaa [viime viikon materiaalista](https://github.com/antonvsdata/r_intro/tree/master/viikko_3#data-frame)). Data frame toimii tähän tarkoitukseen hyvin, sillä siihen voi tallentaa niin numeerisia kuin tekstimuotoisia muuttujia.

Lopussa käydään myös läpi tapoja lukea Excel ja SPSS-tiedostoja. Näitä tiedostoja ei käsitellä kurssin tehtävissä, mutta on hyvä tietää, että niitä voi lukea R:ään suoraan muuttamatta niitä ensin tekstitiedostoiksi.

Tekstitiedostot
---------------

Tekstitiedosto tarkoittaa tässä tapauksessa tiedostoa, joka ei sisällä tekstin lisäksi mitään muuta, kuten erilaisia muotoilutietoja. Tekstitiedostojen yleisimmät tiedostopäätteet ovat .txt ja .csv (comma separated value, tästä lisää pian). Esim. Excelin .xlsx-tiedostot tai Wordin .docx-tiedostot eivät ole tekstitiedostoja, koska niissä on paljon muutakin tietoa tekstin lisäksi.

### read.table()

Kun dataa tallennetaan tekstitiedostoon, tiedoston ensimmäisellä rivillä on usein sarakkeiden nimet, ja seuraavilla riveillä mahdollisesti rivin nimi, ja sitten sarakkeiden arvot. Jokaisen kentän tulee olla erotettu samalla merkillä (field separator character). Yleisiä erotinmerkkejä ovat sarkain eli tab, välilyönti ja pilkku. Alla olevassa esimerkissä on neljältä kuvitteelliselta koehenkilöltä mitattu puna-vihervärisokeuteen liitettyjen geenien OPN1LW ja OPN1MW ilmentymistasot (lukuarvot ovat allekirjoittaneen hihasta). Tässä eri arvot on erotettu sarkaimella.

    Subject_ID  OPN1LW  OPN1MW
    ANKL    11264   12365
    DIPR    10636   12725
    PEPA    5630    13248
    BRWA    8294    13060

Tämä data löytyy myös oheisesta tiedostosta `gene_data.txt`. Tekstitiedostot voi lukea sisään funktiolla `read.table()`, jolla on tiedoston polun (file path) lisäksi monta muutakin argumenttia, joista tärkeimmät ovat:

-   `header`: looginen arvo (TRUE/FALSE), jolla kerrotaan funktiolle, onko ensimmäisellä rivillä sarakkeiden nimet vai ei
-   `sep`: erotinmerkki, jolla arvot on eroteltu
-   `dec`: desimaalierotin eli desimaalilukujen merkki, jolla desimaalit on eroteltu. Tämä on tärkeä lähinnä suomalaisille, koska Suomessa desimaalierotin on jostain syystä pilkku, eikä piste kuten useimmissa muissa maissa.

Luetaan edellisen esimerkin data R:ään data frameksi:

``` r
gene_data <- read.table("gene_data.txt", header = TRUE)
gene_data
```

    ##   Subject_ID OPN1LW OPN1MW
    ## 1       ANKL  11264  12365
    ## 2       DIPR  10636  12725
    ## 3       PEPA   5630  13248
    ## 4       BRWA   8294  13060

Yllä olevassa esimerkissä ei määritelty erikseen erotinmerkkiä, jolloin erotinmerkiksi tulkitaan kaikki tyhjä tila (white space) eli välilyönnit, sarkaimet jne. Halutessaan erotinmerkin voi myös asettaa. Jos erotinmerkki on sarkain, tulee asettaa `sep = "\t"`

``` r
gene_data <- read.table("gene_data.txt", sep = "\t", header = TRUE)
gene_data
```

    ##   Subject_ID OPN1LW OPN1MW
    ## 1       ANKL  11264  12365
    ## 2       DIPR  10636  12725
    ## 3       PEPA   5630  13248
    ## 4       BRWA   8294  13060

Kuten yllä huomattiin, sarkain erotinmerkkinä merkataan `"\t"`, eikä hipsuilla, joiden sisään laitetaan tyhjää tilaa sarkainnäppäimellä. Tämä on yksi esimerkki koodinvaihtomerkin (escape character) `\` käytöstä. R:ssä ja ohjelmointikielissä ylipäätään kenoviiva toimii koodinvaihtomerkkinä, eli sitä ei käsitellä kuin muita merkkejä, vaan se muuttaa seuraavan merkin toimintaa. Usein tämä tarkoittaa sitä, että kenoviivan avulla merkataan sarkainta, rivinvaihtoa (newline, `\n`) ja muita erikoismerkkejä. Koodinvaihtomerkin käyttöä ei tarvitse osata tämän enempää, mutta se esitellään tässä, koska se aiheuttaa ongelmia Windowsin käyttäjille.

Windowsin tiedostopoluissa kansioiden välissä on kenoviiva, kun taas Mac- ja Linux-järjestelmissä käytetään kauttaviivaa `/`. Koska R:ssä kenoviiva on koodinvaihtomerkki, tulee R:ssä käyttää tiedostopoluissa Macin ja Linuxien tyyliä. Eli kun haluaa lukea tiedoston R:ään Windowsissa, kenoviivat `\` pitää korvata kauttaviivoilla `/`, jotta R ei mene sekaisin.

Luetaan seuraavaksi sisään data-kansiossa oleva tiedosto tooth\_growth.csv, joka sisältää dataa tutkimuksesta, jossa tutkittiin c-vitamiinin vaikutusta hampaiden kasvuun marsuilla. .csv-tiedostopääte tulee sanoista comma separated value, eli arvot on eroteltu pilkulla. Annetaan siis sep-parametriksi ",". Tämä tiedosto sisältää myös rivien nimet ensimmäisessä sarakkeessa. Tämä voidaan kertoa `read.table()`-funktiolle parametrilla row.names, jonka arvoksi voi asettaa sarakkeen numeron, josta rivien nimet napataan.

``` r
tooth <- read.table("data/tooth_growth.csv", header = TRUE, sep = ",", row.names = 1)
tooth
```

    ##     len supp dose
    ## 34  9.7   OJ  0.5
    ## 16 17.3   VC  1.0
    ## 55 24.8   OJ  2.0
    ## 44 26.4   OJ  1.0
    ## 58 27.3   OJ  2.0
    ## 26 32.5   VC  2.0
    ## 14 17.3   VC  1.0
    ## 60 23.0   OJ  2.0
    ## 15 22.5   VC  1.0
    ## 9   5.2   VC  0.5

Tässä tutkimuksessa marsuille annettiin C-vitamiinia eri annoksina (dose, mitattu milligrammoina), joka appelsiinimehussa (OJ) tai askorbiinihappona (VC) ja mitattiin odontoblastien (hammasluun emosolu) pituus (len).

### read.csv()

.csv-tiedostot ovat niin yleisiä, että niiden lukemiseen on oma funktion: `read.csv`, joka on käytännössä sama funktio kuin `read.table`, mutta parametrien oletusarvot ovat erilaiset, nii että `read.csv(file)` ~ \`\`\`read.table(file, header = TRUE, sep = ",")).

``` r
tooth <- read.csv("data/tooth_growth.csv", row.names = 1)
tooth
```

    ##     len supp dose
    ## 34  9.7   OJ  0.5
    ## 16 17.3   VC  1.0
    ## 55 24.8   OJ  2.0
    ## 44 26.4   OJ  1.0
    ## 58 27.3   OJ  2.0
    ## 26 32.5   VC  2.0
    ## 14 17.3   VC  1.0
    ## 60 23.0   OJ  2.0
    ## 15 22.5   VC  1.0
    ## 9   5.2   VC  0.5

#### read.csv2()

HUOM: Koska Suomessa pilkkua käytetään desimaalierottimena, kenttien rajaaminen pilkulla ei toimi. Käytännössä tämä näkyy siten, että suomenkielinen Excel tallentaa .csv-tiedosto oletuksena muodossa, jossa desimaalierottimena on pilkku ja kenttien välissä puolipilkku ";". Jos siis olet tallentanut Excelistä taulukon .csv-muotoon ja sen lukeminen R:ään aiheuttaa outouksia, kyse on todennäköisesti tästä. Onneksi R:ssä on valmiina funktio `read.csv2()`, joka osaa lukea suomalaiset .csv-tiedostot oikein.

Data framen rakenteen tutkiminen
--------------------------------

Kun data on luettu sisään R:ään, kannattaa aina tarkistaa, että kaikki data on luettu oikein. Tässä muutama vinkki data framen tutkimiseen, joista osaa käsiteltiin jo viime kerralla:

`dim()` antaa data framen dimensiot, eli rivien ja sarakkeiden määrän.
`View()` avaa data framen erilliseen ikkunaan, jossa sitä voi tarkastella. Suositellaan vain pienemmille data frameille `str()` kertoo rivien ja sarakkeiden määrät sekä kaikkien sarakkeiden luokat. Kätevä tapa tarkistaa mm. että lukuja sisältävät sarakkeet eivät ole vahingossa muuttuneet merkkijonoiksi. `table()` on kätevä kategoristen sarakkeiden tutkimiseen. Se kertoo, kuinka monta havaintoa muuttujan arvoilla on. `table()` voi ottaa vastaan myös kaksi kategorista muuttujaa, ja laskee jokaiselle muuttujien arvojen yhdistelmälle havaintojen lukumäärän.

Katsotaan, mitä `str()` kertoo juuri lukemastamme tooth-datasta.

``` r
str(tooth)
```

    ## 'data.frame':    10 obs. of  3 variables:
    ##  $ len : num  9.7 17.3 24.8 26.4 27.3 32.5 17.3 23 22.5 5.2
    ##  $ supp: Factor w/ 2 levels "OJ","VC": 1 2 1 1 1 2 2 1 2 2
    ##  $ dose: num  0.5 1 2 1 2 2 1 2 1 0.5

Kuten näimme aiemmin, mukana on 10 havaintoa ja 3 muuttujaa. len ja dose ovat luokkaa numeric eli desimaalilukuja ja supp on luokkaa factor. Factor-vektoreita käsitellään enemmän lineaaristen mallien yhteydessä, mutta niillä merkitään usein kategorisia muuttujia.

Lasketaan seuraavaksi, kuinka monelle marsulle annettiin appelsiinimehua ja kuinka monelle askorbiinihappoa.

``` r
table(tooth$supp)
```

    ## 
    ## OJ VC 
    ##  5  5

Kumpaakin annostelutapaa käytettiin siis viisi kertaa. Voimme myös selvittää, miten eri annokset jakautuvat annostelutavan suhteen:

``` r
table(tooth$supp, tooth$dose)
```

    ##     
    ##      0.5 1 2
    ##   OJ   1 1 3
    ##   VC   1 3 1

Appelsiinimehuna annettiin siis 0.5 mg ja 1 mg annoksia kumpaakin 1 kappale, ja 2 mg annoksia 3 kappaletta.

### R:n sisäänrakennetut datasetit

R:ssä on monta sisäänrakennettua (built-in) datasettiä. Näitä on kätevää käyttää nopeaan testaamiseen, ja ne vilahtelevatkin usein R-oppaissa. Esimerkiksi aikaisempi odontoblastien pituuksia sisältävä datasettimme on oikeastaan pieni otos R:n sisäisestä datasetistä ToothGrowth.

R:n sisäiset datasetit ovat koko ajan käytettävissä, vaikka ne eivät näy RStudion ympäristössä (Environment). Voimme esimerkiksi katsoa, millainen rakenne kokonaisella ToothGroth-datasetillä on:

``` r
str(ToothGrowth)
```

    ## 'data.frame':    60 obs. of  3 variables:
    ##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
    ##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ dose: num  0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 ...

R:n datasettejä voi käyttää moneen eri tarkoitukseen, kuten datan visualisoinnin tai tilastollisten toimenpiteiden testaamiseen. Listan kaikista R:n dataseteistä saa komennolla `data()`. Tarkempia tietoja datasetistä saa help-sivulta kuten funktioden tapauksessa, esimerkiksi `?ToothGrowth`

Muut tiedostot
--------------

### Excel

Excelin käyttämiä .xlsx-tiedostoja voi lukea suoraan R:ään, vaikka jossain netissä olevissa ohjeissa suositellaan niiden muuntamista .csv-muotoon. Tätä varten pitää asentaa openxlsx-paketti. Tämän voi tehdä RStudion Packages-valikoksta tai suoraan komennolla `install.packages("openxlsx")`. Tästä paketista löytyvät funktiot `read.xlsx()` ja `write.xlsx()`, joilla voi lukea ja kirjoittaa .xlsx-tiedostoja.

HUOM: .xlsx-tiedostoja varten on olemassa myös xlsx-paketti, mutta se tarvitsee Javaa ja erilaisten Java- ja R-versioiden kanssa voi tulla yhteensopivuusongelmia. Suosittelen siis openxlsx-pakettia, se on toiminut hyvin.

### SPSS

Eri tutkimusryhmissä dataa säilytetään usein SPSS-tiedostoissa (.sav). SPSS-tiedostojen käsittelyyn voi käyttää haven-paketin funktioita `read_sav` ja `write_sav`. haven-paketissa on funktiot myös Stata:n ja SAS:n datatiedostoille.

SPSS-tiedostoja voi lukea myös foreign-paketin avulla, mutta ainakin minulla on parempia kokemuksia haven-paketista. haven on myös osa tidyverse-pakettikokoelmaa, joten oletan sen pysyvän hyvin ajan tasalla jatkossakin.

Vinkkejä tehtäviin
------------------

Datan sisään lukemiseen liittyvät tehtävät ovat melko suorviivaisia yhtä poikkeusta lukuun ottamatta. Ainakin minulle Rkurssi-paketti generoi tiedoston, jonka tiedostopääte oli .csv, mutta alkiot oli erotettu sarkaimella, ei pilkulla. Jos siis saat outoja virheilmoituksia, tarkasta tiedoston rakenne joko R:n komennolla `file.show()` tai avaamalla tiedosto esim. Notepadilla.
