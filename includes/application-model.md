# Compute
Azure vous permet de déployer et de surveiller le code de votre application lors de son exécution dans un centre de données Microsoft. Lorsque vous créez une application et que vous l'exécutez sous Azure, l'ensemble formé par le code et la configuration est appelé « service hébergé Azure ». Vous pouvez facilement gérer, faire évoluer, reconfigurer et mettre à jour ces services hébergés avec les nouvelles versions du code de votre application. Cet article décrit le modèle d'application des services hébergés par Azure.<a id="compare" name="compare"></a>

## Table des matières<a id="_GoBack" name="_GoBack"></a>
* [Avantages du modèle d'application Azure][Avantages du modèle d'application Azure]
* [Principaux concepts des services hébergés][Principaux concepts des services hébergés]
* [Considérations relatives à la conception des services hébergés][Considérations relatives à la conception des services hébergés]
* [Conception de votre application pour l'extensibilité][Conception de votre application pour l'extensibilité]
* [Définition et configuration des services hébergés][Définition et configuration des services hébergés]
* [Fichier de définition de service][Fichier de définition de service]
* [Fichier de configuration de service][Fichier de configuration de service]
* [Création et déploiement d'un service hébergé][Création et déploiement d'un service hébergé]
* [Informations de référence][Informations de référence]

## <a id="benefits"> </a>Avantages du modèle d'application Azure
Lorsque vous déployez votre application en tant que service hébergé, Azure crée une ou plusieurs machines virtuelles qui contiennent le code de votre application, puis démarre les machines virtuelles sur des machines physiques résidant dans l'un des centres de données Azure. Lorsque les demandes du client destinées à votre application hébergée parviennent au centre de données, un programme d'équilibrage de la charge les distribue de manière équitable entre les machines virtuelles. Si votre application est hébergée dans Azure, elle profite de trois avantages essentiels :

* **Haute disponibilité** cela signifie qu'Azure s'assure que vos applications sont exécutées aussi longtemps que possible et sont capables de répondre aux demandes du client. Si votre application s'arrête (par exemple, à cause d'une exception non gérée), Azure détecte ce problème et la redémarre automatiquement. Si la machine sur laquelle votre application est exécutée tombe en panne, Azure le détecte également et crée automatiquement une nouvelle machine virtuelle sur une autre machine physique fonctionnelle, afin d'y exécuter votre code. REMARQUE : pour que votre application atteigne les 99,95 % de disponibilité du contrat de niveau de service Microsoft, vous devez avoir au moins deux machines virtuelles pour exécuter le code de votre application. Ceci permet à une machine virtuelle de traiter les demandes du client tandis qu'Azure déplace votre code d'une machine virtuelle en panne vers une autre, qui fonctionne.
* **Extensibilité** Azure vous permet de modifier facilement et de manière dynamique le nombre de machines virtuelles exécutant le code de votre application, pour améliorer la gestion de la charge de travail traitée par votre application. Vous pouvez ainsi adapter votre application à la charge de travail imposée par vos clients, tandis que vos frais de machines virtuelles restent proportionnels à vos besoins. Lorsque vous voulez changer le nombre de machines virtuelles, Azure répond en quelques minutes, ce qui vous permet de changer le nombre de machines virtuelles en cours d'exécution de manière dynamique, aussi souvent que nécessaire.
* **Facilité de gestion** Comme Azure est une offre PaaS (Platform as a Service), ce produit gère l'infrastructure (le matériel, la consommation électrique et le réseau) requise pour la bonne exécution de ces machines. Azure gère également la plateforme, garantissant la mise à jour du système d'exploitation grâce à des mises à jour de sécurité et des correctifs adéquats, ainsi que des mises à jour de tous les composants, tels que le .NET Framework et Internet Information Server. Comme toutes les machines virtuelles exécutent Windows Server 2008, Azure fournit des fonctionnalités supplémentaires telles que l'analyse de diagnostic, la prise en charge du Bureau à distance, les pare-feu et la configuration de magasin de certificats. Toutes ces fonctionnalités sont fournies sans coût supplémentaire. En fait, lorsque vous exécutez votre application dans Azure, la licence du système d'exploitation Windows Server 2008 est incluse. Comme toutes les machines virtuelles exécutent Windows Server 2008, le code exécuté sur Windows Server 2008 fonctionne parfaitement dans Azure.

