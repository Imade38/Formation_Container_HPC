# Annexe Formation Containers pour le HPC

## Quelques commandes et options pour podman (exactement les mêmes que docker)

### podman run : Cette commande est utilisée pour exécuter un conteneur à partir d'une image.

Options courantes :

\-d ou --detach : Exécute le conteneur en arrière-plan et affiche son ID.

\-p ou --publish : Lie un port du conteneur à un port de l'hôte.

\-v ou --volume : Montre un volume ou un chemin de l'hôte dans le conteneur.

\--name : Donne un nom au conteneur.

\-e ou --env : Définit des variables d'environnement dans le conteneur.

### podman build : Utilisé pour construire une image à partir d'un fichier de construction.

Options courantes :

\-t ou --tag : Spécifie un nom et une étiquette pour l'image construite.

\--file : Spécifie le chemin vers le fichier de construction.

### podman pull : Télécharge une image à partir d'un registre.

Options :

Aucune option courante.

### podman ps : Affiche les conteneurs en cours d'exécution.

Option courante :

\-a ou --all : Affiche tous les conteneurs, y compris ceux qui sont arrêtés.

### podman images : Affiche les images disponibles localement.

Option courante :
Aucune option courante autre que -a ou --all, qui affiche également les images intermédiaires.

### podman exec : Exécute une commande dans un conteneur en cours d'exécution.

Options courantes :

\-it : Permet une interaction avec le terminal du conteneur.

### podman stop : Arrête un ou plusieurs conteneurs en cours d'exécution.

Option :

Aucune option courante autre que le nom ou l'ID du conteneur.

### podman rm : Supprime un ou plusieurs conteneurs (le ou les conteneurs doivent être arrêtés).

Option :

\-f ou --force : Force la suppression du conteneur en cours d'exécution.

### podman rmi : Supprime une ou plusieurs images locales.

Option :

\-f ou --force : Force la suppression de l'image, même si elle est utilisée par des conteneurs.

## Quelques commandes et options pour apptainer

### apptainer run : Cette commande est utilisée pour exécuter un conteneur à partir d'une image.

Options courantes :

\--bind [source]:[destination] : Monte un répertoire ou un fichier de l’hôte dans le conteneur.

\--cleanenv : Ignore les variables d’environnement de l’hôte et utilise uniquement celles définies dans le conteneur.

\--home [chemin] : Définit le répertoire personnel ($HOME) dans le conteneur. Par défaut, le répertoire personnel de l’hôte est monté.

\--writable-tmpfs : Active un système de fichiers temporaire en mode écriture, permettant de modifier temporairement le conteneur sans changer l’image en elle-même.

\--no-mount [type] : Désactive le montage de certains répertoires par défaut (comme /proc, /dev, etc.). Cela permet un contrôle fin sur ce qui est monté.

\--pwd [chemin] : Spécifie le répertoire de travail par défaut dans le conteneur.

\--nv : Active la prise en charge des GPU NVIDIA dans le conteneur (si disponible).

\--rocm : Active la prise en charge des GPU AMD ROCm dans le conteneur.

### apptainer build <nom_image>.sif <definition_file>.def : Cette commande construit une image de conteneur en utilisant un fichier de définition.

\--sandbox : Crée un environnement en mode "sandbox" (répertoire) au lieu d'une image .sif compressée. Cela permet d'avoir un conteneur en lecture-écriture.

\--no-cleanup : Ne pas supprimer les fichiers temporaires utilisés lors de la construction de l'image, ce qui peut être utile pour le débogage.

### apptainer pull : Télécharge une image à partir d'un registre.

Option :

Aucune option courante.

### apptainer shell <nom_image>.sif : Ouvre un shell interactif dans le conteneur.

Options courantes :

Les mêmes que celles de la commande apptainer run vue plus haut.

### apptainer inspect <nom_image>.sif : Affiche les métadonnées ou les informations sur le conteneur.

### apptainer exec <nom_image>.sif <commande> : Exécute une commande dans le conteneur sans l'exécuter en tant qu'application complète.

### apptainer instance start <image>.sif my_instance : Permet d'exécuter des conteneurs en tant que processus en arrière-plan. Cela peut être utile pour exécuter des services tels que des serveurs web ou des bases de données.

### apptainer stop my_instance : Permet d'arrêter une instance

Option courante :

\--force : Force l'arrêt immédiat de l'instance si elle ne répond pas correctement aux signaux d'arrêt normaux.



## Instructions couramment utilisées dans un fichier Dockerfile

FROM: Cette instruction spécifie l'image de base à utiliser pour construire la nouvelle image. Par exemple, FROM ubuntu:latest indique que l'image est basée sur la dernière version de Ubuntu.

RUN: Cette instruction permet d'exécuter des commandes dans l'environnement de construction. Par exemple, RUN apt-get update && apt-get install -y package pour mettre à jour les paquets et installer de nouveaux logiciels.

COPY / ADD: Ces instructions copient des fichiers depuis l'hôte vers le système de fichiers de l'image. COPY est généralement préféré à ADD car il est plus transparent et ne permet que la copie de fichiers locaux dans l'image.

WORKDIR: Cette instruction définit le répertoire de travail à utiliser pour les instructions suivantes. Par exemple, WORKDIR /app définira /app comme répertoire de travail.

CMD / ENTRYPOINT: Ces instructions définissent la commande à exécuter lorsque le conteneur démarre. CMD est souvent utilisé pour fournir des arguments à l'entrée principale du conteneur, tandis que ENTRYPOINT est souvent utilisé pour exécuter un script ou une commande principale.

EXPOSE: Cette instruction indique les ports sur lesquels le conteneur écoutera les connexions entrantes. Cependant, cela ne publie pas effectivement le port. Cela doit être fait lors du démarrage du conteneur.

ENV: Cette instruction définit les variables d'environnement à utiliser dans l'image. Par exemple, ENV PATH /usr/local/bin:$PATH ajoutera /usr/local/bin au chemin d'exécution.

VOLUME: Cette instruction crée un point de montage pour un volume externe ou d'autres conteneurs. Cela permet de persister les données en dehors du cycle de vie du conteneur.

USER: Cette instruction définit l'utilisateur ou l'UID à utiliser lors de l'exécution des commandes RUN, CMD et ENTRYPOINT. Cela permet de définir un utilisateur moins privilégié pour améliorer la sécurité.
