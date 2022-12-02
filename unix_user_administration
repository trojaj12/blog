# Administrace uživatelů UNIX
Created: 15. 02. 2022 23:12

- Ke správě uživatelů v linuxu na lokálním počítači se používají 3 hlavní kofigurační soubory:
	- [[_etc_passwd|/etc/passwd]] - informace o uživatelých
	- [[_etc_shadow|/etc/shadow]] - hesla uživatelů
	- [[_etc_group|/etc/group]] - informace o skupinách
- Uživatelé mají dva hlavní identifikátory
	- **UID**linuxu
		- uid je identifikátor uživatele (každý uživatel by měl mít jinačí a je dle něho identifikován)
		- Dle kovence má *root* *UID* = 0, sytémový uživatelé mají *UID*  < 100 a běžní uživatelé mají *UID* > 1000
	- **GUI**
		- Jedná se o identifikátor skupiny.
	- Každý uživatel má jedno **UID** a jedno **GID**, které určuje jeho primární skupinu. Primární skupina je uvedena v  [[_etc_passwd|/etc/passwd]], neprimárnáí skupiny jsou pak specifikované v [[_etc_group|/etc/group]], kde si každá skupina drží list uživatelů, pro které je neprimární.
- ## Příkazy
	- ```useradd```
	- ```userdel```
	- ```usermod```
	- ```groupadd```
	- ```groupdel```
	- ```groupmod```
	- ```passwd```

## Na co si dát pozor
- Při za zamknutí uživatele se zamkne pouze jeho přihlášení heslem, ale pomocí klíče se stále může přihlásit. Zamknutí provede přidání znamku ```!``` před hash, a tím ho zičí. Pokud ale budeme chtít zakázat i přihlášení pomocí klíč, tak musíme před hash appendnout ```*LK*```, pak dojde i k zamčení pro klíč.

## Další
1. 
## Reference
1. [[20220215-Administrace UNIX]]
2. [[@zdenekmuzikarPrincipyAdministrace]]
- - -
- - -
## Cards 

### Jáké jsou 3 základní konfigurační soubory pro správu uživatelů v unix systémech? #flashcard/BI-ADU/UNIX/Linux/LInuxUsers
- [[_etc_passwd|/etc/passwd]] - informace o uživatelých
- [[_etc_group|/etc/group]] - informace o skupinách
- [[_etc_shadow|/etc/shadow]] - hesla uživatelů
- - -

  ### Co to je **UID**, **GID** #flashcard/BI-ADU/UNIX/Linux/LInuxUsers 
- **UID**
	- uid je identifikátor uživatele (každý uživatel by měl mít jinačí a je dle něho identifikován)
	- Dle kovence má *root* *UID* = 0, sytémový uživatelé mají *UID*  < 100 a běžní uživatelé mají *UID* > 1000
- **GUI**
	- Jedná se o identifikátor skupiny.
- Každý uživatel má jedno **UID** a jedno **GID**, které určuje jeho primární skupinu. Primární skupina je uvedena v  [[_etc_passwd|/etc/passwd]], neprimárnáí skupiny jsou pak specifikované v [[_etc_group|/etc/group]], kde si každá skupina drží list uživatelů, pro které je neprimární.
- - -


  ### Jako jsou příkazy pro administraic uživatelů: #flashcard/BI-ADU/UNIX/Linux/LInuxUsers 
- Přidání, smazání, modifikobání užiatele
	-  ```useradd```,```usermod```,```userdel```
- Přidání, smazání, modifikobání skupiny
	- ```groupadd```, ```groupdel```,```groupmod```
- Změnu hesla uživatele
	- ```passwd```
- - -