## <a id="concepts"> </a>Principaux concepts des services hébergés
Lorsque votre application est déployée en tant que service hébergé dans Azure, elle est exécutée sous la forme d’un ou plusieurs *rôles*. Un *rôle* fait simplement référence à des fichiers d’application et une configuration. Vous pouvez définir un ou plusieurs rôles pour votre application, chacun étant doté de son propre ensemble de fichiers d’application et de sa propre configuration. Vous pouvez indiquer le nombre de machines virtuelles, ou *instances de rôle*, à exécuter pour chaque rôle de votre application. La figure suivante présente deux exemples simples d’une application modélisée en tant que service hébergé avec des rôles et des instances de rôle.

##### Figure 1 : un rôle unique avec trois instances (machines virtuelles) exécutées dans un centre de données Azure
![image][0]

##### Figure 2 : deux rôles, chacun avec deux instances (machines virtuelles) exécutées dans un centre de données Azure
![image][1]

Les instances de rôle traitent normalement des demandes du client Internet en entrant dans le centre de données via un *point de terminaison d’entrée*. Un rôle unique peut avoir plusieurs points de terminaison d’entrée, voire aucun. Chaque point de terminaison indique un protocole (HTTP, HTTPS ou TCP) et un port. Il est courant de configurer un rôle avec deux points de terminaison d'entrée : HTTP à l'écoute sur le port 80 et HTTPS à l'écoute sur le port 443. La figure ci-dessous montre un exemple de deux rôles différents avec des points de terminaison d'entrée différents dirigeant les demandes du client vers ces rôles.

![image][2]

Lorsque vous créez un service hébergé dans Azure, il se voit attribuer une adresse IP adressable publiquement que les clients peuvent utiliser pour y accéder. Une fois le service hébergé créé, vous devez également sélectionner un préfixe d’URL mappé vers cette adresse IP. Comme vous réservez essentiellement l’URL *préfixe*.cloudapp.net à un usage exclusif, le préfixe doit être unique. Les clients communiquent avec vos instances de rôle en utilisant l’URL. En règle générale, vous ne distribuez pas ni ne publiez l'URL *préfixe*.cloudapp.net Azure. Vous allez plutôt acheter un nom DNS auprès du bureau d’enregistrement DNS de votre choix, puis configurer votre nom DNS pour rediriger les demandes du client vers l’URL Azure. Pour plus d’informations, consultez la page [Configuration d’un nom de domaine personnalisé dans Azure][Configuration d’un nom de domaine personnalisé dans Azure].

## <a id="considerations"> </a>Considérations relatives à la conception des services hébergés
Lorsque vous concevez une application pour un environnement cloud, vous devez prendre en compte plusieurs éléments comme la latence, la haute disponibilité et l'extensibilité.

Par exemple, le choix de l’emplacement de votre code d’application est important lorsque vous exécutez un service hébergé dans Azure. Généralement, votre application est déployée vers les centres de données les plus proches de vos clients pour réduire la latence et obtenir les meilleures performances possible. Vous pouvez toutefois choisir un centre de données proche de votre société ou de vos données pour des raisons juridictionnelles ou légales concernant vos données et l’endroit où elles résident. Six centres de données à travers le monde peuvent héberger le code de votre application. Le tableau ci-dessous indique les lieux disponibles :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr>

<td style="width: 100px;">
**Pays/région**

</td>

<td style="width: 200px;">
**Sous-régions**

</td>
</tr>

<tr>

<td>
États-Unis

</td>

<td>
Centre Sud et Centre Nord

</td>
</tr>

<tr>

<td>
Europe

</td>

<td>
Nord et Ouest

</td>
</tr>

<tr>

<td>
Asie

</td>

<td>
Sud-Est et Est

</td>
</tr>
</tbody>
</table>
Lors de la création d’un service hébergé, vous sélectionnez une sous-région indiquant l’emplacement dans lequel vous voulez exécuter votre code.

Pour obtenir des capacités de haute disponibilité et d’extensibilité, il est extrêmement important que les données de votre application soient conservées dans un emplacement de stockage centralisé, accessible par plusieurs instances de rôle. C'est pour cela qu'Azure propose différentes options de stockage telles que les objets blob, les tables et les bases de données SQL. Pour plus d’informations sur ces technologies de stockage, consulter l’article [Offres de stockage de données dans Azure][Offres de stockage de données dans Azure]. La figure ci-dessous montre comment le programme d’équilibrage de la charge du centre de données Azure distribue les demandes du client vers différentes instances de rôle ayant accès au même stockage de données.

