# Porto Seguro Safe Driver Prediction

## Opis projekta
Cilj ovog projekta jeste izgradnja modela koji predviđa da li će određeni vozač u narednom periodu podneti zahtev za naknadu štete, na osnovu anonimnih podataka. Projekat je rađen iz predmeta **Primene algoritama mašinskog učenja** i prati **CRISP-DM** metodologiju.

Precizno predviđanje rizičnih vozača kompaniji omogućava balansiranje cena polisa osiguranja i direktno smanjuje finansijske gubitke kompanije.

## Podaci
Skup podataka potiče sa Kaggle takmičenja *Porto Seguro Safe Driver Prediction* i sadrži 595212 primera na skupu za obučavanje. Problem je formulisan kao binarna klasifikacija sa izraženom nebalansiranošću klasa - svega 3.64% vozaca pripada pozitivnoj klasi (podneli zahtev). 

*Napomena: Zbog veličine, podaci nisu uključeni u ovaj repozitorijum. Mogu se preuzeti sa [Kaggle platforme](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/data).*

## Metodologija (CRISP-DM)
Projekat je sproveden kroz sledeće faze, obuhvatajući ključne korake OZP procesa:

1. **Razumevanje problema i podataka:**
   * Definisan je poslovni cilj: precizna procena rizika radi optimizacije premija osiguranja.
   * Analizirana je struktura dataseta (595212 uzoraka) i vizuelizovana ekstremna nebalansiranost klasa (3.64% pozitivna klasa).
   * Identifikovano je da vrednost `-1` predstavlja prikrivene nedostajuće podatke u 13 različitih kolona.

2. **Priprema podataka:**
   * Stratifikovana podela na trening i test skup (80/20) pre primene bilo kakvih transformacija.
   * Rešavanje problema nedostajućih podataka putem kreiranja binarnih indikatora i imputacije (medijana za numeričke, modus za kategoričke atribute).
   * Primena Target Encoding-a za atribut sa visokom kardinalnošću (`ps_car_11_cat`) i One-Hot Encoding-a za ostale kategoričke promenljive.
   * Skaliranje vrednosti pomoću StandardScaler-a za modele osetljive na distancu.

3. **Baseline modelovanje:**
   * Modelovane referentne tačke korišćenjem `Logistic Regression`, `KNN` i `Decision Tree` algoritama.
   * Logistička regresija je prepoznata kao najjači baseline model (AUC = 0.6386), dok su ostali modeli pokazali slabiju diskriminativnu moć.

4. **Feature Engineering i Selection:**
   * Konstrukcija: Kreirane su nove grupe atributa uključujući sume binarnih vrednosti, statistike numeričkih grupa i proizvode visoko korelisanih parova.
   * Selekcija: Upoređena su tri pristupa za izbor najbitnijih varijabli: Filter (Mutual Information), Embedded (RF Importance) i Wrapper (RFE sa LogReg).

5. **Napredno modelovanje i Optimizacija:**
   * Primena ansambl modela: Random Forest i XGBoost nad različitim podskupovima atributa.
   * Optimizacija hiperparametara (n_estimators, max_depth, learning_rate, itd.) pomoću `RandomizedSearchCV` sa 30 iteracija i stratifikovanom 5-fold kros-validacijom.

6. **Finalna evaluacija:**
   * Odabir optimizovanog XGBoost modela na "fe" skupu atributa kao konačnog rešenja sa najboljim performansama.
   * Detaljna analiza diskriminativne moći modela putem ROC krive.

Kao glavna metrika evaluacije koristi se **Normalized Gini koeficijent** (Gini = 2 x AUC - 1).

## Finalni rezultati
Optimizovani **XGBoost model** treniran na kompletnom skupu atributa nakon feature engineering-a ostvario je najbolje performanse:
* **AUC:** 0.6503
* **Gini koeficijent:** 0.3006

Zanimljiv zaključak je da se `Logistic Regression` pokazala kao iznenađujuće jak osnovni model. Kao predlog za dalja unapređenja izdvaja se primena tehnika balansiranja klasa (SMOTE, class_weight) i detaljnija optimizacija hiperparametara.

## Struktura repozitorijuma
* `notebook.ipynb` - Python skripta sa celokupnom analizom i kodom.
* `PAMU - izveštaj (Porto Seguro).pdf` - Tehnički izveštaj o sprovedenom projektu.
