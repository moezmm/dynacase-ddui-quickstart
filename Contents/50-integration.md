# Intégration d'un document Dynacase Document UIs dans une interface riche {#ddui-qs:18df3951-83c8-4b85-a3b1-3a10c7efc2dd}

Le but est ici de manipuler le document depuis une autre interface.
Il est ainsi possible de présenter des documents dans une interface spécifique,
ainsi que de les manipuler simplement, sans devoir injecter de code dans la famille manipulée.

## Principe {#ddui-qs:8132f4e1-905a-4bb4-b4c8-b59f4cc54660}

Ce tutoriel est livré avec une interface minimaliste de gestion des contacts, accessible à l'adresse
[http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST).

Cette application affiche en colonne de gauche tous les documents de la famille `Contact`.

Pour simplifier le déroulement de ce chapitre, l'application génère déjà des événements et en écoute d'autres,
aussi l'objet de ce chapitre est-il uniquement de manipuler le document lui-même.

![Application de gestion des contacts](50-simple-list.png)

## Mise en œuvre {#ddui-qs:5fe301b0-eabb-4e77-8fc7-3c1c41181ced}

### Chargement d'un document {#ddui-qs:63f77cb2-a6cf-4d32-bc74-3080ad078593}

Lors du clic sur une fiche résumé, l'application déclenche un événement `documentElementClicked` sur la window.
Nous allons écouter cet événement et afficher le document dans la zone `.documentWrapper`.

#### Récupération des sources {#ddui-qs:62e098d5-2992-493a-8453-d531ab51f918}

Les sources avant cette étape correspondent au [tag `step-50-00`][step-50-00].

#### Code {#ddui-qs:3769b0cc-7988-4641-8dd2-94821ddf70b9}

Dans le fichier [`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-10/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété"),
ajoutons un écouteur de l'événement `documentElementClicked`, qui charge le document demandé.

Créons également la méthode `addDocumentListeners`, déclenchée lors de l'initialisation du widget,
et chargée d'[attacher les écouteurs d'événements][ddui-ref_widget-events-listen] au widget.
En particulier, cette méthode écoute l'événement [`ready`][ddui-ref_widget-events-ready] du widget
pour signaler à l'application que ce document est affiché.

    [javascript]
    var loadDocument = function loadDocument(fetchOptions)
        {
            //check if the widget has already been initialised
            if (_.isUndefined($documentWrapper.document("instance"))) {
                //if not, initialize it
                $documentWrapper.document(fetchOptions)
                    //and attach listeners to the newly created widget
                    .on("documentloaded", addDocumentListeners);
            } else {
                //if yes, reuse it
                $documentWrapper.document("fetchDocument", fetchOptions);
            }
        },
        addDocumentListeners = function addDocumentListeners()
        {
            // propagate that this document is opened each time it is reloaded
            $documentWrapper.document(
                "addEventListener",
                "ready",
                {
                    "name": "ready.simple_list"
                },
                function simpleList_propagateReady(event, documentObject)
                {
                    // refresh the currently selected document
                    $window.trigger('documentOpened', $documentWrapper.document("getProperties"));
                }
            );
        };
    
    $window.on('documentElementClicked', function onDocumentElementClicked(event, options)
    {
        var fetchOptions = {
            "initid": options.initid,
            "viewId": "!defaultConsultation"
        };
        loadDocument(fetchOptions)
    });

#### Déploiement {#ddui-qs:53810925-0ca9-4247-832e-240de38546c8}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-10`][step-50-10].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:83947516-76f8-4a36-be48-b4c7b3e2080b}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
un clic sur un document de la liste de gauche l'ouvre dans la zone de droite.

### Écoute des modifications du document {#ddui-qs:175403b2-5851-4893-82dc-932db474f6b7}

Nous allons réagir à l'événement [`change`][ddui-ref_widget-events-change] du widget.

#### Récupération des sources {#ddui-qs:ad0319c7-49c1-4eff-bc7a-d62fdeb576c6}

Les sources avant cette étape correspondent au [tag `step-50-10`][step-50-10].

#### Code {#ddui-qs:c72d0097-d1f3-4587-9fc7-049c2c2e979f}