![image][3]

Généralement, vous voulez stocker le code de votre application et vos données dans le même centre de données, car cela permet de bénéficier d’une latence faible (et donc de meilleures performances) lorsque le code de votre application accède aux données. De plus, vous n’êtes pas facturé pour la bande passante lorsque les données sont déplacées au sein d’un même centre de données.

## <a id="scale"> </a>Conception de votre application pour l'extensibilité
Parfois, vous pouvez avoir besoin d’héberger une application simple (comme un site Web) dans Azure. Mais la plupart du temps, votre application peut se composer de différents rôles qui fonctionnent ensemble. Par exemple, dans la figure ci-dessous, vous pouvez voir deux instances du rôle Site web, trois instances du rôle Traitement de commandes et une instance du rôle Générateur de rapports. Ces rôles fonctionnent tous ensemble et leurs codes peuvent être packagés et déployés comme une seule unité vers Azure.

![image][4]

La principale raison pour diviser une application en plusieurs rôles exécutant chacun son propre ensemble d’instances de rôle (c-à-d. des machines virtuelles) est une volonté de mettre à l’échelle chaque rôle indépendamment des autres. Par exemple, durant les vacances, plusieurs clients peuvent acheter des produits de votre société, vous poussant donc à augmenter le nombre d'instances de rôle exécutant votre rôle Site web, ainsi que le nombre d'instances exécutant votre rôle Traitement de commandes. Après les vacances, vous pouvez avoir de nombreux retours de produits. Il vous faudra donc beaucoup d'instances Site web, mais peu d'instances Traitement de commandes. Pendant le reste de l'année, il est possible que vous n'ayez besoin que de quelques instances Site web et Traitement des commandes. Et pour tout ceci, il se peut que vous ayez besoin d’une seule instance Générateur de rapports. La flexibilité des déploiements basés sur des rôles dans Azure vous permet d’adapter facilement votre application selon vos besoins.

Il est courant que les instances de rôle de votre service hébergé communiquent les unes avec les autres. Par exemple, un rôle Site web peut accepter la commande d'un client, mais il se décharge ensuite du traitement de la commande sur les instances de rôle Traitement de commandes. La meilleure méthode de transmission des tâches d’un ensemble d’instances de rôle à un autre est d’utiliser la technologie de mise en file d’attente fournie par Azure, que ce soit le service de File d’attente ou les files d’attente Service Bus. L’utilisation d’une file d’attente est un élément de scénario important. Grâce aux files d’attente, le service hébergé peut mettre à l’échelle ses rôles de façon indépendante, ce qui vous permet d’équilibrer la charge de travail par rapport aux coûts. Si le nombre de messages de la file d’attente augmente au fur et à mesure, vous pouvez augmenter le nombre d’instances de rôle Traitement de commandes. Si le nombre de messages de la file d’attente diminue, vous pouvez diminuer le nombre d’instances de rôle Traitement de commandes. Ainsi, vous ne payez que pour les instances requises pour gérer la charge de travail en cours.

Les files d’attente améliorent également la fiabilité. Lorsque vous réduisez le nombre d’instances de rôle Traitement de commandes, Azure choisit les instances à arrêter. Il peut décider d’arrêter une instance en train de traiter un message en file d’attente. Cependant, comme le traitement du message n’est pas terminé correctement, ce dernier redevient visible pour une autre instance de rôle Traitement de commandes qui le sélectionne et le traite. En raison de la visibilité des messages en file d’attente, vous pouvez être sûr que les messages finiront toujours par être traités. Les files d’attente font aussi office de programme d’équilibrage de charge en distribuant efficacement leurs messages aux instances de rôle qui leur demandent ces messages.

Pour les instances de rôle Site web, vous pouvez en surveiller le trafic entrant et décider d'augmenter ou de diminuer leur nombre. La file d'attente vous permet de mettre à l'échelle le nombre d'instances de rôle Site web indépendamment des instances de rôle Traitement de commandes. Ceci est extrêmement efficace et accroît considérablement votre flexibilité. Bien sûr, si votre application comporte des rôles supplémentaires, vous pouvez ajouter des files d’attente pour les faire communiquer et ainsi profiter des mêmes avantages en termes d’extensibilité et de coûts.

