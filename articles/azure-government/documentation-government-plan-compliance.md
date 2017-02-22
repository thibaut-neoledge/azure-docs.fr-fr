---
title: "Conformité d’Azure Government | Microsoft Docs"
description: "Fournit une vue d’ensemble des services de conformité disponibles dans Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Conformité d’Azure Government

## <a name="azure-blueprint"></a>Azure Blueprint

Le programme Azure Blueprint est conçu pour faciliter l’utilisation sécurisée et conforme d’Azure pour les agences gouvernementales et les fournisseurs tiers créant pour le compte de l’administration. Les clients Azure Government peuvent tirer parti de l’autorisation d’opérer provisoire (P-ATO) JAB FedRAMP d’Azure Government ou de l’autorisation provisoire (PA) du département de la Défense, ce qui réduit l’étendue des contrôles de sécurité relevant de la responsabilité des clients dans les systèmes Azure. L’héritage des mises en œuvre de contrôles de sécurité d’Azure Government permet aux clients de se concentrer sur des mises en œuvre de contrôle spécifiques à leurs environnements IaaS, PaaS ou SaaS créés dans Azure.

> [!NOTE]
> Dans le cadre d’Azure Blueprint, « client » fait référence à l’organisation créant directement dans Azure. Les clients Azure peuvent inclure des éditeurs de logiciels indépendants tiers créant pour le compte du gouvernement ou des agences gouvernementales créant directement dans Azure.

## <a name="blueprint-customer-responsibilities-matrix"></a>Matrice des responsabilités de client Blueprint

La matrice des responsabilités de client (CRM) Azure Blueprint est conçue pour aider les clients Azure Government à mettre en œuvre et à documenter des contrôles de sécurité spécifiques aux systèmes mis en œuvre dans Azure. La matrice CRM répertorie toutes les exigences de contrôle de sécurité [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) pour les lignes de bases FedRAMP et DISA qui incluent une exigence de mise en œuvre de client. Cela comprend les contrôles avec une responsabilité partagée entre Azure et les clients Azure, ainsi que les contrôles qui doivent être intégralement mis en œuvre par les clients Azure. Le cas échéant, les contrôles sont délimités un niveau de granularité de contrôle inférieur pour des instructions plus spécifiques.

Le format CRM est conçu pour plus de commodité ; il favorise une documentation concentrée uniquement sur les parties client de contrôles de sécurité mis en œuvre.

Par exemple, le contrôle AC-1 exige des stratégies et des procédures de contrôles d’accès documentées pour le système sollicitant une autorisation d’opérer (ATO). Pour ce contrôle, Microsoft dispose de stratégies et de procédures internes spécifiques à Azure relatives aux mécanismes de contrôle d’accès utilisés pour gérer l’infrastructure et la plateforme Azure. Les clients doivent également créer leurs propres stratégies et procédures de contrôle d’accès utilisées au sein de leur système spécifique intégré à Azure. Les documents CRM contrôlent les parties AC-1a, ce qui exige que les stratégies et procédures incluent un contenu spécifique, et AC-1b, ce qui exige que les clients révisent et mettent à jour ces documents une fois l’an.

La matrice CRM Blueprint est disponible en tant que classeur Microsoft Excel pour les lignes de base Moderate et High FedRAMP, et les lignes de base L4 et L5 Cloud Computing SRG DISA.

