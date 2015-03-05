<properties 
	pageTitle="Déploiement de SharePoint 2010 sur des machines virtuelles Azure" 
	description="Présentation des scénarios pris en charge pour l'utilisation de SharePoint 2010 sur des machines virtuelles Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/16/2015" 
	ms.author="josephd"/>




<h1>Déploiement de SharePoint 2010 sur des machines virtuelles Azure</h1>
<h2>Résumé</h2>

Microsoft SharePoint Server 2010 offre une grande flexibilité de déploiement, qui permet aux organisations de déterminer les scénarios de déploiement appropriés afin d'aligner leurs besoins et leurs objectifs. Hébergé et géré dans le cloud, Azure Virtual Machines offre une infrastructure complète, fiable et disponible afin de prendre en charge les charges de travail des différentes applications et bases de données à la demande, par exemple les déploiements Microsoft SQL Server et SharePoint.


Bien qu'Azure Virtual Machines prenne en charge différentes charges de travail, ce document se concentre sur les déploiements SharePoint. Azure Virtual Machines permet aux organisations de créer et de gérer la mise en service rapide et l'accès universel à presque chaque hôte de leur infrastructure SharePoint. Azure Virtual Machines permet une gestion et un contrôle complets des processeurs, de la mémoire vive (RAM), des plages d'unité centrales et d'autres ressources des machines virtuelles SharePoint.

Azure Virtual Machines limite le besoin en matériel afin que les organisations puissent se tourner de la gestion du coût élevé initial et de la complexité vers la création et la gestion d'une infrastructure à l'échelle. Cela signifie qu'elles peuvent innover, expérimenter et itérer en quelques heures au lieu des jours et des semaines que prennent les déploiements traditionnels.

<h3>Qui doit lire ce document ?</h3>

Ce document est destiné aux professionnels de l'informatique. En outre, les preneurs de décisions techniques, comme les architectes et les administrateurs système, peuvent utiliser ces informations et les scénarios fournis afin de planifier et de concevoir une infrastructure SharePoint virtualisée sur Azure.

<h3>Pourquoi lire ce document ?</h3>

Ce document explique comment les organisations peuvent configurer et déployer SharePoint au sein d'Azure Virtual Machines. Il explique également pourquoi ce type de déploiement peut être bénéfique aux organisations de toutes tailles.

<h2>Passage au cloud computing</h2>

Selon le cabinet Gartner, le cloud computing est défini comme un " style d'informatique où des fonctionnalités informatiques fortement extensibles sont fournies 'as a service' à des clients externes à l'aide des technologies Internet. " Les mots clés de cette définition sont " extensibles, services et Internet ". En bref, le cloud computing peut être défini comme des services informatiques qui sont <strong>déployés et délivrés par le biais d'Internet</strong> et qui sont <strong>évolutifs sur demande</strong>.

Indéniablement, le cloud computing représente une évolution majeure de l'informatique d'aujourd'hui. Par le passé, on ne parlait que de consolidation et de coût. Désormais, la discussion porte sur la nouvelle gamme d'avantages que le cloud computing peut offrir. Cela consiste uniquement à transformer la manière dont l'informatique est au service des organisations en maîtrisant une nouvelle forme de pouvoir. Le cloud computing modifie fondamentalement le monde de l'informatique, impactant chaque rôle : des fournisseurs de service et architectes système aux développeurs et utilisateurs finaux.

Des recherches ont révélé que la souplesse, le ciblage et l'économie sont les trois moteurs principaux de l'adoption du cloud :

<ul>
<li><p><strong>Agilité</strong>: le cloud computing peut accélérer la capacité d'une organisation à capitaliser sur de nouvelles opportunités et à répondre à un changement des demandes commerciales.</p></li>
<li><p><strong>Focus</strong>: le cloud computing permet aux services informatiques de réduire très fortement le coût de leur infrastructure. L'infrastructure est abstraite et les ressources sont regroupées. De ce fait, le service informatique fonctionne plus comme un utilitaire que comme un ensemble de services et de systèmes complexes. En outre, le service informatique peut se tourner vers des rôles plus innovants et stratégiques.</p></li>
<li><p><strong>Économie</strong>: le cloud computing réduit le coût de fourniture des services informatiques et accroît l'utilisation et l'efficacité du centre de données. Les coûts de fourniture diminuent car, avec le cloud computing, les applications et les ressources passent en libre-service, et l'utilisation de ces ressources devient mesurable, de manières nouvelles et précises. L'utilisation du matériel diminue également, car les ressources d'infrastructure (stockage, capacité de calcul et réseau) sont désormais regroupées et abstraites.</p></li>
</ul>

<h2>Modèles de fourniture pour Cloud Services</h2>

En termes simples, le cloud computing est l'abstraction des services informatiques. Ces services peuvent aller de l'infrastructure de base à des applications complètes. Les utilisateurs finaux demandent et consomment ces services abstraits sans avoir à gérer (ou même à connaître) ce qui le constitue. De nos jours, le secteur d'activité reconnaît trois modèles de fourniture pour Cloud Services, chacun offrant un compromis distinct entre contrôle/flexibilité et coût total :

<ul>
<li><p><strong>Infrastructure as a Service</strong> (IaaS) : infrastructure virtuelle qui héberge des machines virtuelles et, principalement, des applications existantes.</p></li>
<li><p><strong>Platform as a Service</strong> (PaaS) : infrastructure d'applications cloud qui fournit un environnement d'hébergement d'applications à la demande.</p></li>
<li><p><strong>Software as a Service</strong> (SaaS) : modèle Cloud Services où une application est fournie sur Internet et les clients paient selon leur utilisation (par exemple Microsoft Office 365 ou Microsoft CRM Online).</p></li>
</ul>

La figure 1 décrit la taxonomie Cloud Services et la manière dont elle correspond aux composants d'une infrastructure informatique. Avec le modèle local, le client est responsable de la gestion de l'ensemble de la recherche par rang de la pile, de la connectivité réseau aux applications. Avec IaaS, les niveaux inférieurs de la pile sont gérés par un fournisseur alors que le client est responsable de la gestion du système d'exploitation via les applications. Avec PaaS, un fournisseur de plateforme fournit et gère absolument tout, de la connectivité réseau jusqu'à l'exécution. Il ne reste au client qu'à gérer les applications et les données. Azure offre ses meilleures performances avec ce modèle. Enfin, avec SaaS, un fournisseur fournit les applications et rend tous les services abstraits depuis tous les composants sous-jacents.

<p class="caption">Figure 1 : taxonomie Cloud Services</p>

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

<h2>Azure Virtual Machines</h2>

Azure Virtual Machines introduit des fonctionnalités qui permettent la gestion et le contrôle complets des machines virtuelles, ainsi que de la mise en réseau virtuelle étendue. Cette offre peut fournir aux organisations des avantages robustes, tels que :