## <a id="defandcfg"> </a>Définition et configuration des services hébergés
Le déploiement d’un service hébergé sur Azure requiert un fichier de définition de service et un fichier de configuration de service. Ces deux fichiers sont au format XML et vous permettent de spécifier de manière déclarative des options de déploiement pour votre service hébergé. Le fichier de définition de service décrit tous les rôles constituant votre service hébergé et la façon dont ils communiquent. Le fichier de configuration de service décrit le nombre d’instances pour chaque rôle et les paramètres utilisés pour configurer chaque instance de rôle.

## <a id="def"> </a>Fichier de définition de service
Comme mentionné précédemment, le fichier de définition de service (CSDEF) est un fichier XML décrivant les différents rôles constituant votre application. Le schéma complet de ce fichier XML se trouve ici : [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][]. Le fichier CSDEF contient un élément WebRole ou WorkerRole pour chaque rôle de votre application. Le fait de déployer un rôle en tant que rôle Web (en utilisant l’élément WebRole) signifie que le code sera exécuté sur une instance de rôle contenant Windows Server 2008 et Internet Information Server (IIS). Le fait de déployer un rôle en tant que rôle de travail (en utilisant l’élément WorkerRole) signifie que l’instance de rôle contiendra Windows Server 2008 (IIS ne sera pas installé).

Vous pouvez tout à fait créer et déployer un rôle de travail qui utilise d’autres mécanismes pour écouter les demandes Web entrantes (par exemple, votre code pourrait créer et utiliser un élément HttpListener .NET). Comme les instances de rôle exécutent toutes Windows Server 2008, votre code peut effectuer toutes les opérations disponibles pour une application exécutée sur Windows Server 2008.

Pour chaque rôle, indiquez la taille de la machine virtuelle que les instances de ce rôle doivent utiliser. Le tableau ci-dessous répertorie les différentes tailles de machines virtuelles disponibles actuellement, ainsi que les attributs associés :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr align="left" valign="top">

<td>
**Taille de la machine virtuelle**

</td>

<td>
**UC**

</td>

<td>
**RAM**

</td>

<td>
**Disque**

</td>

<td>
**Pic de réseau en E/S**

</td>
</tr>

<tr align="left" valign="top">

<td>
**Très petite**

</td>

<td>
1&#160;x&#160;1.0 GHz

</td>

<td>
768&#160;Mo

</td>

<td>
20&#160;Go

</td>

<td>
~5&#160;Mbits/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Petite**

</td>

<td>
1&#160;x&#160;1.6 GHz

</td>

<td>
1,75&#160;Go

</td>

<td>
225&#160;Go

</td>

<td>
~100&#160;Mbits/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Moyenne**

</td>

<td>
2&#160;x&#160;1.6 GHz

</td>

<td>
3,5&#160;Go

</td>

<td>
490&#160;Go

</td>

<td>
~200&#160;Mbits/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Grande**

</td>

<td>
4&#160;x&#160;1.6 GHz

</td>

<td>
7&#160;Go

</td>

<td>
1&#160;To

</td>

<td>
~400&#160;Mbits/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Très grande**

</td>

<td>
8&#160;x&#160;1.6 GHz

</td>

<td>
14&#160;Go

</td>

<td>
2&#160;To

</td>

<td>
~800&#160;Mbits/s

</td>
</tr>
</tbody>
</table>
Chaque machine virtuelle que vous utilisez en tant qu’instance de rôle vous est facturée à l’heure. Vous devez également payer pour les données que vos instances de rôle envoient hors du centre de données. Vous ne devez pas payer pour les données entrant dans le centre de données. Pour plus d’informations, consultez la page sur les [tarifs Azure][tarifs Azure]. Généralement, il est conseillé d’utiliser de nombreuses petites instances de rôle plutôt que quelques grandes instances de rôle, pour réduire les risques de panne de votre application. En effet, moins vous aurez d’instances de rôle et plus une panne de l’une d’entre elles sera lourde de conséquences pour l’ensemble de votre application. De même, comme mentionné précédemment, vous devez déployer au moins deux instances pour chaque rôle, afin d’obtenir la disponibilité de 99,95 % du contrat de niveau de service de Microsoft.

