<properties 
	pageTitle="Instructions d'implémentation des services d'infrastructure Azure" 
	description="Découvrez-en plus sur les principales instructions de conception et d'implémentation pour le déploiement d'une charge de travail informatique dans des services d'infrastructure Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Instructions d'implémentation des services d'infrastructure Azure
 
Ces instructions se concentrent sur les principales décisions et tâches en matière de conception afin de déterminer les diverses ressources impliquées dans la plupart des implémentations de service d'infrastructure Azure.

Azure est une excellente plateforme d'implémentation de configurations de validation technique, car elle ne nécessite qu'un investissement réduit pour tester une approche spécifique d'implémentation de solutions. Cependant, vous devez être en mesure de distinguer les pratiques de validation technique faciles à mettre en place des pratiques plus difficiles et détaillées pour l'implémentation entièrement fonctionnelle et prête pour la production d'une charge de travail informatique.

Ce guide identifie les nombreux domaines pour lesquels la planification est un élément essentiel du bon fonctionnement d'une infrastructure informatique ou de la charge de travail dans Azure. En outre, il permet l'implémentation de solutions sur la plateforme Azure en déterminant l'ordre de création des ressources nécessaires. Même si une certaine flexibilité est possible, Microsoft recommande d'appliquer cet ordre au cours de la planification et de la prise de décision.

1.	Conventions d'affectation de noms
2.	Abonnements et comptes
3.	Stockage
4.	Réseaux virtuels
5.	Services cloud
6.	Groupes à haute disponibilité
7.	Machines virtuelles

Une convention d'affectation de noms adaptée et un ordre spécifique et systématique de création des ressources dans Azure sont des facteurs permettant des réduire considérablement la charge administrative et d'augmenter les chances de succès d'un projet d'implémentation.