Enrichissons la méthode `addDocumentListeners` du fichier
[`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-20/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété")
en écoutant l'événement [`change`][ddui-ref_widget-events-change] du widget
pour signaler à l'application que ce document est modifié :

    [javascript]
    // propagate that this document has changed
    $documentWrapper.document(
        "addEventListener",
        "change",
        {
            "name": "change.simple_list"
        },
        function simpleList_propagateChange(event, documentObject)
        {
            $window.trigger('documentChanged', documentObject);
        }
    );

<span class="flag inline nota-bene"></span> Ici, nous nous contentons de déclencher l'événement en charge de la mise à
jour de l'application. Le comportement de l'application étant en dehors du scope de ce tutoriel, son contenu n'est pas
présenté. Il est néanmoins accessible dans le code source dans le fichier
[`SIMPLE_LIST/Layout/main.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-00-00/SIMPLE_LIST/Layout/main.js "voir le fichier sur github").

#### Déploiement {#ddui-qs:dc86d2e9-bf2f-4ef0-8205-5e07228efbc0}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-20`][step-50-20].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:8dc1348b-2b38-4590-bab0-2374d94e47b6}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
lors de la modification d'un document ouvert, le statut _modifié_ (matérialisé par une astérisque jaune)
est reporté dans la liste de gauche.

### Écoute de la sauvegarde d'un document {#ddui-qs:590535e8-6bfa-4d9e-8790-7706818357c6}

Nous allons réagir à l'événement [`afterSave`][ddui-ref_widget-events-afterSave] du widget.

#### Récupération des sources {#ddui-qs:a02ceeef-5d48-462c-be16-b4a6f7f23acf}

Les sources avant cette étape correspondent au [tag `step-50-20`][step-50-20].

#### Code {#ddui-qs:c540c97e-9ffb-41df-997c-65d4af0f0aad}

Enrichissons la méthode `addDocumentListeners` du fichier
[`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-30/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété")
en écoutant l'événement [`afterSave`][ddui-ref_widget-events-afterSave] du widget
pour signaler à l'application que ce document a été sauvegardé :

    [javascript]
    // propagate that this document has been saved
    $documentWrapper.document(
        "addEventListener",
        "afterSave",
        {
            "name": "afterSave.simple_list"
        },
        function simpleList_propagateAfterSave(event, documentObject)
        {
            $window.trigger('documentSaved', documentObject);
        }
    );

<span class="flag inline nota-bene"></span> Ici, nous nous contentons de déclencher l'événement en charge de la mise à
jour de l'application. Le comportement de l'application étant en dehors du scope de ce tutoriel, son contenu n'est pas
présenté. Il est néanmoins accessible dans le code source dans le fichier
[`SIMPLE_LIST/Layout/main.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-00-00/SIMPLE_LIST/Layout/main.js "voir le fichier sur github").

#### Déploiement {#ddui-qs:65a3f452-ee38-4441-bc41-cd622f51e62e}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-30`][step-50-30].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:e901ef80-51c9-4437-950e-9a30fb0fb26c}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
lors de la modification du nom ou du prénom d'un contact, le nouveau titre est reporté dans la liste de gauche
après la sauvegarde du document.

### Affichage de l'interface de création {#ddui-qs:d780c000-fd78-4ab2-96f0-603826eafe3d}

Lors du clic sur le bouton `Nouveau Contact`, l'application déclenche un événement `buttonCreateClicked`.
Écoutons cet événement et affichons l'interface de création dans la zone `.documentWrapper`.

#### Récupération des sources {#ddui-qs:ec65e108-b97e-4520-9f3f-43fe9e74f7c1}

Les sources avant cette étape correspondent au [tag `step-50-30`][step-50-30].

#### Code {#ddui-qs:b9bef7f7-df14-4599-9f6e-17e16bd1a3b2}

Dans le fichier [`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-40/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété"),
ajoutons un écouteur de l'événement `buttonCreateClicked`, qui charge l'interface de création de la famille demandée :

    [javascript]
    $window.on('buttonCreateClicked', function onButtonCreateClicked(event, options)
    {
        var fetchOptions = {
            "initid": options.famid,
            "viewId": "!defaultCreation"
        };
        loadDocument(fetchOptions);
    });

#### Déploiement {#ddui-qs:83501cab-9222-47d7-87e2-66428c7d72a2}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-40`][step-50-40].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:f9f07451-eed4-4c53-9a7e-915523b7ab0e}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
un clic sur le bouton Nouveau Contact ouvre l'interface de création.

### Écoute de la création d'un document {#ddui-qs:61f5e6ee-a2ab-4c9e-a1ea-63b739804b91}

Il n'existe pas d'événement spécifique à la création d'un document.
Pour détecter une création, nous allons écouter l'événement [`afterSave`][ddui-ref_widget-events-afterSave] du widget,
et signaler une création à l'interface dans le cas où le document n'avait pas d'`initid`.

