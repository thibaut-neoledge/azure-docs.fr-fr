
# Résolution des problèmes dans Azure

La *résolution des problèmes* consiste à repérer et à comprendre un comportement incorrect de l'application et à le corriger. Lorsqu'ils développent une application, les développeurs la testent, la lancent et résolvent les problèmes avant son déploiement dans l'environnement de production. C'est le cas lorsque l'application fonctionne sur un ordinateur ou sur un serveur dans le cloud. Mais dans le cas d'une application à plusieurs instances largement distribuée et conçue pour
être montée en charge, le débogage peut être difficile et exiger plus
que les approches et outils standard.

C'est pour cette raison que les applications cloud doivent être conçues en prenant en compte les contraintes de résolution des problèmes. La façon dont la résolution des problèmes est intégrée à votre application dépend tout d'abord de l'endroit où s'exécute l'application, ensuite des langages et des runtimes sur lesquels l'application s'appuie.

Si vous concevez une application qui utilise une machine virtuelle Azure, vous pouvez la plupart du temps envisager la conception de la résolution des problèmes et du débogage comme vous le feriez sur le système d'exploitation s'il s'exécutait sur vos propres serveurs.

Les applications Azure sont des applications à plusieurs instances largement distribuées qui peuvent être difficiles à déboguer. Ce type d'applications exige plus que les outils et approches standard de résolution des problèmes. Cette rubrique détaille des pratiques
éprouvées de résolution de problèmes et contient des liens vers des
informations plus complètes sur les pratiques mentionnées.

**Remarque** : cette rubrique suppose que la conception de votre
application est en cours ou bien que vous avez déployé votre application Azure, mais qu'un problème s'est produit. Elle ne présente pas le déploiement des applications dans Azure. Pour plus d'informations sur le développement et le déploiement de votre application Azure, consultez la page [https://www.windowsazure.com/en-us/develop/overview/][1].