> [AZURE.NOTE]Les groupes d'affinités ne sont pas décrits, car leur utilisation est déconseillée. Pour en savoir plus, consultez [À propos des réseaux virtuels régionaux et des groupes d'affinités](https://msdn.microsoft.com/library/azure/jj156085.aspx).

## 1. Conventions d'affectation de noms

Une convention d'affectation de noms adaptée doit être mise en place avant tout processus de création dans Azure. Une convention d'affectation de noms garantit que toutes les ressources ont un nom prévisible, afin de réduire la charge administrative associée à leur gestion.

Vous pouvez choisir de suivre un ensemble spécifique de conventions d'affectation de noms définies pour votre organisation ou pour un compte ou un abonnement Azure spécifique. Bien qu'il soit facile d'établir des règles implicites au sein d'entreprises lorsque vous travaillez avec des ressources Azure, ce modèle n'est pas très souple lorsqu'une équipe doit travailler sur un projet sur Azure.

Vous devez convenir de l'ensemble des conventions d'affectation de noms en amont. Certains facteurs sont à prendre en compte pour l'ensemble des règles en matière de conventions d'affectation de noms.

### Affixes

Lors de la création de certaines ressources, Azure utilise certains paramètres par défaut pour simplifier la gestion des ressources qui leur sont associées. Par exemple, lorsque vous créez la première machine virtuelle pour un nouveau service cloud, le portail de gestion Azure tente d'utiliser le nom de la machine virtuelle comme nom de service cloud pour la machine virtuelle.

Par conséquent, il est utile d'identifier les types de ressources nécessitant un affixe. En outre, spécifiez clairement si l'affixe sera placé

- au début du nom (préfixe)
- à la fin du nom (suffixe)

Voici deux exemples de noms possibles pour un service cloud hébergeant un moteur de calcul :

- Svc-CalculationEngine (préfixe)
- CalculationEngine-Svc (suffixe)

Les affixes peuvent faire référence à différents aspects des ressources spécifiques. Le tableau suivant présente des exemples généralement utilisés.

Aspect | Exemples | Remarques
--- | --- | ---
Environnement | dev, stg, prod | En fonction de l'objectif et du nom de chaque environnement.
Emplacement | usw (West US), use (East US 2) | En fonction de la région du centre de données et de l'organisation.
Composant, service ou produit Azure | Svc pour service cloud, VNet pour réseau virtuel | En fonction du produit auquel la ressource est associée.
Rôle | sql, ora, sp, iis | En fonction du rôle de la machine virtuelle.
Instance | 01, 02, 03, etc. | Pour les ressources pouvant avoir plusieurs instances. Par exemple, des serveurs Web à charge équilibrée dans un service cloud.
		
Lors de l'établissement de conventions d'affectation de noms, assurez-vous qu'elles indiquent clairement les affixes à utiliser pour chaque type de ressource et à quelle position (suffixe ou préfixe).

### Dates

Dans de nombreux cas, il est important de déterminer la date de création à partir du nom d'une ressource.  Microsoft recommande le format de date AAAAMMJJ. Ce format permet non seulement d'enregistrer la date complète, mais également de trier simultanément par ordre alphabétique et par ordre chronologique deux ressources dont les noms diffèrent uniquement au niveau de la date.

### Ressources d'affectation de noms
Vous devez définir chaque type de ressource dans la convention d'affectation de noms, qui doit comprendre des règles définissant l'attribution de noms pour chaque ressource créée. Ces règles doivent s'appliquer à tous les types de ressources, par exemple :

- Abonnements
- Comptes
- Comptes de stockage
- Réseaux virtuels
- Sous-réseaux
- Groupes à haute disponibilité
- Services cloud
- Machines virtuelles
- Points de terminaison
- Groupes de sécurité réseau
- Rôles

Les noms doivent être aussi descriptifs que possible, afin que le nom puisse fournir suffisamment d'informations pour déterminer la ressource à laquelle il fait référence.

### Noms d'ordinateur

Lorsque les administrateurs créent une machine virtuelle, ils doivent spécifier un nom de machine virtuelle dans Microsoft Azure. Microsoft Azure utilise ensuite le nom de la machine virtuelle comme nom de ressource de la machine virtuelle Azure. Azure utilise le même nom comme nom d'ordinateur pour le système d'exploitation installé sur la machine virtuelle. Toutefois, ces noms peuvent ne pas toujours être identiques.

Si une machine virtuelle est créée à partir d'un fichier .VHD qui contient déjà un système d'exploitation, le nom de la machine virtuelle dans Microsoft Azure peut différer du nom d'ordinateur du système d'exploitation de la machine virtuelle. Dans ce cas, la gestion de la machine virtuelle devient plus difficile. C'est pourquoi nous le déconseillons. Assurez-vous que le nom de ressource de la machine virtuelle Azure est toujours le même que le nom d'ordinateur attribué au système d'exploitation de cette machine virtuelle.

Nous recommandons que le nom de la machine virtuelle Azure soit le même que le nom d'ordinateur du système d'exploitation sous-jacent. Pour cela, suivez les règles d'affectation de noms NetBIOS, comme décrit dans [Conventions d'affectation de noms d'ordinateur NetBIOS de Microsoft](https://support.microsoft.com/kb/188997/).

### Noms de compte de stockage

Le nom des comptes de stockage sont régis par des règles spécifiques. Vous pouvez uniquement utiliser des minuscules et des chiffres. Le nom concaténé attribué au service (blob, table ou file d'attente) et le domaine par défaut (core.windows.net) restituent alors un nom DNS globalement valide et unique. Par exemple, si le compte de stockage est appelé mystorageaccount, les URL suivantes qui en résultent doivent être des noms DNS valides et uniques :

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net

En outre, les comptes de stockage peuvent tirer parti de conteneurs. Ils doivent respecter les conventions d'affectation de noms décrites dans [Affectation de noms et références aux conteneurs, objets BLOB et métadonnées](https://msdn.microsoft.com/library/azure/dd135715.aspx).

### Noms des blocs de construction Azure

Les blocs de construction Azure sont des services de niveau d'application généralement offerts par Azure aux applications tirant parti de fonctionnalités PaaS, bien que certaines ressources IaaS puissent en exploiter certaines, notamment Azure SQL et Traffic Manager.

Ces services s'appuient sur un tableau d'artefacts créés et enregistrés dans Azure. Ils doivent être également pris en compte dans vos conventions d'affectation de noms.

### Récapitulatif des instructions d'implémentation pour les conventions d'affectation de noms

Décision :

- Quelles sont vos conventions d'affectation de noms pour les ressources Azure ? 

Tâche :

- Définir les conventions d'affectation de noms en termes d'affixes, la hiérarchie, les valeurs de chaînes de caractères et d'autres stratégies pour les ressources Windows Azure.

## 2. Abonnements et comptes

Pour utiliser Azure, vous avez besoin d'un ou de plusieurs abonnements Azure. Des ressources telles que des services cloud ou des machines virtuelles existent dans le contexte de ces abonnements.

- Les clients d'entreprises disposent généralement d'une inscription d'entreprise, qui est la ressource principale dans la hiérarchie et est associée à un ou plusieurs comptes. 
- Pour les particuliers et les clients sans inscription d'entreprise, la ressource principale est le compte.
- Les abonnements sont associés à des comptes, et chaque compte peut être associé à un ou plusieurs abonnements. Azure enregistre les informations de facturation au niveau de l'abonnement.

La relation compte/abonnement étant limitée à deux niveaux de hiérarchie, il est important d'aligner la convention d'affectation de noms des comptes et des abonnements sur les besoins liés à la facturation. Par exemple, si une entreprise multinationale utilise Azure, elle peut choisir d'avoir un seul compte par région et des abonnements gérés au niveau régional.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub01.png)
  
Par exemple, vous pouvez utiliser la structure suivante.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub02.png)
  
En reprenant le même exemple, si une région décide d'avoir plusieurs abonnements associés à un groupe spécifique, la convention d'affectation de noms doit alors comprendre un code pour l'information supplémentaire dans le nom du compte ou de l'abonnement. Cette organisation permet le transfert de données de facturation pour générer de nouveaux niveaux de hiérarchie lors de l'établissement de rapports de facturation.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub03.png)

Vous trouverez ci-dessous un exemple d'organisation.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub04.png)
 
Microsoft fournit une facturation détaillée au moyen d'un fichier téléchargeable, pour un compte unique ou pour tous les comptes liés à un Contrat Entreprise. Vous pouvez traiter ce fichier, par exemple, à l'aide d'Excel. Dans ce cas, le processus reçoit des données, partage les ressources qui encodent plusieurs niveaux de hiérarchie dans des colonnes distinctes et utilise un tableau croisé dynamique ou PowerPivot pour permettre la création de rapports dynamiques.

### Récapitulatif des instructions d'implémentation pour les abonnements et les comptes

Décision :

- Quel est l'ensemble d'abonnements et de comptes dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?

Tâche :

- Créer l'ensemble d'abonnements et de comptes à l'aide de votre convention d'affectation de noms.

## 3. Stockage

Le stockage fait partie intégrante d'une solution Azure, car il ne fournit pas seulement des services de niveau application, mais il fait également partie de l'infrastructure prenant en charge des machines virtuelles.
 
Il existe deux types de compte de stockage disponibles dans Azure. Le stockage standard vous donne accès au stockage d'objets blob, de tables, de files d'attente et de fichiers. Le stockage Premium est conçu pour des applications hautes performances, telles que les serveurs SQL dans un cluster AlwaysOn, et prend actuellement en charge uniquement les disques de Machine virtuelle Azure.

Les comptes de stockage sont liés à des objectifs d'extensibilité. Pour vous familiariser avec les limites de stockage Azure actuelles, consultez [Abonnement Microsoft Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md#storage-limits). Consultez également [Objectifs de performances et d'extensibilité d'Azure Storage](storage-scalability-targets.md).

Azure crée des machines virtuelles avec un disque de système d'exploitation, et éventuellement plusieurs disques de données facultatifs. Le disque de système d'exploitation et les disques de données sont des objets blob Azure, tandis que le disque temporaire est sauvegardé à l'aide d'un stockage local sur le nœud comprenant l'emplacement de la machine. Le disque temporaire est alors impropre pour des données qui doivent être conservées au cours d'un recyclage de système, dans la mesure où la machine peut être migrée en mode silencieux à partir d'un nœud vers un autre, ce qui implique la perte de toutes les données de ce disque. Ne stockez rien sur le disque temporaire.

Les disques de système d'exploitation et les disques de données ont une taille maximale de 1 023 Go, étant donné que la taille maximale d'un objet blob est de 1 024 Go et qu'il doit contenir les métadonnées (pied de page) du fichier VHD (un Go est constitué de 10 243 octets). Vous pouvez mettre en place un entrelacement de disques dans Windows pour dépasser cette limite.

### Disques agrégés par bandes
Outre la possibilité de créer des disques d'une taille supérieure à 1 023 Go, l'entrelacement de disques améliore les performances dans de nombreux cas en permettant à plusieurs objets blob de sauvegarder le stockage d'un seul volume. Les E/S nécessaires pour écrire et lire des données à partir d'un seul disque sont alors parallélisées.

Azure impose des limites quant à la quantité de disques de données et de bande passante disponible, selon la taille de la machine virtuelle. Pour en savoir plus, consultez la page [Tailles de machines virtuelles et de services cloud pour Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Si vous utilisez l'entrelacement pour les disques de données Azure, respectez les consignes suivantes :

- Les disques de données doivent toujours avoir la taille maximale (1 023 Go)
- Attachez le nombre maximum autorisé de disques de données pour la taille de machine virtuelle
- Utilisez la configuration des espaces de stockage
- Utilisez la configuration de l'entrelacement du stockage
- Évitez d'utiliser des options de mise en cache des disques de données Azure (Stratégie de mise en cache = Aucune)

Pour en savoir plus, consultez la page [Espaces de stockage : une conception pour la performance](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx) (en anglais).

### Comptes de stockage multiples

L'utilisation de plusieurs comptes de stockage pour sauvegarder les disques associés à de nombreuses machines virtuelles garantit que les E/S agrégées de ces disques sont largement en-dessous des objectifs d'évolutivité pour chaque compte de stockage.

Microsoft vous recommande de commencer par le déploiement d'une machine virtuelle par compte de stockage.

### Conception de la disposition du stockage

Pour mettre en œuvre ces stratégies de manière implémenter le sous-système de disques des machines virtuelles avec des performances optimales, une charge de travail ou une infrastructure informatique tire généralement parti de plusieurs comptes de stockage. Ils hébergeront de nombreux objets blob VHD. Dans certains cas, plusieurs objets blob sont associés à un seul volume sur une machine virtuelle.

Cette situation peut compliquer les tâches de gestion. La conception d'une stratégie solide pour le stockage, notamment la dénomination appropriée pour les disques sous-jacents et les objets BLOB associés, est un élément clé.

### Récapitulatif des instructions d'implémentation pour le stockage

Décisions :

- Avez-vous besoin d'un entrelacement pour créer des disques d'une taille supérieure à 500 To ?
- Avez-vous besoin d'un entrelacement pour optimiser les performances de votre charge de travail ?
- Quel est l'ensemble de comptes de stockage dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?

Tâche :

- Créer l'ensemble de comptes de stockage à l'aide de votre convention d'affectation de noms. Vous pouvez utiliser le portail Azure Preview, le portail de gestion Azure ou l'applet de commande PowerShell **New-AzureStorageAccount**.

## 4. Services cloud

Les services cloud sont un bloc de construction fondamental d'Azure, à la fois pour les services PaaS et IaaS. Pour le PaaS, les services cloud représentent une association de rôles dont les instances peuvent communiquer entre elles. Les services cloud sont associés à une adresse IP virtuelle publique (VIP) et à un équilibrage de charge, qui prend le trafic entrant provenant d'Internet et équilibre la charge pour les rôles configurés de manière recevoir le trafic.

Dans le cas de l'IaaS, les services cloud offrent des fonctionnalités similaires, bien que, dans la plupart des cas, la fonctionnalité d'équilibrage de charge soit utilisée pour transférer le trafic vers des ports TCP ou UDP spécifiques à partir d'Internet vers les nombreuses machines virtuelles au sein de ce service cloud.

Les noms de service cloud sont particulièrement importants dans l'IaaS, car Azure les utilise en tant que partie intégrante de la convention d'affectation de noms pour les disques par défaut. Le nom de service cloud ne peut contenir que des lettres, des chiffres et des traits d'union. Le premier et le dernier caractère du champ doivent être une lettre ou un chiffre.

Microsoft Azure expose les noms de service cloud, dans la mesure où ils sont associés à l'adresse IP virtuelle dans le domaine « cloudapp.net ». Pour une expérience utilisateur de l'application optimale, un surnom doit être configuré pour remplacer le nom de service cloud complet. Pour cela, utilisez un enregistrement CNAME dans votre DNS public qui mappe le nom DNS public de votre ressource (par exemple, www.contoso.com) sur le nom DNS du service cloud qui héberge la ressource (par exemple, le service cloud qui héberge les serveurs web pour www.contoso.com).

En outre, il se peut que la convention d'affectation de noms utilisée pour les services cloud doive tolérer des exceptions, car les noms de service cloud doivent être uniques parmi tous les autres services cloud Microsoft Azure, quel que soit le locataire Microsoft Azure.

Les abonnements Azure peuvent prendre en charge 200 services cloud au maximum.

### Récapitulatif des instructions d'implémentation pour les services cloud

Décision :

- Quel est l'ensemble de services cloud dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ? 

Tâche :

- Créer l'ensemble de services cloud à l'aide de votre convention d'affectation de noms. Vous pouvez utiliser le portail de gestion Azure ou l'applet de commande PowerShell **New-AzureService**.

## 5. Réseaux virtuels

L'étape logique suivante consiste à créer les réseaux virtuels nécessaires pour prendre en charge les communications entre les machines virtuelles dans la solution. Bien qu'il soit possible d'héberger plusieurs machines virtuelles d'une charge de travail informatique dans un unique service cloud, les réseaux virtuels sont recommandés.

Les réseaux virtuels constituent un conteneur pour les machines virtuelles pour lesquelles vous pouvez également spécifier des sous-réseaux, un adressage personnalisé et des options de configuration de DNS. Les machines virtuelles au sein du même réseau virtuel peuvent communiquer directement avec d'autres ordinateurs dans le même réseau virtuel, quel que soit le service cloud dont ils sont membres. Cette communication reste privée au sein du réseau virtuel, sans avoir besoin de passer par des points de terminaison publics. Cette communication peut être établie par l'intermédiaire d'une adresse IP ou par un nom, à l'aide d'un serveur DNS installé dans le réseau virtuel ou sur site, si l'ordinateur virtuel est connecté au réseau d'entreprise.

### Connectivité de site
Si les utilisateurs et les ordinateurs sur site ne nécessitent pas de connectivité continue aux machines virtuelles dans un réseau virtuel Azure, créez un réseau virtuel uniquement dans le cloud.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet01.png)
 
Cette solution s'applique généralement à des charges de travail Internet, telles qu'un serveur web Internet. Vous pouvez gérer ces machines virtuelles à l'aide de connexions Bureau à distance, de sessions PowerShell à distance, de connexions SSH (Secure Shell) et de connexions VPN de point à site.

Les réseaux virtuels ne se connectant pas à votre réseau local, les réseaux virtuels uniquement basés sur le cloud peuvent utiliser une partie de l'espace d'adressage IP privé.

Si les utilisateurs et ordinateurs locaux nécessitent une connectivité continue aux machines virtuelles dans un réseau virtuel Azure, créez un réseau virtuel intersite et connectez-le à votre réseau local avec une connexion ExpressRoute ou VPN de site à site.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet02.png)
 
