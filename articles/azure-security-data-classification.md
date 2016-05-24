<properties
   pageTitle="Classification des données pour Azure | Microsoft Azure"
   description="Cet article fournit une présentation des principes fondamentaux de la classification des données et souligne la plus-value du processus, en particulier dans le contexte du cloud computing et de l’utilisation de Microsoft Azure."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/11/2016"
   ms.author="yurid"/>

# Classification des données pour Azure

Cet article fournit une présentation des principes fondamentaux de la classification des données et souligne la plus-value du processus, en particulier dans le contexte du cloud computing et de l’utilisation de Microsoft Azure.

## Principes fondamentaux de la classification des données

Une classification des données réussie au d’une organisation requiert une large reconnaissance de vos besoins et une connaissance approfondie de l’emplacement de vos ressources de données.
 
Les données peuvent être :

- Au repos 
- En cours de traitement 
- En transit 
 
Ces trois états nécessitent des solutions techniques uniques pour la classification des données, toutefois les principes appliqués doivent être identiques pour chacun. Les données classées comme confidentielles doivent le rester au repos, en cours de traitement et en transit.
 
Les données peuvent également être structurées ou non structurées. Les processus de classification standard pour les données structurées des bases de données et des feuilles de calcul sont moins complexes et fastidieux à gérer que ceux des données non structurées, telles que des documents, du code source et des e-mails.

> [AZURE.TIP] pour plus d’informations sur les fonctionnalités d’Azure et les meilleures pratiques relatives au chiffrement des données, voir [Azure Data Encryption Best Practices](azure-security-data-encryption-best-practices.md) (Meilleures pratiques relatives au chiffrement des données Azure).

En règle générale, les organisations disposent d’un nombre plus important de données non structurées que de données structurées. Indépendamment de savoir si les données sont structurées ou non, il est important pour vous de gérer la sensibilité des données. Lorsqu’elle est correctement mise en œuvre, la classification des données permet de s’assurer que les données sensibles ou confidentielles sont gérées de manière plus sécurisée que les ressources de données considérées comme publiques ou dont la diffusion est libre.

### Contrôle de l’accès aux données 

Authentification et autorisation sont souvent confondues et leurs rôles mal compris. En réalité elles sont très différentes, comme l’illustre la figure suivante :

