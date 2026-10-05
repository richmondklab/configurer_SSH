# Packet Tracer - Configuration de SSH

Sécurisation de l'accès distant à un commutateur : chiffrement des mots de passe, remplacement de Telnet par SSH.

## Table d'adressage
![Image Alt](https://github.com/richmondklab/configurer_SSH/blob/main/Table%20d'adressage.png?raw=true).




 ![Image Alt](https://github.com/richmondklab/configurer_SSH/blob/main/Topologie%20cisco.png?raw=true)

## Objectifs

1. Sécuriser les mots de passe
2. Chiffrer les communications
3. Vérifier l'implémentation de SSH

## Partie 1 : Sécurisation des mots de passe

Depuis l'invite de commande de PC1 (mots de passe user EXEC et EXEC privilégié : `cisco`) :

```
PC> telnet 10.10.10.2
S1> enable
S1# copy running-config startup-config
S1# show running-config
S1# configure terminal
S1(config)# service password-encryption
S1(config)# end
S1# show running-config
```

Résultat attendu : avant la commande, les mots de passe apparaissent en clair ; après, ils sont chiffrés (type 7).

## Partie 2 : Chiffrement des communications

### Étape 1 : Nom de domaine et clés RSA

```
S1# configure terminal
S1(config)# ip domain-name netacad.pka
S1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024
```

### Étape 2 : Utilisateur SSH et lignes VTY

```
S1(config)# username admin secret cisco
S1(config)# line vty 0 15
S1(config-line)# login local
S1(config-line)# transport input ssh
S1(config-line)# no password
S1(config-line)# end
```

## Partie 3 : Vérification

1. Quitter la session Telnet (`exit`), puis retenter :

   ```
   PC> telnet 10.10.10.2
   ```

   La connexion échoue.

2. Se connecter en SSH (l'option `-l` est la lettre L) :

   ```
   PC> ssh -l admin 10.10.10.2
   ```

   Mot de passe : `cisco`.

3. Enregistrer la configuration :

   ```
   S1> enable
   S1# copy running-config startup-config
   ```

> 

## Commandes de vérification utiles

```
S1# show ip ssh
S1# show ssh
S1# show running-config | section line vty
```
