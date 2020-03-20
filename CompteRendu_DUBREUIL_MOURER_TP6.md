# TP 6 - Admin Système
## Piere Dubreuil & Lucie Mourer
### _Date : 20/03/20_

----
# Exercice 1. Disque et partition

## 2. Vérifiez que ce nouveau disque dur est bien détecté par le système

On utilise la commande :
```bash
ll /dev/sd*
```

on observe que le disque dur a bien été crée avec l'apparition du ficher `sdb`

avec la commande 
```bash
lsblk
```

et on remarque que `sdb` a bien une taille de **5Go**

## 3. Partitionnez ce disque en utilisant fdisk : créez une première partition de 2 Go de type Linux (n°83),et une seconde partition de 3 Go en NTFS (n°7)

Il s'agit de faire la commande 
```
fdisk /dev/sdb
```
Ensuite on enchaine les commandes  suivante

`n`  
`p` pour primary  
`1` en premiere section   
`2048`en première section 
`(2 000 000 000 / 512) + 2048)`
avec 512 la taille d'une section en fin de première section.

Voilà la première section est créée. 

Pour la deuxime on répète la même opération  avec la taille qui reste sur le disque dur. 
Après la création on tape la commande `t` puis le code haxedecimal "7" pour avec la partition en NTFS.

BIEN PENSER A ENREGISTER AVEC `w`

## 4. A ce stade, les partitions ont été créées, mais elles n’ont pas été formatées avec leur système de fichiers.
A l’aide de la commande mkfs, formatez vos deux partitions

Formater la partition 1 au format ext2
```bash
sudo mkfs.ext4 /dev/sdb1
```

Formater la partition 2 au format NTFS
```bash
sudo mkfs.ntfs -f /dev/sdb2
```

## 5. Pourquoi la commandedf -T, qui aﬀiche le type de système de fichier des partitions, ne fonctionne-t-elle pas sur notre disque

La commande `df -T` ne marche pas pour notre disque dur car celui-ci n'a pas encore été monté.

## 6. Faites en sorte que les deux partitions créées soient montées automatiquement au démarrage de la machine, respectivement dans les points de montage /data et /win (vous pourrez vous passer des UUID en raison de l’impossibilité d’effectuer des copier-coller)

Creer les dossier `data` et `win`

```bash
sudo mkdir /data
sudo mkdir /win
```

puis on modifie le fichier fstab

```bash
sudo nano /etc/fstab

/dev/sdb1   /data   ext4    defaults 0   0
/dev/sdb2   /win    ntfs    defaults 0   0
```

## 7. Utilisez la commande `mount` puis redémarrez votre VM pour valider la configuration

## 8. Montez votre clé USB dans la VM

```bash
ll /dev/sd*

mkdir /media/usb
mount /dev/sdc1 /media/usb
```

## 9. Créez un dossier partagé entre votre VM et votre système hôte (rem. il peut être nécessaire d’installer les Additions invité de VirtualBox

Création du fichier partagé via VirtualBox:
   - option -> dossier partagé  
   - nom du dossier : Shared_folder  
   - [ ]Lecture seule  
   - [x]Montage automatique  
   - [x]Montage permanent  

Puis lancement de la VM, une fois lancée, on clique sur périphérique puis `Insérer l'image CD des Additions invité...`

```bash
sudo mount /dev/cdrom /media/cdrom
sudo /media/cdrom/./VBoxLinuxAdditions.run
sudo shutdown -r now

mkdir ~/shared_folder
sudo nano /etc/fstab
> Shared_folder    /home/server/shared_folder    vboxsf  comment=systemd.automount     0       0
mount -a
sudo shutdown -r now
```

----
# Exercice 2. Personnalisation de GRUB

## 1. Commencez par changer l’extension du fichier /etc/default/grub.d/50-curtin-settings.cfg s’il est présent dans votre environnement (vous pouvez aussi commenter son contenu)

Fichier non présent sur la VM

## 2. Modifiez le fichier /etc/default/grub pour que le menu de GRUB s’aﬀiche pendant 10 secondes ; passé ce délai, le premier OS du menu doit être lancé automatiquement.

On remplace `GRUB_TIMEOUT=0`  par `GRUB_TIMEOUT=10`


## 5. On va augmenter la résolution de GRUB et de notre VM. Cherchez sur Internet le ou les paramètres à rajouter au fichier grub.

On décommente cette ligne avec 
```
sudo nano /etc/default/grub 
```

```
GRUB_GFXMODE="1600x1200x32"

export GRUB_COLOR_NORMAL="light-gray/black"
export GRUB_COLOR_HIGHLIGHT="green/black"
```

en ajoutant les deux dernières lignes.


## 6. On va à présent ajouter un fond d’écran. Il existe un paquet en proposant quelques uns: grub2-splash-images (après installation, celles-ci sont disponibles dans /usr/share/images/grub).

```
sudo apt install grub2-splashimages
```

On retrouve bien avec `ls /usr/share/images/grub` une liste d'images en extention **.tga** .

## 7. Il est également possible de configurer des thèmes. On en trouve quelques uns dans les dépôts(grub2-themes-*). Installez-en un.


```
sudo nano /etc/default/grub 

export GRUB_THEME="/boot/grub/themes/ubuntu-mate/theme.txt"

```

## 8. Ajoutez une entrée permettant d’arrêter la machine, et une autre permettant de la redémarrer.


## 9. Configurer GRUB pour que le clavier soit en français
```
sudo nano /etc/default/grub 

export GRUB_TERMINAL_INPUT=at_keyboard 
export LANG=fr_FR

sudo update-grub
```

---
# Exercice 3. Noyau

## 1. Commencez par installer le paquet build-essential, qui contient tous les outils nécessaires (compilateurs, bibliothèques) à la compilation de programmes en C (entre autres).