![Accès aux données et contrôle](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### Authentification 

L’authentification se compose généralement d’au moins deux parties : un nom d’utilisateur ou un ID d’utilisateur permettant d’identifier un utilisateur et un jeton, comme un mot de passe pour confirmer que les informations d’identification de nom d’utilisateur sont valides. Le processus ne permet à l’utilisateur authentifié d’accéder à des éléments ou des services ; Il vérifie seulement son identité.

> [AZURE.TIP] [Azure Active Directory](./active-directory/active-directory-whatis.md) fournit des services d’identité basés sur le cloud qui vous permettent d’authentifier et d’autoriser les utilisateurs.

### Autorisation
 
L’autorisation est le processus permettant à un utilisateur authentifié d’accéder à une application, un jeu de données, un fichier de données ou un autre objet. Le fait d’attribuer à des utilisateurs authentifiés des droits d’utilisation, de modification ou de suppression d’éléments auxquels ils peuvent accéder demande de s’intéresser à la classification des données.

Une autorisation réussie nécessite l’implémentation d’un mécanisme permettant de valider les besoins de chaque utilisateur en termes d’accès aux fichiers et informations en se basant sur des considérations relatives au rôle, à la stratégie de sécurité et à la stratégie de risque. Par exemple, il n’est peut-être pas nécessaire que les données issues d’applications métier spécifiques soient accessibles par tous les employés. Seul un petit sous-ensemble d’employés aura probablement besoin d’accéder aux fichiers des ressources humaines (RH). Toutefois, il est nécessaire qu’un système efficace d’authentification des utilisateurs soit en place pour que les organisations puissent contrôler l’identité de la personne accédant aux données, y compris quand et comment elle y accède.

> [AZURE.TIP] dans Microsoft Azure, veillez à utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure pour accorder uniquement la quantité d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Pour en savoir plus, consultez [Utiliser les attributions de rôle pour gérer l’accès à vos ressources Azure Active Directory](./active-directory/role-based-access-control-configure.md).

### Rôles et responsabilités du cloud computing 

Bien que les fournisseurs de cloud puissent aider à gérer les risques, les clients doivent s’assurer que la gestion et l’application de la classification des données est correctement mise en œuvre pour fournir le niveau approprié de services de gestion des données.
 
Les responsabilités en matière de classification de données varient selon le modèle de service cloud mis en place, comme indiqué dans l’illustration suivante. Les trois principaux modèles de service cloud sont les suivants : IaaS (infrastructure as a service), PaaS (platform as a service) et SaaS (software as a service). L’implémentation des mécanismes de classification des données varie également en fonction de la dépendance à l’égard du fournisseur de cloud et de ses attentes.

![contrôleur](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Même si vous êtes responsable de la classification de vos données, les fournisseurs de cloud doivent s’engager par écrit sur le mode de sécurisation et de gestion de la confidentialité des données client stockées dans leur cloud.

- Les obligations des **fournisseurs IaaS** se limitent à s’assurer que l’environnement virtuel peut prendre en charge les fonctionnalités de classifications des données et les exigences de conformité des clients. Les fournisseurs IaaS jouent un rôle mineur dans la classification des données, dans la mesure où ils doivent s’assurer uniquement que les données du client sont conformes aux exigences de conformité. Toutefois, les fournisseurs doivent garantir la conformité de leurs environnements virtuels aux exigences de classification des données en plus de sécuriser leurs centres de données.
- Les obligations des **fournisseurs PaaS** peuvent être diverses, car la plateforme peut être utilisée dans une approche en couche pour assurer la sécurité d’un outil de classification. Ils peuvent être chargés de l’authentification et éventuellement de certaines règles d’autorisation et doivent fournir des fonctionnalités de sécurité et de classification des données à leur couche d’application. À l’instar des fournisseurs IaaS, les fournisseurs PaaS doivent garantir que leur plateforme est conforme aux exigences de classification des données pertinentes.
- Les **fournisseurs SaaS** sont souvent considérés comme faisant partie d’une chaîne d’autorisation et doivent s’assurer que les données stockées dans l’application SaaS peuvent être contrôlées par type de classification. Les applications SaaS peuvent être utilisées pour les applications métier et par leur besoin, de par leur nature, de fournir les moyens d’authentifier et d’autoriser les données utilisées et stockées. 

## Processus de classification 

De nombreuses organisations comprenant le besoin de classification des données et souhaitant la mettre en œuvre ne savent pas par où commencer.

Une façon simple et efficace d’implémenter la classification des données consiste à procéder selon le modèle PLAN, DO, CHECK, ACT (PRÉVOIR, DÉPLOYER, VÉRIFIER, AGIR) à partir d’un fichier [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). La figure suivante présente les tâches qui sont requises pour implémenter la classification des données dans ce modèle.

1. **PLAN (PRÉVOIR)**. Identifiez les ressources de données, un opérateur de données pour déployer le programme de classification et développez les profils de protection. 
2. **DO (DÉPLOYER)**. Une fois les règles de classification des données acceptées, déployez le programme et implémentez des technologies de mise en œuvre pour les données confidentielles en fonction des besoins.  
3. **CHECK (VÉRIFIER)**. Vérifiez et validez les rapports pour vous assurer que les outils et les méthodes utilisées répondent de manière efficace aux stratégies de classification. 
4. **ACT (AGIR)**. Vérifiez l’état de l’accès aux données et consultez les fichiers et les données nécessitant une révision à l’aide d’une méthodologie de reclassification et de révision visant à adopter les modifications et à faire face aux nouveaux risques.  

![Plan, do, Check, Act (Prévoir, déployer, vérifier et agir)](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###Sélectionner un modèle de terminologie qui répond à vos besoins.
 
Il existe plusieurs types de processus de classification des données, notamment des processus manuels, des processus basés sur l’emplacement qui classent les données en fonction de l’emplacement d’un utilisateur ou d’un système, des processus basés sur une application, comme la classification spécifique d’une base de données et des processus automatisés utilisés par diverses technologies, dont certains sont décrits dans la section « Protection des données confidentielles » plus loin dans cet article.
 
Cet article présente deux modèles de terminologie généralisés reposant sur des modèles bien utilisés et respectés dans le secteur. Ces modèles de terminologie, lesquels proposent trois niveaux de sensibilité de classification, figurent dans le tableau suivant.

> [AZURE.NOTE] lors de la classification d’un fichier ou d’une ressource qui combine des données qui seraient généralement classées à différents niveaux, le plus haut niveau de classification présent doit établir la classification globale. Par exemple, un fichier contenant des données sensibles ou limitées doit être classé comme restreint.

| **Sensibilité** | **Modèle de terminologie 1** | **Modèle de terminologie 2** |
|--------------------|---------------------------|-------------------------|
| Élevée | Confidentiel | Restreint |
| Moyenne | À usage interne uniquement | Sensible |
| Faible | Public | Non restreint |

#### Confidentiel (limité) 

Les informations classées comme confidentielles ou restreintes incluent des données, dont la perte ou la compromission peuvent avoir des répercussions graves pour des individus ou des organisations. Ces informations sont souvent fournies en cas de besoin et peuvent inclure :

- Des données personnelles, notamment des informations d’identification personnelle, telles que des numéros de sécurité sociale ou de carte d’identité, de passeport, de carte de crédit, de permis de conduire, de dossier médical et d’assurance maladie.  
- Des données financières, notamment des numéros de compte financier, tels que des numéros de compte bancaire ou d’investissement. 
- Des documents professionnels, tels que des documents ou des données uniques ou soumis à la propriété intellectuelle.  
- Des données juridiques, notamment des documents d’avocat potentiellement confidentiels. 
- Des données d’authentification, notamment des clés de chiffrement privées, des combinaisons nom d’utilisateur/mot de passe ou d’autres séquences d’identification, telles que des fichiers de clés biométriques privées. 

Les données classées comme confidentielles sont souvent soumises à des exigences réglementaires et de conformité en matière de gestion des données.

#### À usage interne uniquement (sensible)
 
Les informations classées comme étant de sensibilité moyenne incluent les fichiers et les données, dont la perte ou la destruction n’aurait pas d’impact majeur sur une organisation et/ou un individu. Ces informations peuvent inclure les éléments suivants :

- Les e-mails, dont la suppression ou la diffusion n’entraînerait pas de situation de crise (à l’exclusion des boîtes aux lettres ou des e-mails provenant d’individus identifiés dans la classification confidentielle).  
- Les documents et les fichiers qui n’incluent pas de données confidentielles.
 
En général, cette classification inclut tout ce qui n’est pas confidentiel. Cette classification peut inclure la plupart des données d’entreprise, dans la mesure où la plupart des fichiers gérés ou utilisés quotidiennement peuvent être classés comme sensibles. À l’exception des données rendues publiques ou confidentielles, toutes les données d’une entreprise peuvent être classées comme étant sensibles par défaut.

#### Public (non restreint)
 
Les informations classées comme étant publiques incluent les fichiers et les données qui ne sont pas critiques pour l’entreprise ou son fonctionnement. Cette classification peut également inclure des données délibérément diffusées au public pour être utilisées, telles que des documents marketing ou des communiqués de presse. En outre, cette classification peut inclure des données, telles que des courriers indésirables stockés par un service de messagerie.

### Définir la propriété des données
 
Il est important d’établir de manière claire une chaîne de surveillance de propriété de toutes les ressources de données. Le tableau suivant identifie les différents rôles de propriété des données dans un effort de classification des données et leurs droits respectifs.

| **Rôle** | **Créer** | **Modifier/supprimer** | **Déléguer** | **Lire** | **Archiver/restaurer** |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Propriétaire | X | X | X | X | X |
| Opérateur | | | X | | |
| Administrateur | | | | | X |
| Utilisateur* | | X | | X | |
**** Les utilisateurs peuvent également bénéficier de droits supplémentaires, comme la modification et la suppression par un opérateur*

> [AZURE.NOTE] ce tableau ne fournit pas de liste exhaustive des rôles et des droits, mais simplement un échantillon représentatif.

Le **propriétaire de la ressource de données** est le créateur d’origine des données, qui peut déléguer la propriété et affecter un responsable. Lorsqu’un fichier est créé, le propriétaire doit être en mesure d’affecter une classification, ce qui signifie qu’il est chargé de comprendre ce qui doit être considéré comme confidentiel selon les stratégies de son organisation. Toutes les données d’un propriétaire de ressources de données peuvent être classées automatiquement comme étant à usage interne uniquement (sensible) à moins qu’il ne soit chargé de l’appartenance ou de la création de types de données confidentielles (restreintes). Souvent, le rôle du propriétaire change une fois que les données sont classées. Par exemple, le propriétaire peut créer une base de données d’informations classées et abandonner ses droits à l’opérateur de données.

> [AZURE.NOTE] les propriétaires des ressources de données utilisent souvent une combinaison de services, de périphériques et de supports, dont certains sont personnels et d’autres appartiennent à l’organisation. Une stratégie d’organisation clairement établie peut permettre de garantir que l’utilisation d’appareils, tels que des ordinateurs portables et des appareils intelligents est en conformité avec les directives relatives à la classification des données.

L’**opérateur de la ressource de données** est affecté par le propriétaire de la ressource (ou son délégué) dans le but de gérer la ressource en fonction des accords conclus avec le propriétaire de la ressource ou conformément aux exigences de la stratégie applicable. Dans l’idéal, le rôle de l’opérateur peut être mis en œuvre dans un système automatisé. Un opérateur des ressources garantit que les contrôles d’accès nécessaires sont assurés et est chargé de la gestion et de la protection des ressources qui lui sont déléguées. Les responsabilités de l’opérateur des ressources sont les suivantes :

- Protéger la ressource selon les directives du propriétaire de la ressource ou en accord avec ce dernier 
- Garantir le respect des stratégies de classification 
- Informer les propriétaires des ressources de toute modification apportée aux contrôles convenus et/ou aux procédures de protection préalables à la mise en application de ces modifications. 
- Informer le propriétaire des ressources de la suppression ou de la modification des responsabilités de l’opérateur des ressources 
- Un **administrateur** représente un utilisateur chargé de s’assurer du maintien de l’intégrité, mais n’est pas un propriétaire de la ressource de données, un opérateur ou un utilisateur. En fait, de nombreux rôles d’administrateur fournissent des services de gestion de conteneur de données sans avoir accès aux données. Le rôle d’administrateur inclut la sauvegarde et la restauration des données, la conservation des enregistrements des ressources, ainsi que le choix, l’acquisition et le fonctionnement des appareils et du stockage hébergeant les ressources. 
- L’utilisateur de la ressource inclut toute personne ayant accès aux données ou à un fichier. L’attribution d’un accès est souvent déléguée par le propriétaire à l’opérateur des ressources.  

### Implémentation
  
Des considérations relatives à la gestion s’appliquent à toutes les méthodes de classification. Ces considérations doivent inclure des informations détaillées d’identité, de type, d’emplacement, de date et de motif concernant l’utilisation, l’accès, la modification ou la suppression d’une ressource de données. La gestion des ressources doit être effectuée en comprenant la manière dont une organisation perçoit ses risques. Une méthodologie simple peut être appliquée comme défini dans le processus de classification des données. Parmi les considérations supplémentaires relatives à la classification des données figurent l’introduction de nouvelles applications et de nouveaux outils et le changement de gestion suite à l’implémentation d’une méthode de classification.

### Reclassification
 
La reclassification ou le changement d’état de la classification d’une ressource de données doit être effectuée lorsqu’un utilisateur ou un système détermine que l’importance ou le profil de risque d’une ressource de données a changé. Cet effort est important pour s’assurer que l’état de la classification reste valide et actuel. La plupart du contenu non classé manuellement peut l’être automatiquement ou en fonction de son utilisation par un opérateur ou propriétaire de données.

### Reclassification manuelle des données
 
Dans l’idéal, cet effort permettrait de s’assurer que les informations relatives à un changement ont été capturées et vérifiées. La raison la plus probable pour une reclassification manuelle serait liée à la sensibilité, au fait de conserver des enregistrements au format papier ou à une exigence de vérification des données mal classées à l’origine. Dans la mesure où ce document traite de la classification des données et de leur transfert vers le cloud, les efforts de reclassification nécessiteraient une étude au cas par cas et, dans l’idéal, de passer en revue la gestion des risques pour répondre aux exigences de classification. En général, un tel un effort prendrait en considération la stratégie de l’organisation relative aux besoins de classification, à l’état de classification par défaut (l’ensemble des données et des fichiers sensibles mais pas confidentiels) et les exceptions liées aux données à haut risque.

### Reclassification automatique des données
 
La reclassification automatique des données utilise la même règle générale que la classification manuelle. L’exception réside dans le fait que des solutions automatisées peuvent garantir que les règles sont suivies et appliquées en fonction des besoins. La classification des données peut être effectuée dans le cadre d’une stratégie de mise en œuvre de la classification de données, qui peut être appliquée lors du stockage, de l’utilisation et du transit des données à l’aide de la technologie d’autorisation.

- En fonction d’une application. Un niveau de classification est défini à l’aide de certaines applications par défaut. Par exemple, les données du logiciel de gestion de la relation client (CRM), des ressources humaines et des outils de gestion des dossiers médicaux sont confidentiels par défaut. 
- En fonction d’un emplacement. L’emplacement des données peut permettre d’identifier la sensibilité des données. Par exemple, les données stockées par un département des ressources humaines ou financier sont davantage susceptibles d’être confidentielles par nature.  
 
### Rétention, récupération et élimination des données 

La récupération et l’élimination des données, telles que leur reclassification, sont un aspect essentiel de la gestion des ressources de données. Les principes de récupération et d’élimination des données seraient définis par une stratégie de rétention des données et appliqués de la même manière que la reclassification des données. Un tel effort serait effectué par les rôles d’opérateur et d’administrateur sous la forme d’une tâche collaborative.

L’absence de stratégie de rétention des données pourrait conduire à une perte de données ou au non-respect des exigences de détection légales et réglementaires. La plupart des organisations ne disposant pas de stratégie de rétention de données clairement définie ont tendance à utiliser une stratégie de rétention consistant à conserver par défaut tous les éléments. Toutefois, une telle stratégie de rétention comporte des risques supplémentaires dans des scénarios de services cloud.

Par exemple, pour les fournisseurs de services cloud, une stratégie de rétention des données peut être considérée pour « la durée de l’abonnement » (les données sont conservées tant que l’on paie pour le service). Tel un accord de rétention lié au paiement peut ne pas convenir aux stratégies de rétention réglementaires ou d’entreprise. La définition d’une stratégie pour les données confidentielles peut garantir un stockage et une suppression des données selon les meilleures pratiques. En outre, une stratégie d’archivage peut être créée pour définir le type de données à supprimer et à quel moment effectuer cette opération.

La stratégie de rétention des données doit répondre aux exigences réglementaires et de conformité, ainsi qu’aux exigences de rétention légales de l’entreprise. Les données classées peuvent susciter des questions sur la durée de rétention et les exceptions liées aux données stockées auprès d’un fournisseur. De telles questions se poseront plus probablement pour les données n’ayant pas fait l’objet d’une classification correcte.

> [AZURE.TIP] pour en savoir plus sur les stratégies de rétention des données Azure, lisez l’article [Contrat d’abonnement en ligne Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/)

## Protection des données confidentielles
  
Une fois les données classées, la recherche et l’implémentation de méthodes de protection des données confidentielles deviennent partie intégrante de toute stratégie de déploiement de la protection des données. La protection des données confidentielles requiert une attention supplémentaire concernant la façon dont les données sont stockées et transmises dans les architectures conventionnelles, ainsi que dans le cloud.

Cette section fournit des informations de base sur certaines technologies permettant d’automatiser les efforts de mise en œuvre visant à protéger les données classées comme confidentielles.
 
Comme le montre l’illustration suivante, ces technologies peuvent être déployées sous la forme de solutions locales ou cloud, ou de manière hybride en associant les deux méthodes. (Certaines technologies, telles que le chiffrement et la gestion des droits, s’appliquent également aux appareils utilisateur.)

![Technologies](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### Logiciel de gestion des droits  

Le logiciel de gestion des droits est une solution permettant d’empêcher la perte de données. Contrairement aux approches qui tentent d’interrompre le flux d’informations sortant d’une organisation, le logiciel de gestion des droits fonctionne de manière approfondie au niveau des technologies de stockage. Les documents sont chiffrés et la vérification des personnes autorisées à les déchiffrer utilise les contrôles d’accès définis dans une solution de contrôle d’authentification, tel qu’un service d’annuaire.

> [AZURE.TIP] Vous pouvez utiliser Azure Rights Management (Azure RMS) comme solution de protection des informations pour protéger les données dans divers scénarios. Consultez [What is Azure Rights Management?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) (Présentation d’Azure Rights Management) pour en savoir plus sur cette solution Azure.

Voici certains avantages du logiciel de gestion des droits :

- Sauvegarde des informations sensibles. Les utilisateurs peuvent protéger leurs données directement à l’aide d’applications compatibles avec la gestion des droits. Aucune étape supplémentaire n’est requise. La création de documents, l’envoi d’e-mails et la publication de données bénéficient d’une protection cohérente des données. 
- Une protection qui suit les données. Les clients continuent de contrôler les personnes ayant accès à leurs données, que ce soit dans le cloud, dans une infrastructure informatique existante ou sur le bureau de l’utilisateur. Les organisations peuvent choisir de chiffrer leurs données et de restreindre l’accès en fonction de leurs besoins. 
- Stratégies de protection des informations par défaut. Les administrateurs et les utilisateurs peuvent utiliser des stratégies standard pour de nombreux scénarios d’entreprise courants, de type « Confidentiel entreprise - Lecture seule » et « Ne pas transférer ». Un ensemble complet de droits d’utilisation est pris en charge, comme la lecture, la copie, l’impression, l’enregistrement, la modification et le transfert pour offrir plus de souplesse dans la définition de droits d’utilisation personnalisés. 

> [AZURE.TIP] Vous pouvez protéger les données dans Azure Storage à l’aide d’[Azure Storage Service Encryption](./storage/storage-service-encryption.md) pour les données au repos. Vous pouvez également utiliser [Azure Disk Encryption](azure-security-disk-encryption.md) pour protéger les données contenues sur les disques virtuels utilisés pour Azure Virtual Machines.

### Passerelles de chiffrement

Les passerelles de chiffrement fonctionnent dans leurs propres couches pour fournir des services de chiffrement en redirigeant tous les accès aux données basées sur le cloud. Cette approche ne doit pas être confondue avec celle d’un réseau privé virtuel (VPN). Les passerelles de chiffrement sont conçues pour fournir une couche transparente aux solutions basées sur le cloud.

Les passerelles de chiffrement peuvent fournir un moyen de gérer et de sécuriser les données qui ont été classées comme confidentielles en chiffrant les données en transit, ainsi que celles au repos.
 
Les passerelles de chiffrement sont placées dans le flux de données entre les appareils utilisateur et les centres de données des applications pour fournir des services de chiffrement/déchiffrement. Ces solutions, comme les réseaux privés virtuels, sont majoritairement des solutions sur site. Elles sont conçues pour fournir à un tiers un contrôle sur les clés de chiffrement, qui permet de réduire le risque de confier la gestion des données et des clés à un seul fournisseur. Ces solutions sont conçues, à l’instar du chiffrement, pour travailler de manière transparente entre les utilisateurs et le service.

> [AZURE.TIP] Azure ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée dédiée. Pour plus d’informations sur cette fonctionnalité, consultez la rubrique [ExpressRoute - Aperçu technique](./expressroute/expressroute-introduction.md). Une autre option pour une connectivité croisée entre votre réseau local et [Azure est une connexion VPN de site à site](./vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### Prévention contre la perte de données 
La perte de données (parfois appelée fuite de données) est un facteur important à prendre en considération, et la prévention contre la perte de données externes via des menaces accidentelles et malveillantes venant de l’intérieur est primordiale pour de nombreuses organisations.
 
Les technologies de prévention contre la perte de données peuvent permettre de s’assurer que des solutions, telles que des services de messagerie, ne transmettent pas de données classées comme confidentielles. Les organisations peuvent tirer parti des fonctionnalités DLP dans des produits existants pour éviter la perte de données. Ces fonctionnalités utilisent des stratégies qui peuvent être facilement créées de toutes pièces ou à l’aide d’un modèle fourni par le fournisseur de logiciel.
 
Les technologies DLP peuvent effectuer une analyse approfondie du contenu via des correspondances avec des mots-clés, des dictionnaires, une évaluation des expressions régulières et l’examen d’autres contenus pour détecter tout contenu violant les stratégies DLP d’une organisation. Par exemple, la technologie DLP permet d’éviter la perte des types de données suivants :

- Numéros de sécurité sociale et de carte d’identité 
- Informations bancaires 
- Numéros de carte de crédit  
- Adresses IP 

Certaines technologies DLP permettent également de remplacer la configuration DLP (par exemple, si une organisation a besoin de transmettre des informations relatives au numéro de sécurité sociale à un responsable du traitement de la paie). En outre, il est possible de configurer DLP afin que les utilisateurs soient informés avant même de tenter d’envoyer des informations sensibles qui ne doivent pas être transmises.

> [AZURE.TIP] vous pouvez utiliser les fonctionnalités DLP d’Office 365 pour protéger vos documents. Consultez [Office 365 compliance controls: Data Loss Prevention](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) (Contrôles de conformité Office 365 : prévention contre la perte de données pour en savoir plus.

## Voir aussi

- [Meilleures pratiques en matière de chiffrement des données Azure](azure-security-data-encryption-best-practices.md)
- [Meilleures pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure](azure-security-identity-management-best-practices.md)
- [Blog de l’équipe de sécurité Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

<!---HONumber=AcomDC_0511_2016-->