<ul>
<li><p><strong>Gestion</strong>: gérer centralement des machines virtuelles dans le cloud avec contrôle complet afin de configurer et d'entretenir l'infrastructure.</p></li>
<li><p><strong>Mobilité des applications</strong>: déplacer des disques durs virtuels entre les environnements locaux et cloud, et vice-versa. Il n'est pas nécessaire de régénérer les applications pour une exécution dans le cloud.</p></li>
<li><p><strong>Accès aux applications des serveurs Microsoft</strong>: exécuter les mêmes applications locales et la même infrastructure dans le cloud, notamment Microsoft SQL Server, SharePoint Server, Windows Server et Active Directory.</p></li>
</ul>

Azure Virtual Machines constitue une plateforme conviviale, ouverte, flexible et puissante qui permet aux organisations de déployer et d'exécuter les machines virtuelles Windows Server et Linux en quelques minutes :

<ul>
<li><p><strong>Convivialité</strong>: avec Azure Virtual Machines, il est convivial et simple de créer, de migrer, de déployer et de gérer des machines virtuelles dans le cloud. Les organisations peuvent migrer des charges de travail vers Azure sans avoir à modifier le code existant, ou elles peuvent configurer de nouvelles machines virtuelles dans Azure en quelques clics. L'offre inclut également une assistance pour le développement de nouvelles applications de cloud en intégrant les fonctionnalités IaaS et PaaS d'Azure.</p></li>

<li><p><strong>Ouverture et flexibilité</strong>: Azure est une plateforme ouverte qui offre aux organisations de la flexibilité. Elles peuvent commencer par une image prégénérée de la bibliothèque d'images, ou elles peuvent créer et utiliser des disques durs virtuels locaux et personnalisés, et les charger dans la bibliothèque d'images. Les versions communautaire et commerciale de Linux sont également disponibles.</p></li>

<li><p><strong>Puissance</strong>: Azure est une plateforme de cloud clé en main destinée à l'exécution d'applications telles que SQL Server, SharePoint Server ou Active Directory dans le cloud. Les organisations peuvent créer des solutions hybrides locales et de cloud avec une connectivité VPN entre le centre de données Azure et leurs propres réseaux.</p></li>
</ul>

<h2>SharePoint sur Azure Virtual Machines</h2>

La flexibilité de SharePoint 2010 prend en charge la plupart des charges de travail d'un déploiement Azure Virtual Machines. Azure Virtual Machines est optimal pour FIS (SharePoint Server pour sites Internet) et les scénarios de développement. De même, les charges de travail SharePoint principales sont également prises en charge. Si une organisation souhaite gérer et contrôler sa propre implémentation SharePoint 2010 tout en exploitant les options de la virtualisation dans le cloud, Azure Virtual Machines est idéal pour le déploiement.

L'offre Azure Virtual Machines est hébergée et gérée dans le cloud. Elle fournit une flexibilité de déploiement et réduit le coût en limitant les dépenses d'investissement dues à l'approvisionnement en matériel. Grâce à une plus grande souplesse d'infrastructure, les organisations peuvent déployer SharePoint Server en quelques heures au lieu de plusieurs jours ou semaines. Azure Virtual Machines permet également aux organisations de déployer des charges de travail SharePoint dans le cloud en utilisant le modèle du paiement à l'utilisation. Lorsque les charges de travail SharePoint s'accroissent, une organisation peut rapidement développer l'infrastructure ; puis lorsqu'elles diminuent, l'organisation peut renvoyer les ressources qui ne sont plus requises, en ne payant que ce qui a été utilisé.

<h3>Réorientation des objectifs informatiques</h3>

De nombreuses organisations sous-traitent les composants courants de leur infrastructure informatique et de sa gestion, notamment le matériel, les systèmes d'exploitation, la sécurité, le stockage des données et la sauvegarde, tout en conservant le contrôle des applications critiques, SharePoint Server par exemple. En déléguant toutes les couches de service non critiques de leurs plateformes informatiques à un fournisseur virtuel, les organisations peuvent réorienter les objectifs informatiques sur des services SharePoint critiques et offrir de la valeur ajoutée avec les projets SharePoint au lieu de passer plus de temps à la configuration de leur infrastructure.

<h3>Déploiement plus rapide</h3>

La prise en charge et le déploiement d'une vaste infrastructure SharePoint peut entraver la capacité du service informatique à agir rapidement pour prendre en charge les besoins de l'organisation. Le délai requis pour créer, tester et préparer les serveurs et batteries de serveurs SharePoint et les déployer dans un environnement de production peut prendre des semaines ou même des mois, selon les processus et contraintes de l'organisation. Azure Virtual Machines permet aux organisations de déployer rapidement leurs charges de travail SharePoint sans dépenses d'investissement pour l'achat de matériel. Ainsi, les organisations peuvent capitaliser sur la souplesse de l'infrastructure pour effectuer un déploiement en quelques heures au lieu de plusieurs jours ou semaines.

<h3>Extensibilité</h3>

Sans la nécessité de déployer, de tester et de préparer les serveurs et batteries de serveurs physiques SharePoint, les organisations peuvent développer et acquérir une capacité de calcul à la demande, sans préavis. Lorsque la charge de travail SharePoint s'accroît, une organisation peut rapidement développer son infrastructure dans le cloud. De même, lorsque les besoins en calcul diminuent, l'organisation peut diminuer les ressources, en ne payant que ce qu'elle utilise. Azure Virtual Machines réduit les dépenses à l'avance et les engagements à long terme, permettant aux organisations de créer et de gérer les infrastructures à l'échelle. Répétons-le, cela signifie que ces organisations peuvent innover, expérimenter et itérer en quelques heures au lieu des jours et des semaines que prennent les déploiements traditionnels.

<h3>Utilisation facturée à l'usage</h3>

Azure Virtual Machines fournit une puissance, une mémoire et un stockage de calcul pour les scénarios SharePoint, dont les prix sont généralement basés sur la consommation des ressources. Les organisations ne paient que ce qu'elles utilisent et le service fournit toute la capacité requise pour exécuter l'infrastructure SharePoint. Pour plus d'informations sur la tarification et la facturation, consultez la page <a href="/fr-fr/pricing/details/">Tarification Azure</a>. Notez que des frais nominaux sont facturés pour le stockage et les données quittant le cloud Azure depuis un réseau local. Néanmoins, Azure ne facture pas le chargement des données.

<h3>Flexibilité</h3>

Azure Virtual Machines offre aux développeurs la flexibilité de choisir leur langue ou environnement d'exécution, avec la prise en charge officielle de .NET, Node.js, Java et PHP. Les développeurs peuvent également choisir leurs outils, avec la prise en charge de Microsoft Visual Studio, WebMatrix, Eclipse et des éditeurs de texte. En outre, Microsoft fournit un chemin à faible coût et risque vers le cloud et offre une mise en service et un déploiement économes et conviviaux pour les besoins de reporting du cloud en fournissant un accès à l'aide à la décision sur l'ensemble des appareils et des sites. Enfin, avec l'offre Azure, les utilisateurs peuvent non seulement déplacer des disques durs virtuels vers le cloud, mais également copier un disque dur virtuel en sens inverse et l'exécuter localement ou par l'intermédiaire d'un autre fournisseur de cloud, du moment qu'il possède la licence appropriée.

<h2>Processus de mise en service</h2>

