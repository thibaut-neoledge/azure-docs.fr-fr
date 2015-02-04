<properties urlDisplayName="How to monitor" pageTitle="Surveillance de sites web - Gestion du service Azure" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Découvrez comment analyser les sites web Azure à l'aide de la page d'analyse du portail de gestion." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Websites" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />



#<a name="howtomonitor"></a>Surveillance de sites web

Les sites Web proposent une fonctionnalité de surveillance via une page d'administration dédiée. Cette page fournit les statistiques de performances d'un site Web, comme décrit ci-dessous.

## Sommaire ##
- [Procédure : ajout des mesures d'un site web](#websitemetrics)
- [Procédure : réception d'alertes générées à partir des mesures d'un site web](#howtoreceivealerts)
- [Procédure : affichage des quotas d'utilisation d'un site web](#howtoviewusage)
- [Procédure : prévention de dépassement des quotas](#resourceusage)
- [Procédure : configuration des diagnostics et téléchargement des journaux d'un site web](#howtoconfigdiagnostics)
- [Procédure : surveillance de l'état d'un point de terminaison web](#webendpointstatus)

##<a name="websitemetrics"></a>Procédure : ajout des mesures d'un site Web
1. Dans le [portail de gestion Azure](http://manage.windowsazure.com/), à partir des pages de gestion du site web, cliquez sur l'onglet **Surveiller** pour afficher la page de gestion **Surveiller**. Par défaut, le graphique de la page **Surveiller** affiche les même mesures que celui de la page **Tableau de bord**. 

2. Pour afficher des mesures supplémentaires sur le site web, cliquez sur **Ajouter des métriques** en bas de la page pour afficher la boîte de dialogue **Choisir des métriques**. 

3. Cliquez pour sélectionner des mesures supplémentaires à afficher sur la page **Surveiller**. 

4. Une fois sélectionnées les mesures à ajouter à la page **Surveiller**, cochez la case en bas. 

5. Une fois que vous avez ajouté des mesures à la page **Surveiller**, cliquez pour cocher/décocher la case d'option à côté de chaque mesure à ajouter/supprimer du graphique en haut de la page.

6. Pour supprimer des mesures de la page **Surveiller**, sélectionnez la mesure à supprimer, puis cliquez sur l'icône **Supprimer une métrique** en bas de la page.

##<a name="howtoreceivealerts"></a>Procédure : réception d'alertes générées à partir des mesures d'un site Web
En mode **Standard** du site web, vous pouvez recevoir des alertes en fonction des indicateurs de surveillance de votre site web. La fonction d'alerte requiert la configuration préalable de la surveillance d'un point de terminaison web, qui s'effectue dans la section **Surveillance** de la page **Configurer**. Dans la page **Paramètres** du portail de gestion Azure, vous pouvez alors créer une règle pour déclencher une alerte lorsque la mesure de votre choix atteint une valeur définie. Vous pouvez également paramétrer l'envoi d'un courrier électronique lorsque l'alerte est déclenchée. Pour plus d'informations, consultez [Procédure : réception de notifications d'alerte et gestion des règles d'alerte dans Azure](http://go.microsoft.com/fwlink/?LinkId=309356).  

##<a name="howtoviewusage"></a>Procédure : affichage des quotas d'utilisation d'un site web

Les sites web peuvent être configurés pour s'exécuter en mode **Partagé** ou **Standard** dans la page de gestion **Mettre à l'échelle** du site web. Chaque abonnement Azure donne accès à une réserve de ressources permettant d'exécuter jusqu'à 100 sites web par région en mode **Partagé**. La réserve de ressources disponibles pour chaque abonnement au site web dans ce but est partagée par d'autres sites web de la même région géographique qui sont configurés pour s'exécuter en mode **Partagé**. Ces ressources étant partagées pour utilisation par d'autres sites Web, tous les abonnements sont limités dans leur utilisation. Les limites appliquées à l'utilisation de ces ressources par abonnement sont exprimées sous forme de quotas d'utilisation, répertoriés dans la section de vue d'ensemble de l'utilisation dans la page de gestion **Tableau de bord**.

>[WACOM.NOTE] Quand un site web est configuré pour s'exécuter en mode **Standard**, des ressources lui sont attribuées, équivalentes à la taille des machines virtuelles (**Petite** (par défaut), **Moyenne** ou **Grande**) répertoriées dans le tableau de la section [Tailles de machine virtuelle et de service cloud pour Azure][vmsizes]. Aucune limite n'est fixée concernant les ressources qu'un abonnement peut utiliser pour exécuter des sites web en mode **Standard**. Toutefois, le nombre de sites web en mode **Standard** pouvant être créés par région est limité à 500.
 
### Procédure : affichage des quotas d'utilisation de sites Web configurés en mode Partagé ###
Pour déterminer dans quelle mesure les quotas d'utilisation des ressources sont affectés par un site Web, procédez comme suit :

1. Ouvrez la page de gestion **Tableau de bord** du site web.
2. Sous la section **vue d'ensemble de l'utilisation**, les quotas d'utilisation de votre plan d'hébergement web sont affichés, sous forme d'un sous-ensemble de ce qui suit :
	-	**Données sortantes**, **Temps processeur** et **Mémoire** : quand le quota est dépassé, Azure arrête le site web jusqu'à la fin de l'intervalle de quota actuel. Azure démarre les sites web au début du prochain intervalle de quota défini.
	-	**Stockage de système de fichiers** : quand ce quota est atteint, le stockage de système de fichiers reste accessible pour les opérations de lecture, mais toutes les opérations d'écriture, y compris celles requises par l'activité normale du site web, sont bloquées. Les opérations d'écriture reprennent quand vous réduisez l'utilisation du stockage ou que vous déplacez le site web vers un plan d'hébergement web doté d'un quota supérieur.
	-	**Ressources liées** : les quotas pour toutes les ressources liées du site web, telles que les bases de données ou le stockage, sont également affichés ici.   

	Certains quotas peuvent être appliqués par plan d'hébergement web, tandis que d'autres sont appliqués par site. Pour plus d'informations sur les quotas d'utilisation de chaque plan d'hébergement web, consultez [Limites des sites web](http://azure.microsoft.com/fr-fr/documentation/articles/azure-subscription-service-limits/#websiteslimits).


##<a name="resourceusage"></a>Procédure : prévention de dépassement des quotas

Les quotas ne sont pas liés aux performances ou au coût, ils sont un moyen pour Azure de gérer l'utilisation des ressources dans un environnement multi-locataire, en empêchant les locataires de trop utiliser les utilisations partagées. Le dépassement des quotas entraîne un temps d'arrêt ou une limitation de certaines fonctionnalités de votre site web. C'est pourquoi vous devez envisager ce qui suit pour assurer le fonctionnement de votre site web quand les quotas arrivent à saturation :

- déplacez votre ou vos sites web vers un plan d'hébergement web de niveau supérieur pour bénéficier d'un quota plus élevé. Par exemple, le seul quota des plans **De base** et **Standard** concerne le stockage de système de fichiers. 
- Si le nombre d'instances d'un site web augmente, la probabilité que les quotas de ressources partagées soient dépassés augmente également. En cas de besoin, envisagez de remettre à l'échelle des instances supplémentaires d'un site web quand les quotas de ressources partagées sont dépassés.


##<a name="howtoconfigdiagnostics"></a>Procédure : configuration des diagnostics et téléchargement des journaux d'un site Web

Les diagnostics sont activés sur la page de gestion **Configurer** du site web. Deux types de diagnostics sont disponibles : **le diagnostic d'application** et le **diagnostic de site**.

####Diagnostic d'application####

La section **diagnostic d'application** de la page de gestion **Configurer** contrôle la journalisation des informations générées par l'application, ce qui est utile pour consigner des événements liés à une application. Par exemple, en cas d'erreur liée à votre application, vous pouvez présenter un message d'erreur simplifié à l'utilisateur tout en renseignant de manière plus détaillée les informations du journal destiné à une analyse ultérieure plus approfondie.

Vous pouvez activer ou désactiver les diagnostics d'application suivants :

- **Journalisation de l'application (système de fichiers)** : active la journalisation des informations générées par l'application. Le champ **Niveau de journalisation** détermine si les informations de niveau Erreur, Avertissement ou Informations sont consignées. Vous pouvez également sélectionner le niveau détaillé, qui permet de consigner l'ensemble des informations générées par l'application.

	Les journaux générés à l'aide de ce paramètre sont stockés dans le système de fichiers de votre site web. Pour les télécharger, suivez la procédure détaillée dans la section **Téléchargement des fichiers journaux d'un site web** ci-dessous.

- **Journalisation de l'application (stockage de table)** : active la journalisation des informations générées par l'application, similaire à l'option Journalisation de l'application (système de fichiers). Toutefois, les informations de journalisation sont stockées dans un compte de stockage Azure d'une table.

	Pour spécifier le compte de stockage Azure et la table, choisissez **Activé**, sélectionnez **Niveau de journalisation**, puis **Gérer le stockage des tables**. Spécifiez le compte de stockage et la table à utiliser, ou créez une nouvelle table.

	Vous pouvez accéder aux informations de journalisation stockées dans la table à l'aide d'un client de stockage Azure.

- **Journalisation de l'application (stockage d'objets blob)** - active la journalisation des informations générées par l'application, similaire à l'option Journalisation de l'application (stockage de table). Toutefois, les informations de journalisation sont stockées dans un objet blob d'un compte de stockage Azure.

	Pour spécifier le compte de stockage Azure et l'objet blob, choisissez **Activé**, sélectionnez le **Niveau de journalisation**, puis **Gérer le stockage des objets blob**. Spécifiez le compte de stockage, le conteneur d'objets blob et le nom de l'objet blob à utiliser, ou créez un nouveau conteneur et un objet blob.

Pour plus d'informations sur les comptes de stockage Azure, consultez [Gestion des comptes de stockage](https://www.windowsazure.com/fr-fr/manage/services/storage/how-to-manage-a-storage-account/).

<div class="dev-callout"on> 
<b>Note</b> 
<p>La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.</p> </div>

La journalisation relative aux applications dans les ressources de stockage nécessitant l'utilisation d'un client de stockage pour afficher les données de journalisation, cette fonction est très utile lorsque vous prévoyez d'utiliser un service ou une application capable de lire et de traiter directement les données à partir d'une table ou du stockage d’objets blob Azure. La journalisation dans le système de fichiers génère des fichiers pouvant être téléchargés sur votre ordinateur local via FTP ou d'autres utilitaires, comme décrit plus loin dans cette section.

<div class="dev-callout"> 
	<b>Remarque</b> 
	<p><b>Les options Diagnostics d'application (système de fichiers)</b>, <b>Diagnostics d'application (stockage de table)</b>et <b>Diagnostics d'application (stockage d'objets blob)</b> peuvent être activées en même temps et chacune à sa propre configuration de niveau de journalisation. Ainsi, vous pouvez par exemple consigner les erreurs et les avertissements pour être stockés dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau détaillé de journalisation du système de fichiers une fois le code de l'application instrumenté pour résoudre un problème.</p> </div>

<div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Les diagnostics peuvent également être activés à partir d'Azure PowerShell via l'applet de commande <b>Set-AzureWebsite</b> .</p><p>Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez <a href="http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/">Utilisation d'Azure PowerShell</a>.</p></div>

<div class="dev-callout"> 
<b>Remarque</b> 
<p>La journalisation des applications s'appuie sur les informations de journalisation générées par votre application. La méthode permettant de générer ce type d'informations, ainsi que le format des informations, sont propres au langage de votre application. Pour obtenir des informations sur le langage utilisé dans le cadre de la journalisation des applications, lisez les articles suivants :</p>
<ul>
<li><b>.NET</b> - <a href="/fr-fr/develop/net/common-tasks/diagnostics-logging-and-instrumentation/">Activer la journalisation de diagnostic pour Azure Web Sites</a></li>
<li><b>Node.js</b> - <a href="/fr-fr/develop/nodejs/how-to-guides/Debug-Website/">Débogage d'une application Node.js dans Azure Web Sites</a></li>
</ul>
<p>La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.</p>
</div>

####Diagnostic de site####

La section **diagnostic de site** de la page de gestion **Configurer** contrôle la journalisation effectuée par le serveur web, par exemple, la journalisation des demandes web, l'échec des pages ou le délai d'attente pour accéder à une page. Vous pouvez activer ou désactiver les options suivantes :

- **Journalisation du serveur web** : permet d'enregistrer les journaux de site au format de journal étendu W3C. La journalisation du serveur Web génère un enregistrement de toutes les demandes entrantes adressées à votre site Web, qui contient des informations telles que l'adresse IP du client, l'URI demandé, le code d'état HTTP de la réponse, ainsi que la chaîne de l'agent utilisateur du client. Vous pouvez enregistrer les journaux sur un compte de stockage Azure ou sur le système de fichiers.

 Pour enregistrer les journaux du serveur web sur un compte de stockage Azure, choisissez **Stockage**, puis **gérer le stockage** pour spécifier un compte de stockage et un conteneur d'objets blob où les journaux seront conservés. Pour plus d'informations sur les comptes de stockage Azure, consultez [Gestion des comptes de stockage](https://www.windowsazure.com/fr-fr/manage/services/storage/how-to-manage-a-storage-account/).

   Pour enregistrer les journaux du serveur web sur le système de fichiers **Système de fichiers**. Cette option active la zone **Quota** permettant de définir l'espace disque maximal alloué aux fichiers journaux. Les valeurs minimales et maximales sont respectivement 25 Mo et 100 Mo. La valeur par défaut est 35 Mo.

 Par défaut, les journaux du serveur Web ne sont jamais supprimés. Pour définir le délai au bout duquel les journaux sont automatiquement supprimés, sélectionnez **Définir la rétention** et entrez dans la zone **Période de rétention** le nombre de jours pendant lequel conserver les journaux. Ce paramètre est disponible pour les options Azure Storage et File System.

- **Messages d'erreur détaillés** : permet de consigner des informations supplémentaires sur les erreurs HTTP (codes d'état supérieurs à 400).

- **Suivi des demandes ayant échoué** : permet de capturer des informations relatives aux échecs de demandes du client, telles que les codes d'état HTTP série 400.  Cette option de suivi génère un document XML qui contient des informations sur les modules pour lesquels la demande a été transmise via le gestionnaire des services Internet, les détails renvoyés par le module et l'heure à laquelle le module a été appelé. Ces informations permettent d'isoler le composant source de la défaillance.


Une fois que le diagnostic a été activé pour un site web, cliquez sur l'icône **Enregistrer** en bas de la page de gestion **Configurer** pour appliquer les options que vous avez définies.

<div class="dev-callout"> 
<b>Important</b> 
<p>Les activités de journalisation et de suivi représentent une charge importante sur un site Web. Il est recommandé de désactiver la journalisation et le suivi une fois que vous avez pu reproduire le ou les problème(s) que vous essayez de résoudre.</p> 
</div>

###Configuration avancée###

Vous pouvez également modifier la fonction de diagnostic en ajoutant des paires clé/valeur dans la section **paramètres de l'application** de la page de gestion **Configurer** management page. Les paramètres suivants peuvent être configurés à partir des **paramètres de l'application** :

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- Emplacement où sont enregistrés les journaux de l'application ; chemin relatif à la racine du serveur Web.

- Valeur par défaut : ..\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- Taille maximale de la mémoire tampon à utiliser au moment de récupérer les journaux de l'application. Initialement, les informations sont écrites dans la mémoire tampon avant d'être transférées dans un fichier ou une ressource de stockage. Si de nouvelles informations sont écrites dans la mémoire tampon avant que les informations précédemment enregistrées soient vidées, ces dernières peuvent être perdues. Si votre application génère d'importants flux d'informations de journalisation, pensez à augmenter la taille de la mémoire tampon.

- Valeur par défaut : 10 Mo

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Taille maximale du dossier **Application** dans lequel les diagnostics d'application écrits dans des fichiers sont stockés.

- Valeur par défaut : 1 Mo

###Téléchargement des fichiers journaux d'un site web###

Les fichiers journaux peuvent être téléchargés à l'aide de FTP, Azure PowerShell ou des outils en ligne de commande Azure.

**FTP**

1. Ouvrez la page de gestion **Tableau de bord** et notez le site FTP indiqué sous **Journaux de diagnostic** et le compte indiqué sous **Utilisateur du déploiement**. Le site FTP héberge les fichiers journaux ; le compte répertorié sous Deployment User permet de vous authentifier auprès du site FTP.
2. Si vous n'avez pas déjà créé les identifiants pour le déploiement, le compte figurant sous **Utilisateur du déploiement** est indiqué comme étant **Non défini**. Dans ce cas, vous devez créer les identifiants de déploiement comme décrit dans la section Reset Deployment Credentials du Tableau de bord, car ces identifiants vous permettent de vous authentifier auprès du site FTP sur lequel sont stockés les fichiers journaux. Azure ne prend pas en charge l'authentification auprès du site FTP avec les informations d'identification Live ID.
3. Pensez à utiliser un client FTP comme [FileZilla][fzilla] pour vous connecter au site FTP. Le client FTP facilite la saisie des informations d'identification. En outre, l'affichage des dossiers d'un site FTP y est généralement plus clair que dans un navigateur.
4. Copiez les fichiers journaux du site FTP sur votre ordinateur en local.

**Azure PowerShell**

1. À partir de l'**écran d'accueil** ou du menu **Démarrer**, recherchez **Azure PowerShell**. Cliquez avec le bouton droit sur l'entrée **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	<div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Si <b>Azure PowerShell</b> n'est pas installé, consultez <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554332.aspx">Prise en main des applets de commande Azure PowerShell</a> pour des informations sur l'installation et la configuration.</p> 
	</div>

2. À l'invite Azure PowerShell, entrez la commande suivante pour télécharger les fichiers journaux :

		Save-AzureWebSiteLog -Name websitename

	Cette commande permet de télécharger les fichiers journaux du site web indiqué par **nom_site_web** et de les enregistrer dans un fichier **log.zip** dans le répertoire actif.
	
	Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

		Get-AzureWebSiteLog -Name websitename -Tail

	Cette commande affiche les informations de journalisation à l'invite Azure PowerShell à mesure qu'elles surviennent.

**Outils en ligne de commande Azure**

Ouvrez une nouvelle invite de commande, la console PowerShell, un interpréteur de commandes ou une session terminal, et exécutez la commande suivante pour télécharger les fichiers journaux :

	azure site log download websitename

Cette commande permet de télécharger les fichiers journaux du site web indiqué par **nom_site_web** et de les enregistrer dans un fichier **log.zip** dans le répertoire actif.

Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

	azure site log tail websitename

Cette commande affiche les informations de journalisation à l'invite de commande, sur la console PowerShell, un interpréteur de commandes ou une session terminal à partir de laquelle la commande est exécutée.

<div class="dev-callout"> 
<b>Remarque</b> 
<p>Si la commande <b>Azure</b> n'est pas installée, consultez <a href="http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/command-line-tools/">Utilisation des outils en ligne de commande Azure</a> pour des informations sur l'installation et la configuration.</p>
</div>

###Lecture des fichiers journaux###

Le contenu des fichiers journaux générés une fois que vous avez activé la journalisation et/ou le suivi d'un site varie en fonction du niveau de journalisation/suivi défini à la page d'administration Configurer du site Web. Voici les emplacements des fichiers journaux et les méthodes permettant de les analyser :

**Type de fichier journal : journalisation d'application**

- Emplacement : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications. Les informations consignées incluent la date et l'heure, l'ID de processus (PID) de l'application, ainsi que la valeur générée par l'instrumentation de l'application.

- Lire les fichiers avec : un éditeur ou un analyseur de texte qui sait interpréter les valeurs générées par votre application

**Type de fichier journal : suivi des demandes ayant échoué**

- Emplacement : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer. 

- Lire les fichiers avec : Internet Explorer

**Type de fichier journal : messages d'erreur détaillés**

- Emplacement : /LogFiles/DetailedErrors/. Le dossier /LogFiles/DetailedErrors/ contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite. 

- Lire les fichiers avec : un navigateur Web

Les fichiers .htm contiennent les sections suivantes :

- **Informations détaillées sur l'erreur :** inclut des informations sur l'erreur, telles que <em>Module</em>, <em>Gestionnaire</em>, <em>Code d'erreur</em>et <em>URL demandée</em>.

- **Causes les plus probables :** indique plusieurs causes d'erreur possibles.

- **Actions à tenter :** indique des solutions possibles au problème signalé par l'erreur.

- **Liens et informations supplémentaires** : fournit des informations supplémentaires sur l'erreur qui peuvent inclure des liens vers d'autres ressources, telles que les articles de la Base de connaissances Microsoft.

**Type de fichier journal : journalisation du serveur web**

- Emplacement : /LogFiles/http/RawLogs. Les informations que contiennent ces fichiers sont enregistrées au [format du journal étendu W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Les champs s-computername, s-ip et cs-version ne sont pas utilisés par Sites Web Azure.

- Lire les fichiers avec : Log Parser. Utilisé pour analyser et interroger les fichiers journaux du gestionnaire des services Internet. Log Parser 2.2 est disponible dans le Centre de téléchargement Microsoft, à l'adresse <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a>Procédure : surveillance de l'état d'un point de terminaison web

Cette fonctionnalité, disponible en mode **Standard**, vous permet de surveiller jusqu'à 2 points de terminaison à partir de 3 régions géographiques. 

Elle paramètre des tests Web à partir de différents emplacements où sont évalués les temps de réponse et de disponibilité des URL. Ce type de test lance une requête HTTP get sur l'URL Web pour déterminer les temps de réponse et de disponibilité depuis chaque emplacement. Chaque emplacement configuré exécute un test toutes les cinq minutes.

La disponibilité est contrôlée à l'aide des codes réponse HTTP et le temps de réponse est mesuré en millisecondes. Le temps d'activité est considéré comme étant à 100 % quand le temps de réponse est inférieur à 30 secondes et le code d'état HTTP inférieur à 400. Le temps d'activité est à 0 % quand le temps de réponse est supérieur à 30 secondes ou que le code d'état HTTP est supérieur à 400.

Une fois que la surveillance des points de terminaison est configurée, vous pouvez rechercher sur chaque point les détails des temps de réponse et de l'état de disponibilité pendant l'intervalle de surveillance, à partir de chaque lieu de test. Vous pouvez également configurer une règle d'alerte quand le temps de réponse des points de terminaison est trop long, par exemple. 

**Pour configurer la surveillance des points de terminaison :**

1.	Ouvrez **Sites web**. Cliquez sur le nom du site Web que vous souhaitez configurer.
2.	Cliquez sur l'onglet **Configurer**. 
3.     Accédez à la section **Surveillance** pour entrer vos paramètres de point de terminaison.
4.	Entrez le nom du point de terminaison.
5.	Entrez l'URL d'une page de votre site web que vous voulez surveiller. Par exemple, [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive). 
6.	Sélectionnez une ou plusieurs régions géographiques dans la liste.
7.	Vous pouvez aussi recommencer les étapes précédentes pour créer un second point de terminaison.
8.	Cliquez sur **Enregistrer**. Il peut s'écouler un certain temps avant que les données de surveillance des points de terminaison web soient disponibles sous les onglets **Tableau de bord** et **Surveiller**.

	Pour créer une règle de courrier électronique, procédez comme suit :

9.	À l'extrémité gauche de la barre de service, cliquez sur **Services de gestion**.
10.	Cliquez sur **Ajouter une règle** en bas.
11.	Dans **Type de service**, sélectionnez **Site web**, puis sélectionnez le site web pour lequel vous avez configuré précédemment la surveillance du point de terminaison. Cliquez sur **Suivant**.
12.	Dans **Métrique**, sélectionnez à présent les mesures supplémentaires pour le point de terminaison configuré. Par exemple : **Temps de réponse(page d'accueil/US : IL-Chicago)**. Sélectionnez la mesure Temps de réponse et tapez 3 dans le champ **Valeur du seuil** pour spécifier un seuil de 3 secondes.
13.	Sélectionnez **Envoyer un courrier électronique à l'administrateur du service et aux coadministrateurs**. Cliquez sur **Terminer**.

	Azure va maintenant surveiller activement le point de terminaison et envoyer une alerte par courrier électronique quand le temps de réponse sera supérieur à 3 secondes.


Pour en savoir plus sur la surveillance des points de terminaison d'un site Web, visionnez les vidéos suivantes :

- [Scott Guthrie présente Azure Web Sites et configure la surveillance des points de terminaison](http://www.windowsazure.com/fr-fr/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Assurer la gestion des sites Web Azure et la surveillance des points de terminaison - avec Stefan Schackow](http://www.windowsazure.com/fr-fr/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)





[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169





<!--HONumber=35.1-->
