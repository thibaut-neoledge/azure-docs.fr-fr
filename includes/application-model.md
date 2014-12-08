# Compute

Azure vous permet de déployer et de surveiller le code de votre application
lors de son exécution dans un centre de données Microsoft. Lorsque vous créez une application
et que vous l'exécutez sur Azure, l'ensemble formé par le code et la configuration est
appelé un " service hébergé Azure ". Vous pouvez facilement
gérer, faire évoluer, reconfigurer et mettre à jour ces services hébergés avec les nouvelles versions du
code de votre application. Cet article décrit le
modèle d'application de service hébergé Azure.<a id="compare" name="compare"></a>

## Sommaire<a id="_GoBack" name="_GoBack"></a>

-   [Avantages du modèle d'application Azure][]
-   [Principaux concepts des services hébergés][]
-   [Considérations relatives à la conception des services hébergés][]
-   [Conception de votre application pour l'extensibilité][]
-   [Définition et configuration des services hébergés][]
-   [Fichier de définition de service][]
-   [Fichier de configuration de service][]
-   [Création et déploiement d'un service hébergé][]
-   [Références][]

## <a id="benefits"> </a>Avantages du modèle d'application Azure

Lorsque vous déployez votre application en tant que service hébergé, Azure
crée une ou plusieurs machines virtuelles qui contiennent le code de votre
application, puis démarre les machines virtuelles sur des machines physiques résidant dans
un des centres de données Azure. Quand les demandes d'un client adressées à votre application
hébergée parviennent au centre de données, un programme d'équilibrage de la charge les
distribue équitablement entre les machines virtuelles. Lorsque votre application est hébergée dans
Azure, elle bénéficie de trois avantages essentiels :

-   **Haute disponibilité :** cela signifie qu'Azure s'assure
    que votre application est exécutée autant que possible et est en mesure de
    répondre aux demandes des clients. Si votre application se ferme (en raison
    d'une exception non prise en charge, par exemple), Azure détecte
    cet événement et la redémarre automatiquement. Si la
    machine sur laquelle votre application s'exécute est affectée par une
    défaillance matérielle, Azure détecte également cet événement,
    crée automatiquement une nouvelle machine virtuelle sur une autre machine physique fonctionnelle
    et exécute votre code à partir de là. REMARQUE : pour que votre application
    la disponibilité à 99,95 % du contrat de niveau de service de Microsoft, vous
    devez avoir au moins deux machines virtuelles exécutant le code de votre application. Cela
    permet à une machine virtuelle de traiter les demandes client pendant qu'Azure déplace
    votre code d'une machine virtuelle défaillante à une autre machine virtuelle en état de marche.

-   **Extensibilité :** Azure vous permet de facilement et dynamiquement
    modifier le nombre de machines virtuelles exécutant votre code d'application afin de gérer la
    charge réelle de votre application. Cela vous permet d'
    ajuster votre application en fonction de la charge de travail à laquelle vos clients
    la soumettent tout en payant uniquement pour les machines virtuelles dont vous avez besoin, lorsque vous en avez besoin
    . Lorsque vous souhaitez modifier le nombre de machines virtuelles, Azure
    répond en quelques minutes, ce qui vous permet de modifier dynamiquement le
    nombre de machines virtuelles en cours d'exécution aussi souvent que nécessaire.

-   **Capacité de gestion :** étant donné qu'Azure est une PaaS
    (Platform as a Service), il gère l'infrastructure (le matériel lui-même,
    la consommation électrique et le réseau) requise pour conserver ces machines
    en fonctionnement. Azure gère également la plateforme, garantissant
    la mise à jour du système d'exploitation à l'aide des mises à jour de sécurité
    et des correctifs adéquats, ainsi que des mises à jour des composants, comme
    .Net Framework et Internet Information Server. Étant donné que toutes les machines virtuelles sont
    exécutées sur Windows Server 2008, Azure fournit des
    fonctionnalités supplémentaires, telles que la surveillance du diagnostic, la prise en charge du Bureau à distance,
    les pare-feu et la configuration de magasin de certificats. Toutes ces fonctionnalités
    sont fournies sans coût supplémentaire. En fait, lorsque vous exécutez votre
    application dans Azure, la licence du système d'exploitation Windows Server 2008
    est incluse. Étant donné que toutes les machines virtuelles s'exécutent sur
    Windows Server 2008, tout code qui s'exécute sur Windows Server 2008 fonctionne
    sans problème lorsqu'il est exécuté dans Azure.

## <a id="concepts"> </a>Principaux concepts des services hébergés

Lorsque votre application est déployée en tant que service hébergé dans Azure,
elle s'exécute sous la forme d'un ou plusieurs *rôles*. Un *rôle* correspond tout simplement
aux fichiers et à la configuration de l'application. Vous pouvez définir un ou plusieurs rôles pour votre
application, chacun avec son propre ensemble de fichiers d'application et
sa propre configuration. Pour chaque rôle de votre application, vous pouvez spécifier le
nombre de machines virtuelles, ou *instances de rôle*, à exécuter. La figure ci-dessous présente deux
exemples simples d'une application modelée en tant que service hébergé à l'aide
de rôles et d'instances de rôle.

##### Figure 1 : rôle unique avec trois instances (machines virtuelles) exécutées dans un centre de données Azure

![image][0]

##### Figure 2 : deux rôles, contenant chacun deux instances (machines virtuelles) exécutées dans un centre de données Azure

![image][1]

Les instances de rôle traitent généralement les demandes des clients Internet entrant dans
le centre de données via ce que l'on appelle un *point de terminaison d'entrée*. Un seul rôle
peut avoir 0, 1 ou plusieurs points de terminaison d'entrée. Chaque point de terminaison indique un protocole
(HTTP, HTTPS ou TCP) et un port. Il est courant de configurer un rôle afin
d'avoir deux points de terminaison d'entrée : HTTP à l'écoute sur le port 80 et HTTPS à l'écoute
sur le port 443. La figure ci-dessous illustre un exemple de deux rôles distincts
avec différents points de terminaison d'entrée leur adressant les demandes des clients.

![image][2]

Lorsque vous créez un service hébergé dans Azure, il lui est attribué une
adresse IP adressable publiquement que les clients peuvent utiliser pour y accéder. Lors
de la création du service hébergé, vous devez également sélectionner un préfixe d'URL
mappé sur cette adresse IP. Ce préfixe doit être unique, étant donné que vous
réservez essentiellement l'URL *préfixe*.cloudapp.net afin que personne d'autre
ne puisse l'utiliser. Les clients communiquent avec vos instances de rôle en utilisant
l'URL. En règle générale, vous ne distribuez pas ni ne publiez l'URL Azure
*préfixe*.cloudapp.net. Au lieu de cela, vous achetez un nom DNS auprès du
bureau d'enregistrement DNS de votre choix et configurez votre nom DNS pour rediriger
les demandes des clients vers l'URL Azure. Pour plus d'informations, consultez la page
[Configuration d'un nom de domaine personnalisé dans Azure][].

## <a id="considerations"> </a>Considérations relatives à la conception des services hébergés

Lorsque vous créez une application qui doit s'exécuter dans un environnement cloud, vous devez
prendre certains points en considération, notamment la latence,
la haute disponibilité et l'extensibilité.

Décider de l'emplacement de votre code d'application est un point important
à prendre en compte lorsque vous exécutez un service hébergé dans Azure. Il est
courant de déployer votre application dans les centres de données les plus proches
de vos clients afin de réduire la latence et d'obtenir les meilleures performances possibles.
Toutefois, vous pouvez choisir un centre de données proche de votre entreprise ou plus proche
de vos données si vous avez des préoccupations d'ordre juridictionnel ou légal concernant
vos données et leur emplacement. Il existe six centres de données dans le
monde capables d'héberger votre code d'application. Le tableau ci-dessous présente
les emplacements disponibles :

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
Lorsque vous créez un service hébergé, vous sélectionnez une sous-région indiquant l'
emplacement dans lequel vous souhaitez exécuter votre code.

Pour optimiser la haute disponibilité et l'extensibilité, il est extrêmement important
que les données de votre application soient conservées dans un référentiel central accessible
à plusieurs instances de rôle. Pour cela, Azure propose
plusieurs options de stockage telles que les objets blob, les tables et les base de données SQL. Veuillez consulter
l'article sur les [Offres de stockage de données dans Azure][] pour de plus amples
informations sur ces technologies de stockage. La figure ci-dessous illustre comment
le programme d'équilibrage de la charge du centre de données Azure distribue
les demandes des clients à des instances de rôle distinctes, toutes ayant accès au
même stockage de données.

![image][3]

En règle générale, il est recommandé de choisir le même centre de données
pour votre code d'application et vos données, car cela permet d'obtenir une latence faible (meilleures performances)
lorsque le code de votre application accède aux données. De plus, vous n'êtes pas
facturé pour la bande passante lorsque les données sont déplacées au sein du même
centre de données.

## <a id="scale"> </a>Conception de votre application pour l'extensibilité

Vous pouvez parfois avoir besoin d'héberger une application simple (comme un site web simple
) dans Azure. Mais bien souvent, votre
application peut comprendre plusieurs rôles qui fonctionnent ensemble. Par
exemple, dans la figure ci-dessous, il existe deux instances de rôle de site web
, trois instances de rôle de traitement des commandes et une instance de
rôle de générateur de rapports. Ces rôles fonctionnent tous ensemble et
le code correspondant peut être packagé et déployé comme une seule
unité vers Azure.

![image][4]

La principale raison pour diviser une application en plusieurs rôles
s'exécutant chacun sur son propre ensemble d'instances de rôle (autrement dit, machines virtuelles) est de pouvoir mettre les rôles à l'échelle
indépendamment. Par exemple, pendant la saison des vacances, il se peut que de nombreux
clients achètent des produits auprès de votre société. Si c'est le cas, vous voudrez
augmenter le nombre d'instances de rôle exécutant votre rôle de site web,
ainsi que le nombre d'instances de rôle exécutant votre rôle de traitement de commandes
. Après les vacances, vous risquez de recevoir un grand nombre de retours de produits.
Par conséquent, vous aurez toujours besoin de nombreuses instances de site web, mais de moins d'instances
de traitement de commandes. Durant le reste de l'année, vous n'aurez sans doute besoin que de quelques
instances de site web et de traitement de commandes. Pour l'ensemble de cette période, vous
n'aurez peut-être besoin que d'une seule instance de générateur de rapports. La flexibilité offerte par les
déploiements basés sur les rôles dans Azure vous permet d'adapter facilement votre
application aux besoins de votre entreprise.

Il est courant que les instances de rôle de votre service hébergé
communiquent entre elles. Par exemple, le rôle de site web accepte une
commande d'un client, mais il se décharge ensuite du traitement de la commande et le transmet aux
instances de rôle de traitement de commandes. Le meilleur moyen de transmettre un travail d'un ensemble
d'instances de rôle à un autre est par le biais de la technologie de mise en file d'attente
fournie par Azure, à savoir le service de File d'attente ou
les files d'attente Service Bus. L'utilisation d'une file d'attente est un élément de scénario important
ici. La file d'attente permet au service hébergé de mettre ses rôles à l'échelle
indépendamment, ce qui vous permet d'équilibrer la charge de travail par rapport aux coûts. Si le
nombre de messages de la file d'attente augmente au fil du temps, vous pouvez augmenter
le nombre d'instances de rôle de traitement des commandes. Si le nombre de
messages de la file d'attente diminue au fil du temps, vous pouvez réduire le
nombre d'instances de rôle de traitement des commandes. De cette façon, vous ne payez que
pour les instances requises pour gérer la charge de travail réelle.

Les files d'attente améliorent également la fiabilité. Lorsque vous réduisez le nombre d'
instances de rôle de traitement de commandes, Azure choisit les instances
à fermer. Il peut décider de fermer une instance alors qu'elle est train de
traiter un message de la file d'attente. Toutefois et comme le traitement du message
n'est pas effectué correctement, le message devient visible
pour une autre instance de rôle de traitement des commandes, qui le récupère et
le traite. Grâce à la visibilité des messages de file d'attente, le traitement des messages
est garanti. La file d'attente fait également office d'équilibrage
de charge en distribuant efficacement ses messages à toutes les instances de rôle
qui lui demandent des messages.

Dans le cas des instances de rôle de site web, vous pouvez surveiller le trafic entrant
et décider d'augmenter ou de réduire le nombre de ces instances. La
file d'attente vous permet de mettre à l'échelle le nombre d'instances de rôle de site web
indépendamment des instances de rôle de traitement des commandes. Cette fonctionnalité est très
puissante et vous offre une grande flexibilité. Bien sûr, si votre
application se compose de rôles supplémentaires, vous pouvez ajouter d'autres
files d'attente comme moyen de communiquer entre eux, afin de tirer parti des
mêmes avantages de mise à l'échelle et de coût.

## <a id="defandcfg"> </a>Définition et configuration des services hébergés

Le déploiement d'un service hébergé dans Azure suppose également que vous ayez un
fichier de définition de service et un fichier de configuration de service. Ces deux
fichiers sont des fichiers XML, qui vous permettent de spécifier de manière déclarative
les options de déploiement de votre service hébergé. Le fichier de définition de service
décrit tous les rôles qui composent votre service hébergé et la façon dont ils
communiquent. Le fichier de configuration de service décrit le nombre d'
instances de chaque rôle et les paramètres utilisés pour configurer chaque instance
de rôle.

## <a id="def"> </a>Fichier de définition de service

Comme mentionné précédemment, le fichier de définition de service (CSDEF) est un fichier XML
qui décrit les différents rôles constituant votre application
. Vous trouverez le schéma complet de ce fichier XML ici :
[http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758711.aspx][].
Le fichier CSDEF contient un élément WebRole ou WorkerRole pour chaque rôle
que vous souhaitez avoir dans votre application. Déployer un rôle en tant que rôle web (à l'aide de
l'élément WebRole) signifie que le code s'exécutera sur une instance de rôle
qui contient Windows Server 2008 et Internet Information Server (IIS).
Déployer un rôle en tant que rôle de travail (à l'aide de l'élément WorkerRole) signifie que
Windows Server 2008 sera installé sur l'instance de rôle (IIS ne sera pas
installé).

Vous pouvez bien entendu créer et déployer un rôle de travail qui utilise un autre
mécanisme pour écouter les demandes web entrantes (par exemple, votre code
peut créer et utiliser un HttpListener .NET). Étant donné que les instances de rôle s'exécutent
toutes sur Windows Server 2008, votre code peut effectuer toutes les opérations
normalement disponibles pour une application s'exécutant sur Windows Server
2008.

Pour chaque rôle, vous indiquez la taille souhaitée pour la machine virtuelle que les instances de ce
rôle doivent utiliser. Le tableau ci-dessous présente les différentes tailles de machine virtuelle disponibles
actuellement et les attributs de chacune d'elles :

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
**Pointe  
Réseau en E/S**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Très petite**

</td>
<td>
1 x 1,0 GHz

</td>
<td>
768 Mo

</td>
<td>
20 Go

</td>
<td>
\~5 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Petite**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 Go

</td>
<td>
225 Go

</td>
<td>
\~100 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Moyenne**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 Go

</td>
<td>
490 Go

</td>
<td>
\~200 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1,6 Go

</td>
<td>
7 Go

</td>
<td>
1 To

</td>
<td>
\~400 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Très grande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 Go

</td>
<td>
2 To

</td>
<td>
\~800 Mbits/s

</td>
</tr>
</tbody>
</table>
Vous êtes facturé par heure pour chaque machine virtuelle que vous utilisez en tant qu'instance de rôle et
pour les données que vos instances de rôle envoient hors du
centre de données. Vous ne devez pas payer pour les données entrant dans le centre de données. Pour
plus d'informations, consultez la page [Tarifs Azure][]. En général, nous vous
conseillons d'utiliser de nombreuses petites instances de rôle plutôt que quelques grandes
instances, afin que votre application soit plus résistante en cas de défaillance. Après
tout, moins vous avez d'instances de rôle, plus une défaillance de
l'une d'elles est désastreuse pour l'ensemble de votre application. En outre et comme mentionné précédemment,
vous devez déployer au moins deux instances de chaque rôle afin d'obtenir le
contrat de niveau de service de 99,95 % fourni par Microsoft.

Le fichier de définition de service (CSDEF) vous permet également de spécifier plusieurs
attributs pour chaque rôle de votre application. Voici certains des
éléments les plus utiles à votre disposition :

-   **Certificats** : vous utilisez des certificats pour chiffrer des données ou si
    votre service web prend en charge SSL. Tous les certificats doivent être téléchargés
    dans Azure. Pour plus d'informations, consultez la rubrique [Gestion des certificats
    dans Azure][]. Ce paramètre XML installe les certificats précédemment téléchargés
    dans le magasin de certificats de l'instance de rôle afin que votre
    code d'application puisse les utiliser.

-   **Nom des paramètres de configuration** : pour les valeurs que vous souhaitez que vos
    applications lisent pendant leur exécution sur une instance de rôle. La valeur réelle
    des paramètres de configuration est définie dans le fichier
    de configuration de service (CSCFG), que vous pouvez mettre à jour à tout moment sans
    avoir à redéployer votre code. En fait, vous pouvez coder vos
    applications de manière à ce qu'elles détectent les valeurs de la configuration modifiée
    sans temps mort.

-   **Points de terminaison d'entrée** : ici, vous spécifiez un point de terminaison HTTP, HTTPS ou TCP
    (avec ports) à exposer au monde extérieur
    par l'intermédiaire de votre URL *préfixe*.cloadapp.net. Lorsqu'Azure déploie votre
    rôle, il configure le pare-feu de l'instance de rôle
    automatiquement.

-   **Points de terminaison internes** : ici, vous spécifiez les points de terminaison HTTP ou TCP
    à exposer à d'autres instances de rôle déployées dans le cadre
    de votre application. Les points de terminaison internes permettent à toutes les instances de rôle
    de votre application de communiquer entre elles, mais ne sont pas
    accessibles aux instances de rôle qui ne font pas partie de votre application.

-   **Modules d'importation** : ces modules installent éventuellement des composants utiles sur
    vos instances de rôle. Des composants existent pour la surveillance du diagnostic,
    le Bureau à distance et Azure Connect (ce qui permet à votre instance de rôle
    d'accéder aux ressources locales via un canal sécurisé).

-   **Stockage local** : ce paramètre alloue un sous-répertoire à l'instance
    de rôle afin que votre application l'utilise. Il est décrit plus en détail
    dans l'article [Offres de stockage de données dans Azure][].

-   **Tâches de démarrage** : les tâches de démarrage vous permettent d'installer
    les composants requis sur une instance de rôle à son démarrage. Les tâches
    peuvent être exécutées avec élévation de privilèges en tant qu'administrateur.

## <a id="cfg"> </a>Fichier de configuration de service

Le fichier de configuration de service (CSCFG) est un fichier XML qui décrit
les paramètres que vous pouvez modifier sans redéployer votre application. Le
schéma complet de ce fichier XML se trouve ici :
[http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx][].
Le fichier CSCFG contient un élément Rôle pour chaque rôle de votre
application. Voici certains des éléments que vous pouvez spécifier dans le fichier CSCFG
 :

-   **Version du système d'exploitation** : cet attribut vous permet de sélectionner la version du système
    d'exploitation à utiliser pour toutes les instances de rôle qui exécutent
    votre code d'application. Ce système d'exploitation est appelé le *SE invité*, et chaque
    nouvelle version inclut les derniers correctifs de sécurité et mises à jour
    disponibles au moment du lancement du système d'exploitation. Si vous définissez la
    valeur d'attribut osVersion sur " \* ", Azure met automatiquement
    le SE invité à jour sur chaque instance de rôle, au fur et à mesure que de nouvelles versions de SE invité
    sont lancées. Toutefois, vous pouvez refuser les
    mises à jour automatiques en sélectionnant une version de SE invité spécifique. Par exemple, 
    si vous affectez la valeur 
    " WA-GUEST-OS-2.8\_201109-01 " à l'attribut osVersion, toutes vos instances de rôle vont obtenir
    ce qui est décrit dans la page web suivante :
    [http://msdn.microsoft.com/fr-fr/library/hh560567.aspx][]. Pour plus
    d'informations sur les versions de SE invité, consultez la page [Gestion des mises à niveau vers
    les systèmes d'exploitation invités Azure].

-   **Instances** : la valeur de cet élément indique le nombre d'instances de rôle
    à configurer pour l'exécution du code pour un rôle particulier
    . Étant donné que vous pouvez télécharger un nouveau fichier CSCFG vers Azure
    (sans redéployer votre application), il est relativement simple de
    modifier la valeur de cet élément et de télécharger un nouveau fichier CSCFG afin de
    dynamiquement augmenter ou diminuer le nombre d'instances de rôle
    exécutant votre code d'application. Cela vous permet de facilement mettre à l'échelle votre
    application en fonction des charges de travail réelles, tout en
    contrôlant ce qui vous est facturé pour l'exécution des instances de rôle.

-   **Valeur des paramètres de configuration** : cet élément indique des valeurs de
    paramétrage (comme défini dans le fichier CSDEF). Votre rôle peut lire ces
    valeurs en cours d'exécution. Les valeurs de ces paramètres de configuration sont
    généralement utilisées pour les chaînes de connexion à la base de données SQL ou à 
    Azure Storage, mais vous pouvez les utiliser à d'autres fins.

## <a id="hostedservices"> </a>Création et déploiement d'un service hébergé

Pour créer un service hébergé, vous devez tout d'abord accéder au [portail de gestion Azure] et configurer un service hébergé en spécifiant
un préfixe DNS et le centre de données dans lequel vous souhaitez que votre code soit exécuté
. Ensuite, dans votre environnement de développement, vous créez votre fichier
de définition de service (CSDEF), générez votre code d'application et empaquetez (zip)
tous ces fichiers dans un fichier de package de services (CSPKG). Vous devez également
préparer votre fichier de configuration de service (CSCFG). Pour déployer votre rôle,
vous téléchargez les fichiers CSPKG et CSCFG avec les API
Gestion des services Azure. Une fois déployé, Azure configure les instances de rôle
dans le centre de données (en fonction des données de configuration),
extrait le code de votre application à partir du package, le copie dans les instances de rôle
et redémarre les instances. ﻿Votre code est désormais opérationnel.

La figure ci-dessous montre les fichiers CSPKG et CSCFG que vous avez créés sur votre
ordinateur de développement. Le fichier CSPKG contient le fichier CSDEF et le
code de deux rôles. Après le téléchargement des fichiers CSPKG et CSCFG avec
l'API Gestion des services Azure, Azure crée les instances de rôle
dans le centre de données. Dans cet exemple, le fichier CSCFG indique
qu'Azure doit créer trois instances du rôle \#1 et deux
instances du rôle \#2.

![image][5]

Pour plus d'informations sur le déploiement, la mise à niveau et la reconfiguration de vos
rôles, consultez l'article [Déploiement et mise à niveau des applications Azure][]
.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Références

-   [Création d'un service hébergé pour Azure][]

-   [Gestion des services hébergés dans Azure][]

-   [Migration des applications vers Azure][]

-   [Configuration d'une application Azure][]

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
  [Références]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/fr-fr/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Tarification Azure]: http://www.windowsazure.com/fr-fr/pricing/calculator/
  [Gestion des certificats dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/fr-fr/library/hh560567.aspx]: http://msdn.microsoft.com/fr-fr/library/hh560567.aspx
  [Gestion des mises à niveau vers les systèmes d'exploitation invités d'Azure]: http://msdn.microsoft.com/fr-fr/library/ee924680.aspx
  [Portail de gestion Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Déploiement et mise à jour des applications Azure]: http://www.windowsazure.com/fr-fr/develop/net/fundamentals/deploying-applications/
  [Création d'un service hébergé pour Azure]: http://msdn.microsoft.com/fr-fr/library/gg432967.aspx
  [Gestion des services hébergés dans Azure]: http://msdn.microsoft.com/fr-fr/library/gg433038.aspx
  [Migration des applications vers Azure]: http://msdn.microsoft.com/fr-fr/library/gg186051.aspx
  [Configuration d'une application Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405486.aspx
