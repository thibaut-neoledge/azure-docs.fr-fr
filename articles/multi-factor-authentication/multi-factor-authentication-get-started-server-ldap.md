---
title: Authentification LDAP et serveur Azure MFA | Microsoft Docs
description: "Il s&quot;agit de la page d&quot;authentification multifacteur Azure qui facilite le déploiement de l’authentification LDAP et du serveur Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1c6386dda94a3e0ca6eb340f542d04cb336159c3
ms.openlocfilehash: 0de97050e385e3efb9e63bbf934712157ab0d0af


---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Authentification LDAP et serveur Azure Multi-Factor Authentication
Par défaut, le serveur Azure Multi-Factor Authentication est configuré pour importer ou synchroniser des utilisateurs à partir d'Active Directory. Toutefois, il peut être configuré pour être lié à d’autres répertoires LDAP, comme un répertoire ADAM ou un contrôleur de domaine Active Directory spécifique. Lorsqu’il est connecté à un annuaire par LDAP, le serveur Azure Multi-Factor Authentication peut servir de proxy LDAP pour effectuer les authentifications. Il permet également d’utiliser la liaison LDAP comme cible RADIUS, pour pré-authentifier les utilisateurs avec l’authentification IIS ou pour effectuer l’authentification principale dans le portail de l’utilisateur Azure MFA.

Pour utiliser Azure Multi-Factor Authentication comme proxy LDAP, insérez le serveur Azure Multi-Factor Authentication entre le client LDAP (par exemple, un équipement VPN ou une application) et le serveur d’annuaire LDAP. Le serveur Azure Multi-Factor Authentication doit être configuré pour communiquer avec les serveurs des clients et l’annuaire LDAP. Dans cette configuration, le serveur Azure Multi-Factor Authentication accepte les requêtes LDAP provenant des serveurs clients et des applications, puis les transmet au serveur de répertoire LDAP cible pour valider les informations d'identification principales. Si la réponse de l’annuaire LDAP indique que les informations d’identification principales sont correctes, Azure Multi-Factor Authentication effectue une seconde vérification d’identité et renvoie une réponse au client LDAP. L’authentification n’est complète que si l’authentification du serveur LDAP et la vérification en deux étapes aboutissent.

## <a name="ldap-authentication-configuration"></a>Configuration de l'authentification LDAP
Pour configurer l'authentification LDAP, installez le serveur Azure Multi-Factor Authentication sur un serveur Windows. Procédez comme suit :

### <a name="add-an-ldap-client"></a>Ajouter un client LDAP

