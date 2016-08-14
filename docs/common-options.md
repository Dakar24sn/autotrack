# Options communes

Beaucoup des plugins d'autotrack acceptent des options communes à plusieurs plugins différents. Les options communes qui suivent sont documentées dans ce guide :

- [`fieldsObj`](#fieldsobj)
- [`attributePrefix`](#attributeprefix)
- [`hitFilter`](#hitfilter)

## `fieldsObj`

Certains des plugins autotrack envoient des hits avec les [valeurs du champ analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference) définies par défaut. Ces plugins acception une option `fieldsObj`, qui vous permet de personnaliser ces valeurs pour chaque plugin. Cela vous permet également de définir les champs qui ne sont pas définis par défaut.

L’option `fieldsObj` est un `Object` dont les propriétés peuvent être un [nom de champ analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference) et dont les valeurs seront utilisées comme la valeur correspondante par défaut pour tous les hits envoyés par le plugin.

### Exemples

#### `mediaQueryTracker`

Cette configuration garantie que tous les évènements envoyés par le plugin `mediaQueryTracker` plugin sont des évènements non-interactifs :

```js
ga('require', 'mediaQueryTracker', {
  definitions: [...],
  fieldsObj: {
    nonInteraction: true
  }
});
```

#### `urlChangeTracker`

Cette configuration définit une [dimension personnalisée](https://support.google.com/analytics/answer/2709828) à l’index 1 pour tous les hits de pageview envoyé par l’`urlChangeTracker`. Cela peut vous permettre de différencier des pageview d’origine de pageview « virtuels » envoyés après le chargement de nouvelles pages en AJAX:

```js
ga('require', 'urlChangeTracker', {
  fieldsObj: {
    dimension1: 'virtual'
  }
});
ga('send', 'pageview', {
  dimension1: 'pageload'
});
```

## `attributePrefix`

Tous les plugins qui envoient des hits à Google Analytics à la suite d’interactions utilisateurs avec des éléments du DOM supportent l’ajout d’attribut déclaratif (Voir le plugin [`eventTracker`](/docs/plugins/event-tracker.md)  pour voir comment il fonctionne). À ce titre, chacun de ces plugins acceptent une option `attributePrefix` option pour personnaliser le préfixe d’attribut à utiliser.

Par défaut, la valeur `attributePrefix` utilisée par chaque plugin est la chaîne `'ga-'`, bien que cette valeur puisse être personnalisée au niveau de chaque plugin.

**Remarque :** Quand le même champ est défini et dans l’option `fieldsObj` et en tant qu’attribut d’élément du DOM, la valeur de l’attribut écrasera la valeur de `fieldsObj`.

### Exemples

#### `eventTracker`

```js
ga('require', 'eventTracker', {
  attributePrefix: 'data-'
});
```

```html
<button
  data-on="click"
  data-event-category="Video"
  data-event-action="play">
  Play video
</button>
```

#### `impressionTracker`

```js
ga('require', 'impressionTracker', {
  elements: ['cta'],
  attributePrefix: 'data-ga'
});
```

```html
<div
  id="cta"
  data-ga-event-category="Call to action"
  data-ga-event-action="seen">
  Call to action
</a>
```

#### `outboundLinkTracker`

```js
ga('require', 'outboundLinkTracker', {
  attributePrefix: ''
});
```

```html
<a href="https://example.com" event-category="External Link">Click</a>
```

## `hitFilter`

L’option `hitFilter` est utile quand vous avez besoin de faire des modifications plus avancées à un hit ou que vous avez besoin de faire complètement échouer un hit.

`hitFilter` est une fonction qui est appelée avec l’[objet modèle](https://developers.google.com/analytics/devguides/collection/analyticsjs/model-object-reference)  du tracker en tant que premier argument et (si le hit été initié par un interaction utilisateur avec un élément du DOM) l’élément du DOM comme second argument.

À l’intérieur de la fonction `hitFilter` vous pouvez récupérer la valeur de n’importe quel champ du modèle d’objet en utilisant la méthode [`get`](https://developers.google.com/analytics/devguides/collection/analyticsjs/model-object-reference#get) sur l’argument `model`. Et vous pouvez définir une nouvelle valeur en utilisant la méthode [`set`](https://developers.google.com/analytics/devguides/collection/analyticsjs/model-object-reference#set) sur l’argument `model`. Pour abandonner le hit, levez une erreur.

Pour modifier le modèle pour le hit courant seulement (et pas pour les hits suivants) assurez vous de définir le troisième argument ([`temporary`](https://developers.google.com/analytics/devguides/collection/analyticsjs/model-object-reference#set)) à `true`.

### Comment ça marche

L’option `hitFilter` fonctionne en annulant le  [`buildHitTask`](https://developers.google.com/analytics/devguides/collection/analyticsjs/tasks) du tracker. La fonction passée `hitFilter` se lance après que les valeurs et les attributes `fieldsObj` aient été définies sur le tracker mais avant de lancer le `buildHitTask` initial. Référez vous au guide sur les [tâches analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/tasks) pour en apprendre davantage.

### Exemples

#### `pageVisibilityTracker`

Cette configuration définie une dimension personnalisée 1 en fonction de la valeur définie pour le champ `eventValue` pour tous les hits d’évènements `visibilitychange`. Cela définit à `true` an tant que troisième argument à la méthode `set`, de façon à ce que ce changement n’affecte que le hit en cours:

```js
ga('require', 'pageVisibilityTracker', {
  hitFilter: function(model) {
    model.set('dimension1', String(model.get('eventValue')), true);
  }
});
```

#### `impressionTracker`

Cette configuration empêche les hits d’être envoyés pour les impressions sur des éléments avec la classe `is-invisible`.

```js
ga('require', 'impressionTracker', {
  hitFilter: function(model, element) {
    if (element.className.indexOf('is-invisible') > -1) {
      throw new Error('Aborting hit');
    }
  }
});
```
