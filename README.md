
# Introducere 

 Pentru a avea un mod de viata sanatos, unele persoane isi doresc sa verifice valoarea nutritiva a alimentelor.Organismul uman 
are nevoie zilnic de o cantitate variabila de substante nutritive în functie de varsta, sex, activitate profesionala depusa, 
particularitati fiziologice. Acest necesar zilnic de substante nutritive se exprima ca necesar energetic, în kilojouli (kJ) sau în kilocalorii (kcal)
si necesar în trofinele de baza (glucide, lipide, substante proteice în grame, iar vitaminele A, D, B1, B2, C, PP, 
elemente minerale Ca, Fe, P în miligrame).
 Pentru un produs alimentar, valoarea nutritiva, respectiv substantele nutritive pe care le furnizeaza organismului uman, 
într-o proportie mai mare si mai usor asimilabila, constituie criteriu major în aprecierea calitatii.
 Aplicatia web dezvoltata are scopul de a afisa lista de nutrienti existenta in ingredientele unei retete aleasa de utilizator.


## Descriere problema:

 

 Am creat o aplicatie web pentru facilitarea obtinerii acestor informatii prin integrarea a 2 API-uri, primul este o baza de date cu retete,
iar a doua este o baza de date cu nutrienti.
 Pentru afisare se face un request intr-o baza de date a primului API  care ne returneaza o lista de retete,
prioritizam prima reteta deoarece este cea mai apropiata de alimentul introdus, al carei link il afisam dedesubtul numelui retetei si luam 
ingredientele intalnite in reteta respectiva, pentru fiecare ingredient din reteta folosim un alt API din care face inca un request 
pentru a lua informatii aditionale despre fiecare ingredient, respectiv nutrientii.
 Dupa prelucrarea acestor date se afiseaza o tabela care continelista de ingrediente impreuna cu cantitatile necesare retetei
si valoarea nutritiva a acestora.



## Descriere API

##### https://www.themealdb.com/

TheMealDB: o bază de date deschisă și plină de rețete de rețete din întreaga lume. API-ul este liber de utilizat în scopuri educaționale, dar necesită o cheie API dacă aplicația este destinată producției,
Acesta consta intr-un API JSON foarte simplu.

Limitări
- Există doar câteva rețete disponibile.

##### https://fdc.nal.usda.gov/index.html

API-ul FoodData Central oferă acces REST la FoodData Central (FDC). Este destinat  să ajute dezvoltatorii de aplicații
care doresc să încorporeze date despre nutrienți în aplicațiile sau site-urile lor web.
Cheia API trebuie încorporată în fiecare cerere API

Caracteristici
- API-ul oferă două functionalitati: cea de căutare a alimentelor, care returnează alimentele care corespund criteriilor de căutare dorite,
și cea pentru Food Details, care returnează detalii despre un anumit aliment.

Limitări
- FoodData Central limitează în prezent numărul de solicitări API la o rată implicită de 3.600 de solicitări pe oră pe adresă IP,
deoarece acest lucru este adecvat pentru majoritatea aplicațiilor. Depășirea acestei limite va determina blocarea temporară a tastei API timp de 1 oră


## Flux de date 

##### Exemple de request / response

In acest proiect am folosit doar request-uri, unul request in baza de date de alimente pentru a returna  o lista de retete

    await fetch('https://www.themealdb.com/api/json/v1/1/search.php?s=' + input_food.val(), {
                                      method: 'GET'
                                    })
                                    .then(response => response.json())
                                    .then(async (result) => {
                                        let object = {}
                                      console.log('Success:', result.meals[0]);
                                      object.recipeName = result.meals[0].strMeal;
                                      object.recipeLink = result.meals[0].strYoutube;
                                      object.ingredients = []
                                      if(result.meals.length > 0){
                                          for(let i=1 ; i< 6 ;i++){  //5
                                              let innerObject = {}
                                              console.log(result.meals[0])
                                              innerObject.name = result.meals[0]['strIngredient'+i]
                                              innerObject.quantity = result.meals[0]['strMeasure'+i]
                                            
in acest exemplu se poate observa ca se returneaza primele 5 ingrediente din lista.
Pentru accesarea acestui API nu a fost nevoie de APIKey.
  
 Al doilea request este pentru baza de date ce returneaza detalii despre un anume aliment, cum ar fi nutrientii.
 
     await fetch('https://api.nal.usda.gov/fdc/v1/foods/search?api_key=tSyqSAzNtohCR4BCzsxpFRnvKpk2pEy6MDPHn5X4&query=' + result.meals[0]['strIngredient'+i], {
                                                  method: 'GET',
                                                })
                                                .then(response => response.json())
                                                .then(result => {
                                                  //console.log('Success:', result);
                                                  if(result.foods.length > 0){
                                                      innerObject.foodNutrients = []
                                                      for(let j = 0 ; j< result.foods[0].foodNutrients.length; j++){
                                                          let innerInnerObject = {}
                                                            innerInnerObject.name = result.foods[0].foodNutrients[j].nutrientName;
                                                            innerInnerObject.value = result.foods[0].foodNutrients[j].value;
                                                            innerObject.foodNutrients.push(innerInnerObject)
                                                      }
   
 folosind acest request returnam toti nutrientii disponibili in API-ul integrat.
 Pentru folosirea acestui API am facut o cere de APIKey ce a fost aprobata intr-un timp foarte scurt.


## Capturi ecran aplicație 

![](images/1.jpg)

## Referinte

www.stackoverflow.com
Cursuri/seminarii cloud computing


