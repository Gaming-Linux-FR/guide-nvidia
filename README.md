# Guide d'installation des pilotes NVIDIA sur Linux pour cartes récentes

Ce guide est destiné aux utilisateurs de cartes graphiques NVIDIA récentes sous Linux. Il couvre les étapes d'installation des pilotes NVIDIA sur différentes distributions Linux, y compris Fedora, Debian, et openSUSE Tumbleweed. Avant de procéder à l'installation, assurez-vous que votre carte graphique est prise en charge par les derniers pilotes Nvidia.

## Table des matières

1. [Désactiver Secure Boot dans le BIOS](#désactiver-secure-boot-dans-le-bios)
2. [Utiliser X11 plutôt que Wayland](#utiliser-x11-plutôt-que-wayland)
3. [Activer les services](#activer-les-services)
4. [Ressources supplémentaires pour ordinateurs portables NVIDIA](#ressources-supplémentaires-pour-ordinateurs-portables-nvidia)
5. [Overclocking avec NVIDIA sous Linux](#overclocking-avec-nvidia-sous-linux)
6. [Installation et configuration des pilotes NVIDIA sur Arch Linux](#installation-et-configuration-des-pilotes-nvidia-sur-arch-linux)
7. [Installation des pilotes NVIDIA sur Fedora Silverblue et Kinoite](#installation-des-pilotes-nvidia-sur-fedora-silverblue-et-kinoite)
8. [Installation des pilotes NVIDIA sur Fedora](#installation-des-pilotes-nvidia-sur-fedora)
9. [Installation des pilotes NVIDIA sur Debian](#installation-des-pilotes-nvidia-sur-debian)
10. [Installation des pilotes NVIDIA sur openSUSE Tumbleweed](#installation-des-pilotes-nvidia-sur-opensuse-tumbleweed)

---

## Désactiver Secure Boot dans le BIOS

Secure Boot est une fonctionnalité de sécurité du firmware UEFI qui empêche le chargement de logiciels non autorisés au démarrage de l'ordinateur. Bien qu'elle protège contre les logiciels malveillants, Secure Boot peut aussi empêcher l'installation de pilotes tiers non signés, tels que ceux de NVIDIA, particulièrement quand ils nécessitent la compilation de modules du noyau via DKMS.

### Étapes pour désactiver Secure Boot :

1. **Redémarrez** votre ordinateur et accédez au BIOS (touche F2, F10, F12, Suppr, selon le fabricant).
2. **Trouvez l'option Secure Boot** sous les menus "Sécurité", "Boot", ou "Avancé".
3. **Désactivez Secure Boot**, changez de "Enabled" à "Disabled".
4. **Sauvegardez et quittez**. Cela peut nécessiter d'appuyer sur une touche spécifique pour sauvegarder les paramètres avant de quitter.

---

## Utiliser X11 plutôt que Wayland

Pour une meilleure compatibilité avec les pilotes NVIDIA, il est recommandé d'utiliser X11 en raison de limitations avec Wayland, en particulier avant l'intégration complète du patch "explicit sync".

### Références sur le patch "explicit sync" :

- **Merge Request GitLab** : [Explicit Sync](https://gitlab.freedesktop.org/xorg/xserver/-/merge_requests/967)
- **Documentation sur Explicit Sync** : [PDF ici](https://indico.freedesktop.org/event/2/contributions/78/attachments/90/143/explicit_sync.pdf).

---

## Ressources supplémentaires pour ordinateurs portables NVIDIA

- **Optimisation des laptops NVIDIA** : [Voir la vidéo](https://youtu.be/GhsP6btpiiw)

---

## Activer les services

Quel que soit la distributon, il faut activer ces services après l'instalation du driver :

```bash
sudo systemctl enable nvidia-suspend.service nvidia-hibernate.service nvidia-resume.service
```

Si pc portable avec technologie "Dynamic Boost" (à vous de bien vérifier)

```bash
sudo systemctl enable nvidia-powerd.service
```

---

## Overclocking avec NVIDIA sous Linux

L'overclocking peut améliorer les performances de votre GPU NVIDIA, mais doit être pratiqué avec prudence.

- **Overclocking Nvidia sous Linux** : [Voir la vidéo](https://youtu.be/bitC0102Uig)

---

## Installation et configuration des pilotes NVIDIA sur Arch Linux

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
sudo rpm-ostree install akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-cuda
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
dnf install -y akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-cuda
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

Mettez à jour vos sources de paquets pour vous assurer que vous avez accès aux dernières versions.

```bash
sudo apt update -y
```

### Installation de software-properties-common

Si `add-apt-repository` n'est pas disponible, installez `software-properties-common`:

```bash
sudo apt install -y software-properties-common
```

## Étape 3 : Ajout des dépôts non-free et contrib

Les pilotes Nvidia sont disponibles dans les sections non-free de Debian.

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

### Étape 3 : Confiance dans le dépôt

Pour s'assurer que le dépôt est sûr et que ses clés sont importées, exécutez :

```bash
sudo zypper --gpg-auto-import-keys ref
```

### Étape 4 : Installation des pilotes Nvidia

Maintenant, installez les pilotes Nvidia en acceptant automatiquement les licences nécessaires.

```bash
sudo zypper install --auto-agree-with-licenses -y nvidia-gfxG05-kmp-default nvidia-glG05 nvidia-glG05-32bit nvidia-utilsG05 nvidia-uvm-gfxG05 nvidia-videoG05 nvidia-videoG05-32bit
```