1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l’icône d’authentification LDAP dans le menu de gauche.
2. Cochez la case **Activer l’authentification LDAP**.

        ![LDAP Authentication](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Dans l’onglet Clients, modifiez le port TCP et le port SSL si le service LDAP Azure Multi-Factor Authentication doit se connecter à des ports non standard pour écouter les requêtes LDAP.
4. Si vous envisagez d'utiliser LDAPS du client au serveur Azure Multi-Factor Authentication, un certificat SSL doit être installé sur le serveur où il est exécuté. Cliquez sur le bouton **Parcourir...** en regard de la zone Certificat SSL et sélectionnez le certificat installé qui sera utilisé pour la connexion sécurisée.
5. Cliquez sur **Ajouter…**.
6. Dans la boîte de dialogue Ajouter un client LDAP, entrez l’adresse IP de l’appliance, du serveur ou de l’application qui s’authentifiera auprès du serveur, et un nom d’application (facultatif). Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile.
7. Cochez la case de **correspondance d’utilisateur Authentification multifacteur Azure requise** si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à la vérification en deux étapes. Si un grand nombre d’utilisateurs n’ont pas encore été importés sur le serveur et/ou ne seront pas soumis à la vérification en deux étapes, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.

Répétez ces étapes pour ajouter d’autres clients LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurer la connexion à l’annuaire LDAP

Lorsque l'authentification multifacteur Azure est configurée pour recevoir des authentifications LDAP, il doit transférer via un proxy ces authentifications au répertoire LDAP. Par conséquent, l'onglet Cible affiche une seule option grisée pour utiliser une cible LDAP.

1. Pour configurer la connexion à l’annuaire LDAP, cliquez sur l’icône **Intégration d’annuaire**.
2. Sous l’onglet Paramètres, sélectionnez le bouton radio **Utiliser une configuration LDAP spécifique**.
3. Sélectionnez **Modifier…**.
4. Dans la boîte de dialogue Modifier la configuration LDAP, remplissez les champs avec les informations requises pour se connecter au répertoire LDAP. Le fichier d’aide du serveur Azure Multi-Factor Authentication fournit une description des champs.

    ![Intégration de répertoires](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Testez la connexion LDAP en cliquant sur le bouton **Test**.
6. Si le test de connexion LDAP a réussi, cliquez sur le bouton **OK**.
7. Cliquez sur l’onglet **Filtres**. Le serveur est préconfiguré pour charger des conteneurs, des groupes de sécurité et des utilisateurs à partir d’Active Directory. En cas de liaison à un autre annuaire LDAP, vous devrez probablement modifier les filtres affichés. Pour plus d’informations sur les filtres, cliquez sur le lien **Aide**.
8. Cliquez sur onglet **Attributes** . Le serveur est préconfiguré pour mapper les attributs à partir d’Active Directory.
9. En cas de liaison à un autre annuaire LDAP ou de modification des mappages d’attributs préconfigurés, cliquez sur **Modifier…**.
10. Dans la boîte de dialogue Modifier les attributs, modifiez les mappages d'attributs LDAP de votre répertoire. Les noms d’attribut peuvent être saisis ou sélectionnés à l’aide du bouton **…** en regard de chaque champ. Pour plus d’informations sur les attributs, cliquez sur le lien **Aide**.
11. Cliquez sur le bouton **OK**.
12. Cliquez sur l’icône **Paramètres de la société** et sélectionnez l’onglet **Résolution du nom d’utilisateur**.
13. Si vous vous connectez à Active Directory à partir d’un serveur joint à un domaine, laissez le bouton radio **Utiliser des identificateurs de sécurité Windows (SID) pour les noms d’utilisateur correspondants** sélectionné. Sinon, sélectionnez le bouton radio **Utiliser un attribut d’identificateur unique LDAP pour les noms d’utilisateur correspondants**. 

Lorsque le bouton radio **Utiliser un attribut d’identificateur unique LDAP pour les noms d’utilisateur correspondants** est sélectionné, le serveur Azure Multi-Factor Authentication tente de résoudre chaque nom d’utilisateur en un identificateur unique dans l’annuaire LDAP. Une recherche LDAP est effectuée sur les attributs Nom d’utilisateur définis dans l’onglet Intégration d’annuaire -> Attributs. Lorsqu’un utilisateur s’authentifie, le nom d’utilisateur est résolu en un identificateur unique dans l’annuaire LDAP, et l’identificateur unique est utilisé pour établir la correspondance avec l’utilisateur dans le fichier de données Azure Multi-Factor Authentication. Cela permet des comparaisons sans prise en compte de la casse et autorise les noms d’utilisateur longs et courts.

Une fois ces opérations terminées, le serveur MFA commence à écouter sur les ports configurés, les demandes d’accès LDAP provenant des clients configurés, et transmet par proxy ces demandes à l’annuaire LDAP à des fins d’authentification.

## <a name="ldap-client-configuration"></a>Configuration du client LDAP
Pour configurer le client LDAP, suivez ces indications :

* Configurez votre appliance, votre serveur ou votre application pour vous authentifier par LDAP auprès du serveur Azure Multi-Factor Authentication, comme s’il s’agissait de votre annuaire LDAP. Utilisez les mêmes paramètres que pour vous connecter directement à votre annuaire LDAP, sauf pour le nom du serveur ou l’adresse IP qui sera celui ou celle du serveur Azure Multi-Factor Authentication.
* Réglez le délai d’expiration LDAP sur 30 à 60 secondes pour pouvoir valider les informations d’identification de l’utilisateur dans l’annuaire LDAP, effectuer la vérification en deux étapes, recevoir leur réponse et répondre à la demande d’accès LDAP.
* Si vous utilisez LDAPS, l’appliance ou le serveur qui effectue les requêtes LDAP doit approuver le certificat SSL installé sur le serveur Azure Multi-Factor Authentication.




<!--HONumber=Feb17_HO3-->


