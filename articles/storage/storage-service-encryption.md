<properties
	pageTitle="Azure Storage Service Encryption pour les données au repos (version préliminaire) | Microsoft Azure"
	description="La fonctionnalité Azure Storage Service Encryption permet de chiffrer votre stockage d’objets blob Azure côté service lors du stockage des données et de le déchiffrer lorsque vous récupérez les données."
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="robinsh"/>

# Azure Storage Service Encryption pour les données au repos (version préliminaire)

Azure Storage Service Encryption pour les données au repos vous aide à protéger vos données pour répondre aux engagements de votre organisation en matière de sécurité et de conformité. Avec cette fonctionnalité, Azure Storage chiffre automatiquement vos données avant de les rendre persistantes dans le stockage et les déchiffre avant la récupération. La gestion du chiffrement, du déchiffrement et des clés est totalement transparente pour les utilisateurs.

Les sections suivantes fournissent des instructions détaillées sur la façon d’utiliser les fonctionnalités Storage Service Encryption et présentent les scénarios et les expériences utilisateur pris en charge.

## Vue d'ensemble


Azure Storage propose un ensemble complet de fonctionnalités de sécurité qui, réunies, permettent aux développeurs de créer des applications sécurisées. Les données peuvent être sécurisées en transit entre une application et Azure au moyen du [chiffrement côté client](storage-client-side-encryption.md), de HTTPs ou de SMB 3.0. Storage Service Encryption est une nouvelle fonctionnalité Azure Storage qui chiffre les données lorsque celles-ci sont écrites dans votre stockage Azure Storage prenant en charge les objets blob de blocs, les objets blob de pages et les objets blob d’ajout. Cette fonctionnalité peut être activée pour les nouveaux comptes de stockage à l’aide du modèle de déploiement Azure Resource Manager. Elle est disponible pour tous les niveaux de redondance (LRS, ZRS, GRS, RA-GRS). La fonctionnalité Storage Service Encryption est disponible pour le stockage Standard et Premium Storage. Elle se charge de la gestion du chiffrement, du déchiffrement et des clés de façon totalement transparente. Toutes les données sont chiffrées à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des algorithmes de chiffrement par blocs les plus puissants disponibles. La section Version préliminaire ci-dessous comporte des informations sur la participation à la version préliminaire de Storage Service Encryption.

