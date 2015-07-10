<properties 
	pageTitle="Sauvegarder une application web dans Azure App Service" 
	description="Apprenez à créer des sauvegardes de vos applications Web dans Azure App Service" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Sauvegarder une application web dans Azure App Service


La fonctionnalité de sauvegarde et de restauration d’[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de créer facilement des sauvegardes d’application Web manuelles et automatiques. Vous pouvez restaurer votre application web à une étape précédente ou créer une nouvelle application web basée sur l’une des sauvegardes de votre application d’origine.

Pour plus d’informations sur la restauration d’une application Web Azure à partir d’une sauvegarde, consultez [Restaurer une application Web](web-sites-restore.md).

<a name="whatsbackedup"></a>
## Éléments sauvegardés 
Web Apps peut sauvegarder les informations suivantes :

* configuration d’une application web ;
* contenu d’un fichier d’application web ;
* serveurs SQL ou bases de données MySQL connectés à votre application (vous pouvez choisir l’élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le conteneur et le compte de stockage Azure que vous indiquez.

> [AZURE.NOTE]Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.

<a name="requirements"></a>
## Exigences et restrictions

* La fonction de sauvegarde et de restauration implique que le site soit en mode Standard. Pour plus d’informations sur l’évolution de votre application Web en vue d’utiliser le mode Standard, consultez [Faire évoluer une application Web dans Azure App Service](web-sites-scale.md). Notez que le mode Premium permet de réaliser un nombre supérieur de sauvegardes quotidiennes par rapport au mode Standard.

* La fonctionnalité de sauvegarde et de restauration requiert un conteneur et un compte de stockage Azure relevant du même abonnement que l’application web que vous sauvegardez. Si vous ne disposez pas encore d’un compte de stockage, vous pouvez en créer un. Pour cela, cliquez sur le **Compte de stockage**, dans le panneau **Sauvegardes** du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), puis, choisissez le **Compte de stockage** et le **Conteneur** dans le panneau **Destination**. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) situés en bas de cet article.

* La fonctionnalité de sauvegarde et de restauration prend en charge jusqu’à 10 Go de contenu de site Web et de base de données. Une erreur est indiquée dans les journaux des opérations lorsqu’un dépassement de ce seuil empêche la sauvegarde.

<a name="manualbackup"></a>
## Création d’une sauvegarde manuelle

1. Dans le portail Azure, choisissez votre application web dans le panneau Web Apps. Les informations détaillées sur votre application web s’affichent dans un nouveau panneau.
2. Sélectionnez l’option **Paramètres**. Le panneau **Paramètres** s’affiche : il vous permet de modifier votre application Web.
	
	![Page Sauvegardes][ChooseBackupsPage]

3. Choisissez l’option **Sauvegardes** dans le panneau **Paramètres**. Le panneau **Sauvegardes** s’affiche.
	
