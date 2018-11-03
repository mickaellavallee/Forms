***********
Formulaires
***********

On va mettre en place un formulaire (`<https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms>`_ ) dans la page de contact.

Mise en place
============= 

Form
^^^^ 


Un formulaire tout simple, :file:`commentaires.ejs` :

.. code-block:: html

    <html>

    <head>
        <meta charset="utf-8" />
        <title>Commentaires</title>
    </head>

    <body>
        <form>
            Pseudo :
            <input id="pseudo" type="text" name="pseudo">
            
            <textarea placeholder="dites nous tout le bonheur que vous apporte ce site" name="comment"></textarea>
            <button type="submit">Envoi</button>            
        </form>
    </body>

    </html>

Voyez comment sont envoyées les données (attribut "name" suivi de la valeur). Mais avant ça, rendons le tout plus joli.


Materialize
^^^^^^^^^^^ 

On utilise MaterializeCSS pour que ce soit plus joli : `<http://materializecss.com/forms.html>`_ 


:file:`commentaires.ejs` :


.. code-block:: html

    <html>

    <head>
        <meta charset="utf-8" />
        <title>Commentaires</title>

        <% include partials/head_css_import.ejs %>

        <style>
            html, body {
                font-size: 1em;
            }
        </style>

    </head>

    <body>

        <% include partials/navbar.ejs %>

        <div class="row">
            <form class="col s12">
                <div class="row">
                    <div class="col s12">
                        Pseudo :
                        <div class="input-field inline">
                            <input id="pseudo" type="text" name="pseudo" />
                            <label for="pseudo">pseudo</label>
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="input-field col s12">
                        <textarea id="comment" class="materialize-textarea" placeholder="dites nous tout le bonheur que vous apporte ce site" name="comment"></textarea>
                        <label for="comment">Petit compliment :</label>
                    </div>
                </div>
                <button class="btn waves-effect waves-light" type="submit">
                    <i class="material-icons right">send</i>
                    Envoi
                </button>
            </form>
        </div>

        <% include partials/js_import.ejs %>

    </body>

    </html>


Query Strings
^^^^^^^^^^^^^ 

Lorsque l'on clique sur le bouton, pour l'instant on envoie une requête GET (cf. les headers) avec les valeurs passées en argument de l'URL. Par exemple : http://localhost:8080/commentaires?pseudo=caro&comment=Trop+cool+ton+site+%28lol%29

On appelle ça des *query string parameters* : `<https://en.wikipedia.org/wiki/Query_string>`_. Le format est assez simple et permet de faire passer des variables dans une url.

Modifion un peu :file:`app.js` pour voir ce qu'il se passe.

.. code-block:: js

    app.get('/commentaires', (request, response) => {
        logger.info(JSON.stringify(request.query))
        response.render("commentaires")
    })



Templating
==========  

On va utiliser ces query strings dans notre template. Commençons par passer les query strings en paramètres de notre template : 

:file:`app.js` :

.. code-block:: js

    app.get('/commentaires', (request, response) => {
        response.render("commentaires", {qs: request.query})
    })


L'objet qs est passé en paramètre de notre template et prend la valeur de notre query string. Dans notre cas, il a donc 2 champs correspondant aux noms de nos formulaires, à savoir :code:`qs.pseudo` et :code:`qs.comment`.

Modifions le template pour les utiliser. On va tout de même faire attention au fait que ces paramètres peuvent être vides. 


.. code-block:: text

    <html>

    <head>
        <meta charset="utf-8" />
        <title>Commentaires</title>

        <% include partials/head_css_import.ejs %>

            <style>
                html,
                body {
                    font-size: 1em;
                }
            </style>

    </head>

    <body>

        <% include partials/navbar.ejs %>

            <div class="row">
                <form class="col s12">
                    <div class="row">
                        <div class="col s12">
                            Pseudo :
                            <div class="input-field inline">
                                <input id="pseudo" type="text" 
                                    name="pseudo"
                                    <% if (qs.pseudo) { %>
                                        value=<%= qs.pseudo %>
                                <% } %>
                            />
                                <label for="pseudo">pseudo</label>
                            </div>
                        </div>
                    </div>
                    <div class="row">
                        <div class="input-field col s12">
                            <textarea id="comment" class="materialize-textarea" placeholder="dites nous tout le bonheur que vous apporte ce site" name="comment"><% if (qs.comment) { %><%= qs.comment %><% } %></textarea>
                            <label for="comment">Petit compliment :</label>
                        </div>
                    </div>
                    <button class="btn waves-effect waves-light" type="submit">
                        <i class="material-icons right">send</i>
                        Envoi
                    </button>
                </form>
            </div>

            <% include partials/js_import.ejs %>

    </body>

    </html>

    
.. note :: Notez comment le code HTML est imbriqué dans le code EJS. C'est un peu sale, on va donc essayer de le faire le moins possible. Pour la zone de texte, on est ainsi obligé de tout mettre sur une seule ligne, sinon les retours chariot sont comptés comme une réponse.

Et avec Bulma ?
===============

On peut également utiliser Bulma qui est très complet : `<https://bulma.io/documentation/form>`_

