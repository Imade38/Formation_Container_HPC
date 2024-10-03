# Containers en HPC pour les débutants

## Hello World

Commençons par télécharger notre première image

    podman pull hello-world

Vérifions qu'elle a bien été enregistrée sur la machine

    podman images

Lançons notre premier container

    podman run hello-world

Vous avez lancé le container le plus basique, en général on le déploie pour tester que l'installation de notre technologie de container s'est bien passée.

## Exécution du premier (vrai) container

Il est temps de passer au vif du sujet. Dans cette section, vous allez exécuter un conteneur Alpine Linux (une distribution Linux légère) sur votre système et découvrir plusieurs commandes Podman (ou Docker c'est la même chose).

Pour commencer, exécutez la commande suivante dans votre terminal :
    
    podman pull alpine

> **Remarque :** La commande `pull` récupère l'**image** Alpine depuis le **registre Docker** et la sauvegarde dans notre système.

Listons les images présentes
    
    podman images

Vous devriez obtenir quelque chose comme ça sur votre terminal :

```
$ podman images
quay.io/podman/hello      latest      39ae24b9cabf  3 days ago   1.7 MB
docker.io/library/alpine  latest      05455a08881e  2 weeks ago  7.67 MB
```
Essayez ça :

    podman run alpine echo "hello from alpine"

Voici le retour du terminal que vous devriez voir:

```
$ podman run alpine echo "hello from alpine"
hello from alpine
```
Dans ce cas, nous avons exécuté la commande `echo` dans notre conteneur alpine puis nous l'avons quitté. Si vous avez remarqué, tout cela s'est passé très rapidement. Imaginez démarrer une machine virtuelle, exécuter une commande, puis la tuer. Maintenant, vous savez pourquoi on dit que les conteneurs sont rapides !

Essayons une autre commande.

    podman run alpine /bin/sh

Rien ne s'est passé. Est-ce un bug ? Eh bien, non. Ces shells interactifs se termineront après l'exécution de toute commande scriptée, à moins qu'ils ne soient exécutés dans un terminal interactif. Donc pour que cet exemple ne se termine pas, vous devez utiliser `podman run -it alpine /bin/sh` (-it étant l'option pour accéder au terminal du container) 

Vous êtes maintenant à l'intérieur du shell du conteneur et vous pouvez essayer quelques commandes comme `ls -l`, `uname -a`, `cat /etc/os-release` et d'autres. Quittez le conteneur en entrant la commande `exit`.


La commande `podman ps` vous montre tous les conteneurs qui sont actuellement en cours d'exécution.

```
$podman ps
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
```
Au point où nous sommes il n'y a aucun container déployé, ceux déployés précedemment ont été immédiatement arrêtés à la fin de l'exécution des commandes. Cependant avec la commande `podman ps -a` on peut voir tous les containers lancés avant, même s'ils sont actuellement à l'arrêt.

Vous pouvez avoir un listing complet des commandes disponibles avec podman en utilisant la commande `podman help`. Si vous voulez être plus spécifique `podman help <commande>` permettra d'afficher le rôle de la commande demandée, ses options et des exemples d'utilisation.

Jusqu'à maintenant nous avons utilisé des images simples déjà prêtes pour construire les containers mais il nous est possible de construire nos propres containers...


## Construction de notre premier container

Alors on commence par créer un fichier vide que l'on va nommer **Dockerfile**.

    touch Dockerfile

Ensuite nous ouvrons un éditeur de texte (vi ou nano) pour modifier le Dockerfile. 

    vi Dockerfile

Créons un Dockerfile qui va proposer ce que nous avons fait précédemment. Rajoutons ce qui suit dans le fichier Dockerfile :

```
# Utilisation de l'image Alpine Linux comme image de base
FROM alpine:latest

# Commande à exécuter lorsque le conteneur démarre
CMD echo "Hello, World!"
```

L'instruction `FROM alpine:latest` va nous permettre de construire notre conteneur sur l'image de base de l'OS alpine et l'instruction `CMD echo "Hello, World!"` sert à afficher le "Hello, World!" juste après le démarrage du conteneur.
Pour plus d'informations sur les instructions relatives au Dockerfile vous pouvez lire l'annexe.

Maintenant que le Dockerfile est prêt nous pouvons construire notre container.

    podman build -t myhelloworld .

L'option **-t** permet de nommer le conteneur à construire et le **.** est un paramètre pour indiquer le chemin auquel se trouve le Dockerfile, dans notre cas c'est un "." car le Dockerfile se trouve à l'endroit où nous sommes. Par défaut le nom de fichier **Dockerfile** sera cherché mais il est possible de donner un nom de fichier différent lors du build avec l'option -f.
On peut vérifier l'existence de notre propre conteneur :

```
$podman images
REPOSITORY                TAG         IMAGE ID      CREATED        SIZE
localhost/myhelloworld    latest      ab13eb48a065  2 minutes ago  7.67 MB
...
```
Voilà, maintenant déployons-le :

    podman run myhelloworld

Voilà, nous avons construit et exécuté notre propre conteneur. Ceci est un conteneur d'une grande simplicité mais il est possible de mettre en place des Dockerfile bien plus complexes et spécifiques qui siéent.   