#### Récupération des sources {#ddui-qs:983ae717-149b-4ffe-80c7-9f61764a5b2b}

Les sources avant cette étape correspondent au [tag `step-50-40`][step-50-40].

#### Code {#ddui-qs:31efccb7-3fd3-4fef-92e0-ef8dcacc4bdd}

Modifions l'écouteur de l'événement [`afterSave`][ddui-ref_widget-events-afterSave]
dans la méthode `addDocumentListeners` du fichier
[`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-50/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété") :

    [javascript]
    // propagate that this document has been saved
    $documentWrapper.document(
        "addEventListener",
        "afterSave",
        {
            "name": "afterSave.simple_list"
        },
        function simpleList_propagateAfterSave(event, currentDocumentObject, previousDocumentObject)
        {
            if(previousDocumentObject.initid === 0) {
                // if previous document had no initid, it is a creation
                $window.trigger('documentCreated', currentDocumentObject);
            } else {
                // otherwise, it is a simple save
                $window.trigger('documentSaved', currentDocumentObject);
            }
        }
    );

#### Déploiement {#ddui-qs:222252c9-0754-4705-a83e-582bb7edf86b}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-50`][step-50-50].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:86b51b57-6b45-422e-96fb-55174193a926}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
lors de la création d'un contact, le nouveau contact est ajouté à la liste de gauche.

### Écoute de la suppression d'un document {#ddui-qs:1611579a-0e27-42b8-b39f-82449bd4eea7}

Nous allons réagir à l'événement [`afterDelete`][ddui-ref_widget-events-afterDelete] du widget.

#### Récupération des sources {#ddui-qs:155d7e4d-93c3-4aff-9430-7549a0453636}

Les sources avant cette étape correspondent au [tag `step-50-50`][step-50-50].

#### Code {#ddui-qs:bb39bcd9-ca27-4484-85b6-bb3983e31ae6}

Enrichissons la méthode `addDocumentListeners` du fichier
[`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-60/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété")
en écoutant l'événement [`afterDelete`][ddui-ref_widget-events-afterDelete] du widget
pour signaler à l'application que ce document a été supprimé:

    [javascript]
    // propagate that this document has been deleted
    $documentWrapper.document(
        "addEventListener",
        "afterDelete",
        {
            "name": "afterDelete.simple_list"
        },
        function simpleList_propagateAfterDelete(event, documentObject)
        {
            $window.trigger('documentDeleted', documentObject);
        }
    );

<span class="flag inline nota-bene"></span> Ici, nous nous contentons de déclencher l'événement en charge de la mise à
jour de l'application. Le comportement de l'application étant en dehors du scope de ce tutoriel, son contenu n'est pas
présenté. Il est néanmoins accessible dans le code source dans le fichier
[`SIMPLE_LIST/Layout/main.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-00-00/SIMPLE_LIST/Layout/main.js "voir le fichier sur github").

#### Déploiement {#ddui-qs:ba8ce5db-22f2-4585-95ac-6c9b160c1f80}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-60`][step-50-60].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:cf2b391c-f421-4f1f-9ca4-549e991bd4d5}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
lors de la suppression d'un contact, sa fiche est supprimée de la liste de gauche.

### Écoute de la restauration d'un document {#ddui-qs:4b16b252-2abf-4e52-9d02-27604506cc76}

Nous allons réagir à l'événement [`afterRestore`][ddui-ref_widget-events-afterRestore] du widget.

#### Récupération des sources {#ddui-qs:45e1cdee-cc73-420c-9cfd-19dec2de0a25}

Les sources avant cette étape correspondent au [tag `step-50-60`][step-50-60].

#### Code {#ddui-qs:2e8d05fb-e6ca-4bc8-ada7-d1f2d9cdd004}

Enrichissons la méthode `addDocumentListeners` du fichier
[`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-70/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété")
en écoutant l'événement [`afterRestore`][ddui-ref_widget-events-afterRestore] du widget
pour signaler à l'application que ce document a été restauré :

    [javascript]
    // propagate that this document has been restored
    $documentWrapper.document(
        "addEventListener",
        "afterRestore",
        {
            "name": "afterRestore.simple_list"
        },
        function simpleList_propagateAfterRestore(event, documentObject)
        {
            $window.trigger('documentRestored', documentObject);
        }
    )

<span class="flag inline nota-bene"></span> Ici, nous nous contentons de déclencher l'événement en charge de la mise à
jour de l'application. Le comportement de l'application étant en dehors du scope de ce tutoriel, son contenu n'est pas
présenté. Il est néanmoins accessible dans le code source dans le fichier
[`SIMPLE_LIST/Layout/main.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-00-00/SIMPLE_LIST/Layout/main.js "voir le fichier sur github").

