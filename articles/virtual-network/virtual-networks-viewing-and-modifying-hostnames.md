<properties 
   pageTitle="Affichage et modification des noms d’hôtes | Microsoft Azure"
   description="Affichage et modification des noms d'hôtes pour les machines virtuelles, les rôles web et les rôles de travail pour la résolution de noms"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# Affichage et modification des noms d'hôtes

Pour permettre le référence par nom d’hôte de vos instances de rôle, vous devez définir la valeur de nom d’hôte dans le fichier de configuration de service associé à chaque rôle. Pour ce faire, ajoutez le nom d’hôte à l’attribut **vmName** de l’élément **Role**. La valeur de l’attribut **vmName** est utilisée comme base de nom d’hôte pour chaque instance de rôle. Par exemple, si **vmName** est *webrole* et qu’il existe trois instances de ce rôle, les hôtes des instances s’intitulent *webrole0*, *webrole1* e*webrole2*. Il n’est pas nécessaire de définir un nom d’hôte pour les machines virtuelles dans le fichier de configuration, car ces noms sont renseignés en fonction du nom de la machine virtuelle. Pour en savoir plus sur la configuration d’un service Microsoft Azure, consultez la section [Schéma de configuration du service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx) (en anglais).

## Affichage des noms d’hôtes

Vous pouvez afficher les noms d’hôtes des machines virtuelles et des instances de rôle dans un service cloud à l’aide des utilitaires ci-dessous.

### Portail Azure

Vous pouvez utiliser le [portail Azure](http://portal.azure.com) pour afficher les noms d’hôtes pour les machines virtuelles sur le panneau de vue d’ensemble d’une machine virtuelle. N’oubliez pas que panneau comporte des valeurs pour les attributs **Nom** et **Nom d’hôte**. Bien que ces deux éléments soient initialement identiques, toute modification du nom d’hôte n’affecte en rien le nom de la machine virtuelle ou de l’instance de rôle.

Les instances de rôle peuvent être également affichées dans le portail Azure. Cependant, lorsque vous répertoriez les instances dans un service cloud, le nom d’hôte ne s’affiche pas. Un nom apparaît pour chaque instance, mais il ne représente aucunement le nom d’hôte.

### Fichier de configuration de service

Vous pouvez télécharger le fichier de configuration de service d’un service déployé à partir du panneau **Configurer** du service dans le portail Azure. Pour afficher le nom d’hôte, il vous suffit de rechercher l’attribut **vmName** associé à l’élément **Nom de rôle**. Gardez à l’esprit que ce nom d’hôte est utilisé comme base pour le nom d’hôte de chaque instance de rôle. Par exemple, si **vmName** est *webrole* et qu’il existe trois instances de ce rôle, les hôtes des instances s’intitulent *webrole0*, *webrole1* et *webrole2*.

### Bureau à distance

Une fois que vous avez activé le Bureau à distance (Windows), l’accès distant Windows PowerShell (Windows) ou les connexions SSH (Linux et Windows) de vos machines virtuelles ou de vos instances de rôle, vous pouvez afficher le nom d’hôte d’une connexion active de Bureau à distance de différentes manières :

- Tapez le nom d’hôte sur l’invite de commande ou le terminal SSH.

- Tapez ipconfig /all sur l’invite de commande (Windows uniquement).

- Affichez le nom d’ordinateur dans les paramètres système.

### API REST de gestion des services Azure

À partir d’un client REST. suivez ces instructions :

1. Assurez-vous de disposer d’un certificat client pour vous connecter au portail Azure. Pour obtenir un certificat client, suivez la procédure présentée dans la section [Téléchargement et importation des paramètres de publication et des informations d’abonnement](https://msdn.microsoft.com/library/dn385850.aspx).

1. Définissez une entrée d’en-tête intitulée x-ms-version , présentant une valeur de 2013-11-01.

1. Envoyez une demande au format suivant : https://management.core.windows.net/\<ID\_abonnement>/services/hostedservices/<nom\_service>?embed-detail=true

1. Recherchez l’élément **HostName** associé à chaque élément **RoleInstance**.

>[AZURE.WARNING] Pour afficher le suffixe de domaine interne de votre service cloud à partir d’une réponse d’appel REST, sélectionnez l’élément **InternalDnsSuffix** ou exécutez ipconfig /all à partir d’une invite de commandes dans une session Bureau à distance (Windows) ou exécutez cat /etc/resolv.conf à partir d’un terminal SSH (Linux).

## Modification d’un nom d’hôte

Pour modifier le nom d’hôte associé à une machine virtuelle ou à une instance de rôle, chargez un fichier de configuration de service modifié, ou renommez l’ordinateur à partir d’une session du Bureau à distance.

## Étapes suivantes

[Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma de configuration de service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schéma de configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Définir les paramètres DNS à l'aide de fichiers de configuration réseau](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

<!---HONumber=AcomDC_0810_2016-->