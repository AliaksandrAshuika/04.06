---
"$title": Spécification AMP pour les annonces
order: '3'
formats:
  - les publicités
teaser:
  text: |2-

    _Si vous souhaitez proposer des modifications à la norme, veuillez commenter le
    [Intention
toc: vrai
---

<!--
This file is imported from https://github.com/ampproject/amphtml/blob/master/extensions/amp-a4a/amp-a4a-format.md.
Please do not change this file.
If you have found a bug or an issue please
have a look and request a pull request there.
-->

<!---
Copyright 2016 The AMP HTML Authors. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS-IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

*Si vous souhaitez proposer des modifications à la norme, veuillez commenter l' [intention de mise en œuvre](https://github.com/ampproject/amphtml/issues/4264)* .

## Règles de format d'annonce AMP HTML<a name="amphtml-ad-format-rules"></a>

<table>
<thead><tr>
  <th>Régner</th>
  <th>Raisonnement</th>
</tr></thead>
<tbody>
<tr>
<td>Doit utiliser <code>&lt;html ⚡4ads&gt;</code> ou <code>&lt;html amp4ads&gt;</code> comme balises englobantes.</td>
<td>Permet aux validateurs d'identifier un document créatif en tant que document AMP général ou en tant que document publicitaire AMPHTML restreint et de l'envoyer de manière appropriée.</td>
</tr>
<tr>
<td>Doit inclure <code>&lt;script async src="https://cdn.ampproject.org/amp4ads-v0.js"&gt;&lt;/script&gt;</code> comme script d'exécution au lieu de <code>https://cdn.ampproject.org/v0.js</code> .</td>
<td>Permet des comportements d'exécution personnalisés pour les annonces AMP HTML diffusées dans des iframes cross-origin.</td>
</tr>
<tr>
<td>Ne doit pas inclure de <code>&lt;link rel="canonical"&gt;</code> .</td>
<td>Les créations publicitaires n'ont pas de "version canonique non AMP" et ne seront pas indexées de manière indépendante sur la recherche, donc l'auto-référencement serait inutile.</td>
</tr>
<tr>
<td>Peut inclure des balises méta facultatives dans l'en-tête HTML en tant qu'identifiants, au format <code>&lt;meta name="amp4ads-id" content="vendor=${vendor},type=${type},id=${id}"&gt;</code> . Ces balises méta doivent être placées avant le <code>amp4ads-v0.js</code> . La valeur de <code>vendor</code> et <code>id</code> sont des chaînes contenant uniquement [0-9a-zA-Z_-]. La valeur de <code>type</code> est soit <code>creative-id</code> soit <code>impression-id</code> .</td>
<td>Those custom identifiers can be used to identify the impression or the creative. They can be helpful for reporting and debugging.<br><br><p>Example:</p>
<pre>
&lt;meta name="amp4ads-id"
  content="vendor=adsense,type=creative-id,id=1283474"&gt;
&lt;meta name="amp4ads-id"
  content="vendor=adsense,type=impression-id,id=xIsjdf921S"&gt;</pre>
</td>
</tr>
<tr>
<td>
<code>&lt;amp-analytics&gt;</code> ne peut cibler que le sélecteur d'annonces complet, via <code>"visibilitySpec": { "selector": "amp-ad" }</code> comme défini dans le <a href="https://github.com/ampproject/amphtml/issues/4018">problème n° 4018</a> et <a href="https://github.com/ampproject/amphtml/pull/4368">PR n° 4368</a> . En particulier, il ne peut cibler aucun sélecteur d'éléments au sein de la création publicitaire.</td>
<td>In some cases, AMPHTML ads may choose to render an ad creative in an iframe.In those cases, host page analytics can only target the entire iframe anyway, and won’t have access to any finer-grained selectors.<br><br> <p>Example:</p> <pre>
&lt;amp-analytics id="nestedAnalytics"&gt;
  &lt;script type="application/json"&gt;
  {
    "requests": {
      "visibility": "https://example.com/nestedAmpAnalytics"
    },
    "triggers": {
      "visibilitySpec": {
      "selector": "amp-ad",
      "visiblePercentageMin": 50,
      "continuousTimeMin": 1000
      }
    }
  }
  &lt;/script&gt;
&lt;/amp-analytics&gt;
</pre> <p>This configuration sends a request to the <code>https://example.com/nestedAmpAnalytics</code> URL when 50% of the enclosing ad has been continuously visible on the screen for 1 second.</p> </td>
</tr>
</tbody>
</table>

### Chaudronnerie<a name="boilerplate"></a>

Les créations publicitaires AMP HTML nécessitent une ligne de style passe-partout différente et considérablement plus simple que [les documents AMP généraux](https://github.com/ampproject/amphtml/blob/master/spec/amp-boilerplate.md) :

[code source:html]

&lt;style amp4ads-boilerplate&gt; body { visibilité : caché ; } &lt;/style&gt;

[/code source]

*Justification :* Le `amp-boilerplate` masque le contenu du corps jusqu'à ce que l'environnement d'exécution AMP soit prêt et puisse l'afficher. Si Javascript est désactivé ou si le runtime AMP ne se charge pas, le passe-partout par défaut garantit que le contenu est finalement affiché malgré tout. Dans les annonces AMP HTML, cependant, si Javascript est entièrement désactivé, les annonces AMP HTML ne seront pas diffusées et aucune annonce ne sera jamais diffusée. La section `<noscript>` En l'absence de l'environnement d'exécution AMP, la plupart des mécanismes sur lesquels reposent les annonces AMPHTML (par exemple, les analyses pour le suivi de la visibilité ou `amp-img` pour l'affichage du contenu) ne seront pas disponibles.

Enfin, le passe-partout publicitaire AMPHTML utilise `amp-a4a-boilerplate` plutôt que `amp-boilerplate` afin que les validateurs puissent facilement l'identifier et produire des messages d'erreur plus précis pour aider les développeurs.

Notez que les mêmes règles concernant les mutations du texte passe-partout s'appliquent que dans le passe- [partout AMP général](https://github.com/ampproject/amphtml/blob/master/spec/amp-boilerplate.md) .

### CSS<a name="css"></a>

<table>
<thead><tr>
  <th>Régner</th>
  <th>Raisonnement</th>
</tr></thead>
<tbody>
  <tr>
    <td>
<code>position:fixed</code> et <code>position:sticky</code> sont interdits dans le CSS créatif.</td>
    <td>
<code>position:fixed</code> cassures du DOM fantôme, dont dépendent les annonces AMPHTML. Par ailleurs, les annonces dans AMP ne sont déjà pas autorisées à utiliser une position fixe.</td>
  </tr>
  <tr>
    <td>
<code>touch-action</code> est interdite.</td>
    <td>Une publicité qui peut manipuler l' <code>touch-action</code> peut interférer avec la capacité de l'utilisateur à faire défiler le document hôte.</td>
  </tr>
  <tr>
    <td>Creative CSS est limité à 20 000 octets.</td>
    <td>Les gros blocs CSS gonflent la création, augmentent la latence du réseau et dégradent les performances de la page.</td>
  </tr>
  <tr>
    <td>La transition et l'animation sont soumises à des restrictions supplémentaires.</td>
    <td>AMP doit pouvoir contrôler toutes les animations appartenant à une publicité, afin de pouvoir les arrêter lorsque la publicité n'est pas à l'écran ou que les ressources système sont très faibles.</td>
  </tr>
  <tr>
    <td>Les préfixes spécifiques au fournisseur sont considérés comme des alias pour le même symbole sans le préfixe à des fins de validation. Cela signifie que si un symbole <code>foo</code> est interdit par les règles de validation CSS, alors le symbole <code>-vendor-foo</code> sera également interdit.</td>
    <td>Certaines propriétés préfixées par le fournisseur offrent des fonctionnalités équivalentes aux propriétés qui sont par ailleurs interdites ou limitées en vertu de ces règles.<br><br><p> Exemple : <code>-webkit-transition</code> et <code>-moz-transition</code> sont tous deux considérés comme des alias pour la <code>transition</code> . Ils ne seront autorisés que dans des contextes où la <code>transition</code> nue serait autorisée (voir la <a href="#selectors">section Sélecteurs</a> ci-dessous).</p>
</td>
  </tr>
</tbody>
</table>

#### Animations et transitions CSS<a name="css-animations-and-transitions"></a>

##### Sélecteurs<a name="selectors"></a>

Les `transition` et d' `animation` ne sont autorisées que sur les sélecteurs qui :

- Ne contiennent que `transition` propriétés transition , `animation` , `transform` , `visibility` ou `opacity`

    *Justification :* Cela permet à l'environnement d'exécution AMP de supprimer cette classe du contexte pour désactiver les animations, lorsque cela est nécessaire pour les performances de la page.

**Bien**

[code source:css] .box { transform: rotate(180deg); transition : transformer 2s ; } [/code source]

**Mal**

Propriété non autorisée dans la classe CSS.

[code source:css] .box { couleur : rouge ; // propriété non-animation non autorisée dans la transformation du sélecteur d'animation : rotate(180deg); transition : transformer 2s ; } [/code source]

##### Propriétés transitionnelles et animables<a name="transitionable-and-animatable-properties"></a>

Les seules propriétés pouvant faire l'objet d'une transition sont l'opacité et la transformation. ( [Justification](http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/) )

**Bien**

[code source:css] transition : transformer 2 s ; [/code source]

**Mal**

[sourcecode:css] transition : background-color 2s ; [/code source]

**Bien**

[sourcecode:css] @keyframes turn { from { transform: rotate(180deg); }

to { transform: rotate(90deg); } } [/code source]

**Mal**

[sourcecode:css] @keyframes slidein { from { margin-left: 100%; largeur : 300 % ; }

à { marge-gauche : 0 % ; largeur : 100 % ; } } [/code source]

### Extensions et fonctions intégrées AMP autorisées<a name="allowed-amp-extensions-and-builtins"></a>

Les modules d'extension AMP et les tags intégrés AMP suivants sont *autorisés dans une création publicitaire AMP HTML.* Les extensions ou balises intégrées qui ne sont pas explicitement répertoriées sont interdites.

- [ampli-accordéon](https://amp.dev/documentation/components/amp-accordion)
- [amp-annonce-sortie](https://amp.dev/documentation/components/amp-ad-exit)
- [amp-analytics](https://amp.dev/documentation/components/amp-analytics)
- [amp-anim](https://amp.dev/documentation/components/amp-anim)
- [amp-animation](https://amp.dev/documentation/components/amp-animation)
- [ampli-audio](https://amp.dev/documentation/components/amp-audio)
- [amp-bind](https://amp.dev/documentation/components/amp-bind)
- [ampli-carrousel](https://amp.dev/documentation/components/amp-carousel)
- [amp-fit-text](https://amp.dev/documentation/components/amp-fit-text)
- [amp-font](https://amp.dev/documentation/components/amp-font)
- [amp-forme](https://amp.dev/documentation/components/amp-form)
- [amp-img](https://amp.dev/documentation/components/amp-img)
- [ampli-disposition](https://amp.dev/documentation/components/amp-layout)
- [ampli-boîte à lumière](https://amp.dev/documentation/components/amp-lightbox)
- amp-mraid, à titre expérimental. Si vous envisagez de l'utiliser, veuillez ouvrir un problème sur [wg-ads](https://github.com/ampproject/wg-ads/issues/new) .
- [ampli-moustache](https://amp.dev/documentation/components/amp-mustache)
- [ampère-pixel](https://amp.dev/documentation/components/amp-pixel)
- [amp-position-observateur](https://amp.dev/documentation/components/amp-position-observer)
- [sélecteur d'ampli](https://amp.dev/documentation/components/amp-selector)
- [amp-social-share](https://amp.dev/documentation/components/amp-social-share)
- [amp-vidéo](https://amp.dev/documentation/components/amp-video)

La plupart des omissions sont soit pour les performances, soit pour simplifier l'analyse des annonces AMPHTML.

*Exemple :* `<amp-ad>` est omis de cette liste. Il est explicitement interdit, car autoriser un `<amp-ad>` dans un `<amp-ad>` pourrait potentiellement conduire à des cascades illimitées de chargement d'annonces, ce qui ne répond pas aux objectifs de performances des annonces AMPHTML.

*Exemple :* `<amp-iframe>` est omis de cette liste. Il est interdit car les publicités pourraient l'utiliser pour exécuter du Javascript arbitraire et charger du contenu arbitraire. Les publicités souhaitant utiliser de telles fonctionnalités doivent renvoyer `false` partir de leur [entrée a4aRegistry](https://github.com/ampproject/amphtml/blob/master/ads/_a4a-config.js#L40) et utiliser le mécanisme de rendu publicitaire « 3p iframe » existant.

*Exemple :* `<amp-facebook>` , `<amp-instagram>` , `<amp-twitter>` et `<amp-youtube>` sont tous omis pour la même raison que `<amp-iframe>` : ils créent tous des iframes et peuvent potentiellement consommer des ressources illimitées en eux.

*Exemple :* `<amp-ad-network-*-impl>` est omis de cette liste. La `<amp-ad>` gère la délégation à ces balises d'implémentation ; les créations ne doivent pas tenter de les inclure directement.

*Exemple :* `<amp-lightbox>` n'est pas encore inclus, car même certaines créations d'annonces AMP HTML peuvent être affichées dans un iframe et il n'existe actuellement aucun mécanisme permettant à une annonce de se développer au-delà d'un iframe. Un soutien peut être ajouté pour cela à l'avenir, s'il y a un désir démontré pour cela.

### Balises HTML<a name="html-tags"></a>

Les tags suivants sont *autorisés* dans une création publicitaire AMP HTML. Les balises non explicitement autorisées sont interdites. Cette liste est un sous-ensemble de la liste d' [autorisation générale de l'addendum des balises AMP](https://github.com/ampproject/amphtml/blob/master/extensions/amp-a4a/../../spec/amp-tag-addendum.md) . Comme cette liste, elle est ordonnée conformément à la spécification HTML5 de la section 4 [Les éléments du HTML](http://www.w3.org/TR/html5/single-page.html#html-elements) .

La plupart des omissions sont soit pour les performances, soit parce que les balises ne sont pas au standard HTML5. Par exemple, `<noscript>` est omis, car les annonces AMPHTML dépendent de l'activation de JavaScript, donc un `<noscript>` ne s'exécutera jamais et, par conséquent, ne fera qu'augmenter la bande passante et la latence de la création. De même, `<acronym>` , `<big>` , et al. sont interdits car non compatibles HTML5.

#### 4.1 L'élément racine<a name="41-the-root-element"></a>

4.1.1 `<html>`

- Doit utiliser les types `<html ⚡4ads>` ou `<html amp4ads>`

#### 4.2 Métadonnées du document<a name="42-document-metadata"></a>

4.2.1 `<head>`

4.2.2 `<title>`

4.2.4 `<link>`

- `<link rel=...>` ne sont pas autorisées, à l'exception de `<link rel=stylesheet>` .

- **Remarque :** Contrairement à l'AMP général, les `<link rel="canonical">` sont interdites.

    4.2.5 `<style>` 4.2.6 `<meta>`

#### 4.3 Rubriques<a name="43-sections"></a>

4.3.1 `<body>` 4.3.2 `<article>` 4.3.3 `<section>` 4.3.4 `<nav>` 4.3.5 `<aside>` 4.3.6 `<h1>` , `<h2>` , `<h3>` , `<h4>` , `<h5>` et `<h6>` 4.3.7 `<header>` 4.3.8 `<footer>` 4.3.9 `<address>`

#### 4.4 Regroupement du contenu<a name="44-grouping-content"></a>

4.4.1 `<p>` 4.4.2 `<hr>` 4.4.3 `<pre>` 4.4.4 `<blockquote>` 4.4.5 `<ol>` 4.4.6 `<ul>` 4.4.7 `<li>` 4.4.8 `<dl>` 4.4. 9 `<dt>` 4.4.10 `<dd>` 4.4.11 `<figure>` 4.4.12 `<figcaption>` 4.4.13 `<div>` 4.4.14 `<main>`

#### 4.5 Sémantique au niveau du texte<a name="45-text-level-semantics"></a>

4.5.1 `<a>` 4.5.2 `<em>` 4.5.3 `<strong>` 4.5.4 `<small>` 4.5.5 `<s>` 4.5.6 `<cite>` 4.5.7 `<q>` 4.5.8 `<dfn>` 4.5. 9 `<abbr>` 4.5.10 `<data>` 4.5.11 `<time>` 4.5.12 `<code>` 4.5.13 `<var>` 4.5.14 `<samp>` 4.5.15 `<kbd >` 4.5.16 `<sub>` et `<sup>` 4.5.17 `<i>` 4.5.18 `<b>` 4.5.19 `<u>` 4.5.20 `<mark>` 4.5.21 `<ruby>` 4.5.22 `<rb>` 4.5.23 `<rt>` 4.5.24 `<rtc>` 4.5. 25 `<rp>` 4.5.26 `<bdi>` 4.5.27 `<bdo>` 4.5.28 `<span>` 4.5.29 `<br>` 4.5.30 `<wbr>`

#### 4.6 Modifications<a name="46-edits"></a>

4.6.1 `<ins>` 4.6.2 `<del>`

#### 4.7 Contenu intégré<a name="47-embedded-content"></a>

- Le contenu intégré est pris en charge uniquement via les balises AMP, telles que `<amp-img>` ou `<amp-video>` .

#### 4.7.4 `<source>`<a name="474-source"></a>

<!---
#### 4.7.18 SVG <a name="4718-svg"></a>

SVG tags are not in the HTML5 namespace. They are listed below without section ids.

`<svg>`
`<g>`
`<path>`
`<glyph>`
`<glyphref>`
`<marker>`
`<view>`
`<circle>`
`<line>`
`<polygon>`
`<polyline>`
`<rect>`
`<text>`
`<textpath>`
`<tref>`
`<tspan>`
`<clippath>`
`<filter>`
`<lineargradient>`
`<radialgradient>`
`<mask>`
`<pattern>`
`<vkern>`
`<hkern>`
`<defs>`
`<use>`
`<symbol>`
`<desc>`
`<title>`
--->

#### 4.9 Données tabulaires<a name="49-tabular-data"></a>

4.9.1 `<table>` 4.9.2 `<caption>` 4.9.3 `<colgroup>` 4.9.4 `<col>` 4.9.5 `<tbody>` 4.9.6 `<thead>` 4.9.7 `<tfoot>` 4.9.8 `<tr>` 4.9. 9 `<td>` 4.9.10 `<th>`

#### 4.10 Formulaires<a name="410-forms"></a>

4.10.8 `<button>`

#### 4.11 Script<a name="411-scripting"></a>

- Comme un document AMP général, la `<head>` la création doit contenir une `<script async src="https://cdn.ampproject.org/amp4ads-v0.js"></script>` .
- Contrairement à l'AMP général, `<noscript>` est interdit.
    - *Justification :* Étant donné que les annonces AMP HTML nécessitent l'activation de Javascript pour fonctionner, les `<noscript>` ne servent à rien dans les annonces AMP HTML et ne coûtent que la bande passante du réseau.
- Contrairement à l'AMP général, `<script type="application/ld+json">` est interdit.
    - *Justification :* JSON LD est utilisé pour le balisage des données structurées sur les pages hôtes, mais les créations publicitaires ne sont pas des documents autonomes et ne contiennent pas de données structurées. Les blocs JSON LD qu'ils contiennent ne feraient que coûter de la bande passante réseau.
- Toutes les autres règles et exclusions de script sont reprises de l'AMP général.
