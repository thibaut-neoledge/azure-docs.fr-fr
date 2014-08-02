<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage web sites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal web site management pages in Microsoft Azure. Details are provided for each web site management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Web Sites" authors="timamm" solutions="" writer="timamm" manager="" editor="" />

Gestion des sites Web
=====================

Vous gérez vos sites Web dans le portail Azure avec un ensemble de pages ou « onglets ». Chaque page de gestion de site Web est décrite ci-dessous.

Démarrage rapide
----------------

La page de gestion **Démarrage rapide** comprend les sections suivantes :

-   **Get the tools** : fournit des liens vers l'[Installation de WebMatrix](http://go.microsoft.com/fwlink/?LinkID=226244) et le [Kit de développement logiciel (SDK) Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=246928).
-   **Publier votre application** : fournit des liens pour télécharger le profil de publication du site Web, réinitialiser les informations d'identification de déploiement pour le site Web, ajouter un emplacement de publication intermédiaire (déploiement) à un site non intermédiaire et en savoir plus sur la publication intermédiaire.
-   **Integrate source control** : configure et gère le déploiement à partir des outils de contrôle du code source ou de sites Web comme TFS, CodePlex, GitHub, Dropbox, Bitbucket ou Local-Git.

Tableau de bord
---------------

La page de gestion **Tableau de bord** comprend les sections suivantes :

- Un graphique qui résume l'utilisation du site Web sous forme de mesures de certains indicateurs.mesure de l'utilisation de l'UC du site Web
 - **Temps processeur** : mesure de l'utilisation de l'UC du site Web.
 - **Data In** : mesure des données envoyées par le site Web aux clients.
 - **Data Out** : mesure des données envoyées par le site Web aux clients.
 - **Erreurs de serveur http** : nombre de messages d'erreur HTTP « 5xx Erreur de serveur » envoyés.
 - **Requests** : nombre de requêtes clients envoyées au site Web.
 <br />**Remarque :**
vous pouvez ajouter des indicateurs de performance supplémentaires sur la page de gestion **Monitor** en choisissant **Add Metrics** en bas de cette page. Pour plus d'informations, consultez la page [Surveillance des sites Web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

