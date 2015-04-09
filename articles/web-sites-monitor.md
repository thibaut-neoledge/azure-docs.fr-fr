<properties 
	pageTitle="Surveiller les applications web dans le Service d'application Microsoft Azure" 
	description="Découvrez comment surveiller des applications web dans le Service d'application Microsoft Azure, à l'aide du portail de gestion." 
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



#<a name="howtomonitor"></a>Surveiller les applications web dans le Service d'application Microsoft Azure 

[Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) fournit des fonctionnalités d'analyse pour les plans Standard et Premium Service de l'application, via la page de gestion d'analyse. Cette page fournit les statistiques relatives aux performances d'un site web, comme décrit ci-dessous.

##<a name="websitemetrics"></a>Ajout de mesures relatives à une application web

1. Dans les pages de gestion de l'application web de [Microsoft Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715), cliquez sur l'onglet **Surveiller** pour afficher la page de gestion **Surveiller**. Par défaut, le graphique de la page **Moniteur** affiche les mêmes mesures que celui de la page **Tableau de bord**. 

2. Pour afficher des mesures supplémentaires concernant l'application web, cliquez sur l'option **Ajouter des métriques** située au bas de la page. La boîte de dialogue **Choisir des métriques** apparaît. 

3. Cliquez pour sélectionner les mesures supplémentaires à afficher sur la page **Moniteur**. 

4. Une fois sélectionnées les mesures à ajouter à la page **Surveiller**, cochez la case située sur la partie inférieure de la page. 

5. Une fois que vous avez ajouté des mesures à la page **Surveiller**, cliquez pour sélectionner/désélectionner la case à cocher de forme arrondie située en regard de chaque mesure à ajouter/supprimer du graphique se trouvant en haut de la page.

6. Pour supprimer des mesures de la page **Surveiller**, sélectionnez la mesure concernée, puis cliquez sur l'icône **Supprimer une métrique** située au bas de la page.