Le fichier de définition de service (CSDEF) permet également de spécifier les attributs de chaque rôle de votre application. Voici certains des éléments les plus utiles pour vous :

* **Certificats** : vous pouvez utiliser les certificats pour chiffrer des données ou si votre service Web prend en charge SSL. Chaque certificat doit être téléchargé vers Azure. Pour plus d’informations, consultez la page [Gestion des certificats dans Azure][]. Ce paramètre XML installe les certificats précédemment téléchargés dans le magasin de certificats de l’instance de rôle pour permettre au code de votre application de les utiliser.
* **Nom des paramètres de configuration** : pour les valeurs que vous voulez faire lire à votre application durant son exécution sur une instance de rôle. La valeur réelle des paramètres de configuration est définie dans le fichier de configuration de service, qui peut être mis à jour à tout moment sans que vous deviez redéployer votre code. En fait, vous pouvez coder vos applications pour qu’elles détectent les modifications de valeurs de configuration sans encourir de temps d’arrêt.
* **Points de terminaison d’entrée** : ici, vous pouvez indiquer les points de terminaison HTTP, HTTPS ou TCP (avec ports) que vous voulez exposer au monde extérieur via votre URL *préfixe*.cloadapp.net. Lorsqu’Azure déploie votre rôle, il configure automatiquement le pare-feu sur l’instance de rôle.
* **Points de terminaison internes** : ici, vous pouvez indiquer les points de terminaison HTTP ou TCP que vous voulez exposer aux autres instances de rôle déployées dans le cadre de votre application. Les points de terminaison internes permettent aux instances de rôle de votre application de communiquer, mais ils ne sont pas accessibles par les instances de rôle situées hors de l’application.
* **Modules d’importation** : facultatifs, ils permettent d’installer des composants utiles sur vos instances de rôle. Ces composants sont disponibles pour les analyses de diagnostic, le Bureau à distance et Azure Connect (ce qui permet à votre instance de rôle d’accéder à des ressources locales via un canal sécurisé).
* **Stockage local** : ceci alloue un sous-répertoire sur l’instance de rôle utilisable par votre application. Pour plus d’informations sur cette option, consultez l’article [Offres de stockage de données dans Azure][Offres de stockage de données dans Azure].
* **Tâches de démarrage** : celles-ci vous permettent d’installer les composants prérequis sur une instance de rôle lorsqu’elle démarre. Le cas échéant, ces tâches peuvent être exécutées avec élévation de privilèges en tant qu’administrateur.

## <a id="cfg"> </a>Fichier de configuration de service
Le fichier de configuration de service (CSCFG) est un fichier XML qui décrit les paramètres que vous pouvez modifier sans redéployer votre application. Le schéma complet pour le fichier XML se trouve ici : [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]. Le fichier CSCFG contient un élément Rôle pour chaque rôle de votre application. Voici certains des éléments que vous pouvez spécifier dans le fichier CSCFG :

* **Version du système d’exploitation** : cet attribut vous permet de sélectionner la version du système d’exploitation à utiliser pour toutes les instances de rôle exécutant le code de votre application. Ce système d’exploitation est nommé *SE invité* et chaque nouvelle version inclut les correctifs et mises à jour de sécurité les plus récents, disponibles au moment de la publication du SE invité. Si vous définissez la valeur de l’attribut osVersion sur « * », Azure met automatiquement à jour le SE invité sur chacune de vos instances de rôle, dès que de nouvelles versions de SE invité sont disponibles. Cependant, vous pouvez désactiver les mises à jour automatiques en sélectionnant une version de SE invité spécifique. Par exemple, en configurant l'attribut osVersion avec la valeur « WA-GUEST-OS-2.8\_201109-01 », toutes vos instances de rôle obtiennent ce qui est décrit sur cette page web : [http://msdn.microsoft.com/library/hh560567.aspx][http://msdn.microsoft.com/library/hh560567.aspx]. Pour plus d’informations sur les versions de SE invité, consultez la page [Gestion des mises à niveau vers les SE invités d’Azure].
* **Instances** : la valeur de cet élément indique le nombre d’instances de rôle que vous voulez configurer en exécutant le code pour un rôle particulier. Comme vous pouvez télécharger un nouveau fichier CSCFG vers Azure (sans redéployer votre application), il est relativement simple de modifier la valeur de cet élément et de télécharger un nouveau fichier CSCFG pour augmenter ou diminuer de façon dynamique le nombre d’instances de rôle exécutant le code de votre application. Ceci vous permet de faire évoluer rapidement votre application pour répondre aux demandes de charges de travail réelles tout en contrôlant vos frais d’exécution d’instances de rôle.
* **Valeur des paramètres de configuration** : cet élément indique les valeurs des paramètres (comme défini dans le fichier CSDEF). Votre rôle peut lire ces valeurs durant son exécution. Ces valeurs des paramètres de configuration sont généralement utilisées pour les chaînes de connexion à SQL Database ou à Azure Storage, mais elles peuvent également être utilisées à d'autres fins, selon vos besoins.

