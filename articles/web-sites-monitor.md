<properties linkid="manage-services-how-to-monitor-websites" urlDisplayName="How to monitor" pageTitle="How to monitor web sites - Azure service management" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Learn how to monitor Azure web sites by using the Monitor page in the Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Web Sites" authors="" solutions="" manager="" editor="" />

Surveillance de sites Web
=========================

Les sites Web proposent une fonctionnalité de surveillance via une page d'administration dédiée. Cette page fournit les statistiques de performances d'un site Web, comme décrit ci-dessous.

Sommaire
--------

-   [Ajout des mesures d'un site Web](#websitemetrics)
-   [Réception d'alertes générées à partir des mesures d'un site Web](#howtoreceivealerts)
-   [Affichage des quotas d'utilisation d'un site Web](#howtoviewusage)
-   [Réduction de l'utilisation des ressources](#resourceusage)
-   [Scénario dans lequel un quota d'utilisation des ressources est dépassé](#exceeded)
-   [Configuration des diagnostics et téléchargement des journaux d'un site Web](#howtoconfigdiagnostics)
-   [Surveillance de l'état d'un point de terminaison Web](#webendpointstatus)

Ajout des mesures d'un site Web
-------------------------------

1.  Dans le [portail de gestion Azure](http://manage.windowsazure.com/), depuis les pages d'administration du site Web, cliquez sur l'onglet **Monitor** pour afficher la page d'administration **Monitor**. Par défaut, le graphique de la page **Monitor** affiche les mêmes mesures que celui de la page **Tableau de bord**.

2.  Pour afficher des mesures supplémentaires concernant le site Web, cliquez sur **Add Metrics** au bas de la page. La boîte de dialogue **Choose Metrics** apparaît.

3.  Cliquez pour sélectionner les mesures supplémentaires à afficher sur la page **Monitor**.

4.  Après avoir sélectionné les mesures à ajouter sur la page **Monitor**, cliquez sur **Ok**.

5.  Une fois que les mesures ont été ajoutées sur la page **Monitor**, cliquez pour activer/désactiver la case d'option en regard de chaque mesure à ajouter/supprimer du graphique en haut de la page.

6.  Pour supprimer des mesures de la page **Monitor**, sélectionnez la ou les mesure(s) de votre choix, puis cliquez sur l'icône **Delete Metric** au bas de la page.

La liste suivante présente les mesures qui peuvent apparaître dans le graphique de la page **Monitor** :

-   **CPUTime** : utilisation du processeur par le site Web.
-   **Requests** : nombre de demandes du client adressées au site Web.
-   **Data Out** : mesure des données envoyées par le site Web aux clients.
-   **Data In** : mesure des données reçues par le site Web à partir des clients.
-   **Http Client Errors** : nombre de messages Http de type « Erreur sur le client 4xx » envoyés.
-   **Http Server Errors** : nombre de messages Http de type « Erreur sur le serveur 5xx » envoyés.
-   **Http Successes** : nombre de messages Http de type « Réussite 2xx » envoyés.
-   **Http Redirects** : nombre de messages Http de type « Redirection 3xx » envoyés.
-   **Http 401 errors** : nombre de messages de type Http « 401 non autorisé » envoyés.
-   **Http 403 errors** : nombre de messages Http de type « 403 interdit » envoyés.
-   **Http 404 errors** : nombre de messages Http de type « 404 introuvable » envoyés.
-   **Http 406 errors** : nombre de messages Http de type « 406 inacceptable » envoyés.

Réception d'alertes générées à partir des mesures d'un site Web
---------------------------------------------------------------

En mode **Standard** du site Web, vous pouvez recevoir des alertes en fonction des indicateurs de surveillance de votre site Web. La fonction d'alerte requiert le paramétrage préalable de la surveillance d'un point de terminaison Web, à effectuer dans la section **Monitoring** de la page **Configure**. Sur la page **Paramètres** du portail de gestion Azure, vous pouvez alors créer une règle pour déclencher une alerte lorsque la mesure de votre choix atteint une valeur définie. Vous pouvez également paramétrer l'envoi d'un courrier électronique lorsque l'alerte est déclenchée. Pour plus d'informations, consultez la page [Réception de notifications d'alerte et gestion des règles d'alerte dans Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

Affichage des quotas d'utilisation d'un site Web
------------------------------------------------

Les sites Web peuvent être configurés pour s'exécuter en mode **Partagé** ou **Standard** à partir de la page d'administration **Scale** du site Web. Chaque abonnement Azure donne accès à une réserve de ressources dans le but d'exécuter jusqu'à 100 sites Web par région en mode **Partagé**. La réserve de ressources disponibles pour chaque abonnement au site Web dans ce but est partagée par d'autres sites Web de la même région géographique qui sont configurés pour s'exécuter en mode **Partagé**. Ces ressources étant partagées pour utilisation par d'autres sites Web, tous les abonnements sont limités dans leur utilisation. Les limites appliquées à l'utilisation de ces ressources par abonnement sont exprimées sous forme de quotas d'utilisation, répertoriés dans la section de la liste des utilisations de la page d'administration **Tableau de bord** de chaque site Web.

**Remarque**
 Lorsqu'un site Web est configuré pour s'exécuter en mode **Standard**, le volume de ressources qui lui est dédié est équivalent aux tailles **Petite** (par défaut), **Moyenne** ou **Grande** de machines virtuelles répertoriées dans la table de la page [Tailles de machines virtuelles et services cloud pour Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=309169). Aucune limite n'est fixée concernant les ressources qu'un abonnement peut utiliser pour exécuter des sites Web en mode **Standard**. Toutefois, le nombre de sites Web en mode **Standard** pouvant être créé par région est limité à 500.

### Affichage des quotas d'utilisation de sites Web configurés en mode Partagé

Pour déterminer dans quelle mesure les quotas d'utilisation des ressources sont affectés par un site Web, procédez comme suit :

1.  Ouvrez la page d'administration **Tableau de bord** du site Web.
2.  Sous la section **usage overview**, sont affichés les quotas d'utilisation pour les paramètres **Data Out**, **Temps processeur** et **Stockage de système de fichiers**. La barre verte affichée pour chaque ressource indique le niveau d'utilisation du quota des ressources d'un abonnement par le site Web courant ; la barre grise indique quant à elle le niveau d'utilisation de ce même quota par les autres sites Web en mode partagé associés à votre abonnement au site Web.

Les quotas d'utilisation des ressources empêchent de surexploiter les ressources suivantes :

-   **Data Out** : volume de données envoyées par les sites Web s'exécutant en mode **Partagé** à leurs clients pendant un intervalle de temps défini pour le quota (24 heures).
-   **Temps processeur** : temps processeur utilisé par les sites Web s'exécutant en mode **Partagé** pendant un intervalle de temps défini pour le quota.
-   **Stockage de système de fichiers** : volume de stockage du système de fichiers utilisé par les sites Web s'exécutant en mode **Partagé**.

En cas de dépassement des quotas d'utilisation d'un abonnement, Azure prend des mesures pour arrêter la surexploitation des ressources. La plateforme empêche ainsi tout abonné d'épuiser les ressources au détriment des autres abonnés.

Réduction de l'utilisation des ressources
-----------------------------------------

Azure calculant les quotas d'utilisation des ressources en mesurant le niveau des ressources utilisées par les sites Web en mode partagé pour un intervalle de 24 heures, tenez compte des conditions suivantes :

-   Plus le nombre de sites Web configuré pour s'exécuter en mode partagé est important, plus la probabilité de dépasser les quotas d'utilisation des ressources en mode partagé est grande. Pensez ainsi à réduire le nombre de sites configurés pour s'exécuter en mode partagé si les quotas sont dépassés.
-   De même, plus le nombre d'instances d'un site Web s'exécutant en mode partagé augmente, plus la probabilité de dépasser les quotas d'utilisation des ressources en mode partagé est grande. En conséquence, il est conseillé de réduire le nombre d'instances des sites Web en mode partagé si les quotas d'utilisation des ressources sont dépassés.

Scénario dans lequel un quota d'utilisation des ressources est dépassé
----------------------------------------------------------------------

Azure prend les mesures suivantes si les quotas d'utilisation des ressources sont dépassés pendant un intervalle de temps défini (24 heures) :

-   **Data Out** : lorsque ce quota est dépassé, Azure arrête tous les sites Web associés à un abonnement qui sont configurés pour s'exécuter en mode **Partagé** pendant le reste de l'intervalle de temps en cours. Azure démarre les sites Web au début du prochain intervalle défini.

-   **Temps processeur** : lorsque ce quota est dépassé, Azure arrête tous les sites Web associés à un abonnement qui sont configurés pour s'exécuter en mode **Partagé** pendant le reste de l'intervalle de temps en cours. Azure démarre les sites Web au début du prochain intervalle défini.

-   **Stockage de système de fichiers** : Azure empêche le déploiement de tout site Web associé à un abonnement qui est configuré pour s'exécuter en mode Partagé si l'opération entraîne le dépassement du quota d'utilisation des ressources de stockage du système de fichiers. Lorsque les ressources utilisées atteignent la taille maximale autorisée par le quota, elles restent accessibles en lecture, mais toutes les opérations d'écriture, y compris celles requises dans le cadre de l'exploitation normale du site Web, sont bloquées. Si tel est le cas, vous pouvez configurer un ou plusieurs sites Web s'exécutant en mode Partagé pour s'exécuter en mode Standard, ou réduire l'utilisation des ressources de stockage du système de fichiers sous le quota d'utilisation correspondant.

Configuration des diagnostics et téléchargement des journaux d'un site Web
--------------------------------------------------------------------------

Les diagnostics sont activés sur la page d'administration **Configure** du site Web. Deux types de diagnostics sont disponibles : le **diagnostic d'application** et le **diagnostic de site**.

#### Diagnostic d'application

La section **Diagnostic d'application** de la page d'administration **Configure** contrôle la journalisation des informations générées par l'application, ce qui est utile pour consigner des événements liés à une application. Par exemple, en cas d'erreur liée à votre application, vous pouvez présenter un message d'erreur simplifié à l'utilisateur tout en renseignant de manière plus détaillée les informations du journal destiné à une analyse ultérieure plus approfondie.

Vous pouvez activer ou désactiver les diagnostics d'application suivants :

-   **Application Logging (File System)** : active la journalisation des informations générées par l'application. Le champ **Logging Level** détermine si les données de type erreur, avertissement ou information sont consignées. Vous pouvez également sélectionner le niveau détaillé, qui permet de consigner l'ensemble des informations générées par l'application.

    Les journaux générés à l'aide de ce paramètre sont stockés dans le système de fichiers de votre site Web. Pour les télécharger, suivez la procédure détaillée dans la section **Téléchargement des fichiers journaux d'un site Web** ci-après.

-   **Application Logging (Table Storage)** : active la journalisation des informations générées par l'application (comme avec l'option Application Logging (File System)). Toutefois, les informations de journalisation sont stockées dans un compte de stockage Azure d'une table.

    Pour spécifier le compte de stockage Azure et la table, choisissez **On**, sélectionnez **Logging Level**, puis choisissez **Manage Table Storage**. Spécifiez le compte de stockage et la table à utiliser, ou créez une nouvelle table.

    Vous pouvez accéder aux informations de journalisation stockées dans la table à l'aide d'un client de stockage Azure.

-   **Application Logging (Blob Storage)** : active la journalisation des informations générées par l'application (comme avec l'option Application Logging (Table Storage)). Toutefois, les informations de journalisation sont stockées dans un objet blob d'un compte de stockage Azure.

    Pour spécifier le compte de stockage Azure et l'objet blob, choisissez **On**, sélectionnez **Logging Level**, puis choisissez **Manage Blob Storage**. Spécifiez le compte de stockage, le conteneur d'objets blob et le nom de l'objet blob à utiliser, ou créez un nouveau conteneur et un objet blob.

Pour plus d’informations sur les comptes de stockage Azure, consultez la page [Gestion des comptes de stockage](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/).

**Remarque**

La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.

La journalisation relative aux applications dans les ressources de stockage nécessitant l'utilisation d'un client de stockage pour afficher les données de journalisation, cette fonction est très utile lorsque vous prévoyez d'utiliser un service ou une application capable de lire et de traiter directement les données à partir d'une table ou du stockage d’objets blob Azure. La journalisation dans le système de fichiers génère des fichiers pouvant être téléchargés sur votre ordinateur local via FTP ou d'autres utilitaires, comme décrit plus loin dans cette section.

**Remarque**

Les options **Diagnostic d'application (système de fichiers)**, **Diagnostic d'application (stockage de table)** et **Diagnostic d'application (stockage d’objets blob)** peuvent être activées simultanément et être associées à des niveaux de journalisation spécifiques. Ainsi, vous pouvez par exemple consigner les erreurs et les avertissements pour être stockés dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau détaillé de journalisation du système de fichiers une fois le code de l'application instrumenté pour résoudre un problème.

**Remarque**

Les diagnostics peuvent également être activés à partir du module Azure PowerShell via la cmdlet **Set-AzureWebsite**.

Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d'Azure PowerShell](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/).

**Remarque**

La journalisation des applications s'appuie sur les informations de journalisation générées par votre application. La méthode permettant de générer ce type d'informations, ainsi que le format des informations, sont propres au langage de votre application. Pour obtenir des informations sur le langage utilisé dans le cadre de la journalisation des applications, lisez les articles suivants :
<ul>
<li><b>.NET</b> - <a href="/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/">Activation de la journalisation de diagnostic pour Azure Web Sites</a></li>
<li><b>Node.js</b> - <a href="/en-us/develop/nodejs/how-to-guides/Debug-Website/">Débogage d'une application Node.js dans les sites Web Azure</a></li>
</ul>
<p>Application logging to table or blob storage is only supported for .NET applications.</p>
</div>

La journalisation relative aux applications dans les tables ou les objets blob n'est prise en charge que pour les applications .NET.

#### Diagnostic de site

La section **Site diagnostics** de la page d'administration **Configure** contrôle la journalisation effectuée par le serveur Web, qui porte notamment sur les demandes Web, ainsi que sur les échecs ou les temps d'accès aux pages. Vous pouvez activer ou désactiver les options suivantes :

-   **Web Server Logging** : permet d'enregistrer les journaux de site au format de journal étendu du W3C. La journalisation du serveur Web génère un enregistrement de toutes les demandes entrantes adressées à votre site Web, qui contient des informations telles que l'adresse IP du client, l'URI demandé, le code d'état HTTP de la réponse, ainsi que la chaîne de l'agent utilisateur du client. Vous pouvez enregistrer les journaux sur un compte de stockage Azure ou sur le système de fichiers.

 Pour enregistrer les journaux du serveur Web sur un compte de stockage Azure, choisissez **Stockage**, puis **manage storage** pour spécifier un compte de stockage et un conteneur d'objet blob Azure destiné à conserver les journaux. Pour plus d’informations sur les comptes de stockage Azure, consultez la page [Gestion des comptes de stockage](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/).

 Pour enregistrer les journaux du serveur Web sur le système de fichiers, choisissez **File System**. Cela active la zone **Quota** permettant de définir l'espace disque maximal alloué aux fichiers journaux. Les valeurs minimales et maximales sont respectivement 25 Mo et 100 Mo. La valeur par défaut est 35 Mo.

 Par défaut, les journaux du serveur Web ne sont jamais supprimés. Pour définir un délai après lequel les journaux sont automatiquement supprimés, sélectionnez **Set Retention** et entrez le nombre de jours de conservation des journaux dans la zone **Retention Period**. Ce paramètre est disponible pour les options Azure Storage et File System.

-   **Detailed Error Messages** : permet de consigner des informations supplémentaires sur les erreurs HTTP (codes d'état supérieurs à 400).

-   **Failed Request Tracing** : permet de capturer des informations relatives aux échecs de demandes du client, telles que les codes d'état HTTP série 400. Cette option de suivi génère un document XML qui contient des informations sur les modules pour lesquels la demande a été transmise via le gestionnaire des services Internet, les détails renvoyés par le module et l'heure à laquelle le module a été appelé. Ces informations permettent d'isoler le composant source de la défaillance.

Une fois que le diagnostic a été activé pour un site Web, cliquez sur l'icône **Save** au bas de la page d'administration **Configure** pour appliquer les options que vous avez définies.

**Important**

Les activités de journalisation et de suivi représentent une charge importante sur un site Web. Il est recommandé de désactiver la journalisation et le suivi une fois que vous avez pu reproduire le ou les problème(s) que vous essayez de résoudre.

### Configuration avancée

Vous pouvez également modifier la fonction de diagnostic en ajoutant des paires clé/valeur dans la section **app settings** de la page d'administration **Configure**. Les paramètres suivants peuvent être configurés à partir de **app settings** :

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

-   Emplacement où sont enregistrés les journaux de l'application ; chemin relatif à la racine du serveur Web.

-   Valeur par défaut : ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

-   Taille maximale de la mémoire tampon à utiliser au moment de récupérer les journaux de l'application. Initialement, les informations sont écrites dans la mémoire tampon avant d'être transférées dans un fichier ou une ressource de stockage. Si de nouvelles informations sont écrites dans la mémoire tampon avant que les informations précédemment enregistrées soient vidées, ces dernières peuvent être perdues. Si votre application génère d'importants flux d'informations de journalisation, pensez à augmenter la taille de la mémoire tampon.

-   Valeur par défaut : 10 Mo

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

-   Taille maximale du dossier **Application** dans lequel les diagnostics d'application écrits dans des fichiers sont stockés.

-   Valeur par défaut : 1 Mo

### Téléchargement des fichiers journaux d'un site Web

Les fichiers journaux peuvent être téléchargés à l'aide de FTP, Azure PowerShell ou des outils en ligne de commande Azure.

**FTP**

1.  Ouvrez la page d'administration **Tableau de bord** du site Web, puis notez le site FTP figurant sous **Diagnostics Logs** et le compte répertorié sous **Deployment User**. Le site FTP héberge les fichiers journaux ; le compte répertorié sous Deployment User permet de vous authentifier auprès du site FTP.
2.  Si vous n'avez pas déjà créé les identifiants pour le déploiement, le compte figurant sous **Deployment User** est associé à **Not set**. Dans ce cas, vous devez créer les identifiants de déploiement comme décrit dans la section Reset Deployment Credentials du Tableau de bord, car ces identifiants vous permettent de vous authentifier auprès du site FTP sur lequel sont stockés les fichiers journaux. Azure ne prend pas en charge l'authentification auprès du site FTP avec les informations d'identification Live ID.
3.  Pensez à utiliser un client FTP comme [FileZilla](http://go.microsoft.com/fwlink/?LinkId=247914) pour vous connecter au site FTP. Le client FTP facilite la saisie des informations d'identification. En outre, l'affichage des dossiers d'un site FTP y est généralement plus clair que dans un navigateur.
4.  Copiez les fichiers journaux du site FTP sur votre ordinateur en local.

**Azure PowerShell**

1.  À partir de l'**écran d'accueil** ou du **menu Démarrer**, recherchez **Azure PowerShell**. Cliquez avec le bouton droit sur l'entrée **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

    **Remarque**

    Si **Azure PowerShell** n'est pas installé, consultez la page [Mise en route des cmdlets Azure PowerShell](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554332.aspx) pour plus d'informations sur l'installation et la configuration.

2.  À l'invite Azure PowerShell, entrez la commande suivante pour télécharger les fichiers journaux :

         Save-AzureWebSiteLog -Name nom_site_Web

    Cette commande permet de télécharger les fichiers journaux du site Web indiqué par **nom\_site\_Web** et les enregistre sous forme de fichier compressé **log.zip** dans le répertoire en cours.

    Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

         Get-AzureWebSiteLog -Name nom_site_Web -Tail

    Cette commande affiche les informations de journalisation à l'invite Azure PowerShell à mesure qu'elles surviennent.

**Outils en ligne de commande Azure**

Ouvrez une nouvelle invite de commande, la console PowerShell, un interpréteur de commandes ou une session terminal, et exécutez la commande suivante pour télécharger les fichiers journaux :

    azure site log download nom_site_Web

Cette commande permet de télécharger les fichiers journaux du site Web indiqué par **nom\_site\_Web** et les enregistre sous forme de fichier compressé **log.zip** dans le répertoire en cours.

Vous pouvez également afficher un flux continu de journaux d'événements à l'aide de la commande suivante :

    azure site log tail nom_site_Web

Cette commande affiche les informations de journalisation à l'invite de commande, sur la console PowerShell, un interpréteur de commandes ou une session terminal à partir de laquelle la commande est exécutée.

**Remarque**

Si la commande **azure** n'est pas installée, consultez la page [Utilisation des outils en ligne de commande Azure](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/command-line-tools/) pour plus d'informations sur l'installation et la configuration.

### Lecture des fichiers journaux

Le contenu des fichiers journaux générés une fois que vous avez activé la journalisation et/ou le suivi d'un site varie en fonction du niveau de journalisation/suivi défini à la page d'administration Configure du site Web. Voici les emplacements des fichiers journaux et les méthodes permettant de les analyser :

**Type de fichier journal : journalisation des applications**

-   Emplacement : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications. Les informations consignées incluent la date et l'heure, l'ID de processus (PID) de l'application, ainsi que la valeur générée par l'instrumentation de l'application.

-   Lire les fichiers avec : un éditeur ou un analyseur de texte qui sait interpréter les valeurs générées par votre application

**Type de fichier journal : suivi des demandes ayant échoué**

-   Emplacement : /LogFiles/W3SVC########\#/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.

-   Lire les fichiers avec : Internet Explorer

**Type de fichier journal : messages d’erreur détaillés**

-   Emplacement : /LogFiles/DetailedErrors/. Le dossier /LogFiles/DetailedErrors/ contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite.

-   Lire les fichiers avec : un navigateur Web

Les fichiers .htm contiennent les sections suivantes :

-   **Informations détaillées sur l'erreur :** informations sur l'erreur telles que le *module*, le *gestionnaire*, le *code d'erreur* et l'*URL requise*.

-   **Causes les plus probables :** liste des causes d'erreur possibles.

-   **Actions à tenter :** liste des solutions possibles au problème signalé par le message d'erreur.

-   **Liens et informations supplémentaires** : fournit des informations supplémentaires sur l'erreur qui peuvent inclure des liens vers d'autres ressources, telles que les articles de la Base de connaissances Microsoft.

**Type de fichier journal : journalisation du serveur Web**

-   Emplacement : /LogFiles/http/RawLogs. Les informations que contiennent ces fichiers sont enregistrées au [format du journal étendu W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Les champs s-computername, s-ip et cs-version ne sont pas utilisés par Sites Web Azure.

-   Lire les fichiers avec : Log Parser. Utilisé pour analyser et interroger les fichiers journaux du gestionnaire des services Internet. Log Parser 2.2 est disponible sur le Centre de téléchargement Microsoft, à l'adresse <http://go.microsoft.com/fwlink/?LinkId=246619>.

Surveillance de l'état d'un point de terminaison Web
----------------------------------------------------

Cette fonction, disponible en mode **Standard**, vous permet de surveiller jusqu'à 2 points de terminaison à partir de 3 régions géographiques.

Elle paramètre des tests Web à partir de différents emplacements où sont évalués les temps de réponse et de disponibilité des URL. Ce type de test lance une requête HTTP get sur l'URL Web pour déterminer les temps de réponse et de disponibilité depuis chaque emplacement. Chaque emplacement configuré exécute un test toutes les cinq minutes.

La disponibilité est contrôlée à l'aide des codes réponse HTTP et le temps de réponse est mesuré en millisecondes. La disponibilité est évaluée à 100 % lorsque le temps de réponse ne dépasse pas 30 secondes et si le code d'état HTTP est inférieur à 400. Elle correspond à 0 % si le temps de réponse dépasse 30 secondes ou si le code d'état HTTP est supérieur à 400.

Une fois que la surveillance des points de terminaison est configurée, vous pouvez rechercher sur chaque point les détails des temps de réponse et de l'état de disponibilité pendant l'intervalle de surveillance, à partir de chaque lieu de test.

**Pour configurer la surveillance des points de terminaison, procédez comme suit :**

1.  Ouvrez **Sites Web**. Cliquez sur le nom du site Web que vous souhaitez configurer.
2.  Cliquez sur l’onglet **Configurer**.
3.  Accédez à la section **Monitoring** pour entrer vos paramètres de point de terminaison.
4.  Entrez le nom du point de terminaison.
5.  Entrez l'URL du service devant être surveillé. Par exemple, <http://contoso.cloudapp.net>.
6.  Sélectionnez une ou plusieurs régions géographiques dans la liste.
7.  Vous pouvez aussi recommencer les étapes précédentes pour créer un second point de terminaison.
8.  Cliquez sur **Enregistrer**. Il peut s'écouler un certain temps avant que les données de surveillance des points de terminaison Web soient disponibles sous les onglets **Tableau de bord** et **Monitor**.

Pour en savoir plus sur la surveillance des points de terminaison d'un site Web, visionnez les vidéos suivantes :

-   [Scott Guthrie présente Sites Web Azure et paramètre la surveillance des points de terminaison](http://www.windowsazure.com/fr-fr/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

-   [Assurer la gestion des sites Web Azure et la surveillance des points de terminaison - avec Stefan Schackow](http://www.windowsazure.com/fr-fr/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)


[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169
