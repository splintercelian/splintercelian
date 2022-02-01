# Le malware MoonBounce
Un nouveau malware dévoilé au courant du mois de Janvier 2022 avec pour caractéristique une forte persistance
car il agit sur la séquence de boot (UEFI) et se loge dans la SPI flash résistant ainsi à un nettoyage ou un remplacement du disque dur de la machine
## Découverte
<!-- Qui? -->
### Qui?
-> Kaspersky

<!-- Quand? -->
### Quand?
-> Fin d'année 2021

<!-- Quoi? -->
### Quoi?
-> Un malware de type UEFI (comme Lojax ou encore MosaicRegressor)

<!-- Comment? -->
### Comment?
-> A partir de logs d'un scanner de firmware (Kaspersky)

## Mode de fonctionnement
### 1ère étape : l'infection depuis la SPI flash
L'attaquant réussi à introduire un code malicieux dans la SPI flash et intercepte l'appel à une fonction d'allocation.
Ce faisant il crée un shellcode qui sera plus tard ré-utilisé notamment pour charger des drivers malicieux
### 2ème étape : les interceptions
Ensuite une deuxième interception sur le même module mais pour une fonction différente permet d'infecter à son tour le chargeur de système d'exploitation (dans ce cas Windows Loader).
Ce faisant une nouvelle interception de fonction est mise en place afin de modifier le comportement durant le chargement du kernel.
Une dernière interception permet ensuite de modifier le comportement du kernel pendant son exécution
### 3ème étape : appel du shellcode au niveau du kernel et création du driver malicieux
Le shellcode précédemment créé est alors appelé. Il va lui même permettre le chargement en mémoire d'un driver malicieux
et va l'appeler.
### 4ème étape : exécution de l'image du driver et injection du malware dans le service réseau
Le driver implémente une fonction de retour qui est appelé à chaque nouvelle image dudit driver.
Si cette image est chargé à partir du process `svchost.exe` alors le driver injecte le code malicieux dans le process.
### Étape finale : téléchargement du stager
Le process `svchost.exe` est alors malicieux et va télécharger un stager depuis une un serveur C&C avec une URL codée en dur.

<!-- Links -->
[MoonBounce - SecureList](https://securelist.com/moonbounce-the-dark-side-of-uefi-firmware/105468/)
