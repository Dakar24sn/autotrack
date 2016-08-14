# `cleanUrlTracker`

Ce guide explique ce qu'est le plugin `cleanUrlTracker` et comment il interagit avec votre implémentation du suivi `analytics.js`.

## Aperçu

Lorsque vous regardez la plupart des pages visitées dans Google Analytics, il n'est pas rare de voir différents chemins d'URL qui référencent la même page sur votre site. Le tableau ci-dessous est un exemple de cela et de l'état de frustration dans lequel se trouvent actuellement beaucoup d'utilisateurs :

<table>
  <tr valign="top">
    <th align="left">Page</th>
    <th align="left">Pages vues</th>
  </tr>
  <tr valign="top">
    <td>/contact</td>
    <td>967</td>
  </tr>
  <tr valign="top">
    <td>/contact/</td>
    <td>431</td>
  </tr>
  <tr valign="top">
    <td>/contact?hl=fr</td>
    <td>67</td>
  </tr>
  <tr valign="top">
    <td>/contact/index.html</td>
    <td>32</td>
  </tr>
</table>

Pour éviter ce problème, il est mieux de se décider pour un chemin d'URL canonique unique pour chaque page que vous souhaitez suivre et de n'envoyer toujours que la version canonique à Google Analytics.

Le plugin `cleanUrlTracker` vous aide à faire ça. Il vous laisse définir votre préférence, si oui ou non il faut inclure les parties superflues du chemin d'URL et met à jour toutes les URLs en conséquence.

### Comment ça marche

Le plugin `cleanUrlTracker` fonctionne en interceptant chaque hit lorsqu'il est envoyé et modifie le champ [`page`](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference#page) en fonction des règles spécifiées dans les [options](#options) de configuration.

S'il le champ `page` n'existe pas, il est crée en se basant sur le chemin d'URL du champ [`location`](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference#location)

**Note :** Bien que le plugin `cleanUrlTracker` modifie la valeur du champ `page` pour chaque hit, il ne modifie jamais le champ `location`. Cela permet de préserver les données encodées dans l'URL entière pour les campagnes et la recherche sur le site.

## Utilisation

Pour activer le plugin `cleanUrlTracker` lancez la commande [`require`](https://developers.google.com/analytics/devguides/collection/analyticsjs/using-plugins), précisez le nom du plugin `'cleanUrlTracker'` et passez les options de configuration que vous souhaitez définir :

```js
ga('require', 'cleanUrlTracker', options);
```

## Options

Le tableau ci-dessous résume toutes les options de configuration possibles pour le plugin `cleanUrlTracker`. Si une de ces options a une valeur par défaut, elle est mentionné explicitement :

<table>
  <tr valign="top">
    <th align="left">Nom</th>
    <th align="left">Type</th>
    <th align="left">Valeur par défaut</th>
  </tr>
  <tr valign="top">
    <td><code>stripQuery</code></a></td>
    <td><code>booléen</code></a></td>
    <td>
      À <code>true</code>, la chaine de caractère de la requête de l'URL sera supprimée.<br>
      <strong>Par défaut :</strong> <code>false</code>
    </td>
  </tr>
  <tr valign="top">
    <td><code>queryDimensionIndex</code></a></td>
    <td><code>chiffre</code></a></td>
    <td>
    Il y a des cas où vous souhaitez enlever la chaîne de la requête de l'URL, mais vous voulez toujours enregistrer la chaine présente à l'origine. Vous pouvez faire ça en créant une <a href="https://support.google.com/analytics/answer/2709829">dimension personnalisée</a> dans Google Analytics. Mettez le scope de la dimension à "hit" et ensuite définissez l'index de la dimension nouvellement créée en tant qu’option <code>queryDimensionIndex</code>. Une fois définie, la chaîne supprimée de la requête sera définie pour la dimension personnalisée à l’index spécifié.
    </td>
  </tr>
  <tr valign="top">
    <td><code>indexFilename</code></a></td>
    <td><code>chaîne</code></a></td>
    <td>
      Lorsqu'elle est définie, la valeur <code>indexFilename</code> sera supprimée de la fin d'une URL. Si votre serveur permet de servir automatiquement les fichiers d'index, vous devriez définir cette valeur à celle utilisée par votre serveur (habituellement <code>'index.html'</code>).
    </td>
  </tr>
  <tr valign="top">
    <td><code>trailingSlash</code></a></td>
    <td><code>chaîne</code></a></td>
    <td>
      Définie à <code>'add'</code>, un slash final est ajouté à la fin de toutes les URLs (s'il n'est pas déjà présent). Définie à <code>'remove'</code>, un slash final est supprimé de la fin de toutes les URLs. Aucun action n'est prise si une autre valeur est utilisée. Note : Lors du recours à l'option <code>indexFilename</code>, les noms de fichier d'index sont nettoyés avant que le slash final ne soit ajouté ou supprimé.
    </td>
  </tr>
</table>

## Méthodes

Le tableau ci-dessous liste toutes les méthodes pour le plugin `cleanUrlTracker`:

<table>
  <tr valign="top">
    <th align="left">Nom</th>
    <th align="left">Description</th>
  </tr>
  <tr valign="top">
    <td><code>remove</code></a></td>
    <td>Supprime le plugin <code>cleanUrlTracker</code> du tracker spécifié et restaure toutes les tâches modifiées à leur état d'origine avant que le plugin ne soit requis.</td>
  </tr>
</table>

Pour savoir comment fonctionnent les méthodes des plugins `analytics.js` et comment les appeler, regardez comment [appeler des méthodes de plugin](https://developers.google.com/analytics/devguides/collection/analyticsjs/using-plugins#calling_plugin_methods) dans la documentation d'`analytics.js`.

## Exemple

Pour les quatre chemins d'URL montrés dans le tableau au début de ce guide, la configuration `cleanUrlTracker` suivante assurera que seul le chemin d'URL `/contact` apparaisse dans vos rapports (en supposant que vous avez créé une dimension personnalisée pour la requête à l'index 1):

```js
ga('require', 'cleanUrlTracker', {
  stripQuery: true,
  queryDimensionIndex: 1,
  indexFilename: 'index.html',
  trailingSlash: 'remove'
});
```

Et pour ces quatre URLs, les champs suivants seront envoyés pour chaque hit respectif :

```
[1] {
      "location": "/contact",
      "page": "/contact"
    }

[2] {
      "location": "/contact/",
      "page": "/contact"
    }

[3] {
      "location": "/contact?hl=en",
      "page": "/contact"
      "dimension1": "hl=en"
    }

[4] {
      "location": "/contact/index.html",
      "page": "/contact"
    }
```