Pour demander une copie de la matrice CRM Azure Blueprint ou pour faire part de vos commentaires, envoyez un e-mail à l’adresse [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="blueprint-system-security-plan"></a>Plan de sécurité de système Blueprint

Le plan de sécurité de système Blueprint (SSP) Azure est axé sur le client et conçu pour documenter les mises en œuvre de contrôles de sécurité client, ainsi que les contrôles hérités d’Azure. Les contrôles qui incluent une responsabilité de client contiennent des instructions sur la documentation de la mise en œuvre des contrôles avec une réponse détaillée et conforme. Des portions de l’héritage Azure décrivent comment les contrôles de sécurité sont mis en œuvre par Azure pour le compte du client.

Le SSP Azure Blueprint est disponible pour les lignes de base Moderate et High FedRAMP, et les lignes de base L4 et L5 Cloud Computing SRG DISA. 

Pour demander une copie du SSP Azure Blueprint ou pour faire part de vos commentaires, envoyez un e-mail à l’adresse [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="azure-blueprint-implementation-guidance"></a>Conseils sur la mise en œuvre Azure Blueprint

Les conseils sur la mise en œuvre Azure Blueprint sont conçus pour aider les architectes de solutions cloud et le personnel de sécurité à comprendre comment les services et fonctionnalités Azure Government peuvent être déployés pour mettre en œuvre un sous-ensemble de contrôles de sécurité FedRAMP et du département de la Défense relevant de la responsabilité du client. Un éventail de documents, outils, modèles et autres ressources sont disponibles pour guider le déploiement sécurisé de services et fonctionnalités Azure. Les ressources Azure peuvent être déployées à l’aide des [blocs de construction](https://github.com/mspnp/template-building-blocks) modèles d’Azure Resource Manager, des [modèles de démarrage rapide](https://azure.microsoft.com/resources/templates/) de la communauté Azure ou par le biais de modèles Resource Manager basés sur JSON [créés par le client](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). L’architecture d’un déploiement de base dans Azure inclut des ressources de calcul, de mise en réseau et de stockage. Ces conseils sur la mise en œuvre traitent de la manière dont ces ressources peuvent être déployées de manière à répondre aux exigences de mise en œuvre de contrôles de sécurité.

### <a name="virtual-machines"></a>Machines virtuelles

Les modèles Azure Resource Manager peuvent être utilisés pour déployer des machines virtuelles préconfigurées qui respectent les guides de mise en œuvre techniques relatifs à la sécurité (STIG) et les références en matière de sécurité du gouvernement américain et du secteur. Il est possible de créer des machines virtuelles Azure personnalisées à l’aide d’une machine existante préconfigurée, ou de déployer une nouvelle machine virtuelle et d’appliquer une stratégie de sécurité à l’aide d’Active Directory, pour les machines jointes à un domaine, ou l’utilitaire local d’objet de stratégie de groupe, pour les machines autonomes. L’extension de script personnalisé de machine virtuelle Azure peut être utilisée pour gérer ces tâches de configuration post-déploiement.

La configuration de machine virtuelle Windows peut inclure des mises en œuvre de contrôle de sécurité pour les éléments suivants :

- Notifications d’utilisation du système et confirmations [NIST SP 800-53 contrôle AC-8]

  > Instruction de mise en œuvre du contrôle AC-8 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise, notamment l’utilisation de notifications d’utilisation du système appropriées, que les utilisateurs doivent confirmer avant la connexion (voir sections 2.3.7.4 et 2.3.7.5 de la référence CIS).*

- Restrictions de compte de machines locales, notamment verrouillage de compte [NIST SP 800-53 contrôle AC-7], contrôle de sessions simultanées [NIST SP 800-53 contrôle AC-10], verrouillage de session [NIST SP-800-53 contrôle AC-11], gestion de l’authentificateur [NIST SP 800-53 contrôle IA-5], entre autres

  > Instruction de mise en œuvre du contrôle AC-7 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise, notamment le lancement d’un verrouillage de compte après des échecs de connexions consécutifs (voir sections 1.2.1, 1.2.2 et 1.2.3 de la référence CIS. Remarque : les valeurs par défaut de la référence CIS doivent être modifiées pour répondre aux exigences de configuration FedRAMP et du département de la Défense).*

  > Instruction de mise en œuvre du contrôle AC-10 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise, notamment la restriction des utilisateurs sur une seule session de services Bureau à distance, conforme aux exigences FedRAMP et du département de la Défense (voir sections 18.9.48.3.2 de la référence CIS).*

  > Instruction de mise en œuvre du contrôle AC-11 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise, notamment le lancement d’un verrouillage de session après 900 secondes (15 minutes) d’inactivité maintenu jusqu’à ce que l’utilisateur s’authentifie de nouveau (voir sections 2.3.7.3, 19.1.3.1 et 19.1.3.3 de la référence CIS).*

  > Instruction de mise en œuvre du contrôle IA-5 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise, notamment les restrictions sur les mots de passe de compte local qui appliquent des restrictions de durée de vie minimum (1 jour) et maximum (60 jours), sur les conditions de réutilisation (24 mots de passe), sur la longueur (14 caractères) et des exigences de complexité conformes aux exigences FedRAMP (voir sections1.1.1, 1.1.2, 1.1.3, 1.1.4 et 1.1.5 de la référence CIS. Remarque : les valeurs par défaut de la référence CIS doivent être modifiées pour répondre aux exigences de configuration du département de la Défense).*

- Paramètres de configuration, y compris les fonctionnalités minimum [NIST SP 800-53 contrôles CM-6, CM-7]

  > Instruction de mise en œuvre du contrôle CM-6 : *un objet de stratégie de groupe contrôlé par la configuration est conservé pour tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise spécifiques aux besoins opérationnels du système. Cet objet de stratégie de groupe est établi, en plus des autres ressources de configuration, afin de documenter et de garantir la mise en œuvre cohérente des paramètres de configuration.*

  > Instruction de mise en œuvre du contrôle CM-7 : *un objet de stratégie de groupe contrôlé par la configuration est conservé pour tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est configuré conformément à la référence CIS pour Windows Server 2012 R2 et personnalisé pour répondre aux besoins d’entreprise spécifiques aux besoins opérationnels du système. L’objet de stratégie de groupe est utilisé pour établir une image de machine virtuelle de ligne de base, qui est conservée pour tous les ordinateurs Windows dans Azure et reflète la configuration des ordinateurs Windows afin de fournir uniquement les fonctionnalités essentielles au fonctionnement du système.*

  Des outils de correction de ligne de base de sécurité, qui incluent des objets de stratégie de groupe préconfigurés, pour les ordinateurs Windows, et des scripts d’interpréteur de commandes, pour les ordinateurs Linux, sont disponibles à partir de [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines), pour les ordinateurs Windows, du [Center for Internet Security](https://benchmarks.cisecurity.org/) et de la [Defense Information Systems Agency (DISA)](http://iase.disa.mil/stigs), pour les ordinateurs Windows et Linux.  

Les machines virtuelles Azure peuvent être chiffrées pour répondre aux exigences de contrôles de sécurité pour protéger les informations au repos [NIST SP 800-53 contrôle SC-28]. Les modèles de démarrage rapide Azure sont disponibles pour déployer le chiffrement sur les machines virtuelles [nouvelles](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) et existantes [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) et [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). 

> Instruction de mise en œuvre du contrôle SC-28 : *les machines virtuelles contrôlées par les clients dans Azure mettent en œuvre le chiffrement de disque pour protéger la confidentialité et l’intégrité des données au repos. Azure Disk Encryption pour Windows est mis en œuvre à l’aide de la fonctionnalité BitLocker de Windows ; le chiffrement de disque pour les machines virtuelles Linux est mis en œuvre à l’aide de la fonctionnalité DM-Crypt de Linux.

Plusieurs extensions de machine virtuelle peuvent être déployées pour répondre aux exigences de contrôles de sécurité. [L’extension de machine virtuelle Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) peut être déployée sur des machines virtuelles nouvelles et existantes. L’extension Antimalware peut activer la protection en temps réel, ainsi que des analyses planifiées et périodiques [NIST SP 800-53 contrôle SI-3].

> Instruction de mise en œuvre du contrôle SI-3 : *les protections contre les logiciels malveillants basés sur les hôtes pour les machines virtuelles Windows contrôlées par le client dans Azure sont mises en œuvre à l’aide de l’extension de machine virtuelle Microsoft Antimalware. Cette extension est configurée pour effectuer des analyses en temps réel et périodiques (hebdomadaires), mettre à jour automatiquement le moteur contre les logiciels malveillants et les signatures de protection, effectuer des actions de correction automatique et fournir des notifications par le biais des diagnostics Microsoft Azure.*

*Outils et ressources supplémentaires*

[Blocs de construction](https://github.com/mspnp/template-building-blocks) du modèle Azure Resource Manager qui peuvent être personnalisés et utilisés pour déployer des ressources Azure, notamment le déploiement de [machines virtuelles](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage) et [d’extensions de machine virtuelle](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions). 

### <a name="virtual-network"></a>Réseau virtuel

Le réseau virtuel Azure (VNet) permet un contrôle total des stratégies de sécurité et de routage dans des architectures de réseau virtuel par le biais du déploiement et de la configuration des sous-réseaux, les groupes de sécurité réseau et les itinéraires définis par l’utilisateur. Les groupes de sécurité réseau sont applicables à des sous-réseaux ou à des ordinateurs individuels pour séparer logiquement les ressources par charge de travail, en fonction d’une architecture à plusieurs niveaux, ou à d’autres fins. Dans l’architecture de référence ci-dessous, les ressources sont regroupées dans des sous-réseaux distincts pour le web, les entreprises et les niveaux de données, et des sous-réseaux pour les ressources et la gestion Active Directory. Les groupes de sécurité réseau sont appliqués à chaque sous-réseau pour limiter le trafic réseau au sein du réseau virtuel. 
 
![Architecture multiniveau à l’aide de Microsoft Azure](./media/documentation-government-plan-compliance/compute-n-tier.png)

Les groupes de sécurité réseau permettent un contrôle total sur les chemins de communication entre les ressources [NIST SP 800-53 contrôle AC-4].

> Instruction de mise en œuvre du contrôle AC-4 : *les machines virtuelles contrôlées par le client dans Azure mettant en œuvre des fonctions web, d’entreprise, et de couches de données sont séparées par sous-réseau. Les groupes de sécurité réseau sont définis et appliqués pour chaque sous-réseau qui limite les flux d’informations sur la couche réseau au minimum nécessaire pour la fonctionnalité du système informatique.*

Les groupes de sécurité réseau peuvent être appliqués pour les communications sortantes à partir des sous-réseaux et des machines virtuelles. Cela permet un contrôle total sur la communication entre les composants du système informatique dans Azure et les systèmes informatiques externes [NIST SP 800-53 contrôle CA-3 (5)]. Le traitement de règle de groupe de sécurité réseau est mis en œuvre comme une fonction « refuser tout » et « autorisation sur exception ». En outre, les itinéraires définis par l’utilisateur peuvent être configurés pour acheminer les communications entrantes et sortantes depuis des sous-réseaux et machines virtuelles spécifiques via une appliance virtuelle comme un pare-feu ou un système IDS/IPS, pour une gestion approfondie des communications système.

> Instruction de mise en œuvre du contrôle CA-3 (5) : *toutes les communications sortantes provenant des ressources contrôlées par le client dans Azure sont limitées par la mise en œuvre des groupes de sécurité réseau, qui sont configurés avec un ensemble de règles sortantes refusant tout le trafic, sauf le trafic explicitement autorisé, afin de prendre en charge des fonctionnalités du système informatique, qui répond aux exigences FedRAMP et du département de la Défense L4. En outre, les itinéraires définis par l’utilisateur sont configurés pour acheminer tout le trafic sortant via une appliance de pare-feu virtuelle, qui est configurée avec un groupe de règles permettant la communication avec les systèmes informatiques externes approuvés uniquement.*

L’architecture de référence ci-dessus montre que les ressources Azure peuvent être regroupées logiquement dans des sous-réseaux distincts avec des ensembles de règles de groupe de sécurité réseau appliqués pour garantir que les fonctions de sécurité et les autres fonctions sont isolées. Dans ce cas, les trois niveaux d’application web sont isolés du sous-réseau Active Directory, ainsi que du sous-réseau de gestion, qui peut héberger le système informatique et les outils et ressources de gestion de la sécurité [NIST SP 800-53 contrôle SC-3].

> Instruction de mise en œuvre du contrôle SC-3 : *les fonctions de sécurité sont isolées des autres fonctions dans l’environnement Azure contrôlé par le client par le biais de la mise en œuvre de sous-réseaux et de groupes de sécurité réseau qui sont appliqués à ces sous-réseaux. Les ressources du système informatique dédiées au web, à l’entreprise et aux couches de données de l’application web sont logiquement séparées du sous-réseau de gestion, où les tâches relatives à la sécurité du système informatique sont exécutées.*

L’architecture de référence met également en œuvre des points de contrôle d’accès gérés pour l’accès à distance au système informatique [NIST SP 800-53 contrôle AC-17 (3)]. Un équilibreur de charge accessible sur Internet est déployé pour distribuer le trafic Internet entrant à l’application web, et le sous-réseau de gestion inclut une jumpbox, ou hôte bastion, par le biais de laquelle l’accès à distance lié à la gestion est contrôlé. Les groupes de sécurité réseau restreignent le trafic au sein du réseau virtuel, garantissant que le trafic externe est uniquement acheminé aux ressources publiques désignées.

> Instruction de mise en œuvre du contrôle AC-17 (3) : *l’accès distant à des composants du système informatique contrôlés par le client dans Azure est limité à deux points de contrôle d’accès réseau gérés. 1) Le trafic Internet désigné pour l’application web est géré par un équilibreur de charge accessible sur Internet, qui distribue le trafic aux ressources de niveau web. 2) L’accès à distance à la gestion est géré via un hôte bastion sur un sous-réseau séparé au sein de l’environnement. Le groupe de sécurité réseau appliqué au sous-réseau de gestion, dans lequel la jumpbox réside, autorise les connexions uniquement à partir des adresses IP publiques dans la liste approuvée et est strictement réservée au trafic de bureau à distance.*

Les groupes de sécurité réseau permettent un contrôle total des communications entre les ressources Azure et les hôtes et systèmes externes, ainsi qu’entre les sous-réseaux internes et les hôtes, en séparant les composants du système informatique qui sont accessibles publiquement et ceux qui ne le sont pas. Outre les solutions dans l’architecture de référence ci-dessus, Azure permet de déployer des appliances virtuelles, comme le pare-feu et les solutions IDS/IPS qui, lorsqu’elles sont utilisées conjointement avec les itinéraires définis par l’utilisateur, renforcent la sécurité et la gestion des connexions entre les ressources Azure et les réseaux et systèmes informatiques externes [NIST SP 800-53 contrôle SC-7].

> Instruction de mise en œuvre du contrôle SC-7 : *les ressources contrôlées par le client dans Azure sont protégées grâce au déploiement de plusieurs mécanismes de protection de limites. Les groupes de sécurité réseau sont définis et appliqués à des sous-réseaux qui limitent le trafic au niveau de la limite du système informatique et au sein du réseau virtuel. Les groupes de sécurité réseau incluent des règles de liste de contrôle d’accès (ACL) qui contrôlent le trafic vers et depuis les sous-réseaux et les machines virtuelles. Les équilibreurs de charge sont déployés pour distribuer le trafic Internet entrant vers des ressources spécifiques. Les sous-réseaux assurent que les composants du système informatique accessibles au public sont séparés logiquement des ressources non publiques.*

Les ensembles de règles pour les groupes de sécurité réseau activent des restrictions sur des ports réseau et des protocoles spécifiques, ce qui permet de s’assurer que les systèmes informatiques sont mis en œuvre de manière à fournir uniquement les fonctionnalités essentielles [NIST SP 800-53 contrôle CM-7].

> Instruction de mise en œuvre du contrôle CM-7 : *les groupes de sécurité réseau appliqués aux sous-réseaux et aux machines virtuelles avec l’environnement Azure contrôlé par le client sont mis en œuvre à l’aide d’une approche « refuser tout » et « autorisation sur exception ». Cela garantit que seul le trafic réseau sur les protocoles et ports explicitement approuvés est autorisé, prenant en charge les fonctionnalités minimum.*

*Outils et ressources supplémentaires*

La documentation de l’architecture de référence ci-dessus est disponible sur le [site de documentation](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm) Azure. Les modèles Azure Resource Manager permettant de déployer l’architecture de référence sont inclus dans la même page. Le site de documentation Azure contient également des informations détaillées concernant les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) et les [itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

### <a name="storage"></a>Storage

Les données stockées dans le stockage Azure sont répliquées pour garantir une haute disponibilité. Bien que plusieurs options de réplication soient disponibles, le stockage géoredondant (GRS) et le stockage géoredondant avec accès en lecture (RA-GRS) garantit que les données sont répliquées dans une région secondaire. Les régions primaires et secondaires sont associées pour garantir une distance nécessaire entre les centres de données afin d’assurer la disponibilité en cas de panne ou d’urgence dans l’ensemble de la zone [NIST SP 800-53 contrôle CP-9]. Pour le stockage géoredondant, hautement disponible, sélectionnez le stockage géoredondant (GRS) ou le stockage géoredondant avec accès en lecture (RA-GRS) lors de la création d’un compte de stockage. 

> Instruction de mise en œuvre du contrôle CP-9 : *tous les comptes de stockage contrôlés par le client dans Azure mettent en œuvre le stockage géoredondant, garantissant que six copies de toutes les données sont conservées sur des nœuds distincts entre deux centres de données.*

Azure Storage Service Encryption (SSE) protège les données au repos dans les comptes de stockage Azure [NIST SP 800-53 contrôles SC-28, SC-28 (1)]. Lorsqu’Azure est activé, les données sont chiffrées automatiquement avant d’être conservées dans le stockage. Les données sont chiffrées à l’aide du chiffrement AES 256 bits. SSE prend en charge le chiffrement des objets blob de blocs, des objets blob d’ajout et des objets blob de pages.

> Instruction de mise en œuvre du contrôle SC-28, SC-28 (1) : *la confidentialité et l’intégrité de tous les objets blob de stockage avec l’environnement Azure contrôlé par le client sont protégées à l’aide d’Azure SSE, qui utilise le chiffrement AES 256 bits pour toutes les données au repos.*

*Outils et ressources supplémentaires*

Le site de documentation Azure comporte des informations détaillées concernant le [chiffrement du service de stockage](https://docs.microsoft.com/azure/storage/storage-service-encryption) et [la réplication du stockage](https://docs.microsoft.com/azure/storage/storage-redundancy). 

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory offre des fonctionnalités d’identité et d’accès pour les systèmes informatiques exécutés dans Microsoft Azure. Grâce à l’utilisation des services de répertoire, les groupes de sécurité et la stratégie de groupe, les clients peuvent aider à contrôler les stratégies d’accès et de sécurité des ordinateurs qui utilisent Azure Active Directory. Les comptes et les groupes de sécurité permettent de mieux gérer l’accès au système informatique. La stratégie de groupe permet de garantir que la configuration requise est respectée.

Les groupes de sécurité et les services de répertoire Azure Active Directory peuvent faciliter la mise en œuvre des schémas de contrôle d’accès en fonction du rôle de contrôle (RBAC) et l’accès au contrôle du système informatique. Cela peut inclure des mises en œuvre de contrôle de sécurité pour :

- Gestion de compte [NIST SP 800-53 contrôle AC-2]

  > Instruction de mise en œuvre du contrôle AC-2.a : *Azure Active Directory est utilisé pour gérer l’accès en mettant en œuvre le contrôle d’accès basé sur les rôles à l’aide des groupes Active Directory. Il existe des exigences établies pour la gestion des comptes et l’accès aux comptes sur les domaines pris en charge par AAD. L’accès aux serveurs membres du domaine est uniquement pris en charge par les groupes de sécurité. Chaque groupe dispose d’un propriétaire principal ou secondaire. Ces propriétaires sont responsables du maintien de la précision de l’appartenance au groupe, de l’autorisation et de la description.*

  > Instruction de mise en œuvre du contrôle AC-2.c : *lorsqu’un utilisateur demande l’accès à un groupe de sécurité, la demande doit être approuvée par le propriétaire du groupe en fonction des critères définis pour l’appartenance. Les conditions de compte sont déterminées par les propriétaires des groupes de sécurité.*

  > Instruction de mise en œuvre du contrôle AC-2.f : *Azure Active Directory est utilisé pour gérer le contrôle d’accès aux systèmes informatiques. Les administrateurs de comptes créent, activent, modifier, désactivent ou supprimer les informations des comptes système en fonction des besoins, suivant la stratégie de gestion de compte.*

  > Instruction de mise en œuvre du contrôle AC-2 (1) : *Azure Active Directory est utilisé pour gérer l’accès en mettant en œuvre le contrôle d’accès basé sur les rôles à l’aide des groupes Active Directory. Il existe des exigences établies pour la gestion des comptes et l’accès aux comptes sur les domaines pris en charge par AAD. L’accès aux serveurs membres du domaine est uniquement pris en charge par les groupes de sécurité. Chaque groupe dispose d’un propriétaire principal ou secondaire. Ces propriétaires sont responsables du maintien de la précision de l’appartenance au groupe, de l’autorisation et de la description.*

  > Instruction de mise en œuvre du contrôle AC-2 (2) : *Azure Active Directory est utilisé pour gérer le contrôle d’accès aux systèmes informatiques. Les administrateurs de comptes créent des comptes temporaires suivant la stratégie de gestion de comptes. Ces comptes temporaires doivent être configurés pour expirer conformément aux exigences de la stratégie.*

  > Instruction de mise en œuvre du contrôle AC-2 (3) : *Azure Active Directory gère le contrôle d’accès aux systèmes informatiques. Les administrateurs de comptes créent des comptes temporaires suivant la stratégie de gestion de comptes. Ces comptes temporaires doivent être configurés pour expirer conformément aux exigences de la stratégie.*

  >Instruction de mise en œuvre du contrôle AC-2 (7) : *Azure Active Directory gère le contrôle d’accès aux systèmes informatiques conformément à un schéma d’accès basé sur le rôle qui organise les privilèges de système informatique dans les rôles qui sont affectés aux groupes de sécurité. Les administrateurs de groupe de sécurité sont chargés de l’octroi de l’accès aux utilisateurs pour affecter des utilisateurs au groupe de sécurité approprié. Des autorisations sont affectées à chaque groupe de sécurité avec l’accès approprié minimum pour effectuer correctement leurs tâches.*

  > Instruction de mise en œuvre du contrôle AC-2 (11) : *Azure Active Directory gère le contrôle d’accès aux systèmes informatiques dans Azure. Des restrictions d’utilisation requises par les stratégies de sécurité sont définies et appliquées au sein d’Azure Active Directory par les administrateurs de comptes.*

- Application de l’accès [NIST SP 800-53 contrôle AC-3]

  > Instruction de mise en œuvre du contrôle AC-3 : *Azure Active Directory applique les autorisations approuvées à l’environnement du client à l’aide du contrôle d’accès basé sur les rôles. L’accès aux groupes de sécurité Azure Active Directory est géré par les administrateurs du groupe de sécurité. Les utilisateurs sont placés dans des groupes de sécurité appropriés en fonction de leurs rôles, en utilisant les principes de séparation de privilèges.*

- Séparation des privilèges [NIST SP 800-53 contrôle AC-6]

  > Instruction de mise en œuvre du contrôle AC-6 (10) : *Azure Active Directory applique les autorisations approuvées à l’environnement du client à l’aide du contrôle d’accès basé sur les rôles. L’accès aux groupes de sécurité Azure Active Directory est géré par les administrateurs du groupe de sécurité. Les utilisateurs dépourvus de privilèges ne sont pas autorisés à accéder à des groupes de sécurité qui leur permettraient d’accéder à des fonctions privilégiées, notamment des autorisations qui leur permettraient de désactiver, de contourner ou de modifier des sauvegardes de sécurité.*

- Accès à distance [NIST SP 800-53 contrôle AC-17]

  > Instruction de mise en œuvre du contrôle AC-17 (1) : *Azure Active Directory est utilisé pour surveiller et contrôler l’ensemble de l’accès à distance. Azure Active Directory comprend des rapports sur la sécurité, les activités et l’audit concernant votre répertoire.*

- Protection des informations d’audit [NIST SP 800-53 contrôle AU-9]

  > Instruction de mise en œuvre du contrôle AU-9 : *des contrôles d’accès étroitement gérés sont utilisés pour protéger les informations d’audit et les outils des accès, modifications et suppressions non autorisés. Azure Active Directory applique l’accès logique approuvé pour les utilisateurs à l’aide d’une approche de couche d’autorisations d’accès et de contrôles logiques à l’aide de stratégies Active Directory et d’appartenances aux groupes en fonction du rôle. La possibilité d’afficher des informations d’audit et d’utiliser les outils d’audit est limitée aux utilisateurs qui demandent ces autorisations.*

  > Instruction de mise en œuvre du contrôle AU-9 (4) : *Azure Active Directory restreint la gestion de la fonctionnalité d’audit aux membres des groupes de sécurité appropriés. Ces autorisations sont uniquement accordées au personnel ayant spécifiquement besoin d’accéder à la gestion de la fonctionnalité.*

- Restrictions d’accès pour modification [NIST SP 800-53 contrôle CM-5]

  > Instruction de mise en œuvre du contrôle CM-5 : *le contrôle d’accès en fonction du rôle appliqué par Azure Active Directory est utilisé pour définir, documenter, approuver et appliquer des restrictions d’accès logique associées aux modifications. Tous les comptes créés dans le système sont en fonction du rôle. Le personnel demande l’accès aux administrateurs de compte et si la demande est approuvée, les utilisateurs sont placés dans les groupes de sécurité appropriés en fonction de leurs rôles. L’accès à l’environnement de production est uniquement autorisé pour les membres des groupes de sécurité spécifiques après approbation.*

  > Instruction de mise en œuvre du contrôle CM-5 (1) : *Azure Active Directory applique des restrictions d’accès logique via l’appartenance aux groupes de sécurité. Les propriétaires de groupe de sécurité doivent accorder l’accès à un groupe de sécurité donné.*

- Identification et authentification de l’utilisateur [NIST SP 800-53 contrôle IA-2]

  > Instruction de mise en œuvre du contrôle IA-2 : *le personnel accédant à l’environnement du système informatique est uniquement identifié par le nom d’utilisateur et s’authentifie à l’aide d’Azure Active Directory. L’authentification sur Azure Active Directory est requise pour accéder au système informatique.*

  > Instruction de mise en œuvre du contrôle IA-2 (8) : *l’accès à l’environnement de production est protégé des attaques par relecture par la fonctionnalité Kerberos intégrée d’Azure Active Directory. Dans l’authentification Kerberos, l’authentificateur envoyé par le client contient des données supplémentaires, comme une liste d’adresses IP chiffrées, les horodateurs client et la durée de vie de ticket. Si un paquet est relu, l’horodatage est vérifié. Si l’horodatage est antérieur à ou identique à un authentificateur précédent, le paquet est rejeté.*

  > Instruction de mise en œuvre du contrôle IA-2 (9) : *tous les comptes sont protégés des attaques par relecture par la fonctionnalité Kerberos intégrée d’Azure Active Directory. Dans l’authentification Kerberos, l’authentificateur envoyé par le client contient des données supplémentaires, comme une liste d’adresses IP chiffrées, les horodateurs client et la durée de vie de ticket. Si un paquet est relu, l’horodatage est vérifié. Si l’horodatage est antérieur à ou identique à un authentificateur précédent, le paquet est rejeté.*

- Gestion de l’authentificateur [NIST SP 800-53 contrôle IA-4]

  > Instruction de mise en œuvre du contrôle IA-4.b : *des identificateurs de compte Azure Active Directory sont utilisés pour identifier les utilisateurs. Ces identificateurs uniques ne sont pas réutilisés.*

  > Instruction de mise en œuvre du contrôle IA-4.c : *Azure Active Directory est le référentiel de comptes central utilisé pour fournir l’accès à l’environnement du service. Lorsque des comptes sont créés dans Azure Active Directory, l’identificateur unique de l’utilisateur est affecté à l’individu.*

  > Instruction de mise en œuvre du contrôle IA-4.d : *les identificateurs d’utilisateur unique ne sont jamais réutilisés. Application par Azure Active Directory.*

  > IA-4.e Instruction de mise en œuvre du contrôle : *tous les comptes dans Azure Active Directory sont configurés pour être automatiquement désactivés après 35 jours d’inactivité.*

  > Instruction de mise en œuvre du contrôle IA-4 (4) : *Azure Active Directory désigne les sous-traitants et fournisseurs, en utilisant les conventions d’affectation de noms appliquées à leurs informations d’identification Azure Active Directory uniques.*

- Gestion de l’authentificateur [NIST SP 800-53 contrôle IA-5]

  > Instruction de mise en œuvre du contrôle IA-5.b : *Azure Active Directory attribue une identification unique et un mot de passe temporaire aléatoire conforme aux exigences de la stratégie au moment de la création du compte initial. Azure Active Directory maintient l’identification unique associée au compte tout au long de la vie du compte. L’identification du compte n’est jamais répétée avec Azure Active Directory*

  > Instruction de mise en œuvre du contrôle IA-5.c : *Azure Active Directory garantit que les mots de passe émis sont conformes aux exigences de complexité de mot de passe.*

  > Instruction de mise en œuvre du contrôle IA-5.d : *les procédures initiales de distribution de l’authentificateur sont gérées par les administrateurs de comptes. Les modifications d’accès aux comptes d’utilisateur passent par le processus de gestion de compte tel que défini dans le contrôle AC-2. Si un authentificateur est perdu ou compromis, les administrateurs de compte suivent les processus définis pour la réinitialisation, le renouvellement ou la révocation de l’authentificateur, selon les besoins.*

  > Instruction de mise en œuvre du contrôle IA-5 (1).c : *Azure Active Directory permet de garantir que tous les mots de passe sont protégés par chiffrement pendant leur stockage et leur transmission. Les mots de passe stockés par Azure Active Directory sont hachés automatiquement dans le cadre de la fonctionnalité d’Azure Active Directory.*

  > Instruction de mise en œuvre du contrôle IA-5 (1).f : *Azure Active Directory est utilisé pour gérer le contrôle d’accès au système informatique. Chaque fois qu’un compte est créé ou qu’un mot de passe temporaire est généré, Azure Active Directory est utilisé pour demander à l’utilisateur de modifier le mot de passe à la connexion suivante.*

  > Instruction de mise en œuvre du contrôle IA-5 (4) : *Azure Active Directory est l’outil automatisé utilisé pour déterminer si les authentificateurs de mot de passe sont suffisamment forts pour satisfaire les restrictions de longueur, de complexité, de rotation et de durée de vie des mots de passe établies dans le contrôle IA-5 (1). Azure Active Directory permet de s’assurer que la force de l’authentificateur de mot de passe lors de la création répond à ces normes.*

La configuration de stratégie de groupe Azure Active Directory peut être utilisée pour déployer des stratégies de sécurité client qui répondent à des exigences de sécurité spécifiques. Ces stratégies peuvent être appliquées par Azure Active Directory sur les machines virtuelles Azure client.
Les configurations de stratégie de groupe Active Directory Azure peuvent inclure des mises en œuvre de contrôle de sécurité pour :

- Séparation des privilèges [NIST SP 800-53 contrôle AC-6]

  > Instruction de mise en œuvre du contrôle AC-6 (9) : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment l’audit de l’exécution de fonctions privilégiées.*

- Tentatives de connexion ayant échoué [NIST SP 800-53 contrôle AC-7]

  > Instruction de mise en œuvre du contrôle AC-7 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment le lancement d’un verrouillage de compte après des échecs de connexions consécutifs.*

- Contrôle de sessions simultanées [NIST SP 800-53 contrôle AC-10], verrouillage de session [NIST SP 800-53 contrôle AC-11], arrêt de sessions [NIST SP 800-53 contrôle AC-12]

  > Instruction de mise en œuvre du contrôle AC-10 : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment la restriction des utilisateurs sur une seule session de services Bureau à distance, conforme aux exigences FedRAMP et du département de la Défense.*

  > Instruction de mise en œuvre du contrôle AC-11.a : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment le lancement d’un verrouillage de session après 900 secondes (15 minutes) d’inactivité maintenu jusqu’à ce que l’utilisateur s’authentifie de nouveau.*

  > Instruction de mise en œuvre du contrôle AC-12 : *Windows met fin automatiquement aux sessions Bureau à distance dès la réception d’une demande de déconnexion de l’utilisateur client. En outre, un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment l’arrêt d’une session Bureau à distance après 900 secondes (15 minutes) d’inactivité.*

- Gestion de l’authentificateur [NIST SP 800-53 contrôle IA-5]

  > Instruction de mise en œuvre du contrôle IA-5.f : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment les restrictions sur les mots de passe de compte local qui appliquent des restrictions de durée de vie minimum (1 jour) et maximum (60 jours), sur les conditions de réutilisation (24 mots de passe), sur la longueur (14 caractères) et des exigences de complexité conformes aux exigences FedRAMP.*

  > Instruction de mise en œuvre du contrôle IA-5.g : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment les restrictions sur les mots de passe de compte local qui appliquent des restrictions de durée de vie maximum (60 jours) conformes aux exigences FedRAMP.*

  > Instruction de mise en œuvre du contrôle IA-5 (1).a : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins des organisations, notamment aux exigences de complexité, conformes aux exigences FedRAMP.*

  > Instruction de mise en œuvre du contrôle IA-5 (1).b : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins des organisations, notamment aux exigences de complexité, conformes aux exigences FedRAMP.*

  > Instruction de mise en œuvre du contrôle IA-5 (1).d : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins d’entreprise, notamment les restrictions sur les mots de passe de compte local qui appliquent des restrictions de durée de vie minimum (1 jour) et maximum (60 jours) conformes aux exigences FedRAMP.

  > IA-5 (1).e Instruction de mise en œuvre du contrôle : *un objet de stratégie de groupe contrôlé par la configuration est appliqué à tous les ordinateurs Windows contrôlés par le client dans Azure. L’objet de stratégie de groupe est personnalisé pour répondre aux besoins des organisations, notamment aux restrictions sur les conditions de réutilisation (24 mots de passe), conformes aux exigences FedRAMP.*

*Outils et ressources supplémentaires*

Des documents, outils, modèles et autres ressources sont disponibles pour guider le déploiement sécurisé de services et fonctionnalités Azure. Prise en main d’Azure Active Directory à l’aide de [Microsoft Azure Docs](https://docs.microsoft.com/azure/active-directory/).

### <a name="key-vault"></a>Key Vault

Azure Key Vault protège les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Grâce à Azure Key Vault, les clients peuvent créer, gérer et protéger des clés et des clés secrètes. Des conteneurs sécurisés (coffres) peuvent servir à stocker et à gérer les clés de chiffrement et des clés secrètes en toute sécurité. Azure Key Vault peut servir à générer des clés de chiffrement à l’aide des modules de sécurité matériels validés selon la norme FIPS 140-2 de niveau 2.

Les conteneurs Azure Key Vault peuvent aider à stocker en toute sécurité des clés de chiffrement avec haute disponibilité. Cela peut inclure des mises en œuvre de contrôle de sécurité pour :

-   Protection des authentificateurs [NIST SP 800-53 contrôle IA-5 (7)]

  > Instruction de mise en œuvre du contrôle IA-5 (7) : *l’utilisation des authentificateurs statiques non chiffrés incorporés dans des applications, scripts d’accès ou touches de fonction est explicitement interdite. Un script ou une application qui utilise un authentificateur passe un appel à un conteneur Azure Key Vault avant chaque utilisation. L’accès aux conteneurs Azure Key Vault fait l’objet d’un audit, ce qui permet de détecter les violations de cette interdiction si un compte de service est utilisé pour accéder à un système sans appel correspondant au conteneur Azure Key Vault.*

- Établissement et gestion de clés de chiffrement [NIST SP 800-53 contrôle SC-12 (1)]

  > Instruction de mise en œuvre du contrôle SC-12 (1) : *Azure Key Vault est utilisé pour stocker les clés et clés secrètes de chiffrement. Ce service piste et surveille l’accès aux clés secrètes. Ce service est utilisé pour s’assurer que les clés secrètes ne sont pas perdues.*

Azure Key Vault peut servir à créer des clés de chiffrement à l’aide de modules de sécurité matériels respectant la norme FIPS 140-2 de niveau 2. Les configurations de stratégie de groupe Active Directory Azure peuvent inclure des mises en œuvre de contrôle de sécurité pour :

- Établissement et gestion de clés de chiffrement [NIST SP 800-53 contrôle SC-12 (2)]

  > Instruction de mise en œuvre contrôle SC-12 (2) : *Azure Key Vault permet de générer, de contrôler et de distribuer des clés de chiffrement. Ces clés sont générées à l’aide de modules de sécurité matériels validés selon la norme FIPS 140-2 de niveau 2. Ces clés sont stockées et gérées dans des conteneurs chiffrés en toute sécurité dans Azure Key Vault.*

*Outils et ressources supplémentaires*

Des documents, outils, modèles et autres ressources sont disponibles pour guider le déploiement sécurisé de services et fonctionnalités Azure. Prise en main d’Azure Key Vault à l’aide de [Microsoft Azure Docs](https://docs.microsoft.com/azure/key-vault/).

### <a name="operations-management-suite"></a>Operations Management Suite

Microsoft Operations Management Suite (OMS) est une solution de gestion informatique basée sur le cloud de Microsoft qui permet de gérer et de protéger l’infrastructure locale et de cloud. La sécurité et la conformité aident à identifier, évaluer et atténuer les risques de sécurité qui menacent l’infrastructure. Ces fonctionnalités d’OMS sont implémentées via plusieurs solutions, notamment Log Analytics, qui analysent les données de journal et surveillent les configurations de sécurité.

Avec OMS, les services de sécurité et d’audit peuvent mettent en œuvre la surveillance et l’enregistrement de compte. Cela peut inclure des mises en œuvre de contrôle de sécurité pour :

- Gestion de compte [NIST SP 800-53 contrôle AC-2]

  > Instruction de mise en œuvre du contrôle AC-2.g : *Microsoft Operations Management Suite (OMS) est utilisé pour surveiller l’utilisation des comptes système. OMS crée des journaux d’audit pour les comptes système qui peuvent ensuite être analysés avec l’analyse d’OMS, et envoie des alertes en fonction d’un ensemble défini de critères.*

  > Instruction de mise en œuvre du contrôle AC-2 (4) : *Microsoft Operations Management Suite (OMS) est utilisé pour générer des journaux d’audit pour les fonctions de gestion de compte, notamment la création, la modification, l’activation, la désactivation et la suppression de comptes. OMS est utilisé pour alerter le propriétaire du système si l’une des conditions ci-dessus a été exécutée.*

  > Instruction de mise en œuvre du contrôle AC-2 (12) : *Microsoft Operations Management Suite (OMS) est utilisé pour surveiller l’utilisation atypique des comptes système. OMS crée des journaux d’audit pour les comptes système qui peuvent ensuite être analysés avec l’analyse d’OMS, et alerte le personnel approprié en fonction d’un ensemble défini de critères.*

- Séparation des privilèges [NIST SP 800-53 contrôle AC-6]

  > Instruction de mise en œuvre du contrôle AC-6 (9) : *Microsoft Operations Management Suite (OMS) est utilisé pour surveiller l’exécution des fonctions privilégiées. OMS crée des journaux d’audit pour une liste de fonctions spécifiées qui peuvent ensuite être analysés avec l’analyse d’OMS, et envoie des alertes en fonction d’un ensemble défini de critères.*

 - Accès à distance [NIST SP 800-53 contrôle AC-17]

  > Instruction de mise en œuvre du contrôle AC-17 (1) : *Microsoft Operations Management Suite (OMS) est utilisé pour surveiller et contrôler l’accès à distance. OMS comprend des rapports sur la sécurité, les activités et l’audit concernant votre répertoire.*

- Événements d’audit [NIST SP 800-53 contrôle AU-2]

  > Instruction de mise en œuvre du contrôle AU-2 : *Microsoft Operations Management Suite (OMS) est utilisé pour l’audit : événements de connexion au compte ayant réussi ou échoué, événements de gestion de compte, accès aux objets, modification de stratégie, fonctions de privilège, suivi des processus et événements système. Pour les applications web : l’ensemble de l’activité d’administration, des contrôles d’authentification, des vérifications d’autorisation, des suppressions de données, de l’accès aux données, des modifications de données et des modifications d’autorisations. Cela concerne également tout autre ensemble d’événements définis par les clients.*

- Contenu des enregistrements d’audit [NIST SP 800-53 contrôle AU-3]

  > Instruction de mise en œuvre du contrôle AU-3 (2) : *Microsoft Operations Management Suite (OMS) est utilisé comme un outil central de gestion et de configuration pour tous les enregistrements d’audit générés par les équipements de réseau, de stockage et de calcul.*

- Capacité de stockage de l’audit [NIST SP 800-53 contrôle AU-4]

  > Instruction de mise en œuvre du contrôle AU-4 : *Microsoft Operations Management Suite (OMS) est utilisé comme un outil central de gestion et de configuration de tous les enregistrements d’audit générés par les équipements de réseau, de stockage et de calcul. L’outil central de gestion permet de configurer la capacité de stockage des enregistrements d’audit.*

- Réponse aux échecs de traitement d’audit [NIST SP 800-53 contrôle AU-5]

  > Instruction de mise en œuvre du contrôle AU-5 : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit pour alerter le personnel approprié de l’organisation en cas d’échecs de traitement de l’audit.*

  > Instruction de mise en œuvre du contrôle AU-5 (1) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit pour alerter le personnel approprié de l’organisation, dans la période définie par l’organisation que le stockage d’enregistrements de stockage a atteint un pourcentage défini par l’organisation du volume maximum de référentiel.*

  > Instruction de mise en œuvre du contrôle AU-5 (2) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit pour alerter le personnel approprié de l’organisation en temps réel lorsque des événements pouvant être audités définis dans la stratégie d’organisation échouent.*

- Révision d’audit, analyse et création de rapports [NIST SP 800-53 contrôle AU-6]

  > Instruction de mise en œuvre du contrôle AU-6 (3) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit, ainsi qu’avec la solution Log Analytics, afin d’analyser les différents référentiels de journal d’audit pour une prise en compte de la situation à l’échelle de l’organisation. L’outil d’analyse est utilisé pour la création de rapports sur des journaux d’audit afin de fournir un état des lieux de la situation.*

  > Instruction de mise en œuvre du contrôle AU-6 (4) : *Microsoft Operations Management Suite (OMS) est utilisé comme un outil central de gestion et de configuration de tous les enregistrements d’audit. L’outil central de gestion permet de passer en revue et d’analyser les enregistrements d’audit issus de toutes les sources.*

  > Instruction de mise en œuvre du contrôle AU-6 (5) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit, ainsi qu’avec la solution Log Analytics, afin d’analyser les différentes données associées à la sécurité, de l’analyse des vulnérabilités à la surveillance des systèmes informatiques en passant par les données associées aux performances. L’analyse de ces différentes ressources permet de mieux identifier toute activité suspecte.*

- Réduction des audits et génération de rapports [NIST SP 800-53 contrôle AU-7]

  > Instruction de mise en œuvre du contrôle AU-7 : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit, ainsi qu’avec la solution Log Analytics, afin de générer des rapports lisibles à la demande, permettant d’effectuer des enquêtes après coup lors d’incidents de sécurité. L’utilisation de l’outil Microsoft Log Analytics ne modifie pas de manière définitive ou irréversible le contenu d’origine des enregistrements d’audit ou l’ordonnancement temporel.*

  > Instruction de mise en œuvre du contrôle AU-7 (1) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit pour interroger les événements pouvant être audités définis par l’organisation.*

- Protection des informations d’audit [NIST SP 800-53 contrôle AU-9]

  > Instruction de mise en œuvre du contrôle AU-9 : *Microsoft Operations Management Suite (OMS) est utilisé avec les groupes de sécurité pour gérer l’accès aux informations et aux outils d’audit pour empêcher les accès, modifications et suppressions non autorisés d’enregistrements d’audit. La possibilité d’afficher des informations d’audit et d’utiliser les outils d’audit est limitée aux utilisateurs qui demandent ces autorisations.*

  > Instruction de mise en œuvre du contrôle AU-9 (2) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit, ainsi qu’avec Azure Backup pour sauvegarder les journaux d’audit dans Azure, qui réplique ensuite les données afin d’assurer la disponibilité et la fiabilité des données. Azure Backup fournit un emplacement sécurisé pour que les journaux d’audit soient stockés sur un système autre que le système en cours d’audit.* 

  > Instruction de mise en œuvre du contrôle AU-9 (4) : *Microsoft Operations Management Suite (OMS) est utilisé avec les groupes de sécurité pour gérer l’accès aux informations et aux outils d’audit. Ces autorisations sont uniquement accordées au personnel ayant spécifiquement besoin d’accéder à la gestion de la fonctionnalité.*

- Rétention des enregistrements d’audit [NIST SP 800-53 contrôle AU-11]

  > Instruction de mise en œuvre du contrôle AU-11 : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit pour configurer la rétention des journaux d’audit. La rétention des journaux d’audit est configurée pour conserver les données d’audit pendant au moins 90 jours.*

- Génération d’audit [NIST SP 800-53 contrôle AU-12]

  > Instruction de mise en œuvre du contrôle AU-12 : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit pour collecter les événements pouvant être audités définis dans le contrôle AU-02 à partir des composants des systèmes informatiques. La solution de sécurité et d’audit d’OMS est utilisée par le personnel défini par l’organisation pour définir les événements pouvant être audités qui sont collectés à partir d’appareils spécifiques.*

  > Instruction de mise en œuvre du contrôle AU&12; (1) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit, ainsi qu’avec Log Analytics, pour compiler des enregistrements d’audit à partir des composants système qui peuvent être triés par horodatages pour créer une piste d’audit à l’échelle du système.*

- Restrictions d’accès pour modification [NIST SP 800-53 contrôle CM-5]

  > Instruction de mise en œuvre du contrôle CM-5 (1) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit et Active Directory pour appliquer les restrictions d’accès logique via l’appartenance aux groupes de sécurité. Les actions effectuées par ces groupes de sécurité sont auditées par OMS.*

- Inventaire de composants du système informatique [NIST SP 800-53 contrôle CM-8]

  > Instruction de mise en œuvre du contrôle CM-8 (3) : *Microsoft Operations Management Suite (OMS) est utilisé avec la solution de sécurité et d’audit, ainsi qu’avec la solution Antimalware pour détecter la présence de logiciels non autorisés. Lors de la détection, OMS désactive le composant infecté et envoie une alerte au personnel défini par l’organisation.*

Des documents, outils, modèles et autres ressources sont disponibles pour guider le déploiement sécurisé de services et fonctionnalités Azure. Prise en main d’Operations Management Suite à l’aide de [Microsoft Azure Docs](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/).

### <a name="additional-implementation-guidance"></a>Recommandations supplémentaires relatives à la mise en œuvre

Outre les services Azure principaux décrits ci-dessus, plusieurs fonctionnalités intégrées peuvent aider les organisations à se conformer aux exigences de conformité de contrôle de sécurité. Toutes les ressources Azure sont organisées en groupes de ressources. Les ressources peuvent être organisées en appliquant des balises, qui sont des paires clé-valeur pouvant être sélectionnées par le client pour identifier les ressources par catégorie ou toute autre propriété. Les groupes de ressources Azure assurent une identification et un suivi complets de toutes les ressources client déployées dans Azure [NIST SP 800-53 contrôle CM-8]. 

> Instruction de mise en œuvre du contrôle CM-8 : *toutes les ressources contrôlées par le client déployées appartiennent au groupe de ressources « client-ressources ». Dans le portail Azure, toutes les ressources déployées sont identifiées dans le panneau du groupe de ressources, garantissant un inventaire précis et actuel. Les balises sont appliquées aux ressources, au besoin, pour satisfaire les besoins de suivi et de création de rapports.*

Les modèles Azure Resource Manager permettent aux clients de définir la configuration des ressources au sein de leur déploiement Azure. Les modèles Resource Manager sont basés sur JSON et utilisent la syntaxe déclarative pour documenter les ressources, les paramètres de configuration et d’autres aspects d’un environnement Azure. Les modèles Resource Manager sont générés automatiquement lorsque des solutions Azure sont déployées à partir du portail Azure. Ils peuvent également être créés et modifiés manuellement pour satisfaire les besoins spécifiques du client. Ces modèles Resource Manager peuvent être une représentation de la configuration de la ligne de base pour un système informatique [NIST SP 800-53 contrôle CM-2]. Les modèles Resource Manager existants, tels que les blocs de construction mentionnés ci-dessus, peuvent être personnalisés pour satisfaire les besoins de tout déploiement.

> Instruction de mise en œuvre du contrôle CM-2 : *en tant que composant de la configuration de la ligne de base du système informatique, les modèles Azure Resource Manager sont mis à jour, sous contrôle de la configuration, représentant les ressources contrôlées par le client déployées et la configuration des composants systèmes informatiques dans Azure. Ces modèles capturent les ressources déployées, notamment les machines virtuelles, le stockage et les ressources du réseau, notamment les configurations des ressources réseau qui contrôlent le trafic réseau entrant, sortant, et au sein de l’environnement Azure.*

## <a name="next-steps"></a>Étapes suivantes

Pour les demandes liées à Azure Blueprint, aux processus FedRAMP, du département de la Défense ou Agency ATO, pour toute aide relative à la conformité ou pour fournir des commentaires sur Blueprint, envoyez un e-mail à [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

[Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Blog Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