Dans cette configuration, le réseau virtuel Azure est essentiellement une extension cloud de votre réseau local.

Les réseaux virtuels intersite ne se connectant pas à votre réseau local, ils doivent utiliser une partie unique de l'espace d'adressage utilisé par votre organisation. Et l'infrastructure de routage doit prendre en charge le routage de trafic vers cette partie en le transmettant à votre périphérique VPN local.

Pour autoriser les paquets à circuler de votre réseau virtuel intersite vers votre réseau local, vous devez configurer le jeu de préfixes d'adresses locales pertinents dans le cadre de la définition du réseau local pour le réseau virtuel. En fonction de l'espace d'adressage du réseau virtuel et de l'ensemble des emplacements locaux concernés, le réseau local peut comprendre de nombreux préfixes d'adresse.

Vous pouvez convertir un réseau virtuel cloud en un réseau virtuel intersite, mais il nécessitera probablement une renumérotation de votre espace d'adressage de réseau virtuel, de vos sous-réseaux et de vos machines virtuelles qui utilisent des adresses IP statiques affectées par Azure, appelées IP dynamiques (DIP). Par conséquent, déterminez avec attention le type de réseaux virtuels dont vous avez besoin (cloud ou intersite) avant de les créer.

### Sous-réseaux
Les sous-réseaux vous permettent d'organiser des ressources apparentées, soit logiquement (par exemple, un sous-réseau pour les machines virtuelles associées à la même application), soit physiquement (par exemple, un sous-réseau par service cloud), ou d'employer des techniques d'isolation de sous-réseaux pour renforcer la sécurité.

