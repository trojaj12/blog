---
title: Intel x86_64
permalink: /intel.md
layout: default 
---

# Intel x86_64
Created: 21. 02. 2022 11:32

## Registry
-  ```EAX```, ```ECX```, ```EDX```, ```EBX``` - general purpous
	- 32bit
	- Nesjou atomicke. registr ```EAX``` zaroven obsahuje ```AX```, kde ```AX``` = ```EAX[4:8]```...
- ```ESI```, ```EDI``` - indexove registry (podpora prace s indexy)
- ```EIP``` - adresa dalsi instrukce
- ```ES```, ```CS```, ```SS```, ```SD```, ```FS```, ```GS``` - konstanty pro OS, progam by s nymi nemel pracovat
- ```EFL``` - obsahuje flagy
- ```ESP``` - obsahuje odkaz na vrchol zasobniku
- ```EBP``` - pomocny registr pro zalohu zasobniku. Do tohoto registru se vzdy pri vstupu do funkce ulozi hodnota ```ESP```. Konvence nařizuje tento registru ukládat na stacku na začátku každé funkce, abychom tento registr zachovali pro volajícího.
## Zádobník
- Datová struktura (*LIFO*), kam si program ukládá lokální proměnné. Má velikost potřebnou pro uložení všech proměných. Argumnety pro volané funkce se ukládají nad zásobník před voláním ```call```. Zásobník ovykle vypadá následovně: ![[Intel x86_64#^d31cc8]]
- Implementovana pomoci "pole", postupne se plni stack od vyžších adres k nižším, pro hlídání stacku nám se používjí registry ```ESP```,```EBP```.
- Jelikož je stack fixní velikosti, tak 

## Intel instrukce
- ```CALL <instrukce_programu>``` - vola funkce
	- ulozi navratovou adresu na zasobnik (```EIP + size_od(call)```) To provede stejně jako ```push```. 
	- nastavi registr ```EIP``` a tim skoci
	- Před každým použitím ```call``` se provádí operace na stacku, které odpovýdají volacím konvencím. [[1. Seznámení s nástrojem OllyDbg#^4c4d56|Volací konvece vstupu do funkce]] [[1. Seznámení s nástrojem OllyDbg#^f026bd|Volací konvecne opuštění funkce]]
- ```PUSH <OBSAH>```
	- vlozi obsah na zasobnik a posune ```ESP```
- ```POP <do_ceho>```
	- ziska data ze zasobniku a ulozi je
- ```RET```
	- precte ulozenou navratovou hodnotu a nastavi ```EIP``` na tu adresu - tim skoci ven z funkce
- ```JMP <kam>```
	- skoci na pozici *kam*, bez jakekoliv podminky.
- ```J<x> <kam>```
	- podminey skok, ktery skace, pouze pokud podminak (```jnz``` - jum if not zero, ```jz``` - jum if zero...)
- ```MOV <cil> <zdroj>```
	- premisti *zdoj* do *cil*.
	- Muze kopirovat registry, konstanty, pameť ```MOV EAX, [0x1234]``` z adresy 0x1234 nasyp data do eax ```EAX = (void*)0x1234 ```.
- ```LEA <cil> <zdroj>```
	- Zjisti aresu argumentu neceho
	- ```LEA EAX, [0x1234]``` = ```EAX = 0x1234 ``` = ```MOV EAX, 0x1234```

## Další
1. 
## Reference
1.  [[# Seznámení s nástrojem OllyDbg]]
  
  ### Zásobník - schema
  ```
	-> pripadne argumenty funkce, kterou budeme volat
ESP	-> lokalni promenne (vrchol stacku)
	-> lokalni promenne
	-> lokalni promenne (dno stacku)
EBP -> zaloha EBP volajiciho teto funkce
    -> navratova adresa z funkce
	-> arg0 funkce
	-> arg1 funkce
  ```

^d31cc8

## Cards
### Jaký význam mají registry: `ESP`, `EBP`  #flashcard/BI-BEK/Intelx86_64
-  ```ESP``` - obsahuje odkaz na vrchol zasobniku
- ```EBP``` - pomocny registr pro zalohu zasobniku. Do tohoto registru se vzdy pri vstupu do funkce ulozi hodnota ```ESP```. Konvence nařizuje tento registru ukládat na stacku na začátku každé funkce, abychom tento registr zachovali pro volajícího.
- - -
  ### Co je to zásobník v programu? #flashcard/BI-BEK/BI-OSY
- Zásobník je datová struktura *LIFO*, kam si program ukládá lokální proměnné. 
- Velikost zásobníku je velikost potřebných lokálních proměnných ve funkci + zarovnání.
- Zásobník nelze dinamicky alokovat, proto se musí dinamicky alokovat na heapě
- - -
  ### Jak je na dnešníh PC implementován zásobník? #flashcard/BI-BEK/BI-OSY
- Na přiděleném rozsahu paměti jako pole, program si pouze drží pointer na aktualní vrchol zásobníku
