<properties
	pageTitle="Surveiller les applications web dans Azure App Service"
	description="Découvrez comment surveiller des applications web dans le Service d’application Microsoft Azure, à l’aide du portail de gestion."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="byvinyal"/>

#<a name="howtomonitor"></a>Surveiller les applications web dans Microsoft Azure App Service

[Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) fournit des fonctionnalités d’analyse pour les plans APP Service Standard et Premium, via la page de gestion Surveiller. Cette page fournit les statistiques relatives aux performances d’un site web, comme décrit ci-dessous.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##Stratégie de rétention des métriques

>[AZURE.NOTE]La stratégie de rétention des métriques d’application varie en fonction de la granularité.

- Les métriques de granularité **Minute** sont conservées **24 heures**
- Les métriques de granularité **Hour** sont conservées **7 jours**.
- Les métriques de granularité **Day** sont conservées **30 jours**.

##<a name="websitemetrics"></a>Procédure : ajouter des métriques relatives à une application web

1. Dans la page de l’application web du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur l’onglet **Surveiller** pour afficher la page de gestion **Surveiller**. Par défaut, le graphique de la page **Monitor** affiche les mêmes mesures que celui de la page **Tableau de bord**.

2. Pour afficher des métriques supplémentaires concernant l’application web, cliquez sur l’option **Ajouter des métriques** située en bas de la page. La boîte de dialogue **Choisir des métriques** apparaît.

3. Cliquez pour sélectionner les mesures supplémentaires à afficher sur la page **Monitor**.

4. Une fois sélectionnées les métriques à ajouter à la page **Surveiller**, cochez la case située dans la partie inférieure de la page.

5. Une fois que vous avez ajouté des métriques à la page **Surveiller**, cliquez pour sélectionner/désélectionner la case à cocher de forme arrondie située en regard de chaque métrique à ajouter/supprimer dans le graphique se trouvant en haut de la page.

6. Pour supprimer des mesures de la page **Monitor**, sélectionnez la ou les mesure(s) de votre choix, puis cliquez sur l'icône **Delete Metric** au bas de la page.



##<a name="howtoreceivealerts"></a>Procédure : recevoir des notifications d’alerte et gérer des règles d’alerte dans Azure

Lorsque l’application web est en mode **Standard**, vous pouvez recevoir des alertes basées sur les métriques de surveillance de cette application web. La fonction d'alerte requiert le paramétrage préalable de la surveillance d'un point de terminaison Web, à effectuer dans la section **Monitoring** de la page **Configure**. Vous pouvez également choisir de recevoir un courrier électronique lorsqu'une mesure de votre choix atteint une valeur que vous spécifiez. Pour plus d’informations, consultez la page [Réception de notifications d’alerte et gestion des règles d’alerte dans Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##<a name="howtoviewusage"></a>Procédure : afficher les quotas d’utilisation d’une application web