Pour les réseaux virtuels intersite, vous devez concevoir des sous-réseaux avec les conventions que vous utilisez pour les ressources locales, en gardant à l'esprit qu'**Azure utilise toujours les trois premières adresses IP de l'espace d'adressage pour chaque sous-réseau**. Pour déterminer le nombre d'adresses nécessaires pour le sous-réseau, comptez le nombre de machines virtuelles dont vous avez besoin maintenant, estimez sa croissance future, puis utilisez le tableau suivant pour déterminer la taille du sous-réseau.
 
Nombre de machines virtuelles nécessaires | Nombre de bits hôte nécessaires | Taille du sous-réseau 
--- | --- | --- 
1 à 3 | 3 | /29
4 à 11 | 4 | /28
12 à 27 | 5 | /27
28 à 59 | 6 | /26
60 à 123 | 7 | /25

> [AZURE.NOTE]Pour des sous-réseaux locaux normaux, le nombre maximal d'adresses d'hôte pour un sous-réseau avec n bits hôte est 2<sup>n</sup> – 2. Pour un sous-réseau Azure, le nombre maximal d'adresses d'hôte pour un sous-réseau avec n bits hôte est 2<sup>n</sup> – 5 (2 plus 3 pour les adresses qu'Azure utilise sur chaque sous-réseau).

Si vous choisissez une taille de sous-réseau trop petite, vous devrez renuméroter et redéployer les machines virtuelles dans le sous-réseau.

### Récapitulatif des instructions d'implémentation pour les réseaux virtuels

Décisions :

- Quel type de réseau virtuel devez-vous pour héberger votre charge de travail ou votre infrastructure informatique (cloud ou intersite) ?
- Pour les réseaux virtuels intersite, de quelle taille d'espace adressage avez-vous besoin pour héberger les sous-réseaux et les machines virtuelles maintenant et pour une extension future raisonnable ?

Tâches :

- Définir l'espace d'adressage du réseau virtuel.
- Définir l'ensemble de sous-réseaux et l'espace d'adressage pour chacun.
- Pour les réseaux virtuels intersite, définir l'ensemble des espaces d'adressage de réseau local pour les emplacements locaux auxquels les machines virtuelles doivent accéder dans le réseau virtuel.
- Créer le réseau virtuel à l'aide de votre convention d'affectation de noms. Vous pouvez utiliser le portail Azure Preview ou le portail de gestion Azure.

## 6. Groupes à haute disponibilité

Dans le PaaS Azure, les services cloud comprennent un ou plusieurs rôles qui exécutent du code d'application. Les rôles peuvent avoir une ou plusieurs instances de machine virtuelle provisionnées automatiquement par la structure. À un moment donné, Azure peut mettre à jour les instances de ces rôles, mais, comme elles font partie du même rôle, Azure ne les met pas à jour simultanément afin d'éviter une interruption de service pour le rôle.

Dans l'IaaS Azure, le concept de rôle n'est pas significatif, étant donné que chaque machine virtuelle IaaS représente un rôle avec une seule instance. Le concept de groupes à haute disponibilité a été introduit afin qu'Azure n'interrompe pas deux machines associées ou plus simultanément (par exemple, pour les mises à jour du système d'exploitation du nœud où elles se trouvent). Un groupe à haute disponibilité indique à Azure ne pas interrompre simultanément toutes les machines dans le même groupe à haute disponibilité afin d'éviter une interruption de service. Les membres de la machine virtuelle d'un groupe à haute disponibilité ont un contrat de niveau service stipulant une disponibilité de 99,95 %.

Les groupes à haute disponibilité doivent faire partie de la planification de la haute disponibilité de la solution. Un groupe à haute disponibilité est défini comme l'ensemble des machines virtuelles au sein d'un unique service cloud qui ont le même nom de groupe de disponibilité. Vous pouvez créer des groupes à haute disponibilité après la création de services cloud.

### Récapitulatif des instructions d'implémentation pour groupes à haute disponibilité

Décision :

- De combien de groupe à haute disponibilité avez-vous besoin pour les différents rôles et niveaux de votre charge de travail ou infrastructure informatique ?

Tâche :

- Définir l'ensemble des groupes à haute disponibilité à l'aide de votre convention d'affectation de noms. Vous pouvez associer une machine virtuelle à un groupe à haute disponibilité lorsque vous créez des machines virtuelles, ou vous pouvez associer une machine virtuelle à un groupe à haute disponibilité après avoir créé la machine virtuelle.

## 7. Machines virtuelles

Dans le PaaS Azure, Azure gère des machines virtuelles et leurs disques associés. Vous devez créer et nommer des services cloud et des rôles, et Azure créera ensuite des instances associées à ces rôles. Dans le cas de l'IaaS Azure, c'est à vous de donner des noms aux services cloud, aux machines virtuelles et aux disques associés.

Pour réduire la charge administrative, le portail de gestion Azure utilise le nom d'ordinateur comme suggestion pour le nom par défaut du service cloud associé (pour le cas où le client choisit de créer un service cloud dans le cadre de l'Assistant de création de machines virtuelles).

En outre, les disques de noms Azure et leurs objets blob VHD associés à l'aide d'une combinaison du nom du service cloud, du nom d'ordinateur et la date de création.

En général, le nombre de disques sera nettement supérieur à la quantité de machines virtuelles. Lorsque vous manipulez des machines virtuelles, veillez à éviter les disques orphelins. En outre, les disques peuvent être supprimés sans supprimer l'objet blob associé. Si c'est le cas, l'objet blob reste dans le compte de stockage jusqu'à sa suppression manuelle.

### Récapitulatif des instructions d'implémentation pour les machines virtuelles

Décision :

- Quel est le nombre de machines virtuelles que vous devez fournir pour l'infrastructure ou la charge de travail informatique ?

Tâches :

- Définir chaque nom de machine virtuelle à l'aide de votre convention d'affectation de noms.
- Créer votre machine virtuelle à l'aide du portail Azure Preview, du portail de gestion Azure ou de l'applet de commande PowerShell **New-AzureVM**.

## Exemple d'une charge de travail informatique : le moteur d'analyse financière Contoso

La société Contoso Corporation a développé un moteur d'analyse financière de nouvelle génération avec les algorithmes propriétaires de pointe pour vous aider dans vos futures transactions financières. Elle souhaite proposer ce moteur à ses clients sous forme d'un ensemble de serveurs dans Azure constitué :

- de deux serveurs web IIS (ou plus) exécutant des services web personnalisés dans un niveau web ;
- de deux serveurs d'application IIS (ou plus) qui exécutent leurs calculs dans un niveau application ;
- d'un cluster SQL Server 2014 avec groupes de disponibilité AlwaysOn (deux serveurs SQL et un témoin de nœud majoritaire) qui stocke les données d'historique et de calcul continu dans un niveau de base de données ;
- de deux contrôleurs de domaine Active Directory pour une forêt autonome et un domaine dans le niveau d'authentification requis par les clusters SQL Server ;
- de tous les serveurs se trouvant sur deux sous-réseaux, un sous-réseau frontal pour les serveurs web et un sous-réseau back-end pour les serveurs d'applications, un cluster SQL server 2014 et des contrôleurs de domaine

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-tiers.png)
 