Cette sous-section aborde les points forts d'Azure. La <strong>bibliothèque d'images</strong> d'Azure fournit la liste des machines virtuelles préconfigurées disponibles. Les utilisateurs peuvent publier SharePoint Server, SQL Server, Windows Server et d'autres ISO/VHD vers la bibliothèque d'images. Pour simplifier la création des machines virtuelles, des images de base sont créées et publiées dans la bibliothèque. Les utilisateurs autorisés peuvent utiliser ces images pour générer la machine virtuelle souhaitée. Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/tutorials/virtual-machine-from-gallery/">Création d'une machine virtuelle exécutant Windows Server 2008 R2</a> sur le site Azure. La figure 2 présente les étapes de base de création d'une machine virtuelle en utilisant le portail de gestion Azure :

<p class="caption">Figure 2 : vue d'ensemble des étapes de création d'une machine virtuelle</p>
![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Les utilisateurs peuvent charger une image préparée avec Sysprep dans le portail de gestion Azure. Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/common-tasks/upload-a-vhd/">Création et chargement d'un disque dur virtuel</a>. La figure 3 présente les étapes de base de chargement d'une image afin de créer une machine virtuelle :

<p class="caption">Figure 3 : vue d'ensemble des étapes de chargement d'une image</p>
![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

<h3>Déploiement de SharePoint 2010 sur Azure</h3>

Vous pouvez déployer SharePoint 2010 sur Azure en procédant comme suit :

<ol>
<li>Ouvrez une session sur le <a href="http://manage.windowsazure.com/">Portail de gestion Azure</a> par l'intermédiaire de votre compte d'abonnement Azure.
<ul>

<li>Si vous n'avez pas de compte Azure, <a href="http://azure.microsoft.com/pricing/free-trial/">inscrivez-vous pour bénéficier d'une version d'évaluation gratuite de Microsoft Azure</a>.</li>
</ul>
</li>

<li>Pour créer une MV avec le système d'exploitation de base, dans le portail de gestion Azure, cliquez sur <strong>NOUVEAU > CALCUL > MACHINE VIRTUELLE > À PARTIR DE LA GALERIE</strong>.</li>

<li>La boîte de dialogue <strong>Choisir une image</strong> apparaît. Cliquez sur l'image de plateforme <strong>Windows Server 2008 R2 SP1</strong> , puis sur la flèche droite.</li>

<li>La boîte de dialogue <strong><em>Configuration de machine virtuelle </em></strong>apparaît. Fournissez les informations suivantes :

<ul>
<li>Entrez un <strong>Nom de machine virtuelle</strong>.
</li>
<li>Sélectionnez la <strong>Taille</strong>.
<ul>
<li>Pour un environnement de production (serveur et base de données d'application SharePoint), il est recommandé d'utiliser A3 <em>(mémoire 4 cœurs, 7 Go de mémoire).</em>.</li>
</ul>
</li>
<li>Dans <strong>Nouveau nom d'utilisateur</strong>, tapez le nom d'un compte d'administrateur local.</li>
<li>Dans la zone <strong>Nouveau mot de passe</strong> , tapez un mot de passe fort.</li>
<li>Dans la zone <strong>Conformer</strong> , retapez le mot de passe et cliquez sur la flèche droite.</li>
</ul>
<li>La seconde boîte de dialogue <strong>Configuration de machine virtuelle</strong> apparaît. Fournissez les informations suivantes :
<ul>
<li>Dans la zone <strong>Service cloud</strong> , choisissez l'une des options suivantes :
<ul>
<li><strong>Créer un nouveau service de cloud computing</strong>, auquel cas vous devez également fournir le nom DNS d'un service cloud.</li>
<li>Sélectionnez un service cloud existant.</li>
</ul>
<li>Dans la zone <strong>Région/Groupe d'affinités/Réseau virtuel </strong>, sélectionnez la région où l'image virtuelle sera hébergée.</li>
<li>Dans la zone <strong>Compte de stockage </strong>, choisissez l'une des options suivantes :
<ul>
<li><strong>Utiliser un compte de stockage généré automatiquement</strong>.</li>
<li>Sélectionnez un nom de compte de stockage existant.</li>
<ul>
<li>Un seul compte de stockage est automatiquement créé par région. Toutes les autres machines virtuelles comportant ce paramètre sont situées dans ce compte de stockage.</li>
<li>Vous êtes limité à 20 comptes de stockage.</li>
<li>Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/common-tasks/upload-a-vhd/#createstorage">Création d'un compte de stockage dans Azure</a>.</li>
</ul>
</li>
<li>Dans la zone <strong>Groupe à haute disponibilité,</strong> sélectionnez <STRONG>(Aucun)</STRONG>, puis cliquez sur la flèche droite.</li>
</ul>
</li>
</ul>
</li>
<li>Dans la troisième boîte de dialogue <strong>Configuration de machine virtuelle </strong>cliquez sur la coche pour créer la machine virtuelle.</li>


<li>Connexion à la machine virtuelle :
<ul>
<li>Ouvrez la machine virtuelle à l'aide du Bureau à distance.</li>
<li>Dans le portail de gestion Azure, sélectionnez votre machine virtuelle, puis la page <strong>Tableau de bord</strong> .</li>
<li>Cliquez sur <strong>Connexion</strong>.</li>
</ul>
</li>
<li>Créez la machine virtuelle SQL Server à l'aide des options suivantes :
<ul>
<li>Créez une machine virtuelle SQL Server 2012 en suivant les étapes 1 à 7 ci-dessus. Cependant, à l' <strong>étape 3</strong>, utilisez l'image SQL Server 2012 au lieu de l'image Windows Server 2008 R2 SP1. Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/common-tasks/install-sql-server/">Configuration d'une machine virtuelle SQL Server sur Azure</a>.
<ul>
<li>Quand vous choisissez cette option, le processus de configuration conserve une copie des fichiers d'installation de SQL Server 2012 dans le chemin d'accès de répertoire <em>C:\SQLServer_11.0_Full</em> pour que vous puissiez personnaliser l'installation. Par exemple, vous pouvez convertir l'installation d'évaluation de SQL Server 2012 en une version sous licence en utilisant votre clé de licence.</li>
</ul>
</li>
<li>Utilisez l'outil SQL Server System Preparation (SysPrep) pour installer SQL Server sur la machine virtuelle avec un système d'exploitation de base (comme indiqué ci-dessus aux étapes 1 à 7). Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/ee210664.aspx">Installation de SQL Server 2012 à l'aide de SysPrep</a>.</li>
<li>Utilisez l'invite de commandes pour installer SQL Server. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/ms144259.aspx#SysPrep">Installation de SQL Server 2012 à partir de l'invite de commandes</a>.</li>
<li>Utilisez un support SQL Server pris en charge et votre clé de licence pour installer SQL Server sur la machine virtuelle avec un système d'exploitation de base (comme indiqué ci-dessus aux étapes 1 à 7).</li>
</ul>
</li>
<li>Créez la batterie de serveurs SharePoint en procédant comme suit :
<ul>
<li>Sous-étape 1 : configurez l'abonnement Azure à l'aide de fichiers de script.</li>
<li>Sous-étape 2 : mettez les serveurs SharePoint en service en créant une autre machine virtuelle avec un système d'exploitation de base (comme indiqué ci-dessus aux étapes 1 à 7). Pour créer un serveur SharePoint sur cette machine virtuelle, sélectionnez l'une des options suivantes :
<ul>
<li>Mise en service via l'interface utilisateur graphique SharePoint :
<ul>
<li>Pour créer et configurer une batterie SharePoint, accédez à <a href="http://technet.microsoft.com/ library/ee805948.aspx#CreateConfigure">Création d'une batterie de serveurs Microsoft SharePoint</a>.</li>
<li>Pour ajouter un serveur Web ou d'applications à la batterie, accédez à <a href="http://technet.microsoft.com/ library/cc261752.aspx">Ajouter un serveur Web ou d'applications à la batterie (SharePoint Server 2010)</a>.</li>
<li>
<p>Pour ajouter un serveur de bases de données à une batterie existante, accédez à <a href="http://technet.microsoft.com/ library/cc262781">Ajouter un serveur de bases de données à une batterie existante</a>.</p>
<ul>
<li>Pour utiliser SQL Server 2012 pour votre batterie de serveurs SharePoint, vous devez télécharger et installer le Service Pack 1 pour SharePoint Server 2010 après avoir installé l'application et choisi de ne pas configurer le serveur. Pour plus d'informations, consultez <a href="http://www.microsoft.com/fr-fr/download/details.aspx?id=26623">Service Pack 1 pour SharePoint Server 2010</a>.</li>
<li>Pour tirer parti des fonctionnalités de SQL Server BI, nous vous recommandons d'installer SharePoint Server en tant que batterie de serveurs plutôt qu'en tant que serveur autonome. Pour plus d'informations, consultez <a href="http://technet.microsoft.com/ library/hh231681(v=sql.110).aspx">Installation des fonctionnalités Business Intelligence de SQL Server 2012</a>.</li>
</ul>
</li>
</ul>
</li>
<li>Mise en service à l'aide de Microsoft Windows PowerShell : vous pouvez utiliser l'outil en ligne de commande Psconfig comme interface de remplacement pour réaliser plusieurs opérations contrôlant comment les produits SharePoint 2010 sont mis en service. Pour plus d'informations, consultez <a href="http://technet.microsoft.com/ library/cc263093.aspx">Référence de la ligne de commande Psconfig</a>.</li>
</ul>
</li>
<li>Sous-étape 3 : configurez SharePoint. Dès que chaque machine virtuelle est prête, configurez SharePoint Server sur chaque serveur à l'aide de l'une des options suivantes :
<ul>
<li>Configurez SharePoint depuis l'interface utilisateur graphique.</li>
<li>Configurez SharePoint à l'aide de Windows PowerShell. Pour plus d'informations, consultez <a href="http://technet.microsoft.com/ library/cc262839.aspx">Installation de SharePoint Server 2010 à l'aide de Windows PowerShell</a>.
<ul>
<li>Vous pouvez également utiliser AutoSPInstaller du projet CodePlex qui est composé de scripts Windows PowerShell, d'un fichier d'entrée XML et d'un fichier de commandes Microsoft Windows. AutoSPInstaller fournit une infrastructure pour un script d'installation SharePoint 2010 basé sur Windows PowerShell. Pour plus d'informations, consultez <a href="http://autospinstaller.codeplex.com/">CodePlex : AutoSPInstaller</a>.

<strong>Remarque</strong>: assurez-vous de configurer la sécurité sur le point de terminaison du portail de gestion et de définir un port entrant sur le pare-feu Windows de la machine virtuelle. Puis, assurez-vous que vous pouvez démarrer une session Windows PowerShell à distance sur l'un des serveurs d'applications SharePoint en ouvrant une session Windows PowerShell avec les informations d'identification Administrateur.
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>Une fois le script terminé, connectez-vous à la machine virtuelle à l'aide du tableau de bord de cette dernière.</li>
<li>Vérifiez la configuration SharePoint : connectez-vous au serveur SharePoint, puis utilisez l'Administration centrale pour vérifier la configuration.</li>
</ol>

<h3>Création et chargement d'un disque dur virtuel</h3>

Vous pouvez également créer vos propres images et les charger dans Azure sous la forme d'un fichier de disque dur virtuel. Pour créer et charger un fichier de disque dur virtuel sur Azure, procédez comme suit :

<ol>
<li>Créez l'image Hyper-V : utilisez le Gestionnaire Hyper-V pour créer le disque dur virtuel Hyper-V. Pour plus d'informations, consultez <a href="http://technet.microsoft.com/ library/cc742509">Créez des disques durs virtuels</a>.</li>
<li>Créez un compte de stockage dans Azure : un compte de stockage dans Azure est requis pour charger un fichier de disque dur virtuel pouvant être utilisé pour la création d'une machine virtuelle. Vous pouvez créer ce compte à l'aide du portail de gestion Azure. Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/common-tasks/upload-a-vhd/">Création d'un compte de stockage dans Azure</a>.</li>
<li>Préparez l'image pour le chargement : avant de pouvoir télécharger l'image sur Azure, elle doit être généralisée en utilisant la commande Sysprep. Pour plus d'informations, consultez <a href="http://technet.microsoft.com/ library/bb457073.aspx">Utilisation de Sysprep : introduction</a>.</li>
<li>Chargez l'image dans Azure : pour charger une image contenue dans un fichier de disque dur virtuel, vous devez créer et installer un certificat de gestion. Obtenez l'empreinte numérique du certificat et l'ID d'abonnement. Définissez la connexion et téléchargez le fichier VHD à l'aide de l'outil en ligne de commande CSUpload. Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/common-tasks/upload-a-vhd/">Téléchargement de l'image sur Azure</a>.</li>
</ol>

<h2>Scénarios d'utilisation</h2>

Cette section aborde quelques scénarios clients majeurs relatifs aux déploiements de SharePoint à l'aide d'Azure Virtual Machines. Chaque scénario est divisé en deux parties : une brève description, suivie d'étapes de démarrage.

<h3>Scénario 1 : développement SharePoint simple et environnement de test</h3>

<h4>Description</h4>

Les organisations recherchent des moyens plus souples de créer des applications SharePoint et de configurer des environnements SharePoint pour le développement et le test à terre/extraterritorialisés. Fondamentalement, elles veulent raccourcir le délai requis pour configurer des projets de développement d'applications SharePoint et diminuer le coût en augmentant l'utilisation de leurs environnements de test. Par exemple, une organisation peut souhaiter réaliser un test de charge à la demande sur SharePoint Server et exécuter le test d'acceptation des utilisateurs (UAT) avec plus d'utilisateurs simultanés situés dans différentes régions. De même, l'intégration des équipes sur terre/extraterritorialisées est, de nos jours, un besoin de plus en plus important pour de nombreuses organisations.

Ce scénario explique comment les organisations peuvent utiliser des batteries de serveurs SharePoint préconfigurées pour le développement et les charges de test. Une topologie de déploiement SharePoint ressemble exactement à ce qu'elle serait dans un déploiement virtualisé local. Les compétences informatiques existantes sont complètement conservées dans un déploiement Azure Virtual Machines, l'avantage majeur résidant en un passage quasiment complet des dépenses d'investissement en dépenses opérationnelles, aucun achat à l'avance de serveur physique n'étant requis. Les organisations peuvent éliminer le coût d'investissement du matériel des serveurs et obtenir une souplesse en réduisant fortement le délai de mise en service requis pour créer, configurer ou étendre une batterie de serveurs SharePoint pour un environnement de test et de développement. Le service informatique peut ajouter et supprimer dynamiquement des capacités afin de prendre en charge des besoins variables en matière de test et de développement. En outre, le service informatique peut se concentrer davantage sur la fourniture de valeur ajoutée avec les projets SharePoint et moins sur la gestion de l'infrastructure.

Pour utiliser pleinement les machines de test de charge, les organisations peuvent configurer le développement virtualisé SharePoint et tester les machines sur Azure avec la prise en charge du système d'exploitation pour Windows Server 2008 R2. Ainsi, les équipes de développement peuvent créer et tester des applications et migrer facilement vers des environnements de production locaux ou cloud sans changement de code. Les mêmes infrastructures et jeux d'outils peuvent être utilisés en local et dans le cloud, permettant à l'équipe dispersée un accès au même environnement. Les utilisateurs peuvent également accéder à des données et applications locales en définissant une connexion VPN directe.

<h4>Prise en main</h4>

La figure 4 montre un environnement de développement et de test SharePoint dans une machine virtuelle Azure. Pour générer ce déploiement, commencez par utiliser le même environnement de développement et de test SharePoint local utilisé pour développer les applications. Puis, chargez et déployez les applications sur la machine virtuelle Azure pour le test et le développement. Si votre organisation décide de replacer l'application en local, elle peut le faire sans avoir à modifier l'application.

<p class="caption">Figure 4 : environnement de développement et de test SharePoint dans Azure Virtual Machines</p>

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

<h4>Configuration de l'environnement du scénario</h4>

Pour implémenter un environnement de développement et de test SharePoint sur Azure, procédez comme suit :

<ol>
<li><em>Mise en service</em>: tout d'abord, mettez une connexion VPN en service entre les éléments locaux et Azure à l'aide d'Azure Virtual Network. Active Directory n'étant pas utilisé ici, un tunnel VPN est requis. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/windowsazure/jj156007.aspx">Réseau virtuel Azure (Considérations relatives à la conception et scénarios de connexion sécurisés)</a>. Puis, utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle en utilisant une image stockée provenant de la bibliothèque d'images.
<ul>
<li>Vous pouvez charger les machines virtuelles locales de développement et de test SharePoint sur votre compte de stockage Azure et référencer ces dernières par l'intermédiaire de la bibliothèque d'images afin de générer l'environnement requis.</li>
<li>Vous pouvez utiliser l'image SQL Server 2012 au lieu de l'image Windows Server 2008 R2 SP1. Pour plus d'informations, consultez <a href="/fr-fr/manage/windows/common-tasks/install-sql-server/">Configuration d'une machine virtuelle SQL Server sur Azure</a>.</li>
</ul>
</li>
<li><em>Installer</em>: installez SharePoint Server, Visual Studio et SQL Server sur les machines virtuelles à l'aide d'une connexion Bureau à distance.
<ul>
<li>Sélectionnez une option d'installation de SharePoint Server :
<ul>
<li>Utilisez le script SharePoint 2010 Easy Setup Script pour créer un ordinateur de développeur SharePoint. Pour plus d'informations, consultez <a href="http://www.microsoft.com/fr-fr/download/details.aspx?id=23415">SharePoint 2010 Easy Setup Script</a>.</li>
<li>Utilisez Windows PowerShell. Pour plus d'informations, consultez <a href="http://technet.microsoft.com/ library/cc262839.aspx">Installation de SharePoint Server 2010 à l'aide de Windows PowerShell</a>.</li>
<li>Utilisez AutoSPInstaller du projet CodePlex. Pour plus d'informations, consultez <a href="http://autospinstaller.codeplex.com/">CodePlex : AutoSPInstaller</a>.</li>
</ul>
</li>
<li>Installez Visual Studio. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/e2h7fzkw.aspx">Installation de Visual Studio</a>.</li>
<li>Installez SQL Server. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/ee210664.aspx">Installation de SQL Server à l'aide de SysPrep</a>.
<ul>
<li>Reportez-vous aux travaux pratiques pour la création et la configuration de SQL Server 2012 pour un déploiement de batteries de serveurs SharePoint : <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Configuration de SQL Server 2012 pour SharePoint dans Azure</a>.</li>
<li>Reportez-vous aux travaux pratiques pour la création d'une batterie de serveurs SharePoint en configurant Active Directory et en utilisant une seule base de données SQL Server : <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Déploiement d'une batterie de serveurs SharePoint avec Azure Virtual Machines</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Développer des packages et scripts de déploiement pour les applications et bases de données</em>: si vous prévoyez d'utiliser une machine virtuelle provenant de la bibliothèque d'images, les applications et bases de données locales souhaitées peuvent être déployées sur Azure Virtual Machines :
<ul>
<li>Créez des packages de déploiement pour les applications et bases de données locales existantes à l'aide SQL Server Data Tools et Visual Studio.</li>
<li>Utilisez ces packages pour déployer des applications et bases de données sur Azure Virtual Machines.</li>
</ul>
</li>
<li><em>Déploiement d'applications et bases de données SharePoint</em>:
<ul>
<li>Configurez la sécurité sur le point de terminaison du portail de gestion et définissez un port entrant sur le pare-feu Windows de la machine virtuelle.</li>
<li>Déployez des applications et bases de données SharePoint sur Azure Virtual Machines à l'aide des packages et scripts de déploiement créés à l'étape 3.</li>
<li>Testez les applications et bases de données déployées.</li>
</ul>
</li>
<li><em>Gérer les machines virtuelles</em>:
<ul>
<li>Surveillez les machines virtuelles à l'aide du portail de gestion.</li>
<li>Surveillez les applications à l'aide de Visual Studio et SQL Server Management Studio.</li>
<li>Vous pouvez également surveiller et gérer les machines virtuelles à l'aide d'un logiciel de gestion local, tel que Microsoft System Center - Operations Manager.</li>
</ul>
</li>
</ol>
<h3>Scénario 2 : batterie de serveurs SharePoint publique avec personnalisation</h3>

<h4>Description</h4>

Les organisations veulent créer une présence Internet hébergée dans le cloud et facilement extensible basée sur le besoin et la demande. Elles veulent également créer des sites Web extranet partenaires pour la collaboration et implémenter un processus convivial pour la création et l'approbation distribuées du contenu des sites Web. Enfin, pour traiter des charges en augmentation constante, les organisations veulent fournir une capacité à la demande sur leurs sites Web.

Dans ce scénario, SharePoint Server est utilisé comme base d'hébergement d'un site Web public. Cela permet aux organisations de déployer, de personnaliser et d'héberger rapidement leurs sites Web professionnels sur une infrastructure de cloud sécurisée et extensible. Avec les sites Web publics SharePoint sur Azure, les organisations peuvent se mettre à l'échelle lorsque le trafic augmente et ne payer que ce qu'elles utilisent. Des outils courants, similaires à ceux utilisés en local, peuvent être utilisés pour la création, le workflow et l'approbation du contenu avec SharePoint sur Azure.

En outre, à l'aide d'Azure Virtual Machines, les organisations peuvent facilement configurer des environnements intermédiaires et de production s'exécutant sur des machines virtuelles. Les machines virtuelles publiques SharePoint créées dans Azure peuvent être sauvegardées sur un stockage virtuel. En outre, à des fins de récupération d'urgence, la fonctionnalité de géo-réplication continue permet aux organisations de sauvegarder automatiquement des machines virtuelles opérant dans un centre de données vers un autre centre de données situé à des kilomètres. (Pour plus d'informations sur la géo-réplication, consultez <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx">Introduction à la géo-réplication pour Azure Storage</a>).

Les machines virtuelles de l'infrastructure Azure sont validées et prises en charge pour fonctionner avec d'autres produits Microsoft, tels que SQL Server et SharePoint Server. Azure et SharePoint Server fonctionnent mieux ensemble : les deux appartiennent à la famille Microsoft et sont parfaitement intégrés, pris en charge et testés ensemble pour offrir une expérience optimale. Ils ont tous deux un seul point de support pour l'application SharePoint et l'infrastructure Azure.

<h4>Prise en main</h4>

Dans ce scénario, des serveurs Web frontaux supplémentaires pour SharePoint Server doivent être ajoutés afin de prendre en charge un trafic supplémentaire. Ces serveurs requièrent une sécurité améliorée et des contrôleurs de domaine de services de domaine Active Directory afin de prendre en charge l'authentification et l'autorisation utilisateur. La figure 5 présente la disposition de ce scénario.

<p class="caption">Figure 5 : batterie de serveurs SharePoint publique avec personnalisation</p>
![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

<h4>Configuration de l'environnement du scénario</h4>

Pour implémenter une batterie de serveurs SharePoint publique sur Azure, procédez comme suit :

<ol>
<li><em>Déployer Active Directory</em>: les exigences fondamentales de déploiement d'Active Directory sur Azure Virtual Machines sont similaires " mais pas identiques " au déploiement sur des machines virtuelles (et, dans une certaine mesure, sur des machines physiques) locales. Pour plus d'informations sur les différences, ainsi que les instructions et d'autres considérations, consultez la page <a href="http://msdn.microsoft.com/library/windowsazure/jj156090">Recommandations en matière de déploiement d'Active Directory sur des machines virtuelles Azure</a>. Pour déployer Active Directory dans Azure :
<ul>
<li>Définissez et créez un réseau virtuel où les machines virtuelles peuvent être affectées à des sous-réseaux spécifiques. Pour plus d'informations, consultez <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Configuration de la mise en réseau virtuel</a>.</li>
<li>Utilisez le portail de gestion pour créer et déployer le contrôleur de domaine sur une nouvelle machine virtuelle sur Azure. Pour plus d'informations, consultez <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Déploiement et création du contrôleur de domaine</a>.
<ul>
<li>Vous pouvez également vous reporter au script Windows PowerShell pour déployer un domaine autonome dans le cloud en utilisant Azure Virtual Machines et Virtual Network. Pour plus d'informations, consultez <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Déploiement d'Active Directory dans Azure (Windows PowerShell)</a>.</li>
<li>Pour plus d'informations sur la création d'une forêt Active Directory sur une machine virtuelle dans Azure Virtual Network, consultez la page <a href="/fr-fr/manage/services/networking/active-directory-forest/">Installation d'une nouvelle forêt Active Directory dans Azure</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Mettre en service une machine virtuelle</em>: Utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle à partir d'une image stockée provenant de la bibliothèque d'images.</li>
<li><em>Déployer une batterie de serveurs SharePoint</em>:
<ul>
<li>Utilisez la machine virtuelle nouvellement mise en service pour installer SharePoint et générer une image réutilisable. Pour plus d'informations sur l'installation de SharePoint Server, accédez à <a href="http://technet.microsoft.com/ library/cc262839.aspx">Installation et configuration de SharePoint Server 2010 à l'aide de Windows PowerShell</a> ou <a href="http://autospinstaller.codeplex.com/">CodePlex : AutoSPInstaller</a>.</li>
<li>Configurez la machine virtuelle SharePoint afin de créer la batterie de serveurs SharePoint et de s'y connecter.</li>
<li>Utilisez le portail de gestion pour configurer l'équilibrage de la charge.
<ul>
<li>Configurez les points de terminaison de la machine virtuelle, sélectionnez l'option permettant d'équilibrer la charge du trafic sur un point de terminaison existant, puis spécifiez le nom de la machine virtuelle à charge équilibrée.</li>
<li>Ajoutez une autre machine virtuelle Web frontale à la batterie de serveurs SharePoint existante pour un trafic supplémentaire.</li>
</ul>
</li>
</ul>
</li>
<li><em>Gérer les machines virtuelles</em>:
<ul>
<li>Surveillez les machines virtuelles à l'aide du portail de gestion.</li>
<li>Surveillez la batterie de serveurs SharePoint à l'aide de l'administration centrale.</li>
</ul>
</li>
</ol>

<h3>Scénario 3 : batterie de serveurs montée en charge pour des services décisionnels supplémentaires</h3>

<h4>Description</h4>

L'aide à la décision est essentielle pour obtenir des idées clés et prendre des décisions rapides et correctes. Lorsque les organisations quittent une approche locale, elles ne veulent pas apporter de modifications à l'environnement décisionnel lors du déploiement des applications décisionnelles existantes dans le cloud. Elles veulent héberger des rapports provenant de SQL Server Analysis Services (SSAS) ou SQL Server Reporting Services (SSRS) dans un environnement fortement durable et disponible, tout en gardant un contrôle complet de l'application décisionnelle, le tout sans passer beaucoup de temps et dépenser beaucoup de budget en maintenance.

Ce scénario décrit comment les organisations peuvent utiliser Azure Virtual Machines pour héberger des applications décisionnelles critiques. Les organisations peuvent déployer des batteries de serveurs SharePoint dans Azure Virtual Machines et monter en charge les composants décisionnels des machines virtuelles du serveur d'applications, tels que SSRS ou Excel Services. En mettant à l'échelle les composants utilisant beaucoup de ressources dans le cloud, les machines virtuelles peuvent plus facilement et mieux prendre en charge des charges de travail spécialisées. Notez que SQL Server dans Azure Virtual Machines offre d'excellentes performances, car il est facile de mettre à l'échelle des instances de SQL Server, dans des petites installations comme des très grandes. Ainsi, les organisations jouissent d'une grande flexibilité, leur permettant de mettre en service dynamiquement (développer) ou de supprimer les privilèges d'accès (réduire) des instances décisionnelles selon les besoins immédiats en charge de travail.

La migration des applications décisionnelles existantes vers Azure fournit une meilleure mise à l'échelle. Avec la puissance de SSAS, de SSRS et de SharePoint Server, les organisations peuvent créer une aide à la décision puissante et des applications et tableaux de bord de reporting pouvant être montés en charge ou réduits. Ces derniers peuvent également être intégrés de manière plus sécurisée aux données et applications locales. Azure garantit la conformité du centre de données grâce à la prise en charge de la norme ISO 27001. Pour plus d'informations, accédez à l' <a href="/fr-fr/support/trust-center/compliance/">Azure Trust Center</a>.

<h4>Prise en main</h4>

Pour monter en charge le déploiement des composants décisionnels, un nouveau serveur d'applications avec des services tels que PowerPivot, Power View, Excel Services ou PerformancePoint Services doit être installé. Ou, des instances décisionnelles SQL Server telles que SSAS ou SSRS doivent être ajoutées à la batterie de serveurs existante afin de prendre en charge un traitement supplémentaire des requêtes. Le serveur peut être ajouté à une nouvelle machine virtuelle Azure avec SharePoint 2010 Server ou SQL Server installé. Puis, les composants décisionnels peuvent être installés, déployés et configurés sur ce serveur (figure 6).

<p class="caption">Figure 6 : batterie de serveurs SharePoint montée en charge pour des services décisionnels supplémentaires</p>

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

<h4>Configuration de l'environnement du scénario</h4>

Pour monter en charge un environnement décisionnel sur Azure, procédez comme suit :

<ol>
<li><em>Mise en service</em>:
<ul>
<li>Mettez une connexion VPN en service entre les éléments locaux et Azure à l'aide d'Azure Virtual Network. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/windowsazure/jj156007.aspx">Réseau virtuel Azure (Considérations relatives à la conception et scénarios de connexion sécurisés)</a>.</li>
<li>Utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle à partir d'une image stockée provenant de la bibliothèque d'images.
<ul>
<li>Vous pouvez charger des images de charge de travail décisionnelles SharePoint Server ou SQL Server dans la bibliothèque d'images, et tout utilisateur autorisé peut sélectionner ces machines virtuelles de composants décisionnels pour créer un environnement monté en charge.</li>
</ul>
</li>
</ul>
</li>
<li><em>Installer</em>: si votre organisation ne possède pas d'images prégénérées des composants décisionnels SharePoint Server ou SQL Server, installez SharePoint Server et SQL Server sur les machines virtuelles à l'aide d'une connexion Bureau à distance.
<ul>
<li>Pour plus d'informations sur l'installation de SharePoint, accédez à <a href="http://technet.microsoft.com/ library/cc262839.aspx">Installation de SharePoint Server 2010 à l'aide de Windows PowerShell</a> ou <a href="http://autospinstaller.codeplex.com/">CodePlex : AutoSPInstaller</a>.</li>
<li>Pour plus d'informations sur l'installation de SQL Server, accédez à <a href="http://msdn.microsoft.com/library/ee210664.aspx">Installation de SQL Server à l'aide de SysPrep</a>.</li>
<li>Reportez-vous aux travaux pratiques pour la création et la configuration de SQL Server 2012 pour un déploiement de batteries de serveurs SharePoint : <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Configuration de SQL Server 2012 pour SharePoint dans Azure</a>.</li>
<li>Reportez-vous aux travaux pratiques pour la création d'une batterie de serveurs SharePoint en configurant Active Directory et en utilisant une seule base de données SQL Server : <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Déploiement d'une batterie de serveurs SharePoint avec Azure Virtual Machines</a>.</li>
</ul>
</li>
<li><em>Ajouter la machine virtuelle décisionnelle</em>:
<ul>
<li>Configurez la sécurité sur le point de terminaison du portail de gestion et définissez un port entrant sur le pare-feu Windows de la machine virtuelle.</li>
<li>Ajoutez la machine virtuelle décisionnelle nouvellement créée à la batterie de serveurs SharePoint ou SQL Server existante.</li>
</ul>
</li>
<li><em>Gérer les machines virtuelles</em>:
<ul>
<li>Surveillez les machines virtuelles à l'aide du portail de gestion.</li>
<li>Surveillez la batterie de serveurs SharePoint à l'aide de l'administration centrale.</li>
<li>Surveillez et gérez les machines virtuelles à l'aide d'un logiciel de gestion local tel que Microsoft System Center - Operations Manager.</li>
</ul>
</li>
</ol>
<h3>Scénario 4 : site Web entièrement personnalisé basé sur SharePoint</h3>

<h4>Description</h4>

De plus en plus, les organisations souhaitent créer des sites Web SharePoint entièrement personnalisés dans le cloud. Elles ont besoin d'un environnement fortement durable et disponible qui offre un contrôle complet permettant de gérer des applications complexes s'exécutant dans le cloud. Mais, elles ne souhaitent pas y consacrer beaucoup de temps, ni de budget.

Dans ce scénario, une organisation peut déployer l'ensemble de sa batterie de serveurs SharePoint dans le cloud et mettre dynamiquement à l'échelle tous les composants afin d'obtenir une capacité supplémentaire. Ou bien, elle peut développer son déploiement local vers le cloud afin d'accroître la capacité et d'améliorer les performances, le cas échéant. Le scénario se concentre sur les organisations souhaitant une " expérience SharePoint " totale pour le développement des applications et la gestion du contenu. Les sites les plus complexes peuvent également inclure un reporting amélioré, Power View, PerformancePoint, PowerPivot, des graphiques détaillés et la plupart des autres fonctionnalités de site SharePoint pour des fonctionnalités de bout en bout et complètes.

Les organisations peuvent utiliser Azure Virtual Machines pour héberger des applications personnalisées et des composants associés sur une infrastructure de cloud économe et fortement sécurisée. Elles peuvent également utiliser Microsoft System Center local comme outil de gestion commun pour les applications locales et cloud.

<h4>Prise en main</h4>

Pour implémenter un site Web SharePoint entièrement personnalisé sur Azure, une organisation doit déployer un domaine Active Directory dans le cloud et mettre en service de nouvelles machines virtuelles dans ce domaine. Puis, une machine virtuelle sous SQL Server 2012 doit être créée, configurée et intégrée à la batterie de serveurs SharePoint. Enfin, la batterie de serveurs SharePoint doit être créée, équilibrée en charge et connectée à Active Directory et SQL Server (figure 7).

<p class="caption">Figure 7 : site Web entièrement personnalisé basé sur SharePoint</p>

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

<h4>Configuration de l'environnement du scénario</h4>

Les étapes suivantes expliquent comment créer un environnement personnalisé de batteries de serveurs SharePoint à partir d'images prégénérées disponibles dans la bibliothèque d'images. Notez, néanmoins, que vous pouvez également charger des machines virtuelles de batterie de serveurs SharePoint dans la bibliothèque d'images, et les utilisateurs autorisés peuvent choisir ces machines virtuelles pour créer la batterie de serveurs SharePoint requise sur Azure.

<ol>
<li>Déployer Active Directory<em>: les exigences fondamentales de déploiement d'Active Directory sur Azure Virtual Machines sont similaires " mais pas identiques " au déploiement sur des machines virtuelles (et, dans une certaine mesure, sur des machines physiques) locales. Pour plus d'informations sur les différences, ainsi que les instructions et d'autres considérations, consultez la page <a href="http://msdn.microsoft.com/library/windowsazure/jj156090">Recommandations en matière de déploiement d'Active Directory sur des machines virtuelles Azure</a>. Pour déployer Active Directory dans Azure :</em>
<ul>
<li>Définissez et créez un réseau virtuel où les machines virtuelles peuvent être affectées à des sous-réseaux spécifiques. Pour plus d'informations, consultez <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Configuration de la mise en réseau virtuel</a>.</li>
<li>Utilisez le portail de gestion pour créer et déployer le contrôleur de domaine sur une nouvelle machine virtuelle sur Azure. Pour plus d'informations, consultez <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Déploiement et création du contrôleur de domaine</a>.
<ul>
<li>Vous pouvez également vous reporter au script Windows PowerShell pour déployer un domaine autonome dans le cloud en utilisant Azure Virtual Machines et Virtual Network. Pour plus d'informations, consultez <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Déploiement d'Active Directory dans Azure (Windows PowerShell)</a>.</li>
<li>Pour plus d'informations sur la création d'une forêt Active Directory sur une machine virtuelle dans Azure Virtual Network, consultez la page <a href="/fr-fr/manage/services/networking/active-directory-forest/">Installation d'une nouvelle forêt Active Directory dans Azure</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Déployer SQL Server</em>:
<ul>
<li>Utilisez le portail de gestion pour mettre en service une nouvelle machine virtuelle à partir d'une image stockée provenant de la bibliothèque d'images.</li>
<li>Configurez SQL Server sur la machine virtuelle. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/ee210664.aspx">Installation de SQL Server à l'aide de SysPrep</a>.</li>
<li>Liez la machine virtuelle au domaine Active Directory nouvellement créé.</li>
</ul>
</li>
<li><em>Déployer une batterie de serveurs SharePoint multiserveur</em>:
<ul>
<li>Créez un réseau virtuel. Pour plus d'informations, consultez <a href="http://msdn.microsoft.com/library/windowsazure/jj156007.aspx">Réseau virtuel Azure (Considérations relatives à la conception et scénarios de connexion sécurisés)</a>.
<ul>
<li>Lorsque vous déployez les machines virtuelles SharePoint, vous avez besoin des sous-réseaux fournis pour SharePoint Server afin que les adresses DNS de la zone Active Directory locale soient disponibles au cours de la mise en service.</li>
</ul>
</li>
<li>Utilisez le portail de gestion pour créer une machine virtuelle.</li>
<li>Installez SharePoint Server sur cette machine virtuelle et générez une image réutilisable. Pour plus d'informations sur l'installation de SharePoint Server, accédez à <a href="http://technet.microsoft.com/ library/cc262839.aspx">Installation et configuration de SharePoint Server 2010 à l'aide de Windows PowerShell</a> ou <a href="http://autospinstaller.codeplex.com/">CodePlex : AutoSPInstaller</a>.</li>
<li>Configurez la machine virtuelle SharePoint afin de créer la batterie de serveurs SharePoint et de s'y connecter à l'aide de la commande <a href="http://technet.microsoft.com/ library/ff607979.aspx">Join-SharePointFarm</a> .</li>
<li>Utilisez le portail de gestion pour configurer l'équilibrage de la charge :
<ul>
<li>Configurez les points de terminaison de la machine virtuelle, sélectionnez l'option permettant d'équilibrer la charge du trafic sur un point de terminaison existant, puis spécifiez le nom de la machine virtuelle à charge équilibrée.
<ul>
<li>Pour plus d'informations sur le déploiement des batteries de serveurs SharePoint sur Azure Virtual Machines, regardez cette <a href="http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327">vidéo TechEd North America 2012</a>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><em>Gérer la batterie de serveurs SharePoint par l'intermédiaire de System Center</em>:
<ul>
<li>Utilisez l'agent Operations Manager et le nouveau Azure Integration Pack pour connecter votre System Center local à Azure Virtual Machines.</li>
<li>Utilisez l'App Controller et l'Orchestrator locaux pour les fonctions de gestion.</li>
</ul>
</li>
</ol>

<h2>Conclusion</h2>
Le cloud computing transforme la relation entre l'informatique et l'organisation, car le cloud computing peut exploiter une nouvelle catégorie d'avantages, notamment une forte diminution du coût associé à une informatique plus ciblée et souple. Azure ouvre la voie au cloud computing en offrant une infrastructure virtuelle conviviale, ouverte, flexible et puissante. Azure Virtual Machines limite les besoins en matériel, permettant aux organisations de réduire le coût et la complexité en créant une infrastructure à l'échelle, avec un contrôle complet et une gestion rationalisée.

Azure Virtual Machines offre une gamme complète de déploiements SharePoint. Azure Virtual Machines est entièrement pris en charge et testé afin de fournir une expérience optimale avec les autres applications Microsoft. Ainsi, les organisations peuvent facilement configurer et déployer SharePoint Server dans Azure, soit pour mettre en service une infrastructure pour un nouveau déploiement SharePoint, soit pour en développer un existant. Lorsque la charge de travail s'accroît, les organisations peuvent rapidement développer leur infrastructure SharePoint. De même, si la charge de travail diminue, les organisations peuvent souscrire à des ressources à la demande, en ne payant que ce qu'elles utilisent. Azure Virtual Machines fournit une infrastructure exceptionnelle pour une large gamme de besoins, comme indiqué dans les quatre scénarios basés sur SharePoint présentés dans ce document.

Un déploiement réussi de SharePoint Server sur Azure Virtual Machines requiert une planification solide, en tenant compte notamment de la gamme d'options d'architecture et de déploiement de batteries de serveurs critiques. Les informations et les meilleures pratiques présentées dans ce document permettent d'orienter les décisions d'implémentation d'un déploiement SharePoint informé.

<h2>Ressources supplémentaires</h2>

<ul>
<li>
<p>SharePoint sur les services d'infrastructure Azure</p>
<p><a href="http://msdn.microsoft.com/library/dn275955.aspx">http://msdn.microsoft.com/library/dn275955.aspx</a></p>
</li>
<li>
<p>Mise en route avec Azure PowerShell</p>
<p><a href="http://msdn.microsoft.com/library/windowsazure/jj156055">http://msdn.microsoft.com/library/windowsazure/jj156055</a></p>
</li>
<li>
<p>Cmdlets de gestion Azure</p>
<p><a href="http://msdn.microsoft.com/library/windowsazure/jj152841">http://msdn.microsoft.com/library/windowsazure/jj152841</a></p>
</li>
<li>
<p>Outils en ligne de commande et cmdlets PowerShell pour différents systèmes d'exploitation</p>
<p><a href="/fr-fr/manage/downloads/">https://www.windowsazure.com/fr-fr/manage/downloads/</a></p>
</li>
<li>
<p>Manuels d'utilisation et documentation sur les meilleures pratiques</p>
<p><a href="/fr-fr/manage/windows/">https://www.windowsazure.com/fr-fr/manage/windows/</a></p>
</li>
</ul>


<!--HONumber=45--> 