Les applications web peuvent être configurées pour s’exécuter en mode **Partagé** ou **Standard** dans la page de gestion **Mettre à l’échelle** de l’application web du [portail Classic](https://manage.windowsazure.com). Chaque abonnement à Microsoft Azure donne accès à un pool de ressources fourni dans le but d’exécuter jusqu’à 100 applications web par région en mode **Partagé**. Le pool de ressources disponibles à cette fin pour chaque abonnement à l’application web est partagé par d’autres applications web situées dans la même région géographique et configurées pour s’exécuter en mode **Partagé**. Ces ressources étant partagées pour pouvoir être utilisées par d’autres applications web, leur exploitation par l’ensemble des abonnements est limitée. Les limites appliquées à l’utilisation de ces ressources par un abonnement sont exprimées sous la forme de quotas d’utilisation, répertoriés dans la section de présentation de l’utilisation, sur la page de gestion **Tableau de bord** de chaque application web.

>[AZURE.NOTE]Lorsqu’une application web est configurée pour s’exécuter en mode **Standard**, elle se voit allouer des ressources dédiées équivalentes aux valeurs **Petit** (par défaut), **Moyen** ou **Grand** associées aux tailles de machine virtuelle figurant dans le tableau situé sur la page [Tailles de machines virtuelles et de services cloud pour Microsoft Azure][vmsizes]. Aucune limite n’est fixée concernant les ressources qu’un abonnement peut utiliser pour exécuter des applications web en mode **Standard**. Toutefois, vous ne pouvez pas créer plus de 500 applications web en mode **Standard**, pour chaque région.

### Procédure : afficher les quotas d’utilisation des applications web configurées en mode Partagé ###
Pour déterminer dans quelle mesure les quotas d’utilisation des ressources sont affectés par une application web, procédez comme suit :

1. Ouvrez la page de gestion **Tableau de bord** de l’application web du [portail Classic](https://manage.windowsazure.com).
2. La section **Présentation de l’utilisation** affiche les quotas d’utilisation de votre plan [App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Il s’agit d’un sous-ensemble des éléments suivants :
	-	**Données sorties**, **Temps processeur** et **Mémoire** : lorsque le quota est dépassé, Microsoft Azure arrête l’application web pendant le reste de l’intervalle de quota en cours. Microsoft Azure démarre l’application web au début de l’intervalle de quota défini suivant.
	-	**Stockage de système de fichiers** : une fois ce quota atteint, l’espace de stockage du système de fichiers reste accessible pour les opérations de lecture, mais les opérations d’écriture (y compris celles que requiert l’activité normale de l’application web) sont bloquées. Les opérations d’écriture sont à nouveau exécutées lorsque l’utilisation des fichiers est restreinte ou que l’application web est déplacée vers un plan App Service associé à un quota plus important.
	-	**Ressources liées** : les quotas des ressources liées à une application web, comme les bases de données ou le stockage, sont également affichés ici.

	Certains quotas peuvent être appliqués par plan d’hébergement web, tandis que d’autres sont appliqués par site. Pour plus d’informations sur les quotas d’utilisation de chaque plan d’hébergement web, consultez [Limites de sites web](azure-subscription-service-limits.md#websiteslimits).

##<a name="resourceusage"></a> Procédure : éviter tout dépassement des quotas

Les quotas ne sont pas liés aux performances ou au coût : ils sont un moyen pour Microsoft Azure de gérer l’utilisation des ressources dans un environnement multi-locataire, en empêchant les locataires de trop utiliser les utilisations partagées. Le dépassement des quotas entraîne un temps d’arrêt ou une limitation de certaines fonctionnalités de votre application web. C’est pourquoi vous devez envisager ce qui suit pour assurer le fonctionnement de l’application quand les quotas arrivent à saturation :

- Déplacez votre ou vos sites web vers un plan App Service de niveau supérieur pour bénéficier d’un quota plus élevé. Par exemple, le seul quota associé aux plans **De base** et **Standard** est « Stockage de système de fichiers ».
- Comme le nombre d’instances d’une application web augmente, il est de plus en plus probable que l’une d’elles dépasse le quota de ressources partagées. En cas de besoin, envisagez de remettre à l’échelle des instances supplémentaires d’une application web lorsque les quotas de ressources partagées sont dépassés.

##<a name="howtoconfigdiagnostics"></a>Procédure : configurer les diagnostics et télécharger les journaux d’une application web

Les diagnostics sont activés sous l’onglet **Configurer** de l’application web du [portail Classic](https://manage.windowsazure.com). Il existe deux types de diagnostics : **diagnostic d’application** et **diagnostics de site**.

#### Diagnostic d'application ####

La section **Diagnostic d'application** de la page d'administration **Configure** contrôle la journalisation des informations générées par l'application, ce qui est utile pour consigner des événements liés à une application. Par exemple, en cas d'erreur liée à votre application, vous pouvez présenter un message d'erreur simplifié à l'utilisateur tout en renseignant de manière plus détaillée les informations du journal destiné à une analyse ultérieure plus approfondie.

Vous pouvez activer ou désactiver les diagnostics d'application suivants :

- **Application Logging (File System)** : active la journalisation des informations générées par l'application. Le champ **Logging Level** détermine si les données de type erreur, avertissement ou information sont consignées. Vous pouvez également sélectionner le niveau détaillé, qui permet de consigner l'ensemble des informations générées par l'application.

	Les journaux générés à l’aide de ce paramètre sont stockés dans le système de fichiers de votre application web. Pour les télécharger, suivez la procédure détaillée dans la section **Téléchargement des fichiers journaux d’une application web** ci-dessous.

- **Application Logging (Table Storage)** : active la journalisation des informations générées par l'application (comme avec l'option Application Logging (File System)). Toutefois, les informations de journalisation sont stockées dans un compte de stockage Azure d'une table.

	Pour spécifier le compte de stockage Azure et la table, choisissez **On**, sélectionnez **Logging Level**, puis choisissez **Manage Table Storage**. Spécifiez le compte de stockage et la table à utiliser, ou créez une nouvelle table.

	Vous pouvez accéder aux informations de journalisation stockées dans la table à l'aide d'un client de stockage Azure.

- **Application Logging (Blob Storage)** : active la journalisation des informations générées par l'application (comme avec l'option Application Logging (Table Storage)). Toutefois, les informations de journalisation sont stockées dans un objet blob d'un compte de stockage Azure.

	Pour spécifier le compte de stockage Azure et l'objet blob, choisissez **On**, sélectionnez **Logging Level**, puis choisissez **Manage Blob Storage**. Spécifiez le compte de stockage, le conteneur d'objets blob et le nom de l'objet blob à utiliser, ou créez un nouveau conteneur et un objet blob.

Pour plus d’informations sur les comptes de stockage Azure, consultez la page [Gestion des comptes de stockage](/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE]La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.

La journalisation relative aux applications dans les ressources de stockage nécessitant l'utilisation d'un client de stockage pour afficher les données de journalisation, cette fonction est très utile lorsque vous prévoyez d'utiliser un service ou une application capable de lire et de traiter directement les données à partir d'une table ou du stockage d’objets blob Azure. La journalisation dans le système de fichiers génère des fichiers pouvant être téléchargés sur votre ordinateur local via FTP ou d'autres utilitaires, comme décrit plus loin dans cette section.

Les options **Diagnostic d'application (système de fichiers)**, **Diagnostic d'application (stockage de table)** et **Diagnostic d'application (stockage d’objets blob)** peuvent être activées simultanément et être associées à des niveaux de journalisation spécifiques. Ainsi, vous pouvez par exemple consigner les erreurs et les avertissements pour être stockés dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau détaillé de journalisation du système de fichiers une fois le code de l'application instrumenté pour résoudre un problème.

Les diagnostics peuvent également être activés à partir du module Azure PowerShell via la cmdlet **Set-AzureWebsite**. Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d'Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE]La journalisation des applications s'appuie sur les informations de journalisation générées par votre application. La méthode permettant de générer ce type d’informations et le format de celles-ci sont propres au langage de votre application. Pour obtenir des informations sur le langage utilisé dans le cadre de la journalisation des applications, lisez les articles suivants :
>
> - **.NET** - [Dépanner une application web dans Azure App Service à l’aide de Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
> - **Node.js** - [Débogage d'une application Node.js dans Sites Web Azure](web-sites-nodejs-debug.md)
>
> La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.

#### Diagnostic de site ####

La section **Site diagnostics** de la page d'administration **Configure** contrôle la journalisation effectuée par le serveur Web, qui porte notamment sur les demandes Web, ainsi que sur les échecs ou les temps d'accès aux pages. Vous pouvez activer ou désactiver les options suivantes :

- **Journalisation du serveur Web** : permet d’activer la journalisation du serveur web pour enregistrer les journaux d’applications web au format de journal étendu W3C. La journalisation du serveur web génère un enregistrement de toutes les demandes entrantes adressées à votre application web, qui contient des informations telles que l’adresse IP du client, l’URI demandé, le code d’état HTTP de la réponse, ainsi que la chaîne de l’agent utilisateur du client. Vous pouvez enregistrer les journaux sur un compte de stockage Azure ou sur le système de fichiers.

 Pour enregistrer les journaux du serveur Web sur un compte de stockage Azure, choisissez **Stockage**, puis **manage storage** pour spécifier un compte de stockage et un conteneur d'objet blob Azure destiné à conserver les journaux. Pour plus d’informations sur les comptes de stockage Azure, consultez la page [Gestion des comptes de stockage](/manage/services/storage/how-to-manage-a-storage-account/).

   Pour enregistrer les journaux du serveur Web sur le système de fichiers, choisissez **File System**. Cela active la zone **Quota** permettant de définir l'espace disque maximal alloué aux fichiers journaux. Les valeurs minimales et maximales sont respectivement 25 Mo et 100 Mo. La valeur par défaut est 35 Mo.

 Par défaut, les journaux du serveur Web ne sont jamais supprimés. Pour définir un délai après lequel les journaux sont automatiquement supprimés, sélectionnez **Set Retention** et entrez le nombre de jours de conservation des journaux dans la zone **Retention Period**. Ce paramètre est disponible pour les options Azure Storage et File System.

- **Detailed Error Messages** : permet de consigner des informations supplémentaires sur les erreurs HTTP (codes d'état supérieurs à 400).

- **Failed Request Tracing** : permet de capturer des informations relatives aux échecs de demandes du client, telles que les codes d'état HTTP série 400. Cette option de suivi génère un document XML qui contient des informations sur les modules pour lesquels la demande a été transmise via le gestionnaire des services Internet, les détails renvoyés par le module et l'heure à laquelle le module a été appelé. Ces informations permettent d'isoler le composant source de la défaillance.


Une fois la fonction de diagnostic activée pour une application web, cliquez sur l’icône **Enregistrer** en bas de la page de gestion **Configurer** pour appliquer les options que vous avez définies.

> [AZURE.IMPORTANT]Les fonctionnalités Messages d’erreur détaillés et Suivi des demandes ayant échoué génèrent des demandes importantes sur une application web. Nous recommandons de désactiver ces fonctionnalités une fois que vous avez reproduit le ou les problèmes que vous devez résoudre.

### Configuration avancée ###

Vous pouvez également modifier la fonction de diagnostic en ajoutant des paires clé/valeur dans la section **app settings** de la page d'administration **Configure**. Les paramètres suivants peuvent être configurés à partir de **app settings** :

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

- Emplacement où sont enregistrés les journaux de l'application ; chemin relatif à la racine du serveur Web.

- Valeur par défaut : ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

- Taille maximale de la mémoire tampon à utiliser au moment de récupérer les journaux de l'application. Initialement, les informations sont écrites dans la mémoire tampon avant d'être transférées dans un fichier ou une ressource de stockage. Si de nouvelles informations sont écrites dans la mémoire tampon avant que les informations précédemment enregistrées soient vidées, ces dernières peuvent être perdues. Si votre application génère d'importants flux d'informations de journalisation, pensez à augmenter la taille de la mémoire tampon.

- Valeur par défaut : 10 Mo

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Taille maximale du dossier **Application** dans lequel les diagnostics d'application écrits dans des fichiers sont stockés.

- Valeur par défaut : 1 Mo

###Téléchargement des fichiers journaux d’une application web

Les fichiers journaux peuvent être téléchargés à l’aide de FTP, d’Azure PowerShell ou de l’interface de ligne de commande Azure.

**FTP**

1. Ouvrez la page de gestion **Tableau de bord** de l’application web du [portail Classic](https://manage.windowsazure.com), puis notez le site FTP figurant sous **Fichiers journaux de diagnostic** et le compte répertorié sous **Utilisateur du déploiement**. Le site FTP héberge les fichiers journaux ; le compte répertorié sous Deployment User permet de vous authentifier auprès du site FTP.
2. Si vous n'avez pas déjà créé les identifiants pour le déploiement, le compte figurant sous **Deployment User** est associé à **Not set**. Dans ce cas, vous devez créer les identifiants de déploiement comme décrit dans la section Reset Deployment Credentials du Tableau de bord, car ces identifiants vous permettent de vous authentifier auprès du site FTP sur lequel sont stockés les fichiers journaux. Azure ne prend pas en charge l'authentification auprès du site FTP avec les informations d'identification Live ID.
3. Pensez à utiliser un client FTP comme [FileZilla][fzilla] pour vous connecter au site FTP. Le client FTP facilite la saisie des informations d'identification. En outre, l'affichage des dossiers d'un site FTP y est généralement plus clair que dans un navigateur.
4. Copiez les fichiers journaux du site FTP sur votre ordinateur en local.

**Azure PowerShell**

1. À partir de l'**écran d'accueil** ou du **menu Démarrer**, recherchez **Azure PowerShell**. Cliquez avec le bouton droit sur l'entrée **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	> [AZURE.NOTE]Si **Azure PowerShell** n'est pas installé, consultez la page [Mise en route des cmdlets Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) pour plus d'informations sur l'installation et la configuration.

2. À l'invite Azure PowerShell, entrez la commande suivante pour télécharger les fichiers journaux :

		Save-AzureWebSiteLog -Name webappname

	Cette commande permet de télécharger les fichiers journaux de l’application web spécifiée par **webappname** et de les enregistrer dans le fichier **log.zip** du répertoire en cours.

	Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

		Get-AzureWebSiteLog -Name webappname -Tail

	Cette commande affiche les informations de journalisation à l’invite Azure PowerShell à mesure qu’elles surviennent.

**Interface de ligne de commande Azure**

Ouvrez une nouvelle invite de commande, la console PowerShell, un interpréteur de commandes ou une session terminal, et exécutez la commande suivante pour télécharger les fichiers journaux :

	azure site log download webappname

Cette commande permet de télécharger les fichiers journaux de l’application web spécifiée par **webappname** et de les enregistrer dans le fichier **log.zip** du répertoire en cours.

Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

	azure site log tail webappname

Cette commande affiche les informations de journalisation à l’invite de commande, sur la console PowerShell, un interpréteur de commandes ou une session terminal à partir de laquelle la commande est exécutée.

> [AZURE.NOTE]Si la commande **azure** n’est pas installée, consultez la page [Utilisation de la ligne de commande Azure](../virtual-machines-command-line-tools.md) pour en savoir plus sur l’installation et la configuration de cette commande.

### Lecture des fichiers journaux ###

Le contenu des fichiers journaux générés une fois que vous avez activé la journalisation et/ou le suivi d’une application web varie en fonction du niveau de journalisation/suivi défini sur la page de gestion Configurer de l’application web. Voici les emplacements des fichiers journaux et les méthodes permettant de les analyser :

**Type de fichier journal : Journal des applications**

- Emplacement : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications. Les informations consignées incluent la date et l'heure, l'ID de processus (PID) de l'application, ainsi que la valeur générée par l'instrumentation de l'application.

- Lire les fichiers avec : un éditeur ou un analyseur de texte capable d’interpréter les valeurs générées par votre application

**Type de fichier journal : Suivi des demandes ayant échoué**

- Emplacement : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.

- Lire les fichiers avec : Internet Explorer

**Type de fichier journal : Messages d’erreur détaillés**

- Emplacement : /LogFiles/DetailedErrors/. Le dossier /LogFiles/DetailedErrors/ contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite.

- Lire les fichiers avec : navigateur Web

Les fichiers .htm contiennent les sections suivantes :

- **Informations détaillées sur l’erreur** : inclut des informations sur l’erreur, par exemple le <em>module</em>, le <em>gestionnaire</em>, le <em>code d’erreur</em> et l’<em>URL demandée</em>.

- **Causes les plus probables** : répertorie plusieurs causes possibles de l’erreur.

- **Actions à tenter** : répertorie les solutions possibles au problème signalé par l’erreur.

- **Liens et autres informations** : fournit des informations récapitulatives supplémentaires sur l’erreur, qui peuvent inclure des liens vers d’autres ressources, telles que des articles de la Base de connaissances Microsoft.

**Type de fichier journal : Journalisation du serveur Web**

- Emplacement : /LogFiles/http/RawLogs. Les informations que contiennent ces fichiers sont enregistrées au [format du journal étendu W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Les champs s-computername, s-ip et cs-version ne sont pas utilisés par les applications web Microsoft Azure.

- Lire les fichiers avec : Log Parser. Utilisé pour analyser et interroger les fichiers journaux du gestionnaire des services Internet. Log Parser 2.2 est disponible sur le Centre de téléchargement Microsoft, à l’adresse <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a>Procédure : surveiller l’état d’un point de terminaison Web

Cette fonction, disponible en mode **Standard**, vous permet de surveiller jusqu'à 2 points de terminaison à partir de 3 régions géographiques.

Elle paramètre des tests Web à partir de différents emplacements où sont évalués les temps de réponse et de disponibilité des URL. Ce type de test lance une requête HTTP get sur l'URL Web pour déterminer les temps de réponse et de disponibilité depuis chaque emplacement. Chaque emplacement configuré exécute un test toutes les cinq minutes.

La disponibilité est contrôlée à l'aide des codes réponse HTTP et le temps de réponse est mesuré en millisecondes. Le temps d’activité est considéré comme étant à 100 % quand le temps de réponse est inférieur à 30 secondes et le code d’état HTTP inférieur à 400. Le temps d’activité est à 0 % quand le temps de réponse est supérieur à 30 secondes ou que le code d’état HTTP est supérieur à 400.

Une fois que la surveillance des points de terminaison est configurée, vous pouvez rechercher sur chaque point les détails des temps de réponse et de l'état de disponibilité pendant l'intervalle de surveillance, à partir de chaque lieu de test. Vous pouvez également configurer une règle d’alerte quand le temps de réponse des points de terminaison est trop long, par exemple.

**Pour configurer la surveillance des points de terminaison, procédez comme suit :**

1.	Ouvrez **Web Apps**. Cliquez sur le nom de l’application web que vous souhaitez configurer.
2.	Cliquez sur l’onglet **Configurer**.
3.     Accédez à la section **Monitoring** pour entrer vos paramètres de point de terminaison.
4.	Entrez le nom du point de terminaison.
5.	Entrez l’URL d’une partie de l’application web que vous souhaitez surveiller. Par exemple, [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive).
6.	Sélectionnez une ou plusieurs régions géographiques dans la liste.
7.	Vous pouvez aussi recommencer les étapes précédentes pour créer un second point de terminaison.
8.	Cliquez sur **Save**. Il peut s'écouler un certain temps avant que les données de surveillance des points de terminaison Web soient disponibles sous les onglets **Tableau de bord** et **Monitor**.

	Pour créer une règle de courrier électronique, procédez comme suit :

9.	Dans la barre de services située à l’extrême gauche de la page, cliquez sur **Services de gestion**.
10.	Cliquez sur **Ajouter une règle** en bas de la page.
11.	Dans le champ **Type de service**, sélectionnez **Application web**, puis l’application web pour laquelle vous avez configuré la fonction de surveillance de point de terminaison. Cliquez sur **Next**.
12.	Dans **Métrique**, vous pouvez maintenant sélectionner les métriques supplémentaires pour le point de terminaison que vous avez configuré. Par exemple : **Temps de réponse (homepage/US: IL-Chicago)**. Sélectionnez la métrique Temps de réponse et saisissez 3 dans le champ **Valeur de seuil**, afin de spécifier un seuil de 3 secondes.
13.	Sélectionnez **Envoyez un courrier électronique à l’administrateur du service et aux coadministrateurs**. Cliquez sur **Terminé**.

	Azure va maintenant surveiller activement le point de terminaison et envoyer une alerte par courrier électronique quand le temps de réponse sera supérieur à 3 secondes.

Pour en savoir plus sur la surveillance du point de terminaison d’une application web, consultez les vidéos suivantes :

- [Scott Guthrie présente Sites Web Azure et paramètre la surveillance des points de terminaison](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Assurer la gestion des sites Web Azure et la surveillance des points de terminaison - avec Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page : [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre le portail Azure et le portail Azure en version préliminaire, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[fzilla]: http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]: http://go.microsoft.com/fwlink/?LinkID=309169
 

<!---HONumber=AcomDC_1203_2015-->