## <a id="hostedservices"> </a>Création et déploiement d'un service hébergé
Pour créer un service hébergé, vous devez d'abord accéder au [portail de gestion Azure] et configurer un service hébergé en spécifiant un préfixe DNS ainsi que le centre de données dans lequel vous voulez que votre code s'exécute. Ensuite, dans votre environnement de développement, vous créez votre fichier de définition de service (CSDEF), vous générez le code de votre application et vous créez un package de tous ces fichiers (vous les compressez), représenté par un fichier de package de service (CSPKG). Vous devez également préparer votre fichier de configuration de service (CSCFG). Pour déployer votre rôle, téléchargez vos fichiers CSPKG et CSCFG avec l’API Azure Service Management. Une fois déployé, Azure configure les instances de rôle dans le centre de données (en fonction des données de configuration), extrait le code de votre application du package, le copie dans les instances de rôle, puis démarre ces instances. Votre code est désormais opérationnel.

La figure ci-dessous montre les fichiers CSPKG et CSCFG créés sur votre ordinateur de développement. Le fichier CSPKG contient le fichier CSDEF et le code pour deux rôles. Une fois les fichiers CSPKG et CSCFG téléchargés avec l’API Azure Service Management, Azure crée les instances de rôle dans le centre de données. Dans cet exemple, le fichier CSCFG indique qu’Azure doit créer trois instances du rôle #1 et deux instances du rôle #2.

![image][5]

Pour plus d'informations sur le déploiement, la mise à niveau et la reconfiguration de vos rôles, consultez l'article [Déploiement et mise à niveau des applications Azure][Déploiement et mise à niveau des applications Azure].<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Informations de référence
* [Création d'un service hébergé pour Azure][Création d'un service hébergé pour Azure]
* [Gestion des services hébergés dans Azure][Gestion des services hébergés dans Azure]
* [Migration des applications vers Azure][Migration des applications vers Azure]
* [Configuration d'une application Azure][Configuration d'une application Azure]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Écrit par Jeffrey Richter (Wintellect)</p>

</div>

[Avantages du modèle d'application Azure]: #benefits
[Principaux concepts des services hébergés]: #concepts
[Considérations relatives à la conception des services hébergés]: #considerations
[Conception de votre application pour l'extensibilité]: #scale
[Définition et configuration des services hébergés]: #defandcfg
[Fichier de définition de service]: #def
[Fichier de configuration de service]: #cfg
[Création et déploiement d'un service hébergé]: #hostedservices
[Informations de référence]: #references
[0]: ./media/application-model/application-model-3.jpg
[1]: ./media/application-model/application-model-4.jpg
[2]: ./media/application-model/application-model-5.jpg
[Configuration d’un nom de domaine personnalisé dans Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
[Offres de stockage de données dans Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
[3]: ./media/application-model/application-model-6.jpg
[4]: ./media/application-model/application-model-7.jpg

[tarifs Azure]: http://www.windowsazure.com/pricing/calculator/
[Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
[Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/library/ee924680.aspx
[portail de gestion Azure]: http://manage.windowsazure.com/
[5]: ./media/application-model/application-model-8.jpg
[Déploiement et mise à niveau des applications Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
[Création d'un service hébergé pour Azure]: http://msdn.microsoft.com/library/gg432967.aspx
[Gestion des services hébergés dans Azure]: http://msdn.microsoft.com/library/gg433038.aspx
[Migration des applications vers Azure]: http://msdn.microsoft.com/library/gg186051.aspx
[Configuration d'une application Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx

<!---HONumber=Oct15_HO3-->