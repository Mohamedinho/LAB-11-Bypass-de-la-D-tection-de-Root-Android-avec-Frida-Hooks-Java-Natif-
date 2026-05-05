# LAB-11-Bypass-de-la-D-tection-de-Root-Android-avec-Frida-Hooks-Java-Natif-

## Installation et preuve:
Objectif : prouver que Frida, Python-Frida et ADB sont bien installés.

<img width="586" height="107" alt="1" src="https://github.com/user-attachments/assets/eaf00b57-08ec-4302-a631-d8bc73328ef0" />

<img width="958" height="112" alt="2" src="https://github.com/user-attachments/assets/d96c533f-69dd-4e8f-9a37-5576cf2ab447" />

<img width="492" height="161" alt="3" src="https://github.com/user-attachments/assets/4a31ef80-d26b-40c2-a91b-f695c311057f" />

Cette étape permet de vérifier que l’environnement de travail est prêt.  
La commande `frida --version` confirme l’installation du client Frida sur Windows.  
La commande Python vérifie que le module `frida` est bien installé dans Python.  
Enfin, `adb devices` confirme que l’émulateur Android est correctement détecté.

## Déploiement et visibilité:
Vérifier l’architecture de l’émulateur

<img width="777" height="117" alt="4" src="https://github.com/user-attachments/assets/086dc89a-3873-4966-851d-67e8af86d783" />

Lancer frida-server:

<img width="1026" height="83" alt="5" src="https://github.com/user-attachments/assets/6927db1f-a196-4348-9f28-d6225f025808" />

Vérifier que frida-server tourne:

<img width="1012" height="113" alt="6" src="https://github.com/user-attachments/assets/4cfa611c-c4b2-4541-9dfb-fe86f7f1a567" />

Lister les applications visibles par Frida

<img width="1008" height="615" alt="7" src="https://github.com/user-attachments/assets/f8586460-0232-4274-9122-3f37e0f7e6b1" />

Cette étape montre que `frida-server` est correctement déployé sur l’émulateur Android.  
Après le lancement du serveur, la commande `frida-ps -Uai` permet d’afficher les applications installées et visibles par Frida.  
La présence d’au moins trois applications dans la liste confirme que la communication entre le PC et l’émulateur fonctionne.

## Bypass Java:

<img width="1160" height="826" alt="8" src="https://github.com/user-attachments/assets/83b2b2d9-ef5e-4c0b-8ffd-8d7c966fc312" />

## Avant / Après:

avant:

<img width="378" height="484" alt="1ZpAxlzP9i7pZLUreCejqXA" src="https://github.com/user-attachments/assets/3cb68899-0e9b-496c-b34e-a3b8fe31a002" />

<img width="1212" height="500" alt="9" src="https://github.com/user-attachments/assets/a93941db-5e41-429c-8120-5fd4e5b3623a" />

apres:

<img width="353" height="760" alt="image" src="https://github.com/user-attachments/assets/1dfe1588-1f20-4904-ad1c-793609bb8b11" />

Le script Frida `bypass_level1.js` intercepte les méthodes Java responsables de la détection root dans l’application UnCrackable Level 1.

Les méthodes de la classe `sg.vantagepoint.a.c` sont forcées à retourner `false`, ce qui empêche l’application de détecter l’environnement rooté.  
Le hook de `System.exit()` empêche également l’application de se fermer automatiquement.

Les logs `[+]` affichés dans le terminal confirment que les hooks ont été installés avec succès.

## Natif / Trace:

<img width="1423" height="813" alt="10" src="https://github.com/user-attachments/assets/3923a5eb-08b2-4f18-89e8-d316626895d3" />

Lancer le bypass natif

<img width="1360" height="687" alt="11" src="https://github.com/user-attachments/assets/da19c696-6e60-4c39-a4e3-d677fd50687d" />

La commande `frida-trace` a permis d’observer les appels natifs utilisés par l’application pour rechercher des fichiers liés au root.  
Parmi les fonctions surveillées, on retrouve `open`, `openat`, `access`, `stat` et `lstat`.

Le script `bypass_native.js` intercepte ces fonctions et vérifie si le chemin demandé correspond à un fichier suspect, comme `/system/bin/su` ou `/system/xbin/su`.  
Si le chemin est suspect, le script modifie la valeur de retour avec `retval.replace(ptr(-1))`, ce qui simule un échec d’accès au fichier.

Les logs `[+] Blocked ...` confirment que les appels natifs liés au root check ont été interceptés.
