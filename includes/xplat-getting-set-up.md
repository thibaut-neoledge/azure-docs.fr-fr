
## Utilisation de l’interface de ligne de commande Azure

Les étapes suivantes vous aident à utiliser facilement l’interface de ligne de commande Azure avec la version la plus récente et l’abonnement approprié. Si vous devez installer l’interface de ligne de commande Azure et la connecter à votre compte, consultez la page [Interface de ligne de commande Azure](xplat-cli.md).

### Étape 1 : mise à jour de l’interface de ligne de commande Azure

Pour utiliser l’interface de ligne de commande Azure avec le mode de gestion des services, vous devez avoir une version récente. Pour vérifier votre version, tapez `azure --version`. Le résultat suivant doit s’afficher :

    $ azure --version
    0.8.17 (node: 0.10.25)

Si vous souhaitez mettre à jour votre version de l’interface de ligne de commande, consultez la page [Interface de ligne de commande](https://github.com/Azure/azure-xplat-cli).

### Étape 2 : configuration de votre compte et de votre abonnement Azure

Une fois que vous avez connecté votre interface de ligne de commande Azure au compte que vous souhaitez utiliser, vous pouvez avoir plusieurs abonnements. Le cas échéant, vous devez examiner les abonnements disponibles pour votre compte en tapant `azure account list`, puis en sélectionnant l’abonnement que vous souhaitez utiliser en tapant `azure account set <subscription id or name> true` où _id ou nom d’abonnement_ est l’id d’abonnement ou le nom d’abonnement que vous souhaitez utiliser dans la session active. Le résultat suivant pourrait s'afficher :

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE]Si vous ne disposez pas d'un compte Azure, mais d'un abonnement MSDN, vous pouvez obtenir des crédits Azure gratuits en activant vos [avantages d'abonné MSDN ici](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ; ou vous pouvez utiliser le compte gratuit. Les deux possibilités permettent d’accéder à Azure.

<!---HONumber=July15_HO4-->