##<a name="howtoreceivealerts"></a>Réception d'alertes sur les mesures relatives à l'application web
Lorsque l'application web est en mode **Standard**, vous pouvez recevoir des alertes basées sur les mesures de surveillance de cette application. La fonction de création d'alertes requiert le paramétrage préalable d'un point de terminaison web pour la surveillance. Pour effectuer cette opération, vous pouvez accéder à la section **Surveillance** de la page **Configurer**. Sur la page **Paramètres** du portail de gestion Microsoft Azure, vous pouvez créer une règle qui déclenche l'envoi d'une alerte lorsqu'une certaine mesure atteint la valeur que vous avez définie. Vous pouvez également paramétrer l'envoi d'un courrier électronique lorsque l'alerte est déclenchée. Pour plus d'informations, consultez la page [Réception des notifications d'alerte et gestion des règles de création d'alerte dans Azure](http://go.microsoft.com/fwlink/?LinkId=309356).  

##<a name="howtoviewusage"></a>Affichage  des quotas d'utilisation d'une application web

Les applications web peuvent être configurées pour s'exécuter en mode **Partagé** ou **Standard** dans la page de gestion **Mise à l'échelle** de l'application web. Chaque abonnement à Microsoft Azure donne accès à un pool de ressources fourni dans le but d'exécuter jusqu'à 100 applications web par région en mode **Partagé**. Le pool de ressources disponible à cette fin pour chaque abonnement à l'application web est partagé avec d'autres applications web, situées dans la même région géographique et configurées pour s'exécuter en mode **Partagé**. Ces ressources étant partagées pour pouvoir être utilisées par d'autres applications web, leur exploitation par l'ensemble des abonnements est limitée. Les limites appliquées à l'utilisation de ces ressources par un abonnement sont exprimées sous la forme de quotas d'utilisation, répertoriés dans la section de présentation de l'utilisation, sur la page de gestion **Tableau de bord** de chaque application web.

>[AZURE.NOTE] Lorsqu'une application web est configurée pour s'exécuter en mode **Standard**, elle se voit allouer des ressources dédiées équivalentes aux valeurs **Petit** (par défaut), **Moyen** ou **Grand** associées aux tailles de machine virtuelle figurant dans le tableau situé sur la page [Tailles de machines virtuelles et de services cloud pour Microsoft Azure][vmsizes]. Aucune limite n'est fixée concernant les ressources qu'un abonnement peut utiliser pour exécuter des applications web en mode **Standard**. Toutefois, vous ne pouvez pas créer plus de 500 applications web en mode **Standard**, pour chaque région.
 
### Affichage des quotas d'utilisation d'applications web configurées en mode Partagé ###
Pour déterminer dans quelle mesure les quotas d'utilisation des ressources sont affectés par une application web, procédez comme suit :

1. Ouvrez la page de gestion **Tableau de bord** de l'application web.
2. Sous la section **Présentation de l'utilisation** présente les quotas d'utilisation du plan [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) correspondant. Il s'agit d'un sous-ensemble des éléments suivants :
	-	**Données sorties**, **Temps processeur** et **Mémoire** : lorsque le quota est dépassé, Microsoft Azure arrête l'application pendant le reste de l'intervalle de quota en cours. Microsoft Azure démarre l'application web au début de l'intervalle de quota défini suivant.
	-	**Stockage de système de fichiers** : une fois ce quota atteint, l'espace de stockage du système de fichiers reste accessible pour les opérations de lecture, mais les opérations d'écriture (y compris celles que requiert l'activité normale de l'application web) sont bloquées. Les opérations d'écriture sont à nouveau exécutées lorsque l'utilisation des fichiers est restreinte ou que l'application web est déplacée vers un plan App Service associé à un quota plus important.
	-	**Ressources liées** : les quotas associés à l'ensemble des ressources liées d'une application web, telles que les bases de données ou le stockage, sont également affichés ici.   

	Certains quotas peuvent être appliqués par plan d'hébergement web, tandis que d'autres sont appliqués par site. Pour en savoir plus sur les quotas d'utilisation de chaque plan d'hébergement web, voir [Limites de sites web](azure-subscription-service-limits/#limites-de-sites-web).


##<a name="resourceusage"></a>Procédure : prévention de dépassement des quotas

Les quotas ne sont pas liés aux performances ou au coût : ils sont un moyen pour Microsoft Azure de gérer l'utilisation des ressources dans un environnement multi-locataire, en empêchant les locataires de trop utiliser les utilisations partagées. Le dépassement des quotas entraîne un temps d'arrêt ou une limitation de certaines fonctionnalités de votre application web. C'est pourquoi vous devez envisager ce qui suit pour assurer le fonctionnement de l'application quand les quotas arrivent à saturation :

- Déplacez votre ou vos sites web vers un plan App Service de niveau supérieur pour bénéficier d'un quota plus élevé. Par exemple, le seul quota associé aux plans **De base** et **Standard** est " Stockage de système de fichiers ". 
- Comme le nombre d'instances d'une application web augmente, il est de plus en plus probable que l'une d'elles dépasse le quota de ressources partagées. En cas de besoin, envisagez de remettre à l'échelle des instances supplémentaires d'une application web lorsque les quotas de ressources partagées sont dépassés.


##<a name="howtoconfigdiagnostics"></a>Configuration des diagnostics et téléchargement des journaux d'une application web

Les diagnostics sont activés sur la page de gestion **Configurer** de l'application web. Deux types de diagnostics sont disponibles : **diagnostics d'application** et **diagnostics de site**.

#### Diagnostics d'application ####

La section **Diagnostic d'application** de la page de gestion **Configurer** contrôle la journalisation des informations produites par l'application. Cela peut s'avérer utile lorsqu'il est nécessaire de journaliser des événements survenus au sein de l'application. Par exemple, en cas d'erreur liée à votre application, vous pouvez présenter un message d'erreur simplifié à l'utilisateur tout en renseignant de manière plus détaillée les informations du journal destiné à une analyse ultérieure plus approfondie.

Vous pouvez activer ou désactiver les diagnostics d'application suivants :

- **Journal des applications (système de fichiers)** : active la journalisation des informations produites par l'application. Le champ **Niveau de journalisation** détermine si des informations présentant un niveau Erreur, Avertissement ou Information sont journalisées. Vous pouvez également sélectionner le niveau détaillé, qui permet de consigner l'ensemble des informations générées par l'application.

	Les journaux générés à l'aide de ce paramètre sont stockés dans le système de fichiers de votre application web. Pour les télécharger, suivez la procédure détaillée dans la section **Téléchargement des fichiers journaux d'une application web** ci-dessous.

- **Journal des applications (stockage de tables)** : active la journalisation des informations produites par l'application, tout comme l'option Journal des applications (Système de fichiers). Toutefois, les informations de journalisation sont stockées dans un compte de stockage Azure d'une table.

	Pour spécifier le compte de stockage Microsoft Azure et la table associée, sélectionnez **Activer**, puis **Niveau de journalisation**, et enfin **Gérer le stockage des tables**. Spécifiez le compte de stockage et la table à utiliser, ou créez une nouvelle table.

	Vous pouvez accéder aux informations de journalisation stockées dans la table à l'aide d'un client de stockage Azure.

- **Journal des applications (stockage d'objets blob)** : active la journalisation des informations produites par l'application, tout comme l'option Journal des applications (stockage de tables). Toutefois, les informations de journalisation sont stockées dans un objet blob d'un compte de stockage Azure.

	Pour spécifier le compte de stockage Microsoft Azure et l'objet blob associé, sélectionnez **Activer**, puis **Niveau de journalisation**, et enfin **Gérer le stockage des objets blob**. Spécifiez le compte de stockage, le conteneur d'objets blob et le nom de l'objet blob à utiliser, ou créez un nouveau conteneur et un objet blob.

Pour en savoir plus sur les comptes de stockage Microsoft Azure, voir [Gestion des comptes de stockage](/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE] La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.

La journalisation relative aux applications dans les ressources de stockage nécessitant l'utilisation d'un client de stockage pour afficher les données de journalisation, cette fonction est très utile lorsque vous prévoyez d'utiliser un service ou une application capable de lire et de traiter directement les données à partir d'une table ou du stockage d'objets blob Azure. La journalisation dans le système de fichiers génère des fichiers pouvant être téléchargés sur votre ordinateur local via FTP ou d'autres utilitaires, comme décrit plus loin dans cette section.

> [AZURE.NOTE] Les options **Diagnostics d'application (système de fichiers)**, **Diagnostics d'application (stockage de table)** et **Diagnostics d'application (stockage d'objets blob)** peuvent être activées en même temps et présentent des niveaux de journalisation individuels. Ainsi, vous pouvez par exemple consigner les erreurs et les avertissements pour être stockés dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau détaillé de journalisation du système de fichiers une fois le code de l'application instrumenté pour résoudre un problème.

> [AZURE.NOTE] Vous pouvez également activer les diagnostics depuis Microsoft Azure PowerShell à l'aide de l'applet de commandes **Set-AzureWebsite**.
> 
> Si le logiciel Microsoft Azure PowerShell n'est pas installé ou si vous ne l'avez pas configuré pour l'utilisation de votre abonnement Microsoft Azure, voir [Utilisation d'Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE] La journalisation des applications s'appuie sur les informations de journalisation générées par votre application. La méthode permettant de générer ce type d'informations, ainsi que le format des informations, sont propres au langage de votre application. Pour obtenir des informations sur le langage utilisé dans le cadre de la journalisation des applications, lisez les articles suivants :
> 
> - **.NET** : [activez la journalisation des diagnostics des sites web Microsoft Azure.](/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
> - **Node.js** : [découvrez comment déboguer une application Node.js dans les sites web Microsoft Azure.](/develop/nodejs/how-to-guides/Debug-Website/)
> 
> La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.

#### Diagnostic de site ####

La section **Diagnostics de site** de la page de gestion **Configurer** contrôle la journalisation effectuée par le serveur web, tout comme la journalisation des demandes web, l'impossibilité de transmettre des pages ou le temps pris par la transmission d'une page. Vous pouvez activer ou désactiver les options suivantes :

- **Web Server Logging** : permet d'activer la journalisation du serveur web pour enregistrer les journaux d'applications web au moyen du format de journal étendu W3C. La journalisation du serveur web génère un enregistrement de toutes les demandes entrantes adressées à votre application web, qui contient des informations telles que l'adresse IP du client, l'URI demandé, le code d'état HTTP de la réponse, ainsi que la chaîne de l'agent utilisateur du client. Vous pouvez enregistrer les journaux sur un compte de stockage Azure ou sur le système de fichiers.

 Pour enregistrer les journaux de serveur web dans un compte de stockage Microsoft Azure, choisissez **Stockage**, puis **Gérer le stockage** pour spécifier le compte de stockage et le conteneur d'objets blob Microsoft Azure dans lequel conserver les journaux. Pour en savoir plus sur les comptes de stockage Microsoft Azure, voir [Gestion des comptes de stockage](/manage/services/storage/how-to-manage-a-storage-account/).

   Pour enregistrer des fichiers journaux de serveur web sur le système de fichiers, choisissez **Système de fichiers**. La zone **Quota** est alors activée. Elle vous permet de définir la quantité maximale d'espace disque à affecter aux fichiers journaux. Les valeurs minimales et maximales sont respectivement 25 Mo et 100 Mo. La valeur par défaut est 35 Mo.

 Par défaut, les journaux du serveur Web ne sont jamais supprimés. Pour spécifier une période de temps après laquelle les journaux seront automatiquement supprimés, sélectionnez **Définir la rétention** et saisissez le nombre de jours de conservation des journaux dans la zone **Période de rétention**. Ce paramètre est disponible pour les options Azure Storage et File System.

- **Messages d'erreur détaillés** : activez la journalisation détaillée des erreurs pour consigner des informations supplémentaires sur les erreurs du protocole HTTP (codes d'état supérieurs à 400).

- **Suivi des demandes ayant échoué** : activez le suivi des demandes ayant échoué pour capturer des informations concernant les demandes des clients en échec, par exemple les codes d'état HTTP 400 et plus.  Cette option de suivi génère un document XML qui contient des informations sur les modules pour lesquels la demande a été transmise via le gestionnaire des services Internet, les détails renvoyés par le module et l'heure à laquelle le module a été appelé. Ces informations permettent d'isoler le composant source de la défaillance.


Une fois la fonction de diagnostic activée pour une application web, cliquez sur l'icône **Enregistrer** située au bas de la page d'administration **Configurer** pour appliquer les options que vous avez définies.

> [AZURE.IMPORTANT] Les fonctions de journalisation et de suivi peuvent s'avérer très lourdes pour l'application web. Il est recommandé de désactiver la journalisation et le suivi une fois que vous avez pu reproduire le ou les problème(s) que vous essayez de résoudre.

### Configuration avancée ###

Vous pouvez afficher les diagnostics plus avant en ajoutant des paires clé/valeur dans la section **Paramètres de l'application** de la page de gestion **Configurer**. Vous pouvez configurer les paramètres suivants dans la section **Paramètres de l'application** :

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- Emplacement où sont enregistrés les journaux de l'application ; chemin relatif à la racine du serveur Web.

- Valeur par défaut : ..\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- Taille maximale de la mémoire tampon à utiliser au moment de récupérer les journaux de l'application. Initialement, les informations sont écrites dans la mémoire tampon avant d'être transférées dans un fichier ou une ressource de stockage. Si de nouvelles informations sont écrites dans la mémoire tampon avant que les informations précédemment enregistrées soient vidées, ces dernières peuvent être perdues. Si votre application génère d'importants flux d'informations de journalisation, pensez à augmenter la taille de la mémoire tampon.

- Valeur par défaut : 10 Go

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Taille maximale du dossier **Application** contenant les diagnostics d'application écrits dans les fichiers.

- Valeur par défaut : 1 Go

###Téléchargement des fichiers journaux d'une application web

Les fichiers journaux peuvent être téléchargés à l'aide de FTP, Azure PowerShell ou des outils en ligne de commande Azure.

**FTP**

1. Ouvrez la page d'administration **Tableau de bord** de l'application web, puis notez le site FTP figurant sous **Fichiers journaux de diagnostic** et le compte répertorié sous **Utilisateur de déploiement**. Le site FTP héberge les fichiers journaux ; le compte répertorié sous Deployment User permet de vous authentifier auprès du site FTP.
2. Si vous n'avez pas encore créé d'informations d'identification de déploiement, le compte répertorié sous **Utilisateur de déploiement** est répertorié comme **Non défini**. Dans ce cas, vous devez créer les identifiants de déploiement comme décrit dans la section Reset Deployment Credentials du Tableau de bord, car ces identifiants vous permettent de vous authentifier auprès du site FTP sur lequel sont stockés les fichiers journaux. Azure ne prend pas en charge l'authentification auprès du site FTP avec les informations d'identification Live ID.
3. Envisagez d'utiliser un client FTP tel que [FileZilla][fzilla] pour vous connecter au site FTP. Le client FTP facilite la saisie des informations d'identification. En outre, l'affichage des dossiers d'un site FTP y est généralement plus clair que dans un navigateur.
4. Copiez les fichiers journaux du site FTP sur votre ordinateur en local.

**Azure PowerShell**

1. À partir de l'**écran Démarrer** ou du **menu Démarrer**, recherchez l'élément **Azure PowerShell**. Cliquez avec le bouton droit de la souris sur l'entrée **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	> [AZURE.NOTE] Si le logiciel **Azure PowerShell** n'est pas installé, consultez la section [Prise en main des applets de commande Microsoft Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) pour obtenir des informations sur l'installation et la configuration de ce logiciel.

2. À l'invite Azure PowerShell, entrez la commande suivante pour télécharger les fichiers journaux :

		Save-AzureWebSiteLog -Name webappname

	Cette commande permet de télécharger les fichiers journaux de l'application web indiquée par **nom_site_web** et les enregistre sous forme de fichier compressé **log.zip** dans le répertoire en cours.
	
	Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

		Get-AzureWebSiteLog -Name webappname -Tail

	Cette commande affiche les informations de journalisation à l'invite Azure PowerShell à mesure qu'elles surviennent.

**Outils de ligne de commande Microsoft Azure**

Ouvrez une nouvelle invite de commande, la console PowerShell, un interpréteur de commandes ou une session terminal, et exécutez la commande suivante pour télécharger les fichiers journaux :

	azure site log download webappname

Cette commande permet de télécharger les fichiers journaux de l'application web indiquée par **nom_site_web** et les enregistre sous forme de fichier compressé **log.zip** dans le répertoire en cours.

Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

	azure site log tail webappname

Cette commande affiche les informations de journalisation à l'invite de commande, sur la console PowerShell, un interpréteur de commandes ou une session terminal à partir de laquelle la commande est exécutée.

> [AZURE.NOTE] Si la commande **azure** n'est pas installée, consultez la page [Utilisation des outils en ligne de commande Azure](virtual-machines-command-line-tools.md) pour en savoir plus sur l'installation et la configuration de cette commande.

### Lecture des fichiers journaux ###

Le contenu des fichiers journaux générés une fois que vous avez activé la journalisation et/ou le suivi d'une application web varie en fonction du niveau de journalisation/suivi défini sur la page de gestion Configurer de l'application web. Voici les emplacements des fichiers journaux et les méthodes permettant de les analyser :

**Type de fichier journal : journalisation des applications**

- Emplacement : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications. Les informations consignées incluent la date et l'heure, l'ID de processus (PID) de l'application, ainsi que la valeur générée par l'instrumentation de l'application.

- Lire les fichiers avec : un éditeur ou un analyseur de texte qui sait interpréter les valeurs générées par votre application

**Type de fichier journal : suivi des demandes ayant échoué**

- Emplacement : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer. 

- Lire les fichiers avec : Internet Explorer

**Type de fichier journal : messages d'erreur détaillés**

- Emplacement : /LogFiles/DetailedErrors/. Le dossier /LogFiles/DetailedErrors/ contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite. 

- Lire les fichiers avec : un navigateur Web

Les fichiers .htm contiennent les sections suivantes :

- **Informations détaillées sur l'erreur :** inclut des informations sur l'erreur, par exemple le <em>module</em>, le <em>gestionnaire</em>, le <em>code d'erreur</em> et <em>l'URL demandée</em>.

- **Causes les plus probables :** répertorie plusieurs causes possibles de l'erreur.

- **Actions à tenter :** répertorie les solutions possibles au problème signalé par l'erreur.

- **Afficher les liens vers les informations légales et autres** : fournit des informations supplémentaires sur l'erreur qui peuvent inclure des liens vers d'autres ressources, telles que les articles de la Base de connaissances Microsoft.

**Type de fichier journal : journalisation du serveur Web**

- Emplacement : /LogFiles/http/RawLogs. Les informations stockées dans les fichiers sont mises en forme à l'aide du [format de journal étendu W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Les champs s-computername, s-ip et cs-version ne sont pas utilisés par les applications web Microsoft Azure.

- Lire les fichiers avec : Log Parser. Utilisé pour analyser et interroger les fichiers journaux du gestionnaire des services Internet. Le logiciel Log Parser 2.2 est disponible sur le Centre de téléchargement Microsoft, à l'adresse suivante : <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a> Surveillance de l'état d'un point de terminaison web

Cette fonctionnalité, disponible en mode **Standard**, vous permet de surveiller jusqu'à 2 points de terminaison à partir de 3 emplacements géographiques maximum. 

Elle paramètre des tests Web à partir de différents emplacements où sont évalués les temps de réponse et de disponibilité des URL. Ce type de test lance une requête HTTP get sur l'URL Web pour déterminer les temps de réponse et de disponibilité depuis chaque emplacement. Chaque emplacement configuré exécute un test toutes les cinq minutes.

La disponibilité est contrôlée à l'aide des codes réponse HTTP et le temps de réponse est mesuré en millisecondes. Le temps d'activité est considéré comme étant à 100 % quand le temps de réponse est inférieur à 30 secondes et le code d'état HTTP inférieur à 400. Le temps d'activité est à 0 % quand le temps de réponse est supérieur à 30 secondes ou que le code d'état HTTP est supérieur à 400.

Une fois que la surveillance des points de terminaison est configurée, vous pouvez rechercher sur chaque point les détails des temps de réponse et de l'état de disponibilité pendant l'intervalle de surveillance, à partir de chaque lieu de test. Vous pouvez également configurer une règle d'alerte quand le temps de réponse des points de terminaison est trop long, par exemple. 

**Pour configurer la fonction de surveillance des points de terminaison, procédez comme suit :**

1.	Ouvrez **Applications web**. Cliquez sur le nom de l'application web que vous souhaitez configurer.
2.	Cliquez sur l'onglet **Configurer**. 
3.     Accédez à la section **Surveillance** pour saisir les paramètres relatifs à votre point de terminaison.
4.	Entrez le nom du point de terminaison.
5.	Entrez l'URL d'une partie de l'application web que vous souhaitez surveiller. Par exemple : [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive). 
6.	Sélectionnez une ou plusieurs régions géographiques dans la liste.
7.	Vous pouvez aussi recommencer les étapes précédentes pour créer un second point de terminaison.
8.	Cliquez sur **Enregistrer**. Il se peut que les données de surveillance des points de terminaison web mettent un certain temps à s'afficher dans les onglets **Tableau de bord** et **Surveiller**.

	Pour créer une règle de courrier électronique, procédez comme suit :

9.	Dans la barre de service située à l'extrême gauche de la page, cliquez sur l'option **Services de gestion**.
10.	Cliquez sur l'option **Ajouter une règle** située au bas de la page.
11.	Dans le champ **Type de service**, sélectionnez **Application web**, puis sélectionnez l'application web pour laquelle vous avez configuré la fonction de surveillance de point de terminaison. Cliquez sur **Suivant**.
12.	Dans **Mesure**, vous pouvez maintenant sélectionner des mesures supplémentaires pour le point de terminaison que vous avez configuré. Par exemple : **Temps de réponse (page d'accueil/US : IL-Chicago)**. Sélectionnez la mesure du temps de réponse et tapez 3 dans le champ **Valeur seuil**, afin de spécifier un seuil de 3 secondes.
13.	Sélectionnez **Envoyez un courrier électronique à l'administrateur du service et aux coadministrateurs**. Cliquez sur **Terminé**.

	Azure va maintenant surveiller activement le point de terminaison et envoyer une alerte par courrier électronique quand le temps de réponse sera supérieur à 3 secondes.


Pour en savoir plus sur la surveillance du point de terminaison d'une application web, consultez les vidéos suivantes :

- [Scott Guthrie présente les sites web Microsoft Azure et configure la surveillance des points de terminaison](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Assurer la gestion des sites Web Azure et la surveillance des points de terminaison, avec Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE] Si vous souhaitez vous familiariser avec le Service d'application Microsoft Azure avant l'ouverture d'un compte Microsoft Azure, accédez à la zone [Essayer le Service d'application](http://go.microsoft.com/fwlink/?LinkId=523751), qui vous permet de créer une application web de démarrage à durée de vie limitée dans le service d'application. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## Nouveautés
* Pour savoir comment passer d'un site web à un service d'application, consultez les sections suivantes : [Service d'application Microsoft Azure et impact sur les services Microsoft Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour en savoir plus sur la transition de l'ancien portail vers le nouveau portail, consultez les sections suivantes : [Référence pour naviguer dans la version préliminaire du portail](http://go.microsoft.com/fwlink/?LinkId=529715)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169


<!--HONumber=49-->