#### Déploiement {#ddui-qs:a5720ea9-da15-40d6-8a31-87d8b3ac5146}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-70`][step-50-70].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:a69b4398-5980-4f23-8568-b0fdc8187057}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
immédiatement après la suppression d'un contact et que sa fiche ait été supprimée de la liste de gauche,
il est possible de le restaurer. Sa fiche est alors ajoutée à nouveau à la liste de gauche.

### Demander confirmation avant la perte d'informations non enregistrées {#ddui-qs:775b7c84-54dc-469b-89bd-cda4524eac0f}

Lors de l'affichage d'un nouveau document, les modifications non enregistrées du document affiché sont perdues.
Nous allons demander une confirmation à l'utilisateur avant la perte de ces modifications.

#### Récupération des sources {#ddui-qs:14f573c9-6d29-4603-beaa-cf7acc6f7687}

Les sources avant cette étape correspondent au [tag `step-50-70`][step-50-70].

#### Code {#ddui-qs:0d08e1c2-a4d3-4fe4-ab30-ced599b9dc53}

Mettons à jour la méthode `loadDocument` du fichier
[`SIMPLE_LIST/Layout/custom.js`](https://github.com/Anakeen/dynacase-ddui-quickstart-code/blob/step-50-80/SIMPLE_LIST/Layout/custom.js "Télécharger le fichier complété")
pour demander une confirmation lorsque le document affiché est modifié :

    [javascript]
    var loadDocument = function loadDocument(fetchOptions)
    {
        //check if the widget has already been initialised
        if (_.isUndefined($documentWrapper.document("instance"))) {
            //if not, initialize it
            $documentWrapper.document(fetchOptions)
                //and attach listeners to the newly created widget
                .on("documentloaded", addDocumentListeners);
        } else {
            //if yes, reuse it
            var properties = $documentWrapper.document("getProperties");
    
            if (properties.isModified) {
                if (!confirm(
                        'Le document ' + properties.title + ' a des modifications non sauvegardées.' +
                        'Voulez-vous continuer et perdre ces modifications ?'
                    )) {
                    return;
                }
            }
            $documentWrapper.document("fetchDocument", fetchOptions);
        }
    }

#### Déploiement {#ddui-qs:29f166df-86df-475e-a989-df2115973fc3}

Les sources telles que déployées à cette étape correspondent au [tag `step-50-80`][step-50-80].

Le déploiement se fait au moyen du _developer toolkit_
(pour plus d'explications sur les outils de développement, se rendre sur [leur documentation][devtools-ref]).

La commande est donc :

-   pour linux :
    
        php dynacase-devtool.phar deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

-   pour windows :
    
        dynacase-devtool.bat deploy -u localhost -p 8080 -c dynacase -s path/to/sources --auto-release

#### Le résultat {#ddui-qs:ed8af3ef-9b9a-453f-8141-c446f7a6c132}

Depuis l'application à l'adresse [http://localhost:8080/?app=SIMPLE_LIST](http://localhost:8080/?app=SIMPLE_LIST),
lorsqu'on demande l'affichage d'un document alors que le document en cours est modifié,
un message de confirmation est affiché.

<!-- links -->
[step-50-00]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-00.zip
[step-50-10]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-10.zip
[step-50-20]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-20.zip
[step-50-30]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-30.zip
[step-50-40]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-40.zip
[step-50-50]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-50.zip
[step-50-60]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-60.zip
[step-50-70]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-70.zip
[step-50-80]:                           https://github.com/Anakeen/dynacase-ddui-quickstart-code/archive/step-50-80.zip
[devtools-ref]:                         #devtools:
[ddui-ref_widget-events-listen]:        #ddui-ref:e04afb6f-d710-41c6-98be-1d35979ac898
[ddui-ref_widget-events-change]:        #ddui-ref:2405df1f-9fd0-4d42-b862-f3d5657f485d
[ddui-ref_widget-events-afterRestore]:  #ddui-ref:8c0bed5f-8865-483a-8211-ee9ee80bd7ff
[ddui-ref_widget-events-afterSave]:     #ddui-ref:968798c4-0d6e-436e-bb83-79c258eb8e4d
[ddui-ref_widget-events-ready]:         #ddui-ref:ec92745b-26e0-46a5-91cc-9851ef66c30a
[ddui-ref_widget-events-afterDelete]:   #ddui-ref:0c4d7fea-c4ab-4802-b757-17e2fb268795