La charge du trafic Web entrant sécurisé des clients Contoso sur Internet doit être répartie sur les serveurs web. Le trafic de requêtes de calcul sous la forme de requêtes HTTP depuis les serveurs web doit être réparti sur les serveurs d'applications. En outre, le moteur doit être conçu pour la haute disponibilité.

La conception qui en résulte doit comprendre :

- un abonnement et un compte Azure ;
- des comptes de stockage ;
- un réseau virtuel avec deux sous-réseaux ;
- un ensemble de services cloud ;
- des groupes à haute disponibilité pour les groupes de serveurs avec un rôle similaire ;
- des machines virtuelles.

Tous les éléments ci-dessus sont conformes aux conventions d'affectation de noms de Contoso :

- Contoso utilise [Charge de travail informatique]-[Emplacement]-[Ressources Azure] comme préfixe. Pour cet exemple, « azfae » (Azure Financial Analysis Engine, moteur d'analyse financière Azure) est le nom de la charge de travail informatique et « use » (East US 2) est l'emplacement, car la plupart des clients initiaux de Contoso sont situés sur la côte est des États-Unis.
- Les comptes de stockage utilisent contosoazfaeusesa[description]. Notez que contoso a été ajouté au préfixe pour garantir l'unicité et que les noms de compte de stockage ne prennent pas en charge l'utilisation de traits d'union.
- Les services Cloud utilisent contoso-azfae-use-cs-[description]. Notez que contoso a été ajouté au préfixe pour garantir l'unicité.
- Les réseaux virtuels utilisent AZFAE-USE-VN[numéro].
- Les groupes à haute disponibilité utilisent azfae-use-as-[rôle].
- Les noms de machine virtuelle utilisent azfae-use-vm-[nom de machine virtuelle].

### Abonnements et comptes Azure

Contoso utilise son abonnement d'entreprise, nommé Contoso Enterprise Subscription, pour fournir des informations de facturation pour cette charge de travail informatique.

### Comptes de stockage

Contoso a déterminé que deux comptes de stockage sont nécessaires :

- **contosoazfaeusesawebapp** pour le stockage standard de serveurs Web, de serveurs d'applications et de contrôleurs de domaine avec leurs disques de données supplémentaires ;
- **contosoazfaeusesasqlclust** pour le stockage premium de serveurs SQL Server en cluster et de leurs disques de données supplémentaires.

Contoso les a créés avec les commandes PowerShell suivantes :

	New-AzureStorageAccount -StorageAccountName "contosoazfaeusesawebapp" -Location "East US 2"
	New-AzureStorageAccount -StorageAccountName "contosoazfaeusesasqlclust" -Location "East US 2" -Type Premium_LRS

### Un réseau virtuel avec des sous-réseaux

Étant donné que le réseau virtuel n'a pas besoin d'une connectivité continue au réseau Contoso local, Contoso a décidé d'adopter un réseau virtuel cloud.

Ils créé un réseau virtuel cloud avec les paramètres suivants à l'aide du portail Azure Preview :

- Nom : AZFAE-USE-VN01
- Emplacement : East US 2
- Espace d'adressage du réseau virtuel : 10.0.0.0/8
- Premier sous-réseau :
	- Nom : FrontEnd
	- Espace d'adressage : 10.0.1.0/24
- Second sous-réseau :
	- Nom : BackEnd
	- Espace d'adressage : 10.0.2.0/24

### Services cloud

Contoso a créé deux services cloud :

- **contoso-azfae-use-cs-frontend** pour les serveurs Web frontaux ;
- **contoso-azfae-use-cs-backend** pour les serveurs d'applications back-end, les serveurs SQL server en cluster et les contrôleurs de domaine.

Contoso les a créés avec les commandes PowerShell suivantes :

	New-AzureService -Service "contoso-azfae-use-cs-frontend" -Location "East US 2"
	New-AzureService -Service "contoso-azfae-use-cs-backend" -Location "East US 2"

### Groupes à haute disponibilité

Pour assurer une haute disponibilité sur les quatre niveaux de son moteur d'analyse financière, Contoso a adopté quatre groupes à haute disponibilité :

- **azfae-use-as-dc** pour les contrôleurs de domaine ;
- **azfae-use-as-web** pour les serveurs Web ;
- **azfae-use-as-app** pour les serveurs d'applications ;
- **azfae-use-as-sql** pour les serveurs du cluster SQL Server.

Ces groupes à haute disponibilité seront créés avec les machines virtuelles.

### Machines virtuelles

Contoso a donné les noms suivants à ses machines virtuelles :

- **azfae-use-vm-dc01** pour le premier contrôleur de domaine ;
- **azfae-use-vm-dc02** pour le second contrôleur de domaine ;
- **azfae-use-vm-web01** pour le premier serveur Web ;
- **azfae-use-vm-web02** pour le second serveur Web ;
- **azfae-use-vm-app01** pour le premier serveur d'applications ;
- **azfae-use-vm-app02** pour le second serveur d'applications ;
- **azfae-use-vm-sql01** pour le premier serveur SQL dans le cluster SQL Server ;
- **azfae-use-vm-sql02** pour le second serveur SQL dans le cluster SQL Server ;
- **azfae-use-vm-sqlmn01** pour le témoin de nœud majoritaire dans le cluster SQL Server.

Voici la configuration obtenue.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-config.png)
 