Cette capture d’écran montre où trouver le paramètre Storage Service Encryption à l’aide du [portail Azure](https://azure.portal.com). Sur cet écran, cliquez sur Chiffrement pour continuer.

![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-service-encryption/image1.png)

Lorsque vous avez cliqué sur le paramètre Chiffrement, vous pouvez activer ou désactiver Storage Service Encryption.

![Capture d’écran du portail affichant les propriétés de chiffrement](./media/storage-service-encryption/image2.png)

##Availability

Pour le stockage Standard, cette fonctionnalité est actuellement disponible dans les États-Unis du Centre, les États-Unis de l’Ouest, l’Asie de l’Est et l’Europe de l’Ouest.

Pour le stockage Premium, cette fonctionnalité est actuellement disponible dans les États-Unis du Centre, les États-Unis de l’Ouest et le Japon de l’Est.

Nous mettrons à jour ce document lorsque de nouvelles régions seront disponibles pour cette fonctionnalité.

##Scénarios de chiffrement

Storage Service Encryption peut être activé au niveau d’un compte de stockage. Les scénarios clients suivants sont pris en charge :

-   Chiffrement des objets blob de blocs, des objets blob d’ajout et des objets blob de pages.

-   Chiffrement des modèles et des disques durs virtuels archivés dans Azure depuis un emplacement local.

-   Chiffrement du système d’exploitation et des disques de données sous-jacents pour les machines virtuelles IaaS créées à l’aide de vos disques durs virtuels.

La version préliminaire publique inclut les limitations suivantes :

-   Le chiffrement des comptes de stockage classiques n’est pas pris en charge.

-   Le chiffrement des comptes de stockage classiques migrés vers des comptes de stockage Resource Manager n’est pas pris en charge.

-   Données existantes : SSE chiffre uniquement les données créées après l’activation du chiffrement. Par exemple, si vous créez un compte de stockage Resource Manager sans activer le chiffrement, puis que vous téléchargez des objets blob ou des disques durs virtuels archivés dans ce compte de stockage avant d’activer SSE, ces objets blob ne sont pas chiffrés, sauf s’ils sont réécrits ou copiés.

-   Prise en charge du Marketplace : activez le chiffrement des machines virtuelles créées à partir du Marketplace à l’aide du (portail Azure) [https://portal.azure.com), de PowerShell et de l’interface de ligne de commande Azure. L’image de base du disque dur virtuel reste non chiffrée. Toutefois, les écritures effectuées une fois que la machine virtuelle est opérationnelle sont chiffrées.

-   Les données des tables, files d’attente et données ne sont pas chiffrées.

##VERSION PRÉLIMINAIRE

Cette fonctionnalité est prise en charge uniquement pour les comptes de stockage Resource Manager qui viennent d’être créés. Les comptes de stockage classiques ne sont pas pris en charge. Pour utiliser cette nouvelle fonctionnalité, vous devez inscrire votre abonnement à l’aide des applets de commande PowerShell. Une fois que votre abonnement est approuvé, vous pouvez activer SSE dans le compte de stockage associé. Comme avec la plupart des versions préliminaires, cela ne doit pas être utilisé pour les charges de travail de production tant que la mise à la disposition générale n’est pas effective. Vous pouvez rejoindre notre groupe dédié à la version préliminaire de Storage Service Encryption sur Yammer pour fournir des commentaires sur votre expérience.

### Inscription à la version préliminaire

-   [Installez les applets de commande Azure PowerShell](../powershell-install-configure.md).

-   Dans Windows 10, ouvrez PowerShell en tant qu’administrateur.

-   Inscrivez-vous auprès de l’espace de noms du fournisseur de ressources de stockage. Cette opération est nécessaire uniquement pour un abonnement qui n’est pas déjà inscrit auprès du fournisseur de ressources de stockage.

    `PS E:> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage" `

-   Pour vous inscrire à la fonctionnalité, vous pouvez utiliser l’applet de commande PowerShell Register-AzureRmProviderFeature.

    `Register-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

-   Pour interroger l’état de votre inscription afin de voir si l’abonnement a été approuvé, vous pouvez utiliser l’applet de commande PowerShell Get-AzureRmProviderFeature.

    `Get-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

Lorsque l’état d’inscription est défini sur Inscrit, cela signifie que votre abonnement a été approuvé. Consultez également notre groupe dédié à la version préliminaire d’Azure Storage Service Encryption sur Yammer.

##Mise en route

###Étape 1 : [S’inscrire à la version préliminaire](#registering-for-preview).

###Étape 2 : [Créer un compte de stockage](storage-create-storage-account.md).

###Étape 3 : Activer le chiffrement.

Vous pouvez activer le chiffrement à l’aide du [portail Azure](https://portal.azure.com).

> [AZURE.NOTE] Si vous souhaitez activer ou désactiver Storage Service Encryption par programme sur un compte de stockage, vous pouvez utiliser [l’API REST du fournisseur de ressources de stockage Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx). Nous ajouterons bientôt cette capacité à la [bibliothèque cliente du fournisseur de ressources de stockage pour .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), Azure PowerShell et l’interface de ligne de commande Azure.

###Étape 4 : Copier les données dans le compte de stockage

#### Utilisation d’AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour copier des données depuis et vers un objet blob Microsoft Azure, un fichier ou un stockage de table en utilisant les commandes avec une performance optimale. Cet utilitaire permet de copier les données d’un compte Blob Storage existant dans votre compte de stockage dans lequel la fonctionnalité de chiffrement est activée.

Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

#### Utilisation des bibliothèques clientes de stockage

Vous pouvez charger et télécharger des données vers et depuis Blob Storage à l’aide de notre vaste choix de bibliothèques clientes de stockage, dont .NET, C++, Java, Android, Node.js, PHP, Python et Ruby.

Pour plus d’informations, consultez la section [Prise en main du stockage d’objets blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md).

#### Utilisation d’un explorateur de stockage

Les explorateurs de stockage permettent de créer des comptes de stockage, de charger et de télécharger des données, d’afficher le contenu des objets blob et de parcourir les répertoires. Nombre d’entre eux prennent en charge les comptes de stockage classiques et Resource Manager.

Vous pouvez utiliser l’un de ces explorateurs de stockage pour télécharger des objets blob dans votre compte de stockage avec le chiffrement activé. Avec certains explorateurs de stockage, vous pouvez également copier les données de votre compte de stockage existant vers votre nouveau compte de stockage pour lequel SSE est activé.

Pour en savoir plus, consultez [Explorateurs du stockage Azure](storage-explorers.md).

###Étape 5 : Interroger l’état des données chiffrées

Lorsque SSE sera disponible dans la majorité des régions, une version mise à jour des bibliothèques clientes de stockage sera déployée afin que vous puissiez interroger l’état d’un objet pour déterminer s’il est chiffré ou non.

En attendant, vous pouvez appeler l’opération [Obtenir les propriétés de compte](https://msdn.microsoft.com/library/azure/mt163553.aspx) afin de vérifier que le chiffrement est activé pour votre compte de stockage ou d’afficher les propriétés du compte de stockage dans le portail Azure.

##Flux de travail de chiffrement et de déchiffrement

Voici une brève description du flux de travail de chiffrement et de déchiffrement :

-   Le client active le chiffrement sur le compte de stockage.

-   Lorsque le client écrit de nouvelles données (PUT Blob, PUT Block, PUT Page, etc.) pour Blob Storage, chaque écriture est chiffrée à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des algorithmes de chiffrement par blocs les plus puissants disponibles.

-   Lorsque le client a besoin d’accéder aux données (GET Blob, etc.), celles-ci sont automatiquement déchiffrées avant d’être renvoyées à l’utilisateur.

-   Si le chiffrement est désactivé, les nouvelles écritures ne sont plus chiffrées et les données chiffrées existantes restent chiffrées jusqu’à ce qu’elles soient réécrites par l’utilisateur. Lorsque le chiffrement est activé, les écritures dans Blob Storage sont chiffrées. L’état des données ne change pas lorsque l’utilisateur bascule entre l’activation et la désactivation du chiffrement pour le compte de stockage.

-   Toutes les clés de chiffrement sont stockées, chiffrées et gérées par Microsoft.

##Forum Aux Questions Azure Storage Service Encryption pour les données au repos

**Q : Je possède déjà un compte de stockage classique. Puis-je activer SSE dessus ?**

R : Non. SSE est uniquement pris en charge sur les nouveaux comptes de stockage Resource Manager dans la version préliminaire.

**Q : Comment chiffrer les données de mon compte de stockage classique ?**

R : Vous pouvez créer un nouveau compte de stockage Resource Manager et copier vos données à l’aide [d’AzCopy](storage-use-azcopy.md) de votre compte de stockage classique existant vers votre nouveau compte de stockage Resource Manager.

**Q : Je possède déjà un compte de stockage Resource Manager. Puis-je activer SSE dessus ?**

R : Pendant la version préliminaire de SSE, vous devez créer un compte pour accéder à cette nouvelle fonctionnalité.

**Q : Puis-je chiffrer les données actuelles dans un compte de stockage Resource Manager existant ?**

R : Si votre compte de stockage Resource Manager existant a été créé avant l’annonce de cette version préliminaire, vous pouvez en créer un nouveau et activer le chiffrement. Vous pouvez ensuite copier vos données de l’ancien compte de stockage vers le nouveau. Les données sont alors automatiquement chiffrées. Toutefois, si votre compte de stockage Resource Manager a été créé après l’annonce de la version préliminaire et que vous avez décidé d’activer le chiffrement ultérieurement, vous pouvez activer le chiffrement pour ce compte de stockage à l’aide du portail Azure et réécrire vos données non chiffrées dans le compte de stockage.

**Q : J’utilise Premium Storage. Puis-je utiliser SSE ?**

R : Oui. SSE est pris en charge par le stockage Standard et Premium Storage.

**Q : Si je crée un compte de stockage et que j’active SSE, puis que je crée une machine virtuelle à l’aide de ce compte de stockage, cela signifie-t-il que ma machine virtuelle est chiffrée ?**

R. : Oui. Les disques créés qui utilisent le nouveau compte de stockage sont chiffrés, à condition qu’ils aient été créés après l’activation de SSE. Si la machine virtuelle a été créée à l’aide d’Azure Marketplace, l’image de base du disque dur virtuel reste non chiffrée. Toutefois, les écritures effectuées une fois que la machine virtuelle est opérationnelle sont chiffrées.

**Q : Puis-je créer des comptes de stockage dans lesquels SSE est activé à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure ?**

R : Nous activerons cette possibilité dans la prochaine version d’Azure PowerShell et de l’interface de ligne de commande Azure à la fin du mois d’avril.

**Q : Quel est le coût d’Azure Storage si SSE est activé ?**

R : Aucun coût supplémentaire n’est facturé.

**Q : Comment s’inscrire à la version préliminaire ?**

R : Vous pouvez vous inscrire pour accéder à la version préliminaire à l’aide de PowerShell. Une fois que votre abonnement est approuvé pour la fonctionnalité, vous pouvez utiliser PowerShell pour activer le chiffrement au repos.

**Q : Quand je m’inscris à la version préliminaire à l’aide de PowerShell, quel est le nom de la fonctionnalité que je dois inscrire ?**

R : EncryptionAtRest.

**Q : Qui gère les clés de chiffrement ?**

R : Les clés sont gérées par Microsoft.

**Q : Puis-je utiliser mes propres clés de chiffrement ?**

R : Nous travaillons activement à la mise en place d’une fonctionnalité permettant aux clients d’utiliser leurs propres clés de chiffrement.

**Q : Est-il possible de révoquer l’accès aux clés de chiffrement ?**

R : Pas pour l’instant. Les clés sont entièrement gérées par Microsoft.

**Q : Est-ce que SSE est activé par défaut lorsque je crée un compte de stockage ?**

R : SSE n’étant pas activé par défaut, utilisez le portail Azure pour l’activer. Vous pouvez également l’activer par programme à l’aide de l’API REST du fournisseur de ressources de stockage.

**Q : En quoi est-ce différent du chiffrement de lecteur Azure ?**

R : Cette fonctionnalité permet de chiffrer les données dans Azure Blob Storage. Azure Disk Encryption permet de chiffrer les disques de système d’exploitation et de données dans les machines virtuelles IaaS. Pour plus d’informations, consultez notre [guide de sécurité sur Storage](storage-security-guide.md).

**Q : Que se passe-t-il si j’active SSE, puis que j’active Azure Disk Encryption sur les disques ?**

R: Cela fonctionne de façon transparente. Vos données sont chiffrées par les deux méthodes.

**Q : Mon compte de stockage est configuré pour être répliqué de manière géo-redondante. Si j’active SSE, ma copie redondante est-elle également chiffrée ?**

R : Oui. Toutes les copies du compte de stockage sont chiffrées et l’ensemble des options de redondance sont prises en charge : stockage localement redondant (LRS), stockage redondant dans une zone (ZRS), stockage géo-redondant (GRS) et stockage géo-redondant avec accès en lecture (RA-GRS).

**Q : Je ne parviens pas à activer le chiffrement sur mon compte de stockage.**

R : Quand avez-vous créé le compte de stockage ? Pendant la version préliminaire, vous devez inscrire votre abonnement et également créer un compte de stockage pour pouvoir utiliser SSE. Vous ne pouvez pas activer SSE sur les comptes de stockage créés avant la version préliminaire.

**Q : La version préliminaire de SSE est-elle autorisée uniquement dans des régions spécifiques ?**

R : La fonctionnalité SSE en version préliminaire est actuellement disponible dans la région Asie de l’Est et Europe de l’Ouest pour le stockage Standard et Japon de l’Est pour le stockage Premium Storage. Nous mettrons à jour ce document lorsque de nouvelles régions seront disponibles dans les mois qui viennent.

**Q : Comment obtenir de l’aide si je rencontre des problèmes ou que je souhaite envoyer des commentaires ?**

R : Contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour les problèmes de chiffrement Storage Service Encryption.

##Étapes suivantes

Azure Storage propose un ensemble complet de fonctionnalités de sécurité qui, réunies, permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez notre [guide de sécurité sur Storage](storage-security-guide.md).

<!---HONumber=AcomDC_0629_2016-->