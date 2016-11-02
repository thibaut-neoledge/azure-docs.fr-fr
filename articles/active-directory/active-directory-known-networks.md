<properties 
    pageTitle="Réseaux connus | Microsoft Azure" 
    description="En configurant les réseaux connus, vous pouvez éviter que des adresses IP appartenant à votre organisation ne soient incluses dans les rapports Connexions depuis plusieurs zones géographiques et Connexions à partir d’adresses IP affichant une activité suspecte." 
    services="active-directory" 
    documentationCenter="" 
    authors="MarkusVi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>


# <a name="known-networks"></a>Réseaux connus


Vous pouvez utiliser les rapports d'accès et d'utilisation Azure Active Directory pour obtenir une visibilité complète sur l'intégrité et la sécurité du répertoire de votre société. Grâce à ces informations, un administrateur de répertoire est capable de déterminer plus précisément les risques de sécurité potentiels et donc de les atténuer au maximum.

Il est possible que les rapports « *Connexions depuis plusieurs zones géographiques* » et « *Connexions à partir d’adresses IP affichant une activité suspecte* » signalent de manière incorrecte des adresses IP qui appartiennent en fait à votre organisation. 

Cela peut, par exemple, se produire dans les cas suivants : 

- Un utilisateur de votre bureau de Boston qui s'est connecté à distance à votre centre de données à San Francisco déclenche le rapport « Connexions depuis plusieurs zones géographiques » 

- Un utilisateur de votre organisation qui tente de se connecter plusieurs fois avec un mot de passe incorrect déclenche le rapport « Connexions à partir d’adresses IP affichant une activité suspecte » 

Pour empêcher ces cas de générer des rapports de sécurité incorrects, vous devez ajouter des plages d'adresses IP connues à la liste d'adresses IP publiques de votre organisation.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Pour ajouter les plages d'adresses IP publiques de votre organisation, procédez comme suit : 

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).

2.  Dans le volet gauche, cliquez sur **Active Directory**. <br><br>![Fonctionnement de Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-01.png)

3.  Dans l'onglet **Annuaire** , sélectionnez votre annuaire.

4.  Dans le menu situé en haut, cliquez sur **Configurer**. <br><br>![Fonctionnement de Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-02.png)

5.  Sous l'onglet Configurer, accédez aux **plages d'adresses IP publiques de votre organisation** <br><br>![Fonctionnement de Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Cliquez sur **Ajouter des plages d'adresses IP connues**.

7.  Ajouter les plages d'adresses dans la boîte de dialogue qui s'affiche, puis cliquez sur le bouton de vérification lorsque vous avez terminé. <br><br>![Fonctionnement de Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-04.png)


**Ressources supplémentaires**


* [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)
* [Connexions à partir d’adresses IP affichant une activité suspecte](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Connexions depuis plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md)





<!--HONumber=Oct16_HO2-->