4. Dans le panneau **Sauvegardes**, choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l’application web que vous sauvegardez.
	
	![Sélection d'un compte de stockage][ChooseStorageAccount]
	
5. Sous l’option **Bases de données incluses** du panneau **Sauvegardes**, sélectionnez les bases de données connectées à l’application Web (SQL Server ou MySQL) que vous souhaitez sauvegarder.

	> [AZURE.NOTE]Pour qu’une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d’application web** du portail.
	
6. Dans le panneau **Sauvegardes**, sélectionnez la destination concernée sous **Destination de sauvegarde**. Vous devez choisir un conteneur et un compte de stockage existants.
7. Dans la barre de commandes, cliquez sur **Backup Now**.
	
	![Bouton Backup Now][BackUpNow]
	
	Un message de progression s’affiche au cours du processus de sauvegarde.
	

Vous pouvez à tout moment effectuer une sauvegarde manuelle.

<a name="automatedbackups"></a>
## Configuration de sauvegardes automatisées

1. Dans le panneau **Sauvegardes**, activez l’option **Sauvegarde planifiée**.
	
	![Activation des sauvegardes automatisées][SetAutomatedBackupOn]
	
2. Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre application web. Ce compte de stockage doit relever du même abonnement que l’application web que vous sauvegardez.
	
	![Sélection d'un compte de stockage][ChooseStorageAccount]
	
3. Dans la zone **Fréquence**, indiquez la fréquence de sauvegarde automatisée de votre choix. Le nombre de jours doit être situé entre 1 et 90 compris (d'une fois par jour à tous les 90 jours).
	
4. Utilisez l’option **Début** pour indiquer la date et l’heure de début des sauvegardes automatisées souhaitées.
	
	> [AZURE.NOTE]Azure stocke les heures de sauvegarde au format UTC, mais les affiche selon l’heure de l’ordinateur que vous utilisez pour afficher le portail.
	
5. Dans la section **Bases de données incluses**, sélectionnez les bases de données connectées à l’application Web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. Pour qu’une base de données apparaisse dans la liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d’application web** du portail.
	
	> [AZURE.NOTE]Si vous avez sélectionné une ou plusieurs bases de données dans la sauvegarde et spécifié une fréquence inférieure à 7 jours, vous serez informé du fait que de fréquentes sauvegardes peuvent augmenter les coûts de vos bases de données.
	
6. D’autre part, définissez la valeur **Rétention (jours)** en fonction du nombre de jours de conservation de la sauvegarde.
7. Dans la barre de commandes, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de votre configuration (ou sélectionnez **Ignorer** pour ne pas les enregistrer).
	
	![Bouton enregistrer][SaveIcon]

<a name="notes"></a>
## Remarques

* Veillez à configurer correctement les chaînes de connexion pour chacune de vos bases de données dans le panneau **Paramètres d’application web** correspondant au panneau **Paramètres** de l’application Web, afin que la fonctionnalité de sauvegarde et de restauration inclue vos bases de données.
* Bien qu’il vous soit possible de sauvegarder plusieurs applications web sur le même compte de stockage, pensez à créer un compte de stockage distinct pour chaque application web afin d’en faciliter la maintenance.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<a name="partialbackups"></a>
## Sauvegarde d’une partie de votre site uniquement

Parfois, vous ne souhaitez pas sauvegarder tout le contenu de votre site, surtout si vous sauvegardez votre site régulièrement, ou si votre site dispose de plus de 10 Go de contenu (ce qui est la quantité maximale que vous pouvez sauvegarder à la fois).

Par exemple, vous ne souhaiterez probablement pas sauvegarder les fichiers journaux. De la même manière, si vous [avez défini des sauvegardes hebdomadaires](https://azure.microsoft.com/fr-fr/documentation/articles/web-sites-backup/#configure-automated-backups), vous ne voudrez certainement pas remplir votre compte de stockage de contenu statique qui ne change jamais, tel que les anciens billets de blog ou les images.

Les sauvegardes partielles vous permettent de choisir exactement les fichiers à sauvegarder.

###Spécifiez les fichiers que vous ne souhaitez pas sauvegarder
Vous pouvez créer une liste de fichiers et dossiers à exclure de la sauvegarde.

Enregistrez la liste sous forme de fichier texte appelé _backup.filter dans le dossier wwwroot de votre site. Pour y accéder facilement, vous pouvez utiliser la [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) à l’adresse `http://{yoursite}.scm.azurewebsites.net/DebugConsole`. 

Les instructions ci-dessous impliquent l’utilisation de la Console Kudu pour créer le fichier _backup.filter, mais vous pouvez utiliser votre méthode de déploiement préférée pour y placer le fichier.

###Procédure
J'ai un site qui contient des fichiers journaux et des images statiques d'années précédentes et qui ne vont jamais changer.

J'ai déjà une sauvegarde complète du site qui inclut les anciennes images. Je souhaite désormais effectuer une sauvegarde quotidienne du site, mais je ne veux pas payer pour le stockage des fichiers journaux ou des images statiques qui ne changent jamais.

![Dossier des journaux][LogsFolder]
![Dossier images][ImagesFolder]
	
Les étapes ci-dessous expliquent comment exclure ces fichiers de la sauvegarde.

####Identifiez les fichiers et dossiers que vous ne souhaitez pas sauvegarder.
C'est facile. Je sais déjà que je ne veux sauvegarder aucun fichier journal. Je dois donc exclure `D:\home\site\wwwroot\Logs`.

Toutes les applications Web d’Azure disposent d’un autre dossier contenant des fichiers journaux. Celui-ci se trouve sous `D:\home\LogFiles`. Excluons-le également.

Je ne veux pas non plus sauvegarder indéfiniment les images des années précédentes. Ajoutons donc `D:\home\site\wwwroot\Images\2013` et `D:\home\site\wwwroot\Images\2014` à la liste.

Enfin, excluons également le fichier brand.png qui se trouve dans le dossier Images, juste pour montrer qu’il est possible d’exclure des fichiers individuels. Il se trouve sous `D:\home\site\wwwroot\Images\brand.png`

Pour résumer, voici les dossiers à exclure de la sauvegarde :

* D:\home\site\wwwroot\Logs
* D:\home\LogFiles
* D:\home\site\wwwroot\Images\2013
* D:\home\site\wwwroot\Images\2014
* D:\home\site\wwwroot\Images\brand.png

#### Créez la liste d'exclusion
Enregistrez la liste des fichiers et dossiers que vous ne souhaitez pas sauvegarder dans un fichier spécial appelé _backup.filter. Créez le fichier et placez-le sous `D:\home\site\wwwroot_backup.filter`.

Répertoriez tous les fichiers et dossiers que vous ne souhaitez pas sauvegarder dans le fichier _backup.filter. Ajoutez le chemin d'accès complet (commençant par D:\home) du dossier ou du fichier que vous souhaitez exclure de la sauvegarde ; un chemin d'accès par ligne.

Pour le site en question, `D:\home\site\wwwroot\Logs` devient `\site\wwwroot\Logs`, `D:\home\LogFiles` devient `\LogFiles`, etc. Le contenu du fichier _backup.filter ressemble donc à cela :

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

Notez que chaque ligne commence par ``. C'est important.

###Effectuez une sauvegarde
Maintenant, vous pouvez exécuter les sauvegardes comme à votre habitude. [Manuellement](https://azure.microsoft.com/fr-fr/documentation/articles/web-sites-backup/#create-a-manual-backup) ou [automatiquement](https://azure.microsoft.com/fr-fr/documentation/articles/web-sites-backup/#configure-automated-backups), comme bon vous semble.

Tous les fichiers et dossiers correspondant aux critères de filtre répertoriés dans le fichier _backup.filter sont exclus de la sauvegarde. Cela signifie que les fichiers journaux et image 2013 et 2014 ne seront plus sauvegardés.

###Restauration de votre site sauvegardé
Pour restaurer les sauvegardes partielles de votre site, procédez de la même façon que pour [restaurer une sauvegarde régulière](https://azure.microsoft.com/fr-fr/documentation/articles/web-sites-restore/). Le résultat est le même.

####Détails techniques
Avec les sauvegardes complètes (non partielles), tout le contenu du site est normalement remplacé par tout ce qui se trouve dans la sauvegarde. Si un fichier se trouve sur le site, mais pas dans la sauvegarde, il est supprimé.

Toutefois, lors de la restauration de sauvegardes partielles, le contenu qui se trouve dans l’un des dossiers à exclure (comme `D:\home\site\wwwroot\images\2014` dans l’exemple ci-dessus) est conservé tel quel. Tous les fichiers individuels exclus restent inchangés lors de la restauration.

<a name="aboutbackups"></a>
## Mode de stockage des sauvegardes

Dès lors que vous avez effectué une ou plusieurs sauvegardes, ces dernières apparaissent dans le panneau **Conteneurs** de votre **Compte de stockage**, ainsi que votre application Web. Dans le **Compte de stockage**, chaque sauvegarde se compose d’un fichier .zip et d’un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip.

Les noms des fichiers de sauvegarde .zip et .xml se composent du nom de votre application web suivi d’un trait de soulignement et de l’heure à laquelle la sauvegarde a eu lieu. L'horodatage comprend la date au format AAAAMMJJ (en chiffres, sans espace) ainsi que l'heure UTC au format 24 heures (par exemple, fabrikam_201402152300.zip). Vous pouvez décompresser ces fichiers pour accéder à vos sauvegardes sans recourir à une restauration de l’application web.

Le fichier XML stocké avec le fichier zip indique le nom du fichier de base de données sous *description_base_données* > *bases_données* > *description_sauvegarde_base_données* > *nom_fichier*.

Le fichier de sauvegarde de base de données est stocké à la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL basée sur l'exportation du fichier BACPAC, suivez les étapes indiquées dans l'article [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/library/hh710052.aspx).

Pour plus d’informations sur la restauration des applications Web (y compris les bases de données) par le biais du portail Azure, consultez [Restaurer une application web dans Azure App Service](web-sites-restore.md).

> [AZURE.NOTE]Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

<a name="bestpractices"></a>
##Meilleures pratiques
Que faire quand une catastrophe se produit et que vous devez restaurer votre site ? Vous devez y être préparé.

Certes, vous pouvez effectuer des sauvegardes partielles, mais faites au moins une sauvegarde complète du site afin de pouvoir récupérer l’intégralité de son contenu en cas de scénario catastrophe. Lorsque vous devrez restaurer vos sauvegardes, vous commencerez par restaurer la sauvegarde complète du site, puis restaurerez la dernière sauvegarde partielle par-dessus.

Pourquoi ? Cela vous permet d'utiliser des [emplacements de déploiement](https://azure.microsoft.com/fr-fr/documentation/articles/web-sites-staged-publishing/) pour tester votre site restauré. Vous pouvez même tester le processus de restauration sans jamais toucher à votre site de production. Tester votre processus de restauration est une [très bonne chose](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Vous n’êtes pas à l’abri de pièges, comme la fois où j’ai tenté de restaurer mon blog et que j’ai fini par perdre la moitié de son contenu.

###Une histoire horrible.

Mon blog est hébergé sur la plate-forme de création de blogs [Ghost](https://ghost.org/). Comme tout développeur responsable, j’avais créé une sauvegarde de mon site et tout était pour le mieux dans le meilleur des mondes. Puis un jour, j'ai reçu un message indiquant qu'une nouvelle version de Ghost était disponible et que je pouvais effectuer la mise à niveau de mon blog vers cette nouvelle version. Parfait !

J'ai créé une sauvegarde supplémentaire de mon site pour sauvegarder les derniers billets de blog et ai procédé à la mise à niveau de Ghost.

Sur mon site de production.

Grosse erreur.

Un problème est survenu lors de la mise à niveau et mon écran d’accueil était vide. « Pas de problème », me suis-je dit, « je vais tout simplement restaurer la sauvegarde que je viens de faire. »

J'ai restauré la sauvegarde, tout est revenu... sauf les billets de blog.

QUOI ???

Il s'avère que dans les [notes de mise à niveau de Ghost](http://support.ghost.org/how-to-upgrade/), il y a cet avertissement :

![Vous pouvez effectuer une copie de votre contenu/de vos données, mais ne le faites pas lorsque Ghost est en cours d'exécution. Pensez à l’arrêter avant d’aller plus loin !][GhostUpgradeWarning]

Si vous tentez de sauvegarder les données pendant que Ghost est en cours d’exécution... les données ne seront pas sauvegardées.

Voilà qui est ballot !

Si j'avais d’abord testé la restauration sur un emplacement de test, j'aurais vu ce problème et n’aurais pas perdu tous mes billets.

C'est la vie. Et cela peut arriver même aux [meilleurs d'entre nous](http://blog.codinghorror.com/international-backup-awareness-day/).

Testez vos sauvegardes.

<a name="nextsteps"></a>
## Étapes suivantes
Pour plus d’informations sur la restauration des applications Web Azure à partir d’une sauvegarde, consultez [Restaurer une application web dans Azure App Service](web-sites-restore.md).

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### En savoir plus sur les comptes de stockage

[Présentation des comptes de stockage](../storage-whatis-account.md)

[Création d’un compte de stockage](../storage-create-storage-account/)

[Surveillance d’un compte de stockage](../storage-monitor-storage-account.md)

[Présentation de la facturation du stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx) (en anglais)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!----HONumber=62-->