Maintenant que nous avons déployé des conteneurs plutôt simplistes, il serait intéressant de voir comment ces conteneurs peuvent être mis au service du HPC...


## Exercice : Traduisez les phrases en commande podman

1) "Je veux exécuter un conteneur en arrière-plan à partir de l'image nginx, avec le port 8080 de l'hôte redirigé vers le port 80 du conteneur. Le conteneur doit être nommé my-nginx."

2) "Je veux construire une image à partir d'un fichier Dockerfile situé dans le répertoire actuel. L'image doit être nommée my-app et étiquetée avec la version v1.0."

3) "Je veux exécuter un conteneur à partir de l'image httpd, en détaché, avec les options suivantes :  
- Le conteneur doit être nommé my-httpd.  
- Le port 8080 de l'hôte doit être redirigé vers le port 80 du conteneur.  
- Un volume doit être monté depuis le répertoire local /mydata vers /usr/local/apache2/htdocs dans le conteneur.  
- La variable d'environnement APACHE_RUN_USER doit être définie sur www-data."  


Les réponses sont disponibles dans le fichier reponse_exo.txt


## Container avec CPU vs GPU

Nous allons maintenant utiliser les ressources de la machine Juliet dans le but de tester les performances d'un tel supercalculateur mais via Apptainer cette fois-ci :

    srun --partition=mesonet --account=m24084-students --reservation=formation --time=03:00:00 --pty --gres=gpu:1 /bin/bash
    cp -r /projects/m24084-students/test_formation/ .
    cd test_formation
    apptainer shell --nv tensorflow_24.01-tf2-py3.sif

La dernière commande comporte une option qui mérite notre intérêt :   
    **--nv** : permet l'utilisation des gpus par les containers    

Une fois à l'intérieur du container, entrez ces commandes :

    pip install jupyter lab
    export JUPYTER_ALLOW_INSECURE_WRITES=true
    JUPYTER_PORT=`python -c 'import socket; s=socket.socket(); s.bind(("", 0)); print(s.getsockname()[1]); s.close()'`
    jupyter lab --allow-root --ip=0.0.0.0 --no-browser

Une fois jupyterlab lancé vous aurez une ligne de ce genre :

    [I 10:05:19.389 LabApp] http://hostname:39467/?token=6164236fec6c5364e6ab474b4d3357b7ffe857789cb8c93

Récupérez le numero de port (après hostname:) et le token (après token=). Dans l'exemple ci-dessus le port est 39467 et le token est 6164236fec6c5364e6ab474b4d3357b7ffe857789cb8c93

Maintenant entrez la commande ci-dessous sur votre machine personnelle pour créer un tunnel SSH qui va vous permettre de récupérer les données sur le supercalculateur et ainsi afficher l'interface web de jupyter.

    ssh -L YYYY:julietX:XXXX m24084-stud**juliet.mesonet.fr #Remplacez XXXX par le port chosi pour lancer le container et YYYY par le port que vous voulez libre sur votre propre machine, julietX correspond au noeud de calcul sur lequel vous avez lancé votre conteneur. ** correspond au numéro de votre nom b'utilisateur.

Ensuite allez sur un navigateur web et entrez l'URL localhost:YYYY. Récupérer le token généré par jupyter (affiché sur le terminal) et entrez-le dans l'interface web de Jupyter.   
Bravo, vous êtes enfin sur JupyterLab. Ouvrez le fichier notebook "basic_gpu_cpu_benchmark.ipynb". Suivez les instructions du notebook.


## Containers en multi-noeud avec MPI

Cette fois on utilise apptainer (ex singularity) pour lancer une sorte de Hello World en multi-noeud grâce à MPI.

    srun --account=m24084-students -N 2 --ntasks-per-node=4 --mpi=pmi2 --time=01:00:00 apptainer exec --nv $HOME/test_formation/multinode3.sif /opt/mpitest

Ici on lance un simple Hello World qui sera diffusé par chaque processus du job, on voit dans les options que le job est lancé sur 2 noeuds (-N 2) avec 4 processus en parallèle sur ces 2 noeuds (--ntasks-per-node=4) pour un total de 8 processus. C'est une manière de montrer que le multi-noeud est fonctionnel via MPI. Dans ce cas-là on utilise pmi2 une interface MPI installée directement avec Slurm pour gérer les processus MPI

L'utilisation de jobs en multi-noeud offre des avantages de scalabilité, performance et d'optimisation de l'utilisation des ressources entre autres.

## Quelques ressources

**Bonnes pratiques :**

https://cloud.google.com/architecture/best-practices-for-building-containers?hl=fr

**Création d'un Dockerfile :**

https://openclassrooms.com/fr/courses/2035766-optimisez-votre-deploiement-en-creant-des-conteneurs-avec-docker/6211517-creez-votre-premier-dockerfile

**Construire un container Apptainer (ou Singularity) :**

https://docs.sylabs.io/guides/3.0/user-guide/build_a_container.html

**Commandes Podman :**

https://docs.podman.io/en/latest/Commands.html

**Commandes Apptainer :**

https://apptainer.org/docs/user/main/cli.html









