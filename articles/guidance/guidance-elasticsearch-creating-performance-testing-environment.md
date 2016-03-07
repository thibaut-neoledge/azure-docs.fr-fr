<properties
   pageTitle="Création d’un environnement pour le test de performances Elasticsearch sur Azure | Microsoft Azure"
   description="Configuration d’un environnement permettant de tester les performances d’un cluster Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Création d’un environnement pour le test de performances Elasticsearch sur Azure

Cet article fait [partie d’une série](guidance-elasticsearch.md).

Ce document explique comment configurer un environnement permettant de tester les performances d’un cluster Elasticsearch. Cette configuration a été utilisée pour tester les performances d’intégration de données et charges de travail de requête, comme décrit dans [Adaptation des performances d’ingestion de données pour Elasticsearch dans Azure][].

Le processus de test de performance a utilisé [Apache JMeter](http://jmeter.apache.org/), avec l’[ensemble standard](http://jmeter-plugins.org/wiki/StandardSet/) de plug-ins installés dans une configuration maître/subordonné à l’aide d’un ensemble de machines virtuelles dédiées (qui ne font pas partie du cluster Elasticsearch) spécialement configuré pour le rôle.

L’[Agent PerfMon Server](http://jmeter-plugins.org/wiki/PerfMonAgent/) a été installé sur chaque nœud Elasticsearch. Les sections suivantes fournissent des instructions permettant de recréer l’environnement de test et vous permettent d’effectuer vos propres tests de performance avec JMeter. Ces instructions supposent que vous avez déjà créé un cluster Elasticsearch avec des nœuds connectés à l’aide d’un réseau virtuel Azure.

Notez que l’environnement de test s’exécute comme un ensemble de machines virtuelles Azure géré par un groupe de ressources Azure unique.

[Marvel](https://www.elastic.co/products/marvel) a également été installé et configuré pour que les aspects internes du cluster Elasticsearch puissent être surveillés et analysés plus facilement ; si les statistiques de JMeter ont montré un pic ou un creux dans les performances, les informations disponibles via Marvel peuvent être très utiles au moment de déterminer la cause des fluctuations.

L’illustration qui suit montre la structure de l’ensemble du système.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

Notez les points suivants :

- La machine virtuelle JMeter Master exécute Windows Server pour fournir l’environnement d’interface graphique pour la console JMeter. La machine virtuelle JMeter Master fournit l’interface utilisateur graphique (l’application *jmeter*) permettant d’activer un testeur afin de créer des tests, de les exécuter et de visualiser les résultats. Cette machine virtuelle se coordonne avec les machines virtuelles JMeter Server qui envoient les demandes constituant les tests.

- Les machines virtuelles subordonnées JMeter exécutent Ubuntu Server (Linux) ; il n’y a pas d’exigence particulière en matière d’interface pour ces machines virtuelles. Les machines virtuelles JMeter exécutent le logiciel JMeter Server (l’application *jmeter-server*) pour envoyer des demandes au cluster Elasticsearch.

- Nous n’avons pas utilisé des nœuds clients dédiés, mais des nœuds maîtres dédiés.

- Le nombre de nœuds présents dans le cluster peut varier, en fonction du scénario testé.

- Tous les nœuds du cluster Elasticsearch exécutent Marvel pour observer les performances lors de l’exécution et pour que l’agent JMeter Server collecte les données de surveillance pour une analyse ultérieure.

- Au moment du test d’Elasticsearch 2.0.0 et versions ultérieures, un des nœuds de données exécute également Kibana ; il s’agit d’une exigence de la version de Marvel qui s’exécute sur Elasticsearch 2.0.0 et version ultérieure.

## Création d’un groupe de ressources Azure pour les machines virtuelles

Le maître JMeter doit être en mesure de se connecter directement à chacun des nœuds du cluster Elasticsearch pour collecter des données de performances. Si le réseau virtuel JMeter est différent de celui du cluster Elasticsearch, la configuration de chaque nœud Elasticsearch devra se faire avec une adresse IP publique. S’il s’agit d’un problème de configuration d’Elasticsearch, envisagez de mettre en œuvre les machines virtuelles JMeter sur le même réseau virtuel en tant que cluster Elasticsearch en utilisant le même groupe de ressource, auquel cas vous pouvez ignorer la première procédure.

Tout d’abord, [créez un groupe de ressources](../articles/resource-group-portal/#create-resource-group-and-resources). Ce document suppose que votre groupe de ressources est nommé *JMeterPerformanceTest*. Si vous souhaitez exécuter les machines virtuelles JMeter sur le même réseau virtuel que le cluster Elasticsearch, utilisez le même groupe de ressources que ce cluster au lieu d’en créer un nouveau.

## Création de la machine virtuelle JMeter Master

Ensuite, [créez une machine virtuelle Windows](../articles/virtual-machines-windows-tutorial/) à l’aide de l’image *Windows Server 2008 R2 SP1*. Nous vous conseillons de sélectionner une taille de machine virtuelle avec suffisamment de cœurs et de mémoire pour exécuter les tests de performances. Dans l’idéal, il s’agit d’une machine comptant au moins 2 cœurs et 3,5 Go de RAM (Standard A2 ou supérieur).

<!-- TODO add info on why disabling diagnostics is positive -->

Nous vous recommandons de désactiver le diagnostic. Lorsque vous créez la machine virtuelle dans le portail, cette opération est effectuée sur le panneau *Paramètres* dans la section *Analyse*, sous *Diagnostics*. Laissez les autres paramètres à leur valeur par défaut.

Vérifiez que la machine virtuelle et toutes les ressources associées ont été créées avec succès en [examinant le groupe de ressources](../articles/resource-group-portal/#browse-resource-groups) du portail. Les ressources répertoriées doivent comporter une machine virtuelle, un groupe de sécurité réseau et une adresse IP publique portant tous le même nom, et une interface réseau et un compte de stockage avec des noms dérivés de celui de la machine virtuelle.

## Création des machines virtuelles subordonnées JMeter

Maintenant, [créez une machine virtuelle Linux](../articles/virtual-machines-linux-tutorial-portal-rm/) à l’aide de l’image *Ubuntu Server 14.04 LTS*. Comme pour la machine virtuelle JMeter Master, sélectionnez une taille de machine virtuelle avec suffisamment de cœurs et de mémoire pour exécuter les tests de performances. Dans l’idéal, il s’agit d’une machine comptant au moins 2 cœurs et 3,5 Go de RAM (Standard A2 ou supérieur).

De nouveau, nous vous recommandons de désactiver le diagnostic.

Vous pouvez créer autant de machines virtuelles subordonnées que vous le souhaitez.

## Installation de JMeter Server sur les machines virtuelles subordonnées JMeter

Les machines virtuelles subordonnées JMeter exécutent Linux et par défaut, il est impossible de s’y connecter en ouvrant une connexion Bureau à distance (RDP). Vous pouvez en revanche [utiliser PuTTY pour ouvrir une fenêtre de ligne de commande](../articles/virtual-machines-linux-how-to-log-on/) sur chaque machine virtuelle.

Une fois que vous êtes connecté à l’un des ordinateurs virtuels subordonnés, nous allons utiliser un interpréteur de commandes pour installer JMeter.

Commencez par installer l’environnement Java runtime requis pour exécuter JMeter.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

À présent, téléchargez le logiciel JMeter qui se présente sous forme de fichier zip.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Installez la commande de décompression, puis utilisez-la pour développer le logiciel JMeter. Le logiciel est copié vers un dossier nommé **apache-jmeter-2.13**.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Remplacez le répertoire *bin* contenant les exécutables JMeter et les programmes *jmeter-server* et *jmeter*.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

À présent, nous devons modifier le fichier `jmeter.properties` situé dans le dossier actif (utilisez l’éditeur de texte avec lequel vous êtes plus familier, comme *vi* ou *vim*). Recherchez les lignes suivantes :

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Supprimez les commentaires (supprimez les caractères ## du début), modifiez ces lignes comme indiqué ci-dessous, puis enregistrez le fichier et fermez l’éditeur :

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

À présent, exécutez les commandes suivantes pour ouvrir le port 4441 au trafic TCP entrant (il s’agit du port sur lequel vous venez de configurer *jmeter-server* pour écouter) :

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

téléchargez un fichier zip qui contient la collection standard des plug-ins de JMeter (ces plug-ins fournissent des compteurs d’analyse des performances), puis décompressez le fichier. La décompression d’un fichier à cet emplacement place les plug-ins dans le dossier approprié.

Si vous êtes invité à remplacer le fichier de licence, tapez A (pour tous) :

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Utilisez `nohup` pour lancer JMeter Server en arrière-plan. Vous obtenez en principe une réponse comportant un ID de processus et un message vous informant qu’il a créé un objet distant et est prêt à commencer à recevoir des commandes.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] Si la machine virtuelle est arrêtée, le programme JMeter Server est arrêté. Vous devez vous connecter à la machine virtuelle et la redémarrer une fois de plus manuellement. Vous pouvez également configurer le système pour exécuter automatiquement la commande *jmeter-server* au démarrage en ajoutant les commandes suivantes au fichier `/etc/rc.local` (avant la commande *exit 0*) :

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Remplacez `<username>` par votre nom de connexion.

Il peut s’avérer utile de laisser la fenêtre du terminal ouverte. Vous pouvez ainsi surveiller la progression de JMeter Server pendant l’exécution du test.

Vous devrez répéter ces opérations pour chaque machine virtuelle subordonnée JMeter.

## L’installation de l’Agent JMeter Server sur les nœuds d’Elasticsearch

Cette procédure suppose que vous disposez d’un accès en connexion aux nœuds Elasticsearch. Si vous avez créé le cluster à l’aide du modèle ARM, vous pouvez vous connecter à chaque nœud via la machine virtuelle Jump Box, comme indiqué dans la section [Topologie de Production Azure](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies). Vous pouvez également connecter la Jump Box en utilisant PuTTY.

À partir de là, vous pouvez utiliser la commande *ssh* pour vous connecter à chacun des nœuds du cluster Elasticsearch.

Connectez-vous à l’un des nœuds Elasticsearch en tant qu’administrateur. À l’invite de l’interpréteur de commandes, saisissez les commandes suivantes pour créer un dossier destiné à contenir l’Agent de JMeter Server et accéder à ce dossier :

```bash
mkdir server-agent
cd server-agent
```

Exécutez les commandes suivantes pour installer la commande *unzip* (si elle n’est pas déjà installée), téléchargez le logiciel de l’Agent JMeter Server et décompressez-le :

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Exécutez la commande suivante pour configurer le pare-feu et activer le trafic TCP via le port 4444 (il s’agit du port utilisé par l’agent JMeter Server) :

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Exécutez la commande suivante pour démarrer l’Agent JMeter Server en arrière-plan :

```bash
nohup ./startAgent.sh &
```

L’Agent JMeter Server doit répondre avec des messages indiquant qu’il a démarré et qu’il est à l’écoute sur le port 4444. Appuyez sur Entrée pour obtenir une invite de commandes en tant qu’administrateur, puis exécutez la commande suivante : Remplacez `<nodename>` par le nom de votre nœud. Si vous n’êtes pas sûr du nom de votre nœud, vous pouvez le trouver en exécutant la commande `hostname` :

```bash
telnet <nodename> 4444
```

Cette commande ouvre une connexion telnet au port 4444 de votre ordinateur local. Vous pouvez utiliser cette connexion pour vérifier que l’Agent JMeter Server fonctionne correctement.

Si l’Agent JMeter Server ne fonctionne pas, vous recevrez la réponse

`*telnet: Unable to connect to remote host: Connection refused*.`

Si l’agent JMeterServer s’exécute et si le port 4444 a été configuré correctement, vous devez obtenir la réponse suivante :

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] La session telnet ne fournit aucune invite de commandes une fois connectée.

Dans la session telnet, saisissez la commande suivante :

``` 
test
```

Si l’Agent JMeter Server est configuré et écoute correctement, il doit indiquer qu’il a reçu la commande et répondre avec le message *Yep*.

> [AZURE.NOTE] Vous pouvez saisir d’autres commandes pour obtenir des données d’analyse des performances. Par exemple, la commande `metric-single:cpu:idle` vous donnera la proportion du temps pendant lequel le processeur est inactif (il s’agit d’un instantané). Pour obtenir une liste complète des commandes, visitez la page [Agent PerfMon Server](http://jmeter-plugins.org/wiki/PerfMonAgent/).

Dans la session telnet, saisissez la commande suivante pour quitter la session et revenir à l’invite de l’interpréteur de commandes :

``` 
exit
```

> [AZURE.NOTE] Comme avec les machines virtuelles JMeter subordonnées, si vous vous déconnectez, ou si l’ordinateur est arrêté et redémarré, alors vous devrez redémarrer manuellement l’Agent JMeter Server en utilisant la commande `startAgent.sh`. Si vous souhaitez que l’Agent JMeter Server démarre automatiquement, ajoutez la commande suivante à la fin du fichier `/etc/rc.local`, avant la commande *exit 0*. Remplacez `<username>` par votre nom de connexion :

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Remplacez `<username>` par votre nom de connexion.

Vous pouvez maintenant soit répéter ce processus pour tous les autres nœuds du cluster Elasticsearch, soit utiliser la commande `scp` pour copier le dossier et le contenu de l’agent du serveur sur tous les nœuds, puis utiliser la commande `ssh` pour démarrer l’Agent JMeter Server comme indiqué ci-dessous. Remplacez `<username>` par votre nom d’utilisateur, et `<nodename>` par le nom du nœud sur lequel vous souhaitez copier et exécuter le logiciel (vous pouvez être invité à fournir votre mot de passe lorsque vous exécutez chaque commande) :

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## Installation et configuration de JMeter sur la machine virtuelle JMeter Master

Dans le portail Azure, cliquez sur *Groupes ressources*. Dans le panneau *Groupes ressources*, cliquez sur le groupe de ressources contenant JMeter Master et les machines virtuelles subordonnées. Dans le panneau *Groupe de ressources*, cliquez sur la machine virtuelle JMeter Master. Dans le panneau de machine virtuelle, dans la barre d’outils, cliquez sur *Se connecter*. Ouvrez le fichier RDP lorsque vous y êtes invité par le navigateur web. Windows crée une connexion Bureau à distance à votre machine virtuelle. Lorsque vous y êtes invité, saisissez le nom d’utilisateur administrateur et le mot de passe correspondant à la machine virtuelle.

Dans la machine virtuelle, à l’aide d’Internet Explorer, accédez à la page [Télécharger Java pour Windows](http://www.java.com/en/download/ie_manual.jsp). Suivez les instructions pour télécharger et exécuter le programme d’installation Java.

Dans le navigateur web, accédez à la page [Télécharger Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) et téléchargez le fichier zip contenant le fichier binaire le plus récent. Enregistrez le fichier zip dans un emplacement approprié sur votre machine virtuelle.

Accédez le site sur les [plug-ins JMeter personnalisés](http://jmeter-plugins.org/), puis téléchargez l’ensemble de plug-ins standard. Enregistrez le fichier zip dans le même dossier que le téléchargement de JMeter de l’étape précédente.

Dans l’Explorateur Windows, accédez au dossier contenant le fichier zip apache-jmeter-*xx* (où *xx* est la version actuelle de JMeter), puis extrayez les fichiers dans le dossier actuel (le fichier zip crée un sous-dossier appelé apache-jmeter-*xxx*).

Extrayez les fichiers dans le fichier JMeterPlugins-Standard-*yyy* (où *yyy* est la version actuelle des plug-ins) dans le dossier apache-jmeter -*xxx*. Cette opération ajoutera les plug-ins au dossier correct pour JMeter (vous pouvez fusionner en toute sécurité les dossiers lib, et remplacer les fichiers de licence et les fichiers lisez-moi si vous y êtes invité).

Accédez au dossier apache-jmeter -*xxx*/bin et modifiez le fichier jmeter.properties dans le bloc-notes. Dans le fichier `jmeter.properties`, recherchez la section intitulée *Remote hosts and RMI configuration* (Hôtes distants et configuration RMI). Dans cette section du fichier, recherchez la ligne suivante :

```yaml
remote_hosts=127.0.0.1
```

modifiez cette ligne et remplacez l’adresse IP 127.0.0.1 par une liste d’adresses IP ou des noms d’hôte séparés par des virgules pour chacun des serveurs subordonnés JMeter. Par exemple :

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Recherchez la ligne suivante, puis supprimez le caractère `#` au début de cette ligne de caractères et modifiez la valeur des paramètres client.rmi.localport à partir de :

```yaml
#client.rmi.localport=0
```

to:

```yaml
client.rmi.localport=4440
```

Enregistrez le fichier et fermez le bloc-notes.

Dans la barre d’outils Windows, cliquez sur *Démarrer*, sur *Outils d’administration*, puis sur *Pare-feu Windows avec fonctions avancées de sécurité*. Dans la fenêtre *Pare-feu Windows avec fonctions avancées de sécurité* dans le volet de gauche, cliquez avec le bouton droit sur *Règles de trafic entrant*, puis sur *Nouvelle règle*.

Dans l’*Assistant* *Nouvelle règle de trafic entrant*, sur la page *Type de règle*, sélectionnez *Port*, puis cliquez sur *Suivant*. Sur la page *Protocoles et ports*, sélectionnez *TCP*, sélectionnez *Ports locaux spécifiques*, dans le type de zone de texte `4440-4444`, puis cliquez sur *Suivant*. Dans la boîte de dialogue *Action*, sélectionnez *Autoriser la connexion*, puis cliquez sur *Suivant*. Sur la page *Profil*, laissez toutes les options activées, puis cliquez sur *Suivant*. Sur la page *Nom*, dans la zone de texte *Nom*, saisissez *JMeter*, puis cliquez sur *Terminer*. Fermez la fenêtre *Pare-feu Windows avec fonctions de sécurité avancées*.

Dans l’Explorateur Windows, dans le dossier apache-jmeter -*xx*/bin, double-cliquez sur le fichier batch Windows *jmeter* pour lancer l’interface graphique. L’interface utilisateur doit s’afficher :

![](./media/guidance-elasticsearch/performance-image17.png)

Dans la barre de menus, cliquez sur *Exécuter*, sur *Démarrer à distance*, puis vérifiez que les deux machines subordonnées JMeter sont répertoriées :

![](./media/guidance-elasticsearch/performance-image18.png)

Vous êtes maintenant prêt à commencer le test de performances.

## Installation et configuration Marvel

Le modèle de démarrage rapide Elasticsearch pour Azure installera et configurera automatiquement la version appropriée de Marvel si vous définissez les paramètres MARVEL et KIBANA sur true au moment de la création du cluster :

![](./media/guidance-elasticsearch/performance-image19.png)

Si vous ajoutez Marvel à un cluster existant, vous devez exécuter l’installation manuelle, et la procédure est différente selon que vous utilisez Elasticsearch version 1.7.x ou 2.x, comme indiqué dans les procédures suivantes.

### Installation de Marvel avec Elasticsearch 1.73 ou version antérieure

Si vous utilisez Elasticsearch 1.7.3 ou une version antérieure, exécutez les étapes suivantes *sur chaque nœud* du cluster :

- Connectez-vous au nœud et accédez au répertoire de base Elasticsearch. Sous Linux, le répertoire de base classique est `/usr/share/elasticsearch`.

-  Exécutez la commande suivante pour télécharger et installer le plug-in Marvel pour Elasticsearch :

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Arrêtez et redémarrez Elasticsearch sur le nœud :

```bash
sudo service elasticsearch restart
```

- Pour vérifier que Marvel a été correctement installé, ouvrez un navigateur Internet et accédez à l’URL `http://<server>:9200/_plugin/marvel`. Remplacez `<server>` par le nom ou l’adresse IP de n’importe quel serveur Elasticsearch dans le cluster. Vérifiez qu’une page similaire à celle qui figure ci-dessous s’affiche :

![](./media/guidance-elasticsearch/performance-image20.png)


### Installation de Marvel avec Elasticsearch 2.0.0 ou version ultérieure

Si vous utilisez Elasticsearch 2.0.0 ou une version ultérieure, exécutez les tâches suivantes *sur chaque nœud* du cluster :

Connectez-vous au nœud et acceptez le répertoire de base Elasticsearch (généralement `/usr/share/elasticsearch`) Exécutez les commandes suivantes pour télécharger et installer le plug-in Marvel pour Elasticsearch :

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Arrêtez et redémarrez Elasticsearch sur le nœud :

```bash
sudo service elasticsearch restart
```

Dans la procédure suivante, remplacez `<kibana-version>` par 4.2.2 si vous utilisez Elasticsearch 2.0.0 ou Elasticsearch 2.0.1 ou par 4.3.1 si vous utilisez Elasticsearch 2.1.0 ou version ultérieure. Remplacez `<marvel-version>` par 2.0.0 si vous utilisez Elasticsearch 2.0.0 ou Elasticsearch 2.0.1 ou par 2.1.0 si vous utilisez Elasticsearch 2.1.0 ou version ultérieure. Exécutez les tâches suivantes *sur un nœud* dans le cluster :

Connectez-vous au nœud et téléchargez la version de Kibana correspondant à votre version Elasticsearch à partir du [site web de téléchargement Elasticsearch](https://www.elastic.co/downloads/past-releases), puis extrayez le package :

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Ouvrez le port 5601 pour accepter les demandes entrantes :

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Accédez au dossier de configuration kibana (`kibana-<kibana-version>-linux-x64/config`), modifiez le fichier `kibana.yml` et ajoutez la ligne suivante. Remplacez `<server>` par le nom ou l’adresse IP d’un serveur Elasticsearch dans le cluster :

```yaml
elasticsearch.url: "http://<server>:9200"
```

Accédez au dossier bin de kibana (`kibana-<kibana-version>-linux-x64/bin`), et exécutez la commande suivante pour intégrer le plug-in Marvel dans Kibana :

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Démarrez Kibana :

```bash
sudo nohup ./kibana &
```

Pour vérifier l’installation Marvel, ouvrez un navigateur web et accédez à l’URL `http://<server>:5601/app/marvel`. Remplacez `<server>` par le nom ou l’adresse IP du serveur exécutant Kibana.

Vérifiez qu’une page similaire à celle qui s’affiche ci-dessous apparaît (le nom de votre cluster varie probablement par rapport à celui qui s’affiche dans l’image).

![](./media/guidance-elasticsearch/performance-image21.png)

Cliquez sur le lien correspondant à votre cluster (elasticsearch210 dans l’image ci-dessus). En principe, une page similaire à celle qui figure ci-dessous s’affiche :

![](./media/guidance-elasticsearch/performance-image22.png)

[Adaptation des performances d’ingestion de données pour Elasticsearch dans Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->