-   **Web Endpoint Status** : liste des points de terminaison Web configurés pour la surveillance. Si aucun point de terminaison n'a été configuré, cliquez sur **Configure Web Endpoint Monitoring** et allez à la section **Monitoring** de la page de gestion **Configurer**. Pour plus d'informations, consultez la page [Surveillance des sites Web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

-   **Autoscale Status** : en mode Standard, vous pouvez mettre automatiquement vos ressources à l'échelle pour ne pas dépenser plus que nécessaire. Pour activer la mise à l'échelle automatique, choisissez **Configure Autoscale**, ce qui vous amène sur la page **Scale**. Si votre site Web est en mode Gratuit ou Partagé, vous devez le passer en mode Standard (vous pouvez le faire sur la page **Scale**) avant de pouvoir configurer la mise à l'échelle automatique. **Autoscale Operation Logs** vous dirige sur le portail **Management Services** où vous pouvez voir l'historique de mise à l'échelle automatique de votre site Web. La requête par défaut concerne les dernières 24 heures, mais vous pouvez modifier la requête.

-   **Usage Overview** : cette section affiche les statistiques de l'utilisation de la mémoire, du système de fichiers et de l'UC du site Web.
-   **Linked Resources** : cette section affiche une liste de ressources, comme une base de données SQL ou MySQL ou bien un compte de stockage Microsoft Azure, connectées à votre site Web. Cliquez sur le nom de la ressource pour la gérer. Si vous avez une base de données MySQL, lorsque vous cliquez sur son nom, vous êtes dirigé vers la page de gestion ClearDB. Vous pouvez y voir les mesures de performances ou aller dans le tableau de bord ClearDB, où vous pouvez mettre à niveau votre base de données MySQL si nécessaire. Si aucune ressource ne figure dans la liste, cliquez sur **Manage Linked Resources** pour aller sur la page **Linked Resources**, où vous pouvez ajouter un lien vers une ressource pour votre site Web.
-   Une section **Quick Glance** qui comprend les informations résumées suivantes ainsi que des liens (en fonction de vos paramètres, il se peut que certaines options mentionnées ci-dessous ne s'affichent pas) :
 - **View Applicable Add-ons** : ouvre la boîte de dialogue **Purchase from Store** où vous pouvez choisir des modules complémentaires à acheter fournissant des fonctionnalités supplémentaires à votre site Web. Il se peut que certains modules complémentaires ne soient pas disponibles dans votre région ou environnement.
 - **View connection strings** : affichez les chaînes de connexion de base de données de votre site Web.
 - **Télécharger le profil de publication** : cliquez sur ce lien pour télécharger votre profil de publication pour votre site Web. Le profil de publication contient vos informations d'identification (nom d'utilisateur et mot de passe) et les URL permettant le chargement de contenu vers votre site Web via FTP et Git. Le fichier de profil est au format XML et il peut être affiché dans un éditeur de texte.
 - **Set up deployment credentials** : cliquez sur cette option pour créer un nom d'utilisateur et un mot de passe permettant le chargement de contenu vers votre site Web via FTP ou Git. Après avoir créé vos informations d'identification de déploiement FTP/Git, vous pouvez les utiliser pour placer du contenu sur n'importe quel site Web de votre abonnement en utilisant FTP ou Git. Pour afficher les informations d'identification après les avoir créées, cliquez sur **Télécharger le profil de publication**. Le profil de publication à télécharger est un fichier texte au format XML et vous pouvez le consulter dans un éditeur de texte. **Remarque** : l'authentification à un hôte FTP ou référentiel Git en utilisant des informations d'identification de compte Microsoft (Live ID) n'est pas prise en charge.
 - **Reset your publish profile credentials** : réinitialise le profil de publication pour votre site Web. Les profils de publication téléchargés précédemment ne sont plus valides.
 - **Set up deployment from source control** : affiche une boîte de dialogue où vous pouvez configurer une publication en continu à partir de Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket ou LocalGit.
 - **Add a new deployment slot** : pour les sites en mode Standard, utilisez cette fonctionnalité pour créer un module de transfert pour le site. Le module de transfert (site intermédiaire) vous permet de valider le contenu du site et la configuration avant de le basculer en production. Vous pouvez également utiliser la version intermédiaire du site pour ajouter progressivement des mises à jour du contenu, puis basculer le site en production lorsque les mises à jour sont terminées sur le module de transfert. (Vous ne pouvez pas ajouter d'emplacement à un site qui est déjà en préparation.)
 - **Edit in Visual Studio Online** : cliquez sur ce lien pour modifier votre site Web directement en ligne en utilisant Visual Studio Online à partir du portail Microsoft Azure. Cette option n'apparaît pas sauf si vous l'activez sur la page **Configurer**.
 - **Disconnect from Dropbox** : si vous avez configuré une connexion à Dropbox à des fins de déploiement, ce lien vous permet de vous déconnecter de Dropbox.
 - **Delete Git repository** : si vous avez configuré un référentiel Git, ce lien vous permet de le supprimer.
 - **Status** : indique si le site Web fonctionne.
 - **Management Services** : cliquez sur le lien **Operation Logs** pour voir les journaux d'opérations pour votre site Web à partir du portail de services de gestion Microsoft Azure.
 - **Adresse IP virtuelle** : affiche l'adresse IP virtuelle du site Web si vous avez configuré une liaison SSL sur IP pour le site Web dans la section **SSL Bindings** de l'onglet **Configurer**.
 - **URL du site** : spécifie l'adresse du site Web accessible à tous sur Internet.
 - **Compute Mode** : spécifie si le site Web fonctionne en mode Gratuit, Partagé, Basique ou Standard. Pour plus d'informations sur les modes de groupe d'échelle Web , consultez la page [Mise à l’échelle d’un site Web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites).
 - **FTP Hostname** : spécifie l'URL à utiliser lorsque vous publiez sur le site Web par FTP.
 - **FTPS Hostname** : spécifie l'URL à utiliser lorsque vous publiez sur le site Web par FTPS.
 - **Deployment User / FTP User** : indique le compte utilisé lors du déploiement du site Web vers Microsoft Azure via FTP ou Git.
 - **FTP Diagnostic Logs** : spécifie l'emplacement FTP des journaux de diagnostic du site Web si la journalisation des diagnostics est activée sur la page de gestion **Configurer**.
 - **FTPS Diagnostic Logs** : spécifie l'emplacement FTPS des journaux de diagnostic du site Web si la journalisation des diagnostics est activée sur la page de gestion **Configurer**.
 - **Emplacement** : spécifie la région du centre de données qui héberge le site Web.
 - **Nom d'abonnement** : spécifie le nom de l'abonnement auquel le site Web est associé.
 - **ID d'abonnement** : spécifie l'identifiant unique (GUID) de l'abonnement auquel le site Web est associé.

Déploiements
------------

Cet onglet s'affiche seulement si vous avez mis en place le déploiement à partir du contrôle de code source. La page de gestion **Déploiements** propose un résumé de tous les déploiements effectués sur le site Web à l'aide de la méthode de publication de votre choix. Si la publication Git a été configurée pour le site Web, mais qu'aucun déploiement n'a été effectué, la page de gestion **Déploiements** fournit des informations décrivant l'utilisation de Git pour le déploiement de votre application Web vers le site Web.

Monitor
-------

La page de gestion **Monitor** propose un graphique qui affiche des informations sur l'utilisation pour le site Web. Par défaut, ce graphique affiche les mêmes indicateurs de performance que sur la page **Tableau de bord** comme décrit ci-dessus dans la section Tableau de bord. Le graphique peut également être configuré pour afficher les mesures des réussites HTTP, redirections HTTP, erreurs HTTP 401, erreurs HTTP 403, erreurs HTTP 404 et erreurs HTTP 406. Pour plus d'informations sur ces indicateurs, consultez la page [Surveillance des sites Web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

WebJobs
-------

La page de gestion WebJobs vous permet de créer des tâches sur demande, planifiées ou exécutées en continu pour votre site Web. Pour plus d'informations, consultez la page [Utilisation de la fonctionnalité WebJobs dans les sites Web Microsoft Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-create-web-jobs/).

Configurer
----------

La page de gestion **Configurer** est utilisée pour définir les paramètres propres à une application y compris :

-   **Général** : définissez la version du .NET Framework, de PHP, de Python ou de Java requise par votre application Web. Pour les sites en mode Standard ou Basique, une option permet de choisir une plate-forme 64 bits. Définissez **Mode pipeline géré** sur **Classique** seulement si vous avez des sites Web hérités fonctionnant exclusivement sur des versions plus anciennes d'IIS (**Intégré** est la valeur par défaut.) Pour permettre à votre site Web d'utiliser des applications de modèle de requête en temps réel comme le chat, vous pouvez définir **Web Sockets** sur **On**. Pour activer la modification de votre site Web directement en ligne, définissez **Edit in Visual Studio Online** sur **On**.
-   **Certificats** : téléchargez un certificat SSL pour un domaine personnalisé. Les certificats SSL peuvent être téléchargés uniquement en mode Standard ou Basique. Les certificats téléchargés sont listés ici et peuvent être assignés à n'importe quel site Web dans vos abonnement et région. Les certificats avec caractère générique (certificats avec un astérisque) sont pris en charge.
-   **Noms de domaine** : affichez ou ajoutez des noms de domaines personnalisés supplémentaires pour un site Web. Les noms de domaine personnalisés peuvent être utilisés uniquement en mode Partagé, Basique ou Standard.
-   **SSL Bindings** : les liaisons SSL pour les domaines personnalisés peuvent être utilisées uniquement en mode Basique ou Standard. Choisissez un mode SSL (**SNI**, **IP** ou **Pas de SSL**) pour un nom de domaine particulier. Si vous choisissez SNI ou IP, vous pouvez spécifier un certificat pour le domaine à partir des certificats que vous avez téléchargés.
-   **Déploiements** : cette section s'affiche seulement si vous avez activé le déploiement à partir du contrôle de code source. Utilisez ces paramètres pour configurer les déploiements.
-   **Diagnostic d'application** : définissez les options pour la collecte des informations de diagnostic pour une application Web prenant en charge la journalisation. Vous pouvez choisir de les journaliser sur le système de fichiers ou sur un compte Microsoft Azure Storage, puis choisir un niveau de journalisation pour spécifier la quantité d'informations collectées.
-   **Site Diagnostics** : définissez les options de journalisation pour la collecte d'informations de diagnostic pour votre site Web ou activez Visual Studio 2012 ou Visual Studio 2013 pour déboguer votre site Web à distance pendant un maximum de 48 heures.
-   **Monitoring** : pour les sites Web en mode Standard, testez la disponibilité des points de terminaison HTTP ou HTTPS à partir des emplacements géo-distribuées.
-   **Developer Analytics** : des analyses surveillent la performance de votre application Web. Choisissez un composant additionnel analytique à partir du Microsoft Azure Store ou choisissez un fournisseur d'analyses personnalisé comme New Relic.
-   **Paramètres d'application** : indiquez les paires nom/valeur qui sont chargées par votre application Web au démarrage. Pour les sites .NET, ces paramètres sont inclus dans la configuration .NET du site Web AppSettings au runtime, remplaçant les paramètres existants. Pour les sites Web PHP et Node, ces paramètres sont disponibles en tant que variables d’environnement au runtime.
-   **Chaînes de connexion** : affiche les chaînes de connexion aux ressources liées. Pour les sites .NET, ces chaînes de connexion sont incluses dans les paramètres de configuration .NET du site Web au runtime, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée. Pour les sites Web PHP et Node, ces paramètres sont disponibles en tant que variables d’environnement au runtime.
-   **Default Documents** : ajoutez le document par défaut de votre site Web à cette liste s'il ne s'y trouve pas déjà. Si votre site Web contient plusieurs fichiers de la liste, assurez-vous que le document par défaut de votre site Web apparaît en haut de la liste en changeant l'ordre des fichiers dans la liste.
-   **Mappages de gestionnaires** : ajoutez des scripts de processeurs personnalisés qui gèrent les requêtes pour des types de fichiers spécifiques (par exemple, \*.php).
-   **Virtual Applications and Directories** : configurez les applications et les répertoires virtuels associés à votre site Web. Vous avez également la possibilité de marquer un répertoire virtuel comme une application dans la configuration du site.

Pour plus d'informations sur la configuration d'un site Web, consultez la page [Configuration des sites Web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites).

Mise à l’échelle
----------------

Sur la page de gestion **Scale** vous pouvez spécifier le mode de groupe d'échelle Web (**Gratuit**, **Partagé**, **Basique** ou **Standard**). **Partagé**, **Basique** et **Standard** sont des modes qui proposent un meilleur débit et de meilleures performances. **Partagé**, **Basique** et **Standard** sont des modes qui vous permettent d'augmenter le **Nombre d'instances**, qui est le nombre de machines virtuelles utilisées par votre site Web et vos autres sites Web dans le même groupe d'échelle Web.

En mode **Standard**, vous pouvez également augmenter le nombre principal et la capacité mémoire de chaque instance en modifiant la **Taille de l'instance**. Pour une meilleure rentabilité, vous pouvez choisir l'option **Autoscale** pour que Microsoft Azure alloue des ressources pour votre site Web de manière dynamique.

Pour plus d'informations sur la configuration des options d'échelle, consultez la page [Mise à l’échelle d’un site Web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites).

Ressources liées
----------------

La page de gestion **Linked Resources** propose une liste de ressources Microsoft Azure que votre site Web utilise, incluant des bases de données SQL, des bases de données MySQL et les comptes de stockage Azure. Cliquez sur le nom de la ressource pour la gérer.

Sauvegarde
----------

La page de gestion **Sauvegarde** vous permet de créer des sauvegardes automatisées ou manuelles de votre site Web, de restaurer votre site Web à une étape précédente ou de créer un nouveau site Web basé sur l'une de vos sauvegardes. Pour plus d'informations, consultez les pages [Sauvegarde des sites Web Microsoft Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-backup/) et [Restauration d'un site Web Microsoft Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-restore/).

Icônes de la page de gestion
----------------------------

Les icônes sont affichées au bas de chaque page de gestion du site Web. Plusieurs de ces icônes apparaissent sur plusieurs pages et certaines sont affichées uniquement sur des pages spécifiques. Les icônes suivantes sont affichées en bas de la page de gestion **Tableau de bord** :

-   **Parcourir** : ouvre la page par défaut du site Web.
-   **Arrêter** : arrête le site Web.
-   **Redémarrer** : redémarre le site Web.
-   **Gérer les domaines** : mappe un domaine au site Web. Non disponible pour les sites en mode d'ajustement **Gratuit**.
-   **Supprimer** : supprime le site Web.
-   **WebMatrix** : ouvre les sites Web pris en charge dans WebMatrix, vous permettant d'apporter des modifications au site Web et de les publier sur le site Web sur Microsoft Azure.

Les icônes suivantes ne sont pas affichées en bas de la page de gestion **Tableau de bord**, mais se trouvent en bas d'autres pages de gestion pour accomplir des tâches particulières :

-   **Add Metrics** : en bas de la page de gestion **Monitor**, vous permet d'ajouter des indicateurs au graphique affiché sur la page de gestion Moniteur.
-   **Liaison** : en bas de la page de gestion **Linked Resources**, vous permet de créer des liens de gestion vers d'autres
-   ressources Microsoft Azure. Par exemple, si votre site Web accède à une base de données SQL, vous pouvez créer un lien de gestion vers la ressource de base de données en cliquant sur **Liaison**.

