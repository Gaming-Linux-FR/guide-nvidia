# Guide d'installation des pilotes NVIDIA sur Linux pour cartes récentes

Ce guide est destiné aux utilisateurs de cartes graphiques NVIDIA récentes sous Linux. Il couvre les étapes d'installation des pilotes NVIDIA sur différentes distributions Linux, y compris Fedora, Debian, et openSUSE Tumbleweed. Avant de procéder à l'installation, assurez-vous que votre carte graphique est prise en charge par les derniers pilotes Nvidia.

## Table des matières

1. [Désactiver Secure Boot dans le BIOS](#désactiver-secure-boot-dans-le-bios)
2. [Utiliser X11 plutôt que Wayland](#utiliser-x11-plutôt-que-wayland)
3. [Choisir une distribution qui fait tout ce qui suit pour vous](#choisir-une-distribution-qui-fait-tout-ça-pour-vous)
4. [Activer les services](#activer-les-services)
5. [Ordinateurs portables](#ressources-supplémentaires-pour-ordinateurs-portables-nvidia)
6. [Overclocking avec NVIDIA sous Linux](#overclocking-avec-nvidia-sous-linux)
7. [Installation des pilotes NVIDIA sur Arch Linux](#installation-et-configuration-des-pilotes-nvidia-sur-arch-linux)
8. [Installation des pilotes NVIDIA sur Fedora Silverblue et Kinoite](#installation-des-pilotes-nvidia-sur-fedora-silverblue-et-kinoite)
9. [Installation des pilotes NVIDIA sur Fedora](#installation-des-pilotes-nvidia-sur-fedora)
10. [Installation des pilotes NVIDIA sur Debian](#installation-des-pilotes-nvidia-sur-debian)
11. [Installation des pilotes NVIDIA sur openSUSE Tumbleweed](#installation-des-pilotes-nvidia-sur-opensuse-tumbleweed)

---

## Désactiver Secure Boot dans le BIOS

Secure Boot est une fonctionnalité de sécurité du firmware UEFI qui empêche le chargement de logiciels non autorisés au démarrage de l'ordinateur. Bien qu'elle protège contre les logiciels malveillants, Secure Boot peut aussi empêcher l'installation de pilotes tiers non signés, tels que ceux de NVIDIA, particulièrement quand ils nécessitent la compilation de modules du noyau via DKMS.

### Étapes pour désactiver Secure Boot :

1. **Redémarrez** votre ordinateur et accédez au BIOS (touche F2, F10, F12, Suppr, selon le fabricant).
2. **Trouvez l'option Secure Boot** sous les menus "Sécurité", "Boot", ou "Avancé".
3. **Désactivez Secure Boot**, changez de "Enabled" à "Disabled".
4. **Sauvegardez et quittez**. Cela peut nécessiter d'appuyer sur une touche spécifique pour sauvegarder les paramètres avant de quitter.

Certaines distributions comme Ubuntu, Linux Mint ou Opensuse, ont des outils qui peuvent aider à la mise en place de secureboot avec Nvidia sous Linux. Néanmoins ça reste peu accéssible aux débutants, le plus simple reste de désactiver sécureboot.

---

## Utiliser X11 plutôt que Wayland

Pour une meilleure compatibilité avec les pilotes NVIDIA, il est recommandé d'utiliser X11 en raison de limitations avec Wayland, en particulier avant l'intégration complète du patch "explicit sync". Certaines disctributions ont déjà appliqué ce patch permetant d'avoir une exelente expérience sur Wayland avec Nvidia, notamment **Nobara Linux** et **CachyOS**.

Cependant, étant donné son âge, X11 présente plusieurs limitations inhérentes à sa conception originelle, qui ne prenait pas en compte les évolutions technologiques récentes. Un exemple notable de ces limitations est sa gestion des écrans avec des taux de rafraîchissement différents ou encore le HDR. Lorsque vous utilisez deux moniteurs avec des taux de rafraîchissement différents, par exemple, un écran à 60 Hz et un autre à 144 Hz, X11 peut rencontrer des difficultés à gérer ces différences. Dans certains cas, cela peut conduire à une situation où le système, dans le but de maintenir une synchronisation cohérente entre les écrans, bride le taux de rafraîchissement du moniteur le plus rapide (144 Hz) pour le faire correspondre à celui du moniteur le plus lent (60 Hz). Donc il peut être intéressant si on a une Nvidia d'aller sur une distribution qui applique le patch "explicit sync" comme **Nobara Linux** et **CachyOS** et d'utiliser Wayland.

### Références sur le patch "explicit sync" :

- **Merge Request GitLab** : [Explicit Sync](https://gitlab.freedesktop.org/xorg/xserver/-/merge_requests/967)
- **Documentation sur Explicit Sync** : [PDF ici](https://indico.freedesktop.org/event/2/contributions/78/attachments/90/143/explicit_sync.pdf).

---

## Choisir une distribution qui fait tout ce qui suit pour vous.

Il existe un grand nombres de distributions qui font tout ce qui suit pour vous. Ubuntu et toutes les base Ubuntu comme POP-OS et Zorin mais aussi, Nobara Linux, CachyOS, Endeavor OS, Manjaro...

---

## Ordinateurs portables

- **Optimisation des laptops NVIDIA** : [Voir la vidéo](https://youtu.be/GhsP6btpiiw)

**Configuration de cartes graphiques hybrides Intel/Nvidia ou AMD/Nvidia sous Linux**

**1. Vérification de la carte graphique utilisée pour l'affichage de bureau :**

Pour savoir quelle carte graphique est actuellement utilisée pour l'affichage de votre bureau, utilisez la commande suivante dans un terminal :

```
glxinfo | grep "OpenGL renderer"
```
<p align="center">
  <img width="850" src="https://github.com/Gaming-Linux-FR/guide-nvidia/blob/main/screenshot/glxinfo" alt="glxinfo">
</p>

Il est préférable que le retour indique l'utilisation d'Intel, car il n'est pas nécessaire d'utiliser la carte Nvidia pour des tâches telles que la navigation sur le web, le traitement de texte, et autres activités similaires. Utiliser Nvidia pour ces tâches pourrait inutilement augmenter la température de votre PC et réduire la durée de vie de la batterie.

**2. Surveillance de l'activité de votre carte graphique Nvidia :**

Pour voir ce que fait votre carte graphique Nvidia, utilisez la commande :

```
nvidia-smi
```

<p align="center">
  <img width="850" src="https://github.com/Gaming-Linux-FR/guide-nvidia/blob/main/screenshot/nvidia-smi" alt="nvidia-smi">
</p>

Si une application utilise Nvidia, elle apparaîtra dans la liste affichée par cette commande. 

**3. Forcer une application à utiliser Nvidia :** 

**INUTILE dans 99% des cas**. Si un jeu ne se lance pas avec Nvidia c'est que vous avez mal installé le driver ou que vous être tomber sur un des ultras rares jeux qui nécessite de forcer Nvidia.

Bien que cela soit normalement inutile, si vous souhaitez explicitement qu'une application utilise Nvidia, voici comment procéder selon votre distribution Linux :

- **Sur Arch Linux :**

  Installez le paquet en exécutant :
  ```
  sudo pacman -S nvidia-prime
  ```
  Pour lancer une application avec Nvidia, utilisez :
  ```
  prime-run nomdelapp
  ```
  Pour les jeux sur Steam, ajoutez dans les options de lancement :
  ```
  prime-run %command%
  ```

- **Sur d'autres distributions Linux :**

  Pour forcer une application via le terminal, utilisez :
  ```
  __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia nomdelapp
  ```
  Pour les jeux Steam, dans les options de lancement :
  ```
  __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia %command%
  ```

Une fois de plus cela est généralement inutile si votre pilote Nvidia est correctement installé. Depuis l'arrivée de Prime dans les pilotes NVIDIA version [435.17](https://www.phoronix.com/news/NVIDIA-435.17-Linux-Driver). en 2019, il n'est normalement pas nécessaire de faire d'autres manipulations après l'installation du pilote. Oubliez les bidouillages avec Optimus, Bumblebee, et autres complications.


--- 

J'espère que cette version améliorée répond à vos attentes !

---

## Activer les services

Quelle que soit la distributon, il faut activer ces services après l'instalation du driver :

```bash
sudo systemctl enable nvidia-suspend.service nvidia-hibernate.service nvidia-resume.service
```

Si pc portable avec technologie "Dynamic Boost" (à vous de bien vérifier) :

```bash
sudo systemctl enable nvidia-powerd.service
```

---

## Overclocking avec NVIDIA sous Linux

L'overclocking peut améliorer les performances de votre GPU NVIDIA, mais doit être pratiqué avec prudence.

- **Overclocking Nvidia sous Linux** : [Voir la vidéo](https://youtu.be/bitC0102Uig)

---

## Installation des pilotes NVIDIA sur Arch Linux

### Prérequis

Avant de commencer, mettez à jour votre système :

```bash
sudo pacman -Syu
```

### Étape 1 : Installation des headers du noyau

Les headers du noyau Linux sont nécessaires pour la compilation de modules externes, comme ceux des pilotes NVIDIA. Voici comment installer les headers pour différentes versions du noyau :

- Pour le noyau standard :

  ```bash
  sudo pacman -S linux-headers
  ```

- Pour le noyau LTS (Long Term Support) :

  ```bash
  sudo pacman -S linux-lts-headers
  ```

- Pour le noyau Zen, optimisé pour les performances :

  ```bash
  sudo pacman -S linux-zen-headers
  ```

Assurez-vous de correspondre les headers à votre version du noyau actuellement utilisée.

### Étape 2 : Configuration des pilotes NVIDIA

#### 2.1 Configuration des options noyau

```bash
echo -e 'options nvidia NVreg_UsePageAttributeTable=1 NVreg_InitializeSystemMemoryAllocations=0 NVreg_DynamicPowerManagement=0x02' | sudo tee -a /etc/modprobe.d/nvidia.conf
echo -e 'options nvidia_drm modeset=1' | sudo tee -a /etc/modprobe.d/nvidia.conf
```

#### 2.2 Choix et installation des pilotes NVIDIA

```bash
sudo pacman -S nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader
```

### Étape 3 : Support pour laptops Intel/NVIDIA

Pour les utilisateurs de laptops hybrides Intel/NVIDIA :

```bash
sudo pacman -S intel-media-driver intel-gmmlib onevpl-intel-gpu
```

---

## Installation des pilotes NVIDIA sur Fedora Silverblue et Kinoite

### Étape 1 :

```bash
sudo rpm-ostree install akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-cuda xorg-x11-drv-nvidia-libs
sudo rpm-ostree kargs --append=rd.driver.blacklist=nouveau --append=modprobe.blacklist=nouveau --append=nvidia-drm.modeset=1
```

### Étape 2 : Suppression de l'option `nomodeset` (si nécessaire)

```bash
sudo rpm-ostree kargs --delete=nomodeset
```
Si le système retourne le message suivant :

```error: No key 'nomodeset' found```
Cela signifie que l'option nomodeset n'était pas activée, ce qui est l'état souhaité pour garantir une compatibilité optimale avec les pilotes Nvidia.

---

# Installation des pilotes Nvidia sur Fedora

## Étape 1 : Activation de RPM Fusion et installation des pilotes Nvidia

### Activation des dépôts RPM Fusion Free et Non-Free

RPM Fusion contient des logiciels que Fedora ne fournit pas. Il est divisé en deux dépôts : Free et Non-Free. Le script installe ces dépôts.

```bash
dnf install -y https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
dnf install -y https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### Installation des pilotes Nvidia

Pilotes Nvidia ainsi que les modules du noyau nécessaires.

```bash
dnf install -y akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-cuda xorg-x11-drv-nvidia-libs
akmods
```

### Optionnel : Installation du support 32 bits et de Vulkan

Ces packages sont utiles pour certains jeux et applications.

```bash
dnf install -y vulkan
dnf install -y xorg-x11-drv-nvidia-libs.i686
```
---

# Installation des pilotes Nvidia sur Debian

## Étape 1 : Préparation du système

### Ajout de l'architecture 32 bits

Certains pilotes et jeux peuvent nécessiter une compatibilité 32 bits.

```bash
sudo dpkg --add-architecture i386
```

## Étape 2 : Mise à jour des paquets et installation des prérequis

### Mise à jour de la liste des paquets

Ajoutez le support des librairies 32bit et mettez à jour vos sources de paquets pour vous assurer que vous avez accès aux dernières versions.

```bash
sudo dpkg --add-architecture i386
sudo apt update -y
```

### Installation de software-properties-common

Si `add-apt-repository` n'est pas disponible, installez `software-properties-common`:

```bash
sudo apt install -y software-properties-common
```

## Étape 3 : Ajout des dépôts non-free et contrib

Les pilotes Nvidia sont disponibles dans le dépot non-free de Debian.

### Ajout des dépôts

Exécutez les commandes suivantes pour ajouter les dépôts nécessaires :

```bash
sudo add-apt-repository -y non-free
sudo add-apt-repository -y non-free-firmware
sudo add-apt-repository -y contrib
```

### Mise à jour des paquets

Après avoir ajouté les nouveaux dépôts, mettez à jour à nouveau la liste des paquets :

```bash
sudo apt update -y
```

## Étape 4 : Installation des pilotes Nvidia

### Installation des pilotes et des composants nécessaires

Installez les pilotes Nvidia, les en-têtes Linux pour votre version du noyau, dkms, et d'autres paquets nécessaires :

```bash
sudo apt install -y linux-headers-amd64 build-essential dkms firmware-misc-nonfree nvidia-driver nvidia-driver-libs:i386 vulkan-tools libnvoptix1
```

## Étape 5 : Configuration de DRM Modeset

Pour améliorer l'expérience avec les pilotes Nvidia, vous pouvez activer le modeset DRM et une meilleure gestion de l'alimentation.

```bash
echo -e 'options nvidia NVreg_UsePageAttributeTable=1 NVreg_InitializeSystemMemoryAllocations=0 NVreg_DynamicPowerManagement=0x02' | sudo tee -a /etc/modprobe.d/nvidia.conf
echo "options nvidia-drm modeset=1" | sudo tee /etc/modprobe.d/nvidia.conf
```

---

## Installation des pilotes Nvidia sur openSUSE Tumbleweed

### Étape 1 : Rafraîchissement des dépôts

La première étape consiste à rafraîchir la liste des paquets disponibles pour s'assurer que toutes les mises à jour sont prises en compte.

Ouvrez un terminal et exécutez :

```bash
sudo zypper ref
```

### Étape 2 : Ajout du dépôt Nvidia

Ajoutez ensuite le dépôt Nvidia spécifique à Tumbleweed. Ce dépôt contient les pilotes Nvidia les plus récents compatibles avec Tumbleweed.

```bash
sudo zypper addrepo --refresh https://download.nvidia.com/opensuse/tumbleweed NVIDIA
```

### Étape 3 : Installation des pilotes Nvidia

Maintenant, installez les pilotes Nvidia en acceptant automatiquement les licences nécessaires.

```bash
sudo zypper install --auto-agree-with-licenses -y nvidia-gfxG05-kmp-default nvidia-glG05 nvidia-glG05-32bit nvidia-utilsG05 nvidia-uvm-gfxG05 nvidia-videoG05 nvidia-videoG05-32bit
```