Cette rubrique décrit tout d'abord certaines des meilleures pratiques pour vous aider à concevoir votre application afin d'être en mesure de résoudre simplement les problèmes éventuels. (Si vous ne concevez pas votre application de façon à pouvoir suivre le flux du code, il peut
être très difficile de repérer les problèmes lorsqu'ils surviennent.)
Ces meilleures pratiques sont valables pour tous les types d'applications qui s'exécutent sous Azure, quels que soient le modèle et le langage de l'application.

Les sections qui suivent décrivent les approches du développement d'applications Azure durables, quel que soit leur type : sites Web, services cloud ou machines virtuelles

Chaque section contient une description générale, ainsi que des liens vers des ressources qui démontrent l'application de ces meilleures pratiques en détail ou décrivent leur mise en œuvre.

## Dans ce document

* [Meilleures pratiques de résolution des problèmes
  Azure](#BestPractices)
* [Sites Web Azure](#Websites)
* [Services cloud Azure](#CloudServices)
* [Machines virtuelles Azure](#Vms)
* [Services Azure](#PlatformServices)
* [Résolution des problèmes de base de données SQL](#SQLTroubleshooting)

<h2><a id="BestPractices" ></a>Meilleures pratiques de résolution des problèmes Azure</h2>


Cette section décrit les meilleures pratiques qui s'appliquent aux applications Azure, quel que soit le modèle d'hébergement ou le langage utilisé. Elle contient des ressources pour la discussion plus détaillée de ces pratiques.

Afin de créer une base solide pour la résolution des problèmes dans Azure, concentrez vos efforts dans ces trois domaines :

* Gestion appropriée des erreurs : *chaque composant du service doit
  pouvoir supporter l'échec de services ou d'infrastructures
  dépendantes.*
* Suivi, journalisation et surveillance : *chaque composant du service
  doit comporter des éléments de débogage, de suivi et de journalisation
  des événements et des erreurs.*
* Débogage à tous les niveaux : *avant de passer l'application en
  production, mais aussi au niveau composant et au niveau réseau lors de
  l'exécution.*

Lorsque l'application est conçue selon ces principes, elle est en mesure de vous fournir les informations nécessaires pour repérer l'origine des problèmes.

### Gestion appropriée des erreurs

Les applications doivent gérer autant que possible les erreurs de façon appropriée. Du fait du caractère distribué d'Azure, cette exigence est encore plus importante. Une résolution des problèmes solide commence par une bonne conception de la gestion des erreurs temporaires. Les erreurs temporaires sont un des domaines dans lesquels les applications cloud ne se comportent pas comme prévu, en raison de circonstances temporaires inhérentes aux applications Internet.

Les erreurs temporaires sont des échecs liés aux latences ou à l'intermittence des connexions réseau, caractéristiques des ressources partagées sur Internet. Voici quelques exemples :

* Les ressources partagées de l'ordinateur, telles que les services
  cloud Azure ou la base de données SQL, peuvent se montrer moins
  réactives de temps à autre.
* Retards de réactivité dus aux exigences de résistance des services.
  Par exemple, SQL Azure conserve plusieurs copies synchronisées des
  bases de données afin d'assurer cette résistance, mais cela a un
  impact sur la réactivité.
* Retards HTTP ou dus à d'autres connexions de protocole se terminant
  avant la fin de la tâche. Par exemple, les requêtes HTTP peuvent ne
  pas atteindre un point de terminaison et revenir avant l'expiration du
  délai.

Afin de réduire l'impact des erreurs temporaires, voici ce que doivent faire les applications Azure :

* Ne doivent pas présenter un lien trop étroit, afin que les composants
  ne soient pas dépendants au niveau local de services qui échouent plus
  souvent que dans un environnement local.
* Passer des appels asynchrones là où il est possible de le faire, afin
  de permettre aux processus de ne pas dépendre de réponses immédiates.
* Utiliser une gestion des erreurs temporaires qui détecte certaines
  catégories d'échecs et peut appliquer un certain nombre de tentatives
  en fonction d'une stratégie configurable.

Les appels aux services doivent contenir ou utiliser une couche de gestion des erreurs temporaires afin de détecter les scénarios d'erreur les plus courants, et relancer les appels en fonction d'une configuration définie. Pour les développeurs .NET, la bibliothèque recommandée est [Microsoft Enterprise Library 5.0 Integration Pack for Azure][2]. La bibliothèque Microsoft Enterprise Library est un ensemble de blocs applicatifs réutilisables qui répondent à des besoins transversaux du développement de logiciels d'entreprise. Le pack Microsoft Enterprise Library Integration Pack for Azure est une extension de Microsoft Enterprise Library 5.0 qui peut être utilisée avec Azure. Il comprend le bloc applicatif de mise à l'échelle automatique, le bloc applicatif de gestion des erreurs temporaires, la source de configuration des objets blob, le fournisseur de configuration protégée, ainsi que des documents de formation. [CloudFx][3] est une autre bibliothèque .NET, plus simple et offrant moins de fonctionnalités. CloudFx est un ensemble de composants et de blocs de construction de qualité, conçu pour accélérer la mise en œuvre de solutions et de services Azure complexes, fiables et extensibles.

### Application du suivi et de la journalisation

En raison de la complexité des applications distribuées et montées en charge, les débogueurs traditionnels qui travaillent sur un processus sont bien moins utiles pour étudier les problèmes qui se présentent à l'exécution de votre application. Ainsi, le suivi et la journalisation sont d'une très grande importance. L'exécution de votre application et de ses données est partagée sur plusieurs services, qui sont hébergés sur différents ordinateurs. Dans une application distribuée à grande
échelle, il est difficile, voire impossible, de déterminer quelle
instance de service lier et déboguer. Le suivi et la journalisation vous permettent de suivre le flux de l'exécution de l'application et de celui des données, ce qui vous donne une meilleure idée de l'état de votre application.

Les applications Azure réussies comportent une stratégie de suivi et de journalisation, intégrée dès la conception de l'application. Ceci réduit le temps et les efforts nécessaires pour repérer les problèmes et les résoudre, sans avoir à appeler le support technique Microsoft.

**Remarque** : l'écriture du suivi et de la journalisation a un impact
sur les performances. Si elle est faite de façon intensive, cet impact est d'autant plus important. Votre application doit donc être conçue avec une stratégie de suivi et de journalisation configurable, qui peut
être ajustée en fonction des besoins. Le niveau de journalisation doit
idéalement être réglable depuis le fichier
**ServiceConfiguration.cscfg** afin de pouvoir le modifier sans
redéploiement.

Le fait d'avoir un grand volume de journaux ne garantit pas la rapidité de détection et de résolution des erreurs. Le déchiffrage d'une grande quantité de données prend du temps et la collecte d'un tel volume a aussi un impact sur les performances de votre application. La journalisation ajustable contrôle la taille des données de journalisation et son impact sur le stockage.

Dans l'article MSDN Magazine [Contrôler la journalisation et le suivi dans Azure][4], Mike Kelly décrit quatre types de sorties de débogage à prendre en considération :

* Sortie de débogage : uniquement dans la version de débogage, comprend
  les affirmations
* Suivi : suit le flux de contrôle au cours de l'exécution
* Journalisation des événements : principaux événements de l'exécution
  du programme
* Journalisation des erreurs : situation exceptionnelle ou dangereuse

Les autres langages, plateformes d'applications ou systèmes d'exploitation peuvent utiliser une autre terminologie de suivi et de journalisation. Si vous utilisez une de ces plateformes de développement sous Azure, utilisez la stratégie et les outils équivalents pour le langage ou la plateforme que vous utilisez.

Les applications en mode mixte sont des applications qui exécutent un mélange de machines virtuelles Azure, de sites Web et de services cloud. Lorsque vous concevez des applications de ce type, le suivi et la journalisation prennent encore plus d'importance, car elles sont distribuées encore plus largement. Pour résoudre les problèmes de ces applications en mode mixte, vous devez suivre le flux des données et d'exécution afin d'identifier les problèmes. Les mécanismes de suivi et de journalisation d'une application en mode mixte dépendent du modèle d'hébergement du composant.

### Surveillance de votre application

Le suivi et la journalisation font partie du domaine de la surveillance. La surveillance permet d'effectuer les opérations suivantes :

* découvrir les applications Azure ;
* déterminer l'état de chaque instance de rôle ;
* collecter et surveiller les informations relatives aux performances, y
  compris la latence et le débit ;
* collecter et surveiller les événements ;
* collecter et surveiller les messages de suivi ;
* surveiller l'utilisation des ressources ;
* surveiller la qualité des mesures de service ;
* planifier la capacité ;
* analyser le trafic (utilisateurs, affichages, heures d'activité
  maximale)
* estimer la facturation ;
* effectuer des audits.

La surveillance se fait à l'aide d'un outil ou d'un ensemble d'outils. L'outil utilisé dépend de la plateforme et/ou des langages utilisés par votre application, ainsi que des objectifs et des besoins de la surveillance.

**Pack Microsoft System Center Monitoring Pack pour applications Azure**

Ce [pack de surveillance][5] vous permet d'utiliser [Microsoft System Center Operations Manager][6] pour surveiller la disponibilité et les performances des applications Azure.

Microsoft System Center Operations Manager 2007 est le meilleur moyen de surveiller l'état de votre application Azure.

**Azure Platform Management Tool**

[Azure Platform Management Tool (MMC)][7] est un autre outil qui vous
permet de gérer les services hébergés et les comptes de stockage Azure. Cet outil est fourni comme exemple, avec son code source complet, pour que vous puissiez voir comment effectuer différentes tâches de gestion et de configuration à l'aide des API d'administration et de diagnostic Azure.

**Outils Cerebrata**

Cerebrata propose un certain nombre d'outils qui vous permettent de surveiller et de gérer vos applications Azure. Ils comprennent [Azure Diagnostics Manager][8], [Cloud Storage Studio][9] et [Azure Management Cmdlets][10].

Azure Diagnostics Manager 2 est un client Windows qui assure la gestion des diagnostics Azure. Il vous permet de consulter, de télécharger et de gérer les données de diagnostic collectées par les applications qui s'exécutent dans Azure. Pour plus d'informations sur ces produits, consultez la page [http://www.cerebrata.com][11].

Cloud Storage Studio 2 est un client Windows qui assure la gestion du stockage Azure.

Azure Management Cmdlets est un jeu d'applets de commandes Windows PowerShell pour la gestion du stockage Azure, des services hébergés, des instances de base de données SQL et des diagnostics. Il contient
également des applets de commande pour sauvegarder et restaurer les
comptes de stockage.

**Surveillance réseau : AlertBot, Gomez, Keynote, Pingdom**

L'application de gestion des performances de Compuware, [Gomez][12],
[Keynote][13], [Pingdom][14] et [AlertBot][15] sont des solutions de
surveillance à distance de votre application Azure. Elles vous permettent de surveiller la disponibilité de votre application et d'optimiser ses performances. Certains services comme Pingdom permettent l'envoi de notifications par courrier électronique, SMS ou par une application lorsqu'une erreur est détectée. Ce type de surveillance simule ce que fait un utilisateur final pour surveiller une application.

**Outils Apica**

Apica propose des outils qui surveillent votre application Web Azure depuis << l'extérieur >>. Pour plus d'informations, consultez la page
[http://www.apicasystem.com/integration-partners/][16].

**AVIcode**

Microsoft a racheté AVIcode, qui fait désormais partie de Microsoft System Center. [AVIcode][17] offre des possibilités de surveillance des performances des applications .NET à l'aide d'une gamme complète de fonctions de surveillance des applications.

**Profilage des performances**

Vous pouvez créer un [profil][18] de votre application Azure lorsqu'elle s'exécute dans Azure afin de repérer les problèmes de performances. Lorsque vous publiez votre application Windows Azure depuis Visual Studio, vous pouvez choisir un profil pour l'application et sélectionner les paramètres de profil qui vous conviennent.

**Assistant MV Azure**

L'outil [Assistant MV][19] est un projet CodePlex qui rassemble toutes les données de résolution des problèmes utiles lorsque vous vous connectez à une instance de machine virtuelle avec le Bureau à distance. Le bouton **VM Health** présente l'état actuel de l'instance.

### Débogage à tous les niveaux

Avant de déployer une application dans Azure, il est recommandé de la déboguer en local. Le Kit de développement logiciel (SDK) Azure contient des émulateurs qui reproduisent l'environnement cloud d'Azure, ce qui vous permet d'exécuter votre application et d'effectuer les tests de base sans avoir à déployer l'application. Les outils de débogage varient en fonction du langage de programmation et des outils de développement que vous utilisez.

Après le déploiement de l'application, vous pouvez lancer le débogage dans le cloud avec un débogueur comme Visual Studio. Ceci nécessite de créer une version de débogage et de la déployer. Pour cela, vous devez vous connecter à une instance de rôle spécifique. Si votre application est complexe, avec de nombreux rôles et instances de rôle, il peut être très difficile de déterminer à quelle instance de rôle se connecter. Visual Studio Ultimate prend en charge IntelliTrace, qui permet aux rôles .NET de suivre les informations de débogage. En cas de problème, vous pouvez télécharger ces informations et les charger dans Visual Studio. Vous pouvez consulter le journal IntelliTrace de chaque instance de rôle pour déterminer où le problème s'est produit. Bien que le débogage dans le cloud présente certains inconvénients, il peut être nécessaire dans certains cas. Tous les services Azure ne disposent pas d'un émulateur (Service Bus par exemple) et tous les outils de développement pris en charge (Mac et Linux par exemple) ne comportent pas d'émulateurs.

Une fois que vous avez débogué votre application en local, vous devrez très certainement vous appuyer sur les fonctions intégrées à votre application pour déterminer où les problèmes se produisent.

**Débogage Node.js**

Pour le débogage des applications Node.JS, vous pouvez utiliser l'outil Node Inspector, disponible sur [GitHub][20]. Node Inspector peut être exécuté en local sur votre ordinateur de développement à l'aide de l'émulateur de stockage Azure. Pour plus d'informations, consultez le blog de Jim Wang : [Débogage de nœuds avec l'émulateur Azure][21].

Si vous déboguez votre application dans Azure, installez la version complète d'IISNode depuis [GitHub][22] sur votre rôle Web, votre rôle de travail ou votre instance de machine virtuelle. Comme présenté plus haut, cette méthode de débogage de l'application n'est pas recommandée lorsqu'elle est en production et divisée en plusieurs instances, car il est difficile de déterminer quelle instance de rôle ou de machine virtuelle doit être déboguée.

Pour utiliser Node-Inspector sur un rôle Web, installez le package dans ce dernier et utilisez l'outil Node-Inspector comme d'habitude. Pour plus d'informations sur le débogage avec Node Inspector, consultez la page [Débogage avec Node Inspector][23].

**IntelliTrace**

Microsoft Visual Studio Ultimate comprend [IntelliTrace][24], qui peut
être activé pour déboguer les applications avant leur déploiement en
production. IntelliTrace prend en charge les applications ASP.NET et WCF. Intellitrace n'est pas pris en charge dans un service de production, mais peut être utilisé pour obtenir les exceptions d'une application après déploiement dans Azure. L'article sur le blog de Jim Nakashima décrit l'utilisation d'[IntelliTrace pour déboguer les services cloud Azure].

**Fiddler**

[Fiddler][25] est un proxy de débogage Web qui collecte tout le trafic
HTTP(S) entre votre ordinateur et Internet. Il vous permet d'inspecter le trafic, de définir des points d'arrêt et de modifier les données entrantes et sortantes. Il est particulièrement utile pour résoudre les problèmes de stockage Azure.

Pour utiliser Fiddler sur la structure de développement locale, utilisez ipv4.fiddler plutôt que 127.0.0.1 :

* Lancez Fiddler.
* Lancez votre service dans la structure de développement.
* Accédez à http://ipv4.fiddler:/. Fiddler doit suivre la demande.

Pour utiliser Fiddler sur le stockage de développement local, modifiez le fichier de configuration de service pour qu'il pointe sur Fiddler.

Ouvrez le fichier ServiceConfiguration.cscfg et modifiez la chaîne de connexion comme suit :

    Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

* Lancez Fiddler.
* Lancez votre service. Fiddler doit suivre toutes les demandes de
  stockage.

## Résolution des problèmes et modèles d'hébergement Azure

Cette section présente les meilleures pratiques de débogage des applications à l'aide des différents modèles d'hébergement Azure.

<h2><a id="Websites" ></a>Sites Web Azure</h2>


Lorsque vous concevez un site Web Azure durable, suivez autant que possible les recommandations faites plus haut dans ce document, ce qui comprend la vérification et la gestion des erreurs, l'application d'une logique des nouvelles tentatives pour les erreurs temporaires, le suivi et la journalisation. La résolution des problèmes des sites Web Azure se fait en configurant les sites pour qu'ils affichent les erreurs de l'application, en configurant les diagnostics du site Web, en collectant les données de diagnostic, puis en analysant ces données collectées pour identifier et résoudre les problèmes.

Les sites Web Azure permettent la configuration des options suivantes :

* journalisation du serveur Web ;
* messages d'erreur détaillés ;
* suivi des demandes ayant échoué.

Pour plus d'informations, consultez la rubrique : [Résolution des problèmes d'un site Web Azure](/en-us/develop/net/best-practices/troubleshooting-web-sites/).

Lorsque les journaux de serveur Web sont activés pour les sites Web Azure, le site Web enregistre toutes les transactions HTTP dans un fichier journal selon le [format de journal étendu du W3C][26]. Vous pouvez ensuite utiliser [Log Parser][27] pour lancer des requêtes sur le fichier journal. Vous trouverez des exemples de requêtes sur [Log Parser Plus][28] et [Exemples d'utilisation de Log Parser sur TechNet][29]. Si vous souhaitez générer le type de sortie CHART sur un ordinateur équipé d'Office 2007/2010, installez les [Composants Web d'Office 2003][30] selon les instructions de [Log Parser Plus][31].

Les sites Web Azure utilisent la même fonctionnalité de suivi des demandes ayant échoué disponible dans IIS 7.0 et versions ultérieures. Cette fonctionnalité n'est cependant pas configurable comme le suivi des demandes ayant échoué d'IIS. Lorsque vous activez le suivi des demandes ayant échoué pour les sites Web Azure, **toutes** les demandes ayant
échoué sont conservées.

<h2><a id="Cloudservices" ></a>Services cloud Azure</h2>


En raison du caractère distribué des services cloud Azure, il est important de défendre votre application en passant les appels de façon asynchrone et en gérant les nouvelles tentatives pour les échecs temporaires, comme décrit précédemment.

La technique de débogage employée pour les services cloud Azure dépend du type de problème rencontré. Les problèmes impliquant un rôle ou une instance de rôle spécifique, par exemple un rôle qui ne démarre pas ou effectue un cycle, peuvent être mieux étudiés avec le Bureau à distance. Dans ce cas, vous savez quel rôle ou quelle instance de rôle pose problème et vous pouvez vous connecter au rôle concerné. Lorsqu'un problème survient et que vous n'êtes pas certain de l'instance de rôle à l'origine du problème, le suivi et la journalisation sont la meilleure méthode de résolution des problèmes. Azure Diagnostics comprend un mécanisme de collecte et de gestion des informations de suivi et de journalisation.

Certaines fonctionnalités de débogage ont été ajoutées au Kit de développement logiciel (SDK) Azure 1.7. Elles permettent de rechercher les suivis de la pile en cas d'exception. D'autres améliorations assurent une meilleure connexion avec le Bureau à distance. Les suivis de pile font maintenant partie du journal des événements Windows, ce qui permet de voir plus facilement ce qui pose problème. De plus, la connexion avec le Bureau à distance a été améliorée. Si votre rôle effectue un cycle ou est annulé, vous pouvez utiliser le Bureau à distance pour vous y connecter et étudier le problème.

Le portail Azure offre l'accès aux données de surveillance, qui peuvent aider les professionnels de l'informatique et les développeurs à anticiper et à diagnostiquer les problèmes des services cloud Azure. Par défaut, les données telles que le pourcentage processeur, les données entrantes, les données sortantes et le débit d'écriture sur le disque sont collectées par la machine virtuelle hôte. Aucune configuration n'est nécessaire pour activer ces mesures pour les instances de rôle, et l'impact en termes de coût est inexistant pour les clients. Des informations supplémentaires sur les performances peuvent aussi être collectées. Afin de collecter des informations de diagnostic détaillées, vous devez disposer d'une chaîne de connexion de diagnostic valide, car ces informations sont stockées dans le stockage Azure et entraînent donc des coûts de stockage supplémentaires. Lorsque l'utilisateur active la surveillance détaillée, le portail configure des instances de rôle à distance pour collecter le jeu de compteurs de performances par défaut.

### Azure Diagnostics

Le Kit de développement logiciel (SDK) Azure 1.0 comprenait des fonctionnalités pour collecter les données de diagnostic et les placer dans le stockage Azure. Ces fonctionnalités portaient le nom de
<< diagnostic Azure >>. Ce logiciel basé sur l'infrastructure Suivi
d'événements pour Windows répond à deux besoins de conception dus à l'architecture de montée en charge d'Azure :

* Enregistrer les données de diagnostic qui seraient perdues en cas de
  création d'une nouvelle image de l'instance.
* Servir de référentiel pour le diagnostic de plusieurs instances.

Après avoir configuré le diagnostic dans le rôle, les données associées sont collectées dans toutes les instances de ce rôle. Celles-ci peuvent servir au débogage et à la résolution des problèmes, à mesurer les performances, à suivre l'utilisation des ressources, à analyser le trafic et à prévoir et analyser la capacité. Les transferts vers le compte de stockage Azure pour garantir la persistance peuvent se faire à la demande ou bien de façon planifiée.

Les diagnostics Azure modifient la configuration du serveur de quatre manières :

* Le diagnostic doit être activé au moment de la création de
  l'application.
* Des outils et procédures spécifiques sont nécessaires pour voir les
  résultats de diagnostic.
* Les échecs entraînent la perte des données de diagnostic, sauf si
  elles se trouvent sur un support de stockage durable (stockage Azure,
  et non sur chaque instance).
* Le stockage du diagnostic entraîne des frais mensuels s'il se trouve
  sur le stockage Windows Azure.

Le coût est particulièrement important, car un des avantages principaux de Azure est la réduction des coûts. Le seul moyen actuellement disponible pour éliminer le coût du diagnostic est de laisser les données sur la machine virtuelle. Ceci peut fonctionner dans un déploiement de petite taille, mais n'est pas pratique dans le cas où il existe de nombreuses instances. Voici quelques moyens par lesquels l'impact financier peut être réduit :

* Vérifiez que le compte de stockage se trouve dans le même centre de
  données que votre application. Si ce n'est pas le cas, choisissez avec
  soin la fréquence des transferts planifiés. Une fréquence de transfert
  élevée augmente la pertinence des données, mais ce compromis peut ne
  pas justifier la bande passante supplémentaire et le surdébit de
  traitement.
* Copiez régulièrement les données de diagnostic et effacez-les du
  stockage Azure. Les données de diagnostic transitent par le stockage
  Azure, mais n'y restent pas nécessairement. Il existe plusieurs outils
  pour effectuer ces opérations : System Center Monitoring Pack for
  Azure, Azure Diagnostics Manager de Cerebrata et les applets de
  commande PowerShell Azure.
* Choisissez uniquement les données de diagnostic dont vous avez besoin
  pour surveiller votre application et résoudre les problèmes. Si vous
  collectez trop de données, la résolution des problèmes risque d'être
  trop complexe, en plus de générer des frais supplémentaires.
* Contrôlez la collecte et la portée des données de diagnostic en
  mettant en place un commutateur à la demande dans votre application.
* Utilisez le niveau de journalisation (détaillé, info, avertissement,
  erreur) afin que toutes les informations soient disponibles, puis
  utilisez la configuration de diagnostic post-déploiement pour
  collecter les données de façon plus précise.

<h2><a id="Vms" ></a>Machines virtuelles Azure</h2>


La résolution des problèmes d'applications exécutées sur des machines virtuelles Azure recourt normalement aux mêmes techniques que celles employées sur les plateformes et le système d'exploitation utilisés. Par exemple :

* Windows Server 2008 R2 x64 (RTM et SP1), Windows 8 Service x64
  (Datacenter et Core). Il existe de très nombreuses informations sur ce
  sujet, et de nombreux outils pour vous aider.

* Pour plus d'informations sur les conditions, consultez la page
  [Comment : déboguer les applications de service Windows][32].

* Pour regarder une vidéo illustrant les meilleures techniques,
  consultez la [vidéo de Daniel Pearson sur le débogage et la résolution
  des problèmes Windows][33].

* Open Suse Linux. Il existe également un très grand nombre de
  ressources pour la résolution des problèmes des applications sur Suse
  Linux, à la fois dans la [documentation de Suse Linux][34] et sur
  Internet.
* Ubuntu Linux. Là aussi, il existe une grande quantité d'informations.
  Pour commencer par la documentation du produit, consultez la page
  [https://help.ubuntu.com/][35].
* CentOS Linux. Pour plus d'informations, consultez la page
  [http://centos.org/][36].

<h2><a id="PlatformServices" ></a>Résolution des problèmes des services Azure</h2>


La plupart des services Azure, tels que la base de données SQL Azure, Azure Active Directory et le stockage Azure ont des conseils de résolution des problèmes qui leur sont propres, que l'application s'exécute ou non sous Azure, quel que soit le langage utilisé, les bibliothèques de programmation sur lesquelles l'application est basée ou que l'application s'exécute sur un système d'exploitation Microsoft ou non. Les informations qui suivent présentent les meilleures pratiques spécifiques à certains de ces services.

De nombreuses bibliothèques mettent en œuvre les meilleures pratiques pour les appels asynchrones, le suivi et la journalisation des
événements, tel que décrit dans la partie de ce document consacrée à la
conception.

#### Résolution des problèmes du stockage Azure

Les liens qui suivent présentent des architectures ou des pratiques visant à limiter les problèmes à résoudre ou bien les emplacements dans lesquels vous devez ajouter le suivi ou la journalisation.

* Codes d'état et codes d'erreur du stockage :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179382.aspx][37]

* Analyse du stockage :
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx][38]
  (voir les trois liens << pour plus d'informations >> en bas de la
  page).

* Présentation des stratégies de nouvelles tentatives dans la
  bibliothèque cliente du stockage Azure :
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx][39]

* Comment tirer le meilleur parti des tables Azure :
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx][40]
  
  * Consultez ces sections : Meilleures pratiques, Conseils de
    programmation pour Azure, ainsi que << Expiration du délai et
    ServerBusy, est-ce normal ? >>

* Protection des objets blob contre les erreurs des applications :
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx][41]

**Explorateurs du stockage Azure**

Il existe plusieurs manières d'explorer le stockage Azure. L'équipe du stockage Windows Azure a créé une [liste d'explorateurs][42]. Tous ces outils vous permettent d'afficher les fichiers de diagnostic et les fichiers d'analyse du stockage Azure. [Explorer for Azure Blob Storage][43] de Cloudberry Lab offre une interface utilisateur permettant d'activer l'analyse du stockage directement dans l'application en cliquant sur **Storage Settings**.

<h2><a id="Misc" ></a>Résolution des problèmes Azure Service Bus</h2>


Cette section présente des informations générales sur le développement d'une application qui utilise Azure Service Bus de façon robuste et durable afin de réduire le nombre de problèmes possibles. Elle contient
également des détails sur l'identification et la résolution des
problèmes les plus courants de Service Bus.

### Conseils généraux

Service Bus est un [bus de services d'entreprise][44] adapté à Internet qui prend en charge les fonctions de messagerie répartie et de messagerie par relais. Service Bus applique des quotas et des seuils au niveau du système pour les deux types de messagerie. Si votre application dépasse ces quotas, elle sera limitée, ou vos requêtes ou vos messages seront rejetés. Pour plus d'informations sur les quotas définis par Service Bus et le comportement qui se produit lorsqu'ils sont dépassés, consultez la page [Quotas Azure Service Bus][45]. Certains quotas sont définis par l'utilisateur, par exemple la taille de la file d'attente ou de la rubrique, définie lors de la création de l'entité.

Afin de voir l'entité de messagerie Service Bus et de savoir comment elle est traitée, vous pouvez utiliser [Service Bus Explorer][46] ou l'Explorateur de serveurs dans les outils Azure pour Visual Studio
(version 1.7 ou ultérieure) pour créer, supprimer et tester des files
d'attente, des rubriques, des abonnements et des règles. C'est un bon moyen de résoudre les problèmes d'une application qui s'exécute, mais qui ne traite pas les données comme vous le souhaitez. Ces outils comprennent des fonctions permettant de tester des files d'attente, des rubriques et des entités de relais, de suivre des opérations effectuées par les tâches de l'expéditeur et du destinataire, de surveiller la progression et les performances d'un passage de test en cours et de générer des journaux détaillés des résultats, avec les messages d'erreur
éventuels.

### Service Bus Relay

Le service Service Bus Relay s'exécute dans le cloud et prend en charge plusieurs protocoles de transport et normes de services Web, dont SOAP, WS-\* et REST. Vous pouvez l'utiliser comme délégué pour écouter les sessions entrantes et les demandes envoyées au service WCF. Dans le modèle de messagerie par relais, un service local ou basé dans le cloud se connecte au service de relais via un port sortant et crée un socket bidirectionnel lié à des adresses de rendez-vous spécifiques. Le client n'a pas à savoir où se trouve le service et le service sur site n'a pas besoin d'ouvrir de ports entrants sur le pare-feu. Cependant, en fonction de votre configuration réseau, vous risquez de rencontrer des problèmes lors de la connexion au relais Service Bus derrière un pare-feu ou via un serveur proxy. La page [Hébergement derrière un pare-feu avec Service Bus][47] décrit comment résoudre ces problèmes de connexion.

### Files d'attente et rubriques Service Bus

Les files d'attente et les rubriques Service Bus comportent des fonctionnalités de messagerie répartie : les messages sont envoyés vers la file d'attente ou la rubrique Service Bus où ils sont conservés en attendant que le destinataire soit en mesure de les traiter. Les expéditeurs et les destinataires des messages n'ont pas besoin d'être en ligne en même temps. L'infrastructure de messagerie stocke de façon fiable les messages jusqu'à ce que la partie chargée du traitement soit prête. L'API de messagerie peut rencontrer plusieurs erreurs qui peuvent avoir un impact sur votre application. Elles peuvent être regroupées en quatre grandes catégories :

* Erreur de l'utilisateur, par exemple un code indiquant un argument non
  valide. Action recommandée : essayez de corriger le code avant de
  poursuivre.
* Erreur d'installation ou de configuration : par exemple une file
  d'attente ou une rubrique associée à l'action n'existe pas ou a été
  supprimée. Action recommandée : révisez la configuration et
  modifiez-la si besoin.
* Erreur temporaire : par exemple une réponse indiquant que le service
  n'a pas pu traiter la demande à un moment donné. Action recommandée :
  relancez l'opération ou avertissez les utilisateurs. Pour plus
  d'informations, consultez la page [Erreurs de communication
  transitoires][48].
* Autres erreurs : par exemple les erreurs de délai d'attente ou les
  erreurs indiquant que le verrouillage d'un message a été perdu. Action
  recommandée : vous ne gérez normalement pas ces exceptions pour
  effectuer des nettoyages ou des annulations. Elles peuvent être
  utilisées pour le suivi.

La page [Exceptions de messagerie][49] offre un aperçu des exceptions que les utilisateurs des bibliothèques client de Service Bus pour .NET peuvent rencontrer. Elle présente également des recommandations sur la gestion de chaque type d'exception. Comme les bibliothèques clientes de
.NET sont étroitement liées à la structure des bibliothèques Service Bus
des autres langages, ces conseils peuvent être utiles, même si vous ne programmez pas avec un langage .NET. Dans certains cas, par exemple pour les erreurs temporaires, vous pouvez réessayer la même action. Vous pouvez suivre les recommandations sur la gestion des erreurs temporaires présentées plus haut dans cet article afin de gérer les erreurs temporaires de façon efficace. En outre, pour plus de détails, de meilleures pratiques et d'exemples de code qui démontrent la gestion des erreurs Service Bus temporaires dans une application .NET, consultez la section [Erreurs de communication transitoires][48] de l'article
[Meilleures pratiques pour tirer parti de l'API de messagerie répartie
d'Azure Service Bus][50] de la documentation sur le développement d'applications Azure.

Un autre domaine auquel vous devez prêter attention lors du développement d'une application qui utilise la messagerie répartie est de vous assurer de mettre en œuvre une logique de réception des messages fiable qui peut gérer de façon efficace et précise les anomalies. La section sur la mise en œuvre de boucles de réception de messages fiables de l'article [Meilleures pratiques pour tirer parti de l'API de messagerie répartie d'Azure Service Bus][50] décrit un certain nombre de techniques d'utilisation du mode de réception **PeekLock**, qui est le mode qui prend en charge la livraison de messages multiple si le message n'est pas correctement livré la première fois. Cet article recommande certaines pratiques afin que votre application ne traite pas les messages en double. Il aide également à éviter les problèmes dus aux délais de verrouillage et améliore les performances générales en mode
**PeekLock** en assurant un traitement rapide des messages. Cet article
contient également des exemples de code qui utilisent les bibliothèques clientes Service Bus pour .NET.

### Autres ressources de résolution des problèmes

Pour plus de détails sur les erreurs Service Bus les plus courantes et leur résolution, consultez la page [Résolution des problèmes relatifs à Service Bus][51].

## Service de contrôle d'accès (ACS) Azure Active Directory

Résolution des problèmes

* Codes d'erreur :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/gg185949.aspx][52]
* Limitations du service de contrôle d'accès (ACS) :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/gg185909.aspx][53]

<h2><a id="SQLTroubleshooting" ></a>Résolution des problèmes de base de données SQL Azure</h2>


Lorsque vous communiquez avec une base de données SQL Azure, soyez particulièrement prudent en raison du caractère distribué des applications de base de données SQL Azure. Cette section présente plusieurs domaines auxquels vous devez prêter attention. Il ne s'agit en aucun cas d'une liste exhaustive. La clé du succès lorsqu'il s'agit de créer du code de base de données SQL Azure durable est d'examiner les codes de retour et de s'assurer que le code des nouvelles tentatives peut gérer les échecs.

Votre application doit gérer les échecs de connexion de façon appropriée. Les instances de base de données SQL nécessitent l'utilisation de l'authentification SQL. Si la connexion échoue, soit vos informations de connexion ne sont pas valides, soit la base de données demandée n'est pas disponible.

Votre application doit gérer le cas où le service n'est pas accessible. Si le serveur est déjà provisionné et que le service de base de données SQL Azure est disponible (vous pouvez le vérifier sur la page
[présentant l'état d'Azure][54]), le problème vient très certainement de
problèmes de configuration sur votre installation sur site. Par exemple, il est possible que vous ne soyez pas en mesure de résoudre le nom (qui peut être testé avec des outils comme tracert), qu'un pare-feu qui bloque le port 1433 utilisé par la base de données SQL ou que le serveur proxy que vous utilisez ne soit pas correctement configuré. Pour résoudre ces problèmes, appliquez les mêmes techniques que pour SQL Server. Pour plus d'informations, consultez les pages [Guide de résolution des problèmes de connexion à la base de données SQL][55] et
[Dépannage de Base de données SQL Windows Azure][56].

Votre application doit pouvoir gérer les erreurs réseau générales. Vous risquez de recevoir des erreurs réseau générales, car la base de données SQL Azure peut déconnecter des utilisateurs dans les circonstances qui suivent :

* Lorsqu'une connexion est restée trop longtemps inactive.
* Lorsqu'une connexion consomme trop de ressources ou retient une
  transaction pendant une longue période.
* Si le serveur est trop occupé.

Pour améliorer les performances de la base de données SQL Azure, utilisez les mêmes techniques qu'avec une base de données SQL Server. Pour plus d'informations, consultez les rubriques suivantes :

* [Dépannage des requêtes][57] dans la documentation SQL Server en ligne
* [Résolution et optimisation des requêtes avec les bases de données
  SQL][58]
* [Performances et résolution des problèmes des bases de données
  SQL][59]
* [Amélioration des performances d'entrée/sortie][60]
* [Pack d'administration System Center pour SQL Server][61]

La base de données SQL Azure utilise un sous-ensemble de messages d'erreur de SQL Server. Pour plus d'informations sur les erreurs SQL Server, consultez la page [Références relatives aux erreurs et aux
événements (moteur de base de données)][62] dans la documentation SQL
Server en ligne.

Si vous avez besoin de récupérer des noms de connexion ou des mots de passe, contactez votre administrateur de service, qui peut vous donner accès au serveur et à la base de données. Les administrateurs de service peuvent aussi réinitialiser leur propre mot de passe à l'aide du portail de gestion Azure.

Les requêtes de base de données SQL peuvent échouer pour de nombreuses raisons : requête incorrectement formulée, problèmes réseau, etc. Certaines erreurs sont temporaires, c'est-à-dire que le problème disparaît en général de lui-même. Pour ce type d'erreurs, il est possible de relancer la requête après avoir attendu quelques instants. Si la requête échoue encore après plusieurs tentatives, vous pouvez signaler une erreur. Bien entendu, toutes les erreurs ne sont pas temporaires. L'erreur SQL 102 << Syntaxe incorrecte >> ne disparaît pas, quel que soit le nombre de fois où vous relancez la requête. En bref, la mise en place d'une logique robuste de gestion des nouvelles tentatives demande de la réflexion. Un jeton d'erreur TDS (Tabular Data Stream) est envoyé avant la déconnexion des utilisateurs, si possible. Afin d'améliorer le fonctionnement de l'application, nous vous recommandons de mettre en place une logique des nouvelles tentatives dans vos applications de base de données SQL afin de capter ces erreurs. En cas d'erreur, rétablissez la connexion, puis réexécutez les commandes ou la requête ayant échoué. Pour plus d'informations, consultez les liens suivants :

* [Logique des nouvelles tentatives pour les erreurs temporaires dans la
  base de données SQL][63]
* [Exemple de logique des nouvelles tentatives pour la base de données
  SQL][64]
* [Bloc applicatif de gestion des erreurs temporaires][65]

### Stratégie de sauvegarde et de restauration de la base de données SQL Azure

La base de données SQL Azure doit disposer de sa propre stratégie de sauvegarde et de restauration, en raison de l'environnement et des outils disponibles. Les risques sont largement réduits du fait que la base de données se trouve dans les centres de données Microsoft. Les outils dont nous disposons à l'heure actuelle couvrent les autres facteurs de risque, mais de nouveaux outils vont arriver, qui vont simplifier le travail. Red Gate a récemment publié un outil gratuit de sauvegarde et de restauration de bases de données SQL. Vous le trouverez ici : [http://www.red-gate.com/products/dba/sql-azure-backup/][66].

La synchronisation des données SQL vous permet de facilement créer et planifier des synchronisations bidirectionnelles depuis le site Web Synchronisation des données, sans avoir à écrire une seule ligne de code. Pour plus d'informations, consultez la page suivante :
[http://msdn.microsoft.com/fr-fr/library/windowsazure/hh456371.aspx][67].

Pour plus d'informations sur les stratégies de sauvegarde et de restauration de la base de données SQL, consultez les articles suivants :

* Cette rubrique offre un aperçu des stratégies de sauvegarde et de
  restauration de la base de données SQL :
  [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx][68]
* Cette rubrique explique comment sauvegarder une base de données dans
  une autre base de données sur le même serveur :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/ff951631.aspx][69]
* Cette rubrique explique comment exporter une instance de base de
  données SQL vers un objet blob sur un compte de stockage donné :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/hh335292.aspx][70]
* Cette rubrique explique comment importer une instance de base de
  données SQL à partir d'un fichier bacpac stocké dans un objet blob :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/hh335291.aspx][71]
* Cette rubrique décrit les capacités de continuité des activités de
  l'entreprise offertes par la base de données SQL :
  [http://msdn.microsoft.com/fr-fr/library/windowsazure/hh852669.aspx][72]

<h2><a id="Cache" ></a>Mise en cache Azure</h2>


Azure Caching existe en deux versions : Azure Shared Caching et Azure Caching (Preview), basé sur les rôles. Shared Caching est un service Azure multi-locataire qui fournit des services de mise en cache. Azure Caching (Preview) héberge la mise en cache sur un rôle à l'aide d'une partie de la mémoire des machines virtuelles qui hébergent vos instances de rôle. Afin de résoudre les problèmes d'Azure Caching, observez le comportement du cache en vérifiant les codes d'erreur et en détectant les exceptions. Lorsque vous utilisez Caching (Preview) basé sur les rôles, vous pouvez également utiliser les compteurs de performances. Les erreurs de mise en cache peuvent généralement être classées dans une des catégories suivantes :

* Erreurs de quota : un quota a été dépassé (Shared Caching)
* Limitation : lorsque la mémoire physique n'est plus suffisante pour
  prendre en charge d'autres éléments en cache.
* Éviction : les éléments sont rejetés de force pour faire de la place
  à de nouveaux éléments et cette éviction se fait d'une manière qui
  affecte les performances de l'application.
* Expiration : les délais d'expiration sont trop courts ou trop longs.

Pour plus d'informations sur les erreurs liées aux quotas, consultez la page [Présentation des quotas pour Windows Azure Shared Caching][73].



[1]: https://www.windowsazure.com/en-us/develop/overview/
[2]: http://msdn.microsoft.com/en-us/library/hh680918%28v=pandp.50%29.aspx
[3]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[4]: http://msdn.microsoft.com/en-us/magazine/ff714589.aspx
[5]: http://www.microsoft.com/download/en/details.aspx?id=11324
[6]: http://www.microsoft.com/en-us/server-cloud/system-center/operations-manager.aspx
[7]: http://wapmmc.codeplex.com/
[8]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[9]: http://cerebrata.com/Products/CloudStorageStudio/
[10]: http://cerebrata.com/Products/AzureManagementCmdlets/
[11]: http://www.cerebrata.com
[12]: http://www.compuware.com/application-performance-management/
[13]: http://www.keynote.com/solutions/
[14]: http://pingdom.com/
[15]: http://www.alertbot.com/products/website-monitoring/default.aspx
[16]: http://www.apicasystem.com/integration-partners/
[17]: http://www.microsoft.com/en-us/server-cloud/system-center/avicode.aspx
[18]: http://msdn.microsoft.com/en-us/library/windowsazure/hh369930.aspx
[19]: http://azurevmassist.codeplex.com/
[20]: https://github.com/dannycoates/node-inspector
[21]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[22]: https://github.com/windowsazure/iisnode/downloads
[23]: http://howtonode.org/debugging-with-node-inspector
[24]: http://msdn.microsoft.com/en-us/library/dd264915.aspx
[25]: http://www.fiddler2.com/fiddler2/
[26]: http://go.microsoft.com/fwlink/?LinkID=90561
[27]: http://go.microsoft.com/fwlink/?LinkId=246619
[28]: http://logparserplus.com/Examples
[29]: http://technet.microsoft.com/en-us/library/ee692659.aspx
[30]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[31]: http://logparserplus.com/article/2
[32]: http://msdn.microsoft.com/en-us/library/7a50syb3%28v=vs.90%29.aspx
[33]: http://technet.microsoft.com/en-us/edge/Video/hh867800
[34]: https://www.suse.com/documentation/
[35]: https://help.ubuntu.com/
[36]: http://centos.org/
[37]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179382.aspx
[38]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx
[39]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx
[40]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
[41]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx
[42]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[43]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[44]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[45]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732538.aspx
[46]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[47]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706729.aspx
[48]: http://msdn.microsoft.com/en-us/library/hh851746(VS.103).aspx
[49]: http://msdn.microsoft.com/en-us/library/hh418082.aspx
[50]: http://msdn.microsoft.com/en-us/library/windowsazure/hh545245.aspx
[51]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706702.aspx
[52]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185949.aspx
[53]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185909.aspx
[54]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[55]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[56]: http://msdn.microsoft.com/en-us/library/ee730906.aspx
[57]: http://msdn.microsoft.com/en-us/library/ms186351(SQL.100).aspx
[58]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[59]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[60]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[61]: http://www.microsoft.com/en-us/download/details.aspx?id=10631
[62]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[63]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[64]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[65]: http://msdn.microsoft.com/en-us/library/hh680934(PandP.50).aspx
[66]: http://www.red-gate.com/products/dba/sql-azure-backup/
[67]: http://msdn.microsoft.com/en-us/library/windowsazure/hh456371.aspx
[68]: http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx
[69]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951631.aspx
[70]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335292.aspx
[71]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335291.aspx
[72]: http://msdn.microsoft.com/en-us/library/windowsazure/hh852669.aspx
[73]: http://msdn.microsoft.com/en-us/library/gg185683.aspx