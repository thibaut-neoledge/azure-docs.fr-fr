<properties
	pageTitle="Documentation Azure Government | Microsoft Azure"
	description="This provides a comparision of features and guidance on developing applications for Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Vue d’ensemble de la documentation Azure Government

##  Introduction à la documentation Azure Government

Ce site décrit les fonctionnalités des services [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) et fournit des indications générales applicables à tous les clients. Avant d’inclure des données spécifiquement réglementées à votre abonnement Azure Government, vous devez vous familiariser avec les fonctionnalités Azure Government et contacter votre équipe de compte si vous avez des questions.

Reportez-vous à la [page de conformité du centre de gestion de la confidentialité Azure Microsoft](http://www.microsoft.com/fr-FR/TrustCenter/Compliance/default.aspx) pour obtenir des informations actuelles sur les services Azure Government couverts par les accréditations et réglementations spécifiques. D’autres services Microsoft peuvent également être disponibles, mais ne font pas partie des services Azure Government couverts et ne sont pas traités dans ce document. Les services Azure Government peuvent également vous autoriser à utiliser différents services, ressources ou applications supplémentaires fournis par des tiers, ou par Microsoft conformément à des conditions d’utilisation et des stratégies de confidentialité distinctes, qui ne sont pas inclus dans le cadre de ce document. Il est de votre responsabilité de passer en revue les termes de toutes ces offres « additionnelles », telles que les offres du Marketplace, afin de vous assurer qu’elles répondent à vos besoins en matière de conformité.

Azure Government est disponible pour les entités gérant des données soumises à certaines réglementations et exigences gouvernementales (par exemple, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS) pour lesquelles l’utilisation d’Azure Government est requise à des fins de conformité. Les clients Azure Government sont soumis à un contrôle d’éligibilité.

Les entités ayant des questions relatives à l’éligibilité pour Azure Government doivent consulter leur équipe de compte.

##  Principes de sécurisation des données client dans Azure Government

Azure Government fournit une gamme de fonctionnalités et de services que vous pouvez utiliser pour créer des solutions cloud répondant à vos besoins en matière de données réglementées/contrôlées. Une solution client conforme est tout simplement une implémentation efficace des capacités Azure Government prêtes à l’emploi, associée à des fonctionnalités solides pour la sécurité des données. Lorsque vous hébergez une solution dans Azure Government, Microsoft gère un grand nombre de ces exigences au niveau de l’infrastructure cloud.

Le diagramme suivant illustre le modèle de défense approfondie d’Azure. Par exemple, Microsoft fournit une protection de base contre les DDoS à l’encontre de l’infrastructure cloud, ainsi que des fonctionnalités client telles que des dispositifs de sécurité pour les besoins DDoS des applications client spécifiques.

![texte de remplacement](./media/azure-government-Defenseindepth.png)

Cette page décrit les principes fondamentaux de la sécurisation de vos services et applications, et fournit des conseils et meilleures pratiques sur leur application. En d’autres termes, elle détaille comment utiliser Azure Government de manière optimale pour se conformer aux obligations et responsabilités requises pour une solution gérant des informations ITAR.

Les principes fondamentaux pour la sécurisation des données client sont :
* Protection des données à l’aide du chiffrement
* Gestion des secrets
* Isolation pour restreindre l’accès aux données

##  Protection des données client à l’aide du chiffrement

L’atténuation des risques et le respect des obligations réglementaires donnent un poids et une importance croissants au chiffrement des données. Implémentez le chiffrement de manière efficace pour améliorer les mesures de sécurité actuelles pour le réseau et les applications, et réduire le risque global pour votre environnement cloud.

### {0}{0}Chiffrement au repos
Le chiffrement des données au repos s’applique à la protection du contenu client stocké sur disque. Il existe plusieurs façons de procéder :

### {0}{0}Storage Service Encryption

Azure Storage Service Encryption est appliqué au niveau du compte de stockage, ce qui a pour effet de chiffrer automatiquement les objets blob de bloc et les objets blog de pages quand ils sont écrits dans Azure Storage. Les données lues à partir d’Azure Storage sont déchiffrées par le service de stockage avant d’être renvoyées. Utilisez ce processus pour sécuriser vos données sans avoir à modifier ni ajouter de code dans les applications.

### {0}{0}Azure Disk Encryption
Utilisez Azure Disk Encryption pour chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle Azure. L’intégration avec Azure Key Vault vous permet de contrôler le chiffrement et vous aide à gérer les clés de chiffrement de disque.

### {0}{0}Chiffrement côté client
Le chiffrement côté client est intégré aux bibliothèques clientes de stockage Java et .NET, qui peuvent utiliser les API Azure Key Vault. Cela simplifie l’implémentation. Utilisez Azure Key Vault pour accorder l’accès aux clés secrètes dans Azure Key Vault à des utilisateurs spécifiques à l’aide d’Azure Active Directory.

### {0}{0}Chiffrement en transit

Le chiffrement de base disponible pour la connectivité à Azure Government prend en charge le protocole de sécurité TLS (Transport Level Security) 1.2 et les certificats X.509. Les algorithmes de chiffrement de la norme FIPS (Federal Information Processing Standard) 140-2, niveau 1 sont également utilisés pour les connexions réseau de l’infrastructure entre les centres de données Azure Government. Windows Server 2012 R2, les machines virtuelles Windows 8 et les partages de fichiers Azure peuvent utiliser SMB 3.0 pour le chiffrement entre la machine virtuelle et le partage de fichiers. Utilisez le chiffrement côté client pour chiffrer les données avant leur transfert vers un espace de stockage dans une application client, et les déchiffrer après leur transfert à partir de l’espace de stockage.

### {0}{0}Meilleures pratiques pour le chiffrement

* Machines virtuelles IaaS : utilisez Azure Disk Encryption. Activez Storage Service Encryption pour chiffrer les fichiers VHD utilisés pour la sauvegarde des disques dans Azure Storage. Notez que cette fonctionnalité chiffre uniquement les nouvelles données écrites. Autrement dit, si vous créez une machine virtuelle et activez ensuite Storage Service Encryption sur le compte de stockage qui contient le fichier VHD, les modifications apportées sont chiffrées, mais pas le fichier VHD d’origine.
* Chiffrement côté client : il s’agit de la méthode la plus sûre pour chiffrer vos données, car il chiffre les données avant leur transit et il chiffre les données au repos. Toutefois, cette méthode vous oblige à ajouter du code dans vos applications qui utilisent le stockage, ce qui peut ne pas vous convenir. Dans ce cas, vous pouvez utiliser le protocole HTTPS pour vos données en transit, et Storage Service Encryption pour chiffrer les données au repos. Le chiffrement côté client entraîne également une plus grande charge pour le client. Vous devez donc tenir compte de ce paramètre dans vos plans d’extensibilité, en particulier si vous chiffrez et transférez une grande quantité de données.

Pour plus d’informations sur les options de chiffrement dans Azure, consultez le [Guide de sécurité Azure Storage](/storage-security-guide).

##  Protection des données client grâce à la gestion des secrets

Une gestion sécurisée des clés est primordiale pour la protection des données du cloud. Les clients doivent s’efforcer de simplifier la gestion des clés et de garder le contrôle des clés utilisées par les services et applications cloud pour chiffrer les données.

### <a name="Overview"></a>Meilleures pratiques pour la gestion des secrets

* Utilisez Azure Key Vault pour réduire les risques d’exposition des secrets dans les fichiers de configuration codés en dur, les scripts ou le code source. Azure Key Vault chiffre les clés (par exemple, les clés de chiffrement pour Azure Disk Encryption) et les secrets (tels que les mots de passe), en les stockant dans des modules de sécurité matériels (HSM) conformes à la norme FIPS 140-2 de niveau 2. Pour une meilleure garantie, vous pouvez importer ou générer des clés dans ces HSM.
* Le code et les modèles d’application ne doivent contenir que des références URI à des secrets, ce qui signifie que les secrets réels ne se trouvent pas dans le code, la configuration ou les référentiels de code source. Cela évite les attaques de type hameçonnage sur les clés dans les référentiels internes ou externes, par exemple, de programmes zombies dans Github.
* Utilisez des contrôles RBAC forts dans Azure Key Vault. Si un opérateur de confiance quitte l’entreprise ou est transféré dans un autre groupe de l’entreprise, son accès aux secrets doit lui être retiré.

Pour plus d’informations, consultez [Key Vault pour Azure Government](/azure-government/azure-government-tech-keyvault)

##  Isolation pour restreindre l’accès aux données

L’isolation correspond à l’utilisation de limites, de segmentation et de conteneurs pour que seuls les utilisateurs, services et applications autorisés aient accès aux données. Par exemple, la séparation entre les clients est un mécanisme de sécurité essentiel pour les plateformes de cloud mutualisées comme Microsoft Azure. L’isolation logique permet d’empêcher un client d’interférer avec les opérations d’un autre client.

### {0}{0}Isolation de l’environnement
L’environnement Azure Government est une instance physique et isolée du réseau qui est distincte du reste du réseau de Microsoft. L’isolation s’effectue à l’aide d’une série de contrôles physiques et logiques qui incluent les éléments suivants :
* Sécurisation de barrières physiques à l’aide d’appareils biométriques et de caméras.
* Utilisation d’informations d’identification et d’une authentification multifacteur par le personnel Microsoft nécessitant un accès logique à l’environnement de production.
* Toute l’infrastructure de service d’Azure Government se trouve aux États-Unis.

#### {0}{0}Isolation par client
Azure implémente le contrôle d’accès réseau et la répartition via l’isolation des réseaux locaux virtuels (VLAN), des listes de contrôle d’accès, des équilibreurs de charge et des filtres IP.

Les clients peuvent isoler davantage leurs ressources au niveau des abonnements, des groupes de ressources, des réseaux virtuels et des sous-réseaux.

Pour plus d’informations sur l’isolation dans Microsoft Azure, consultez la [section Isolation du guide de sécurité Azure](/azure-security-getting-started/#isolation).

Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.

<!---HONumber=AcomDC_0831_2016-->