Installation/configuration de Bulma
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Une fois dans le répertoire de travail et dans assets (:code:`cd`)


.. code-block:: os

	npm install bulma 
	
Bulma est installé. Reste à l'initialiser dans le fichier html. Pour cela, il faut rajouter dans le :code:`header` la ligne suivante :

.. code-block:: html

	<link rel="stylesheet" href="static/node_modules/bulma/css/bulma.min.css">

	
Exemple de formulaire avec Bulma
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

L'exemple d'un champs basique est le suivant :

.. code-block:: html

	<div class="field">
	  <label class="label">Nom du champs (Pseudo, Nom, Prénom, etc.)</label>
	  <div class="control">
	    <input class="input" type="text" placeholder="Texte mis en indication dans le champs à remplir ('input')">
	  </div>
	  <p class="help">"Help text" pour éventuellement donner des indications</p>
	</div>

Observez bien cet exemple et essayez de jouer avec les différentes possibilités pour bien comprendre. Par exemple, dans la balise :code:`<input ...>` le type peut être changé en fonction de ce que vous voulez que l'utilisateur entre (principalement :code:`text`, :code:`email` et :code:`url`)

Template de formulaire complet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Maintenant que nous avons les bases, voici ce que nous proposons pour le code html du formulaire. Les principaux types de chanmps sont utilisés ce code, libre à vous d'observer comment cela fonctionne — notammeent à l'aide de la documentation. Cependant, ce fichier ne récupère pas encore les informations envoyées, il faudra passer par du php pour extraire et traiter les informations (comme précedemment).

.. code-block:: html

	<html>
	<head>
	    <% include partials/head_css_import.ejs %>
	    <meta charset="utf-8" />
	    <title>Commentaires</title>
	    <link rel="stylesheet" href="/static/node_modules/bulma/css/bulma.min.css">
	</head>
	<body>

	<% include partials/navbar.ejs %>

	<div class="row">
	<form id="myForm" class="col s12">
		<div class="field is-horizontal">
		  <div class="field-label is-normal">
		    <label class="label">De</label>
		  </div>
		  <div class="field-body">
		    <div class="field">
		      <p class="control is-expanded">
			<input class="input" type="text" placeholder="Ton petit nom" required="required">
		      </p>
		    </div>
			<div class="field has-addons has-icons-right">
			  <p class="control">
			    <input class="input" type="text" placeholder="prenom.nom">
				<!-- mettre type="email" si vous faites un champs email classique -->
			  </p>
			  <p class="control">
			    <a class="button is-static">
			      @
			    </a>
			</p>
			<div class="select is-fullwidth">
			  <select>
			    <option>centrale-marseille.fr</option>
			    <option>gmail.com</option>
			    <option>outlook.com</option>
					<option>hotmail.fr</option>
					<option>aucun-donc-je-ne-peux-pas-vous-contacter.fr</option>
			  </select>
			</div>
			</div>
		  </div>
		</div>

		<div class="field is-horizontal">
		  <div class="field-label"></div>
		  <div class="field-body">
		    <div class="field is-expanded">
		      <div class="field has-addons">
			<p class="control">
			  <a class="button is-static">
			    +33
			  </a>
			</p>
			<p class="control is-expanded">
			  <input class="input" type="tel" placeholder="Numéro de téléphone">
			</p>
		      </div>
		      <p class="help">Du coup, n'écrivez pas le premier "0"</p>
		    </div>
		  </div>
		</div>

		<div class="field is-horizontal">
		  <div class="field-label">
		    <label class="label">Es-tu un 
				ninja ?</label>
		  </div>
		  <div class="field-body">
		    <div class="field is-narrow">
		      <div class="control">
			<label class="radio">
			  <input type="radio" name="member">
			  Oui
			</label>
			<label class="radio">
			  <input type="radio" name="member">
			  Non
			</label>
		      </div>
		    </div>
		  </div>
		</div>

		<div class="field is-horizontal">
		  <div class="field-label is-normal">
		    <label class="label">Objet</label>
		  </div>
		  <div class="field-body">
		    <div class="field">
		      <div class="control">
			<input class="input is-required" type="text" placeholder='Par exemple : Spam via formulaire de contact'>
		      </div>
		      <p class="help is-required">
			Ce champs est obligatoire
		      </p>
		    </div>
		  </div>
		</div>

		<div class="field is-horizontal">
		  <div class="field-label is-normal">
		    <label class="label">Question</label>
		  </div>
		  <div class="field-body">
		    <div class="field">
		      <div class="control">
			<textarea class="textarea" placeholder="Comment puis-je vous aider ?"></textarea>
		      </div>
		    </div>
		  </div>
		</div>

		<div class="field is-horizontal">
		  <div class="field-label">

		  </div>
		  <div class="field-body">
		    <div class="field">
		      <div class="control">
			<button class="button is-primary" type="submit">
			  Envoyer le formulaire
			</button>
		      </div>
		    </div>
		  </div>
		</div>
	</form>

	<% include partials/js_import.ejs %>
	</body>
	</html>
	
JavaScript
^^^^^^^^^^
