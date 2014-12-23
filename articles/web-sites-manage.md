<properties urlDisplayName="How to manage" pageTitle="Gestion de sites web - Gestion des services Microsoft Azure" metaKeywords="Azure portal website management" description="A reference for the Portal website management pages in Microsoft Azure. Details are provided for each website management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Websites" authors="cephalin"  solutions="" writer="mwasson" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

#<a name="howtomanage"></a>Gestion des sites web via le portail de gestion Azure

Vous gérez vos sites web dans le portail Azure avec un ensemble de pages ou " onglets ". Chaque page de gestion de site web est décrite ci-dessous.

## Démarrage rapide ##
La page de gestion **Démarrage rapide** comprend les sections suivantes :

- **Obtenir les outils** : fournit des liens vers l'[Installation de WebMatrix][mswebmatrix] et le [Kit de développement logiciel (SDK) Microsoft Azure][azuresdk].
- **Publier votre application** : fournit des liens pour télécharger le profil de publication du site web, réinitialiser les informations d'identification du déploiement pour le site web, ajouter un emplacement de publication intermédiaire (déploiement) à un site non intermédiaire et en savoir plus sur la publication intermédiaire.
- **Intégrer le contrôle de code source** : configure et gère le déploiement à partir des outils de contrôle du code source ou de sites web comme TFS, CodePlex, GitHub, Dropbox, Bitbucket ou LocalGit.

## Tableau de bord ##
La page de gestion **Tableau de bord** comprend les sections suivantes :

Graphique qui résume l'utilisation du site web sous forme de mesures de certains indicateurs.

 - **Temps processeur** : mesure de l'utilisation de l'UC du site web.
 - **Données entrantes** : mesure des données reçues par le site web à partir des clients.
 - **Données sortantes** : mesure des données envoyées par le site web aux clients.
 - **Erreurs de serveur HTTP** : nombre de messages d'erreur HTTP " 5xx Erreur de serveur " envoyés.
 - **Requêtes** : nombre de requêtes clientes envoyées au site web.

**Remarque :** vous pouvez ajouter des indicateurs de performance supplémentaires dans la page de gestion **Surveiller** en choisissant **Ajouter des métriques** en bas de cette page. Pour plus d'informations, consultez la page [Surveillance des sites Web][Surveiller].

**Web Endpoint Status** : liste des points de terminaison Web configurés pour la surveillance. Si aucun point de terminaison n'a été configuré, cliquez sur **Configure Web Endpoint Monitoring** et allez à la section **Monitoring** de la page de gestion **Configurer**. Pour plus d'informations, consultez la page [Surveillance des sites Web][Surveiller].