Cette configuration comprend :

- un réseau virtuel cloud avec deux sous-réseaux (FrontEnd et BackEnd) ;
- deux services cloud ;
- deux comptes de stockage ;
- quatre groupes à haute disponibilité, un pour chaque niveau du moteur d'analyse financière ;
- les machines virtuelles pour les quatre niveaux ;
- un jeu d'équilibrage de charge externe pour le trafic Web basé sur HTTPS depuis Internet vers les serveurs web ;
- un jeu d'équilibrage de charge interne pour le trafic Web non crypté depuis les serveurs Web vers les serveurs d'applications.

Ces commandes Azure PowerShell créent les machines virtuelles dans cette configuration pour les comptes de stockage, les services cloud et le réseau virtuel créés précédemment.

	#Specify the storage account for the web and application servers
	Set-AzureSubscription –SubscriptionName "Contoso Enterprise Subscription" -CurrentStorageAccountName "contosoazfaeusesawebapp"
	
	#Specify the cloud service name for the web servers
	$ServiceName="contoso-azfae-use-cs-frontend"
	
	#Get the image string for the latest version of the Windows Server 2012 R2 Datacenter image in the gallery
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	#Create the first web server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first web server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-web01 -InstanceSize large -ImageName $image -AvailabilitySetName azfae-use-as-web
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames FrontEnd
	$vm1 | Add-AzureEndpoint -Name Web1 -Protocol tcp -LocalPort 443 -PublicPort 443 -LBSetName "WebSet" -DefaultProbe
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second web server 
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second web server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-web02 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-web
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames FrontEnd
	$vm1 | Add-AzureEndpoint -Name Web2 -Protocol tcp -LocalPort 443 -PublicPort 443 -LBSetName "WebSet" -DefaultProbe
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Specify the cloud service name for the application, SQL server, and authentication tiers
	$ServiceName="contoso-azfae-use-cs-backend"
	
	#Create the first domain controller server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-dc01 -InstanceSize Small -ImageName $image -AvailabilitySetName azfae-use-as-dc
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second domain controller server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-dc02 -InstanceSize Small -ImageName $image -AvailabilitySetName azfae-use-as-dc
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-	AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create an internal load balancer instance for the application server tier 
	Add-AzureInternalLoadBalancer -ServiceName $ServiceName -InternalLoadBalancerName "AppTierILB" –SubnetName BackEnd –StaticVNetIPAddress 10.0.2.100
	
	#Create the first application server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first application server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-app01 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-app
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureEndpoint -Name App1 -Protocol tcp -LocalPort 80 -PublicPort 80 -LBSetName "AppSet" -InternalLoadBalancerName "AppTierILB" -DefaultProbe
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 500 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-	AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second application server 
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second application server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-app02 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-app
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Add-AzureEndpoint -Name App2 -Protocol tcp -LocalPort 80 -PublicPort 80 -LBSetName "AppSet" -InternalLoadBalancerName "AppTierILB" -DefaultProbe
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 500 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Specify the premium storage account for the SQL Server cluster
	Set-AzureSubscription –SubscriptionName "Contoso Enterprise Subscription" -CurrentStorageAccountName "contosoazfaeusesasqlclust"
	
	#Create the majority node witness server for the SQL Server cluster
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the majority node witness server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sqlmn01 -InstanceSize Medium -ImageName $image -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Change the image string for the latest version of the SQL Server 2014 image in the gallery
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	#Create the first SQL Server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sql01 -InstanceSize A5 -ImageName $image  -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1000 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second SQL Server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sql02 -InstanceSize A5 -ImageName $image  -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1000 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01

## Ressources supplémentaires

[Abonnement Microsoft Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md#storage-limits)

[Tailles de machines virtuelles et services cloud pour Windows Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx)

[Objectifs de performance et évolutivité d'Azure Storage](storage-scalability-targets.md)

<!--HONumber=54-->