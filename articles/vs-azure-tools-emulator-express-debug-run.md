<properties
   pageTitle="Utilisation de l’émulateur express pour exécuter et déboguer un service cloud sur une machine locale | Microsoft Azure"
   description="Utilisation de l’émulateur express pour exécuter et déboguer un service cloud sur une machine locale"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# Utilisation de l’émulateur express pour exécuter et déboguer un service cloud sur une machine locale

Avec l’émulateur express, vous testez et déboguez un service cloud sans avoir à exécuter Visual Studio en tant qu’administrateur. Vous pouvez définir les paramètres du projet pour utiliser l’émulateur express ou l’émulateur complet selon la configuration requise de votre service cloud. Pour plus d’informations sur l’émulateur complet, consultez [Exécuter une application Azure dans l’émulateur de calcul](./storage/storage-use-emulator.md). L’émulateur express a été introduit dans le Kit de développement logiciel (SDK) Azure 2.1 et, depuis le Kit de développement logiciel (SDK) Azure 2.3, il s’agit de l’émulateur par défaut.

## Utilisation de l’émulateur express dans l’IDE Visual Studio

Quand vous créez un projet dans le Kit de développement logiciel (SDK) Azure 2.3 ou une version ultérieure, l’émulateur express est déjà sélectionné. Pour les projets existants créés à l’aide d’une version antérieure du kit de développement logiciel (SDK), suivez ces étapes pour sélectionner l’émulateur express.

### Pour configurer un projet pour utiliser l’émulateur express

1. Dans le menu contextuel du projet Azure, choisissez **Propriétés**, puis l’onglet **Web**.

1. Sous **Serveur de développement local**, choisissez la case d’option **Utiliser IIS Express**. L’émulateur express n’est pas compatible avec le serveur web IIS.

1. Sous **Émulateur**, choisissez la case d’option **Utiliser l’émulateur express**.

    ![Émulateur express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## Démarrage de l’émulateur express à partir d’une invite de commande

À partir d’une invite de commande, exécutez la version express de l’émulateur de calcul Azure (csrun.exe) avec l’option /useemulatorexpress.

## Limitations

Avant d’utiliser l’émulateur express, prenez connaissance des limitations ci-dessous :

- L’émulateur express n’est pas compatible avec le serveur web IIS.

- Votre service cloud peut contenir plusieurs rôles, mais chaque rôle est limité à une seule instance.

- Vous n’avez pas accès aux numéros de port inférieurs à 1 000. Par exemple, si vous faites appel à un fournisseur d’authentification qui utilise habituellement un numéro de port inférieur à 1 000, vous devrez peut-être remplacer cette valeur par un numéro de port supérieur à 1 000.

- Les limitations qui s’appliquent à l’émulateur de calcul Azure s’appliquent aussi à l’émulateur express. Par exemple, il ne peut pas y avoir plus de 50 instances de rôle par déploiement. Consultez [Exécuter une application Azure dans l’émulateur de calcul](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## Étapes suivantes

[Débogage de Cloud Services](https://msdn.microsoft.com/library/azure/ee405479.aspx)

<!---HONumber=AcomDC_0817_2016-->