**Autoscale Status** : en mode Standard, vous pouvez mettre automatiquement vos ressources à l'échelle pour ne pas dépenser plus que nécessaire. Pour activer la mise à l'échelle automatique, choisissez **Configure Autoscale**, ce qui vous amène sur la page **Scale**. Si votre site web est en mode Gratuit ou Partagé, vous devez le passer en mode Standard (vous pouvez le faire dans la page **Mettre à l'échelle**) avant de pouvoir configurer la mise à l'échelle automatique. **Journaux des opérations de mise à l'échelle automatique** vous dirige vers le portail **Services de gestion**, où vous pouvez voir l'historique de mise à l'échelle automatique de votre site web. La requête par défaut concerne les dernières 24 heures, mais vous pouvez modifier la requête.

**Vue d'ensemble de l'utilisation** : cette section affiche les statistiques de l'utilisation de la mémoire, du système de fichiers et de l'UC du site web.

**Ressources liées** : cette section affiche une liste de ressources, comme une base de données SQL ou MySQL ou bien un compte Microsoft Azure Storage, connectées à votre site web. Cliquez sur le nom de la ressource pour la gérer. Si vous avez une base de données MySQL, lorsque vous cliquez sur son nom, vous êtes dirigé vers la page de gestion ClearDB. Vous pouvez y voir les mesures de performances ou aller dans le tableau de bord ClearDB, où vous pouvez mettre à niveau votre base de données MySQL si nécessaire. Si aucune ressource ne figure dans la liste, cliquez sur **Gérer les ressources liées** pour aller sur la page **Ressources liées**, où vous pouvez ajouter un lien vers une ressource pour votre site web.

Une section **Aperçu rapide** qui comprend les informations résumées suivantes ainsi que des liens (en fonction de vos paramètres, il se peut que certaines options mentionnées ci-dessous ne s'affichent pas) :

 - **Afficher les modules complémentaires applicables** : ouvre la boîte de dialogue **Acheter** où vous pouvez choisir des modules complémentaires à acheter pour ajouter des fonctionnalités supplémentaires à votre site web. Il se peut que certains modules complémentaires ne soient pas disponibles dans votre région ou environnement.
 - **Afficher les chaînes de connexion** : affichez les chaînes de connexion de base de données de votre site web.
 - **Télécharger le profil de publication** : cliquez sur ce lien pour télécharger votre profil de publication pour votre site web. Le profil de publication contient vos informations d'identification (nom d'utilisateur et mot de passe) et les URL permettant le chargement de contenu vers votre site web via FTP et Git. Le fichier de profil est au format XML et il peut être affiché dans un éditeur de texte.
 - **Configurer les informations d'identification du déploiement** : cliquez pour créer un nom d'utilisateur et un mot de passe permettant le chargement de contenu vers votre site web via FTP ou Git. Vous pouvez utiliser ces informations d'identification pour effectuer une transmission de type push du contenu vers un site web de votre abonnement. (Consultez [Informations d'identification FTP]). **Remarque** : l'authentification à un hôte FTP ou référentiel Git en utilisant des informations d'identification de compte Microsoft (Live ID) n'est pas prise en charge.
 - **Redéfinir vos informations d'identification du profil de publication** : réinitialise le profil de publication pour votre site web. Les profils de publication téléchargés précédemment ne sont plus valides.
 - **Configurer le déploiement depuis le contrôle de code source** : affiche une boîte de dialogue où vous pouvez configurer une publication en continu à partir de Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket ou LocalGit.
 - **Add a new deployment slot** : pour les sites en mode Standard, utilisez cette fonctionnalité pour créer un module de transfert pour le site. Le module de transfert (site intermédiaire) vous permet de valider le contenu du site et la configuration avant de le basculer en production. Vous pouvez également utiliser la version intermédiaire du site pour ajouter progressivement des mises à jour du contenu, puis basculer le site en production lorsque les mises à jour sont terminées sur le module de transfert. (Vous ne pouvez pas ajouter d'emplacement à un site qui est déjà en préparation.)
 - **Modifier dans Visual Studio Online** : cliquez sur ce lien pour modifier votre site web directement en ligne en utilisant Visual Studio Online à partir du portail Microsoft Azure. Cette option n'apparaît pas, sauf si vous l'activez sur la page **Configurer**.
 - **Déconnecter de Dropbox** : si vous avez configuré une connexion à Dropbox à des fins de déploiement, ce lien vous permet de vous déconnecter de Dropbox.
 - **Supprimer le référentiel Git** : si vous avez configuré un référentiel Git, ce lien vous permet de le supprimer.
 - **État** : indique si le site web fonctionne.
 - **Services de gestion** : cliquez sur le lien **Journaux des opérations** pour voir les journaux d'opérations de votre site web à partir du portail des services de gestion Microsoft Azure.
 - **Adresse IP virtuelle** : affiche l'adresse IP virtuelle du site web si vous avez configuré une liaison SSL sur IP pour le site web dans la section **Liaisons SSL** de l'onglet **Configurer**. 
 - **Site URL** : spécifie l'adresse du site web accessible à tous sur Internet.
 - **Mode de calcul** : spécifie si le site web fonctionne en mode Gratuit, Partagé, De base ou Standard. Pour plus d'informations sur les modes de groupe de mise à l'échelle web , consultez la page [Mise à l'échelle d'un site web][Mise à l'échelle].
 - **Nom d'hôte FTP** : spécifie l'URL à utiliser quand vous publiez sur le site web via FTP. (Consultez [Informations d'identification FTP]).
 - **Nom d'hôte FTPS** : spécifie l'URL à utiliser quand vous publiez sur le site web via FTPS. (Consultez [Informations d'identification FTP]). 
 - **Utilisateur du déploiement/Utilisateur FTP** : indique le compte utilisé durant le déploiement du site web vers Microsoft Azure via FTP ou Git. (Consultez [Informations d'identification FTP]).
 - **Journaux de diagnostic FTP** : spécifie l'emplacement FTP des journaux de diagnostic du site web si la journalisation des diagnostics est activée sur la page de gestion **Configurer**.
 - **Journaux de diagnostic FTPS** : spécifie l'emplacement FTPS des journaux de diagnostic du site web si la journalisation des diagnostics est activée sur la page de gestion **Configurer**.
 - **Emplacement** : spécifie la région du centre de données qui héberge le site web.
 - **Nom d'abonnement** : spécifie le nom de l'abonnement auquel le site web est associé.
 - **ID d'abonnement** : spécifie l'identifiant unique (GUID) de l'abonnement auquel le site web est associé.


##Déploiements##
 Cet onglet s'affiche seulement si vous avez mis en place le déploiement à partir du contrôle de code source. La page de gestion **Déploiements** propose un résumé de tous les déploiements effectués sur le site web à l'aide de la méthode de publication de votre choix. Si la publication Git a été configurée pour le site web, mais qu'aucun déploiement n'a été effectué, la page de gestion **Déploiements** fournit des informations décrivant l'utilisation de Git pour le déploiement de votre application web vers le site web.

##Surveiller##
La page de gestion **Surveiller** propose un graphique qui affiche des informations sur l'utilisation pour le site web. Par défaut, ce graphique affiche les mêmes indicateurs de performance que sur la page **Tableau de bord** comme décrit ci-dessus dans la section Tableau de bord. Le graphique peut également être configuré pour afficher les mesures des réussites HTTP, redirections HTTP, erreurs HTTP 401, erreurs HTTP 403, erreurs HTTP 404 et erreurs HTTP 406. Pour plus d'informations sur ces indicateurs, consultez la page [Surveillance des sites web][Surveiller].

##WebJobs##
La page de gestion WebJobs vous permet de créer des tâches à la demande, planifiées ou exécutées en continu pour votre site web. Pour plus d'informations, consultez la page [Utilisation de WebJobs pour exécuter des tâches en arrière-plan dans les sites Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-create-web-jobs/).

##Configurer##
La page de gestion **Configurer** est utilisée pour définir les paramètres propres à une application.

Pour plus d'informations, consultez la page [Configuration des sites web][Configurer].


##Mise à l'échelle##
Sur la page de gestion **Mise à l'échelle**, vous pouvez spécifier le mode de groupe de mise à l'échelle web (**Gratuit**, **Partagé**, **De base** ou **Standard**). **Partagé**, **Basique** et **Standard** sont des modes qui proposent un meilleur débit et de meilleures performances. **Partagé**, **De base** et **Standard** sont des modes qui vous permettent d'augmenter le **Nombre d'instances**, c'est-à-dire le nombre de machines virtuelles utilisées par votre site web et vos autres sites web dans le même groupe de mise à l'échelle web.
 
En mode **Standard**, vous pouvez également augmenter le nombre principal et la capacité mémoire de chaque instance en modifiant la **Taille de l'instance**.  Pour une meilleure rentabilité, vous pouvez choisir l'option **Mise à l'échelle automatique** pour que Microsoft Azure alloue des ressources pour votre site web de manière dynamique. 

Pour une meilleure rentabilité, vous pouvez choisir l'option [Mise à l'échelle automatique][Mise à l'échelle].

##Ressources liées##
La page de gestion **Ressources liées** propose une liste des ressources Microsoft Azure que votre site web utilise, notamment des bases de données SQL, des bases de données MySQL et des comptes Azure Storage. Cliquez sur le nom de la ressource pour la gérer.

##Sauvegardes##
La page de gestion **Sauvegardes** vous permet de créer des sauvegardes automatisées ou manuelles de votre site web, de restaurer votre site web à un état antérieur ou de créer un site web basé sur l'une de vos sauvegardes. Pour plus d'informations, consultez les pages [Sauvegarde de Sites Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-backup/) et [Restauration d'un site web Microsoft Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-restore/).

##Icônes de la page de gestion##
Les icônes sont affichées au bas de chaque page de gestion du site web. Plusieurs de ces icônes apparaissent sur plusieurs pages et certaines sont affichées uniquement sur des pages spécifiques.  Les icônes suivantes sont affichées en bas de la page de gestion **Tableau de bord** :

- **Parcourir** : ouvre la page par défaut du site web.
- **Arrêter** : arrête le site web.
- **Redémarrer** : redémarre le site web.
- **Gérer les domaines** : mappe un domaine au site web. Non disponible pour les sites en mode de mise à l'échelle **Gratuit**.
- **Supprimer** : supprime le site web.
- **WebMatrix** : ouvre les sites web pris en charge dans WebMatrix, ce qui vous permet d'apporter des changements à ces derniers et de les republier sur Microsoft Azure.

Les icônes suivantes ne sont pas affichées en bas de la page de gestion **Tableau de bord**, mais se trouvent en bas d'autres pages de gestion pour accomplir des tâches particulières :

- **Ajouter des métriques** : en bas de la page de gestion **Surveiller**, vous permet d'ajouter des indicateurs au graphique affiché sur la page de gestion Surveiller.
- **Liaison** : en bas de la page de gestion **Ressources liées** , vous permet de créer des liens de gestion vers d'autres
-  ressources Microsoft Azure. Par exemple, si votre site web accède à une base de données SQL, vous pouvez créer un lien de gestion vers la ressource de base de données en cliquant sur **Liaison**.


## Informations d'identification FTP

Vous pouvez utiliser deux jeux d'informations d'identification FTP, les informations d'identification du *déploiement* et du *profil de publication*.  Voici les principales différences :

**Informations d'identification du déploiement**

- Elles sont associées à un compte Microsoft. 
- Elles peuvent être utilisées pour effectuer un déploiement vers n'importe quel site web, dans tous les abonnements associés au compte. 
- Vous choisissez le nom d'utilisateur/mot de passe.
- Elles sont généralement utilisées pour un déploiement Git ou FTP.

	 
**Informations d'identification du profil de publication**

- Elles sont associées à un seul site web. 
- Vous ne choisissez pas le nom d'utilisateur, ni le mot de passe.
- Elles sont généralement utilisées pour le déploiement web, mais elles peuvent également être utilisées pour FTP.


Vous pouvez utiliser les informations d'identification de votre choix. Les noms d'hôtes FTP et FTPS sont répertoriés dans le tableau de bord, sous **Aperçu rapide**.


### Utilisation des informations d'identification du déploiement

Pour configurer les informations d'identification du déploiement : 

1.	Dans le portail de gestion, accédez à la page **Tableau de bord** de votre site web.
2.	Cliquez sur **Configurer les informations d'identification du déploiement**.
3.	Dans la boîte de dialogue, entrez un nom d'utilisateur et un mot de passe.

Remarque : À l'étape 2, si vous avez déjà déployé des informations d'identification, le portail indique **Réinitialiser les informations d'identification du déploiement**. Cliquez sur cette option pour définir un nouveau mot de passe ou changer le nom d'utilisateur.

Les informations d'identification du déploiement sont associées à un compte Microsoft. Si vous changez le nom d'utilisateur ou le mot de passe, le changement s'applique à tous les sites web associés au compte. Si un abonnement Azure comporte plusieurs administrateurs, chaque personne a ses propres informations d'identification. 
Le nom d'utilisateur FTP complet est " site_web\nom_utilisateur ".  Cela est indiqué dans le portail sous **Aperçu rapide**, sous la forme **Utilisateur du déploiement/Utilisateur FTP**.


### Utilisation des informations d'identification du profil de publication

Chaque site web a ses propres informations d'identification du profil de publication. Pour afficher ces informations d'identification :

1.	Dans le portail de gestion, accédez à la page **Tableau de bord** de votre site web.
2.	Cliquez sur **Télécharger le profil de publication**.

Le fichier du profil de publication est un fichier XML. Il contient deux profils, l'un pour le déploiement web, et l'autre pour FTP.

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.azurewebsites.net:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.azurewebsites.net"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.azurewebsites.net" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

Recherchez le profil comportant <code>publishMethod="FTP"</code>.  Le nom d'utilisateur est indiqué sous <code>userName</code> et le mot de passe sous <code>userPWD</code>.

Pour réinitialiser le mot de passe, cliquez sur **Redéfinir vos informations d'identification du profil de publication**. Pour obtenir de nouvelles informations d'identification, téléchargez à nouveau le profil de publication. Les informations d'identification du profil de publication sont associées au site web. Chaque site web a son propre profil de publication.



<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:http://go.microsoft.com/fwlink/?LinkId=246928

[Configurer]: http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-configure-websites

[Surveiller]: http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-monitor-websites/

[Mise à l'échelle]: http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-scale-websites


<!-- Anchors. -->
[Informations d'identification FTP]: #ftp-credentials

