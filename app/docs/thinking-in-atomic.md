# Thinking in Atomic

&quot;**Atomic**&quot; is a [CSS architecture](http://www.smashingmagazine.com/2013/10/21/challenging-css-best-practices-atomic-approach/) powered by [Atomizer](/guides/atomizer.html). It does not come with a UI, it does not come with a grid system, it does not even come with a style sheet!

**Atomizer** relies on the [Atomic syntax](/guides/syntax.html) to write CSS *on the fly* &mdash; it only creates rules that are relevant to a given project. These rules come from classes found in files within the project or via custom values set in the config file.

Custom style sheets are used *[whenever it makes sense](#be-pragmatic)*. They include styles that Atomic cannot create <b class="Fw(n)">[\[1\]](#footnote)<a id="footnote-1" class="D(ib)"></a></b> or styles for elements that live in multiple places in the code base <b class="Fw(n)">[\[2\]](#footnote)<a id="footnote-2" class="D(ib)"></a></b>.

Adopting this methodology addresses common CSS challenges:

<dl class="Mstart(20px) Mb(30px)">
<dt class="Fs(i) C(#000)">Changes are predictable</dt>
<dd class="Mstart(20px) Mt(5px)">Because of the single responsibility principle (one class == one style) it is easy to predict what removing or adding a class will do.</dd>
<dt class="Fs(i) C(#000)">Scope is limited</dt>
<dd class="Mstart(20px) Mt(5px)">There is no reliance on descendant/contextual selectors &mdash; styling is done inside &quot;[specificity layers](#style-sheets-organization)&quot;.</dd>
<dt class="Fs(i) C(#000)">CSS is lean</dt>
<dd class="Mstart(20px) Mt(5px)">There is very little redundancy and no dead weight (all styles are relevant to the project).</dd>
<dt class="Fs(i) C(#000)">Components are portable</dt>
<dd class="Mstart(20px) Mt(5px)">Classes used to style a component are not specific to that component, hence components can live in any other project that uses Atomizer <b class="Fw(n)">[\[3\]](#footnote)<a id="footnote-3" class="D(ib)"></a></b>.</dd>
<dt class="Fs(i) C(#000)">Bad CSS is &quot;OK&quot;</dt>
<dd class="Mstart(20px) Mt(5px)">People who don't know CSS very well don't create bloat because they don't write new rules, they mostly re-use existing ones.</dd>
</dl>

<p class="noteBox info">Another advantage we noticed with this methodology during code review is the fact that you get to see the styling **and** the structure at once (less guessing).</p>

## Who's Atomic for?

**Atomic** is for developers who see the benefits of styling &quot;*outside of style sheets*&quot; &mdash; who want to write markup and styles in one place while benefiting from a Atomic architecture. It is a switch of priorities. You don't maintain style sheets but *components*. If your environment is not well suited for this kind of development then you should not bother with this methodology because you may not gain much from it.

## Be pragmatic

If you think creating the style you want could be better achieved via classic styling, then by all means go that route. In other words, only use **Atomic** when it makes the most sense.

## Grids

There is no such thing as &quot;Grid&quot; in Atomic. Atomic does not provide classes to create columns based on an opinionated construct (`float`, `inline-block`, etc.). Instead, Atomic gives you all the tools you need to create [any grid you want](/tutorials/grid-system.html).

## Specificity, the Atomic take

### From `0,0,1,0` to &quot;*infinity*&quot;

Atomizer creates *atomic.css*, a style sheet in which the specificity of every rule can be fine tuned by the use of a *namespace*. Best practice is to keep specificity as low as possible but depending on other rules in your project you may want to include a namespace to give enough weight to Atomic classes.

Keep in mind that the weight of rules is *not as important as* making sure specificity is homogeneous across rules.
For example, styles like these:

```css
#namespace .myBox {}           /* 0,1,1,0 */
#namespace .menu_item {}       /* 0,1,1,0 */
#namespace .list_active {}     /* 0,1,1,0 */
#namespace .article_summary {} /* 0,1,1,0 */
#namespace .nav_link {}        /* 0,1,1,0 */
```

are easier to maintain than styles like those:

<pre><code class="lang-html"><span class="hljs-class">.myBox</span> {}                      <span class="hljs-comment">/* 0,0,1,0 */</span>
<span class="hljs-class">.menu</span> <span class="hljs-class">.menu_item</span> {}            <span class="hljs-comment">/* 0,0,2,0 */</span>
<span class="hljs-id">ul</span><span class="hljs-class">.list</span> <span class="hljs-class">.active</span> {}             <span class="hljs-comment">/* 0,0,2,1 */</span>
<span class="hljs-class">.main</span> <span class="hljs-class">.article</span> <span class="hljs-class">.summary</span> {}     <span class="hljs-comment">/* 0,0,3,0 */</span>
<span class="hljs-class">.nav</span> <span class="hljs-class">.list</span> <span class="hljs-class">.item</span> <span class="hljs-tag">a</span> {}          <span class="hljs-comment">/* 0,0,3,1 */</span>
</code></pre>

Choosing to include a namespace or not, and to use a *class* or a *id* for that, depends on the weight of rules in other style sheets. For Atomic classes to be relevant they must have enough weight to overwrite non-atomic styles. The specificity can be the same as long as *atomic.css* comes **after** other style sheets.

This table suggests the namespace to use depending on the weight of your other rules ([Specificity Calculator](http://specificity.keegan.st/)).

<table class="Ta(start) W(100%)">
    <caption class="Hidden">Namespace consideration</caption>
    <thead>
        <tr>
            <th scope="col" class="P(10px)">Specificity</th>
            <th scope="col" class="P(10px)">Namespace</th>
        </tr>
    </thead>
    <tbody>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">`0,0,1,1`</th>
            <td class="Va(t) P(10px)">No need for a namespace</td>
        </tr>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">Less or equal to `0,0,1,1`</th>
            <td class="Va(t) P(10px)">Use `html` (type) for namespace <b class="Fw(n)">[\[4\]](#footnote)<a id="footnote-4" class="D(ib)"></a></b></td>
        </tr>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">Less or equal to `0,0,2,0`</th>
            <td class="Va(t) P(10px)">Use a `class` for namespace</td>
        </tr>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">Less or equal to `0,1,1,0`</th>
            <td class="Va(t) P(10px)">Use a `id` for namespace</td>
        </tr>
            <tr class="BdT Bdc(#0280ae.3)">
                <th scope="row" class="Va(t) Whs(nw) P(10px)">More than `0,1,1,0`</th>
                <td class="Va(t) P(10px)">Use whatever it takes <b class="Fw(n)">[\[5\]](#footnote)<a id="footnote-5" class="D(ib)"></a></b></td>
            </tr>
    </tbody>
</table>

<p class="noteBox info">You can set up a namespace using [grunt-atomizer](https://github.com/yahoo/grunt-atomizer#examples) or via [the command line](https://github.com/yahoo/atomizer#cli).</p>

### Style sheets organization

Take advantage of the *cascade* by creating &quot;specificity layers&quot; <b class="Fw(n)">[\[6\]](#footnote)<a id="footnote-6" class="D(ib)"></a></b> and loading files in proper order.

<table class="Ta(start) W(100%)">
    <caption class="Hidden">Namespace consideration</caption>
    <thead>
        <tr>
            <th scope="col" class="P(10px)">Layer</th>
            <th scope="col" class="P(10px)">Specificity</th>
            <th scope="col" class="P(10px)">Style sheets</th>
        </tr>
    </thead>
    <tbody>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">type selectors</th>
            <td class="Va(t) P(10px)">`0,0,0,x`</td>
            <td class="Va(t) P(10px)">normalize.css,<br> base.css,<br> etc.</td>
        </tr>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">single class</th>
            <td class="Va(t) P(10px)">`0,0,1,0`</td>
            <td class="Va(t) P(10px)">helpers.css,<br> utility.css,<br> etc.</td>
        </tr>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">contextual selectors<br> (any number of classes)</th>
            <td class="Va(t) P(10px)">`0,0,x,x`</td>
            <td class="Va(t) P(10px)">layout.css,<br> custom.css,<br> etc.</td>
        </tr>
        <tr class="BdT Bdc(#0280ae.3)">
            <th scope="row" class="Va(t) Whs(nw) P(10px)">Atomic classes</th>
            <td class="Va(t) P(10px)">`0,1,1,0`</td>
            <td class="Va(t) P(10px)">atomic.css</td>
        </tr>
            <tr class="BdT Bdc(#0280ae.3)">
                <th scope="row" class="Va(t) Whs(nw) P(10px)">`@style`</th>
                <td class="Va(t) P(10px)">`1,0,0,0`</td>
                <td class="Va(t) P(10px)">Inline Styles</td>
            </tr>
            <tr class="BdT Bdc(#0280ae.3)">
                <th scope="row" class="Va(t) Whs(nw) P(10px)">`!important` rule</th>
                <td class="Va(t) P(10px)">Trumps all the above <b class="Fw(n)">[\[6\]](#footnote)<a id="footnote-6" class="D(ib)"></a></b></td>
                <td class="Va(t) P(10px)">Can be anywhere<br> (as an exception)</td>
            </tr>
    </tbody>
</table>

<p class="noteBox info">This web site uses *flat selectors* (`0,0,1,0`) which allows us to follow the same logic as above without the need for a namespace.</p>

<hr class="Mt(50px)">

<ol id="footnote" class="ol-list">
    <li>For example, Atomizer cannot create/style pseudo-elements [\[↩\]](#footnote-1).</li>
    <li>An example of this could be the markup of a &quot;button&quot; that could appear in many places across a project (versus a unique &quot;component&quot; *included* in multiple places) [\[↩\]](#footnote-2).</li>
    <li>Unless that component relies on custom values from the config file - in which case, keys from that file would need to be added to the config of the other project [\[↩\]](#footnote-3).</li>
    <li>You cannot attach Atomic classes to the element used to namespace those classes. This is because all styles target descendant elements of that node. [\[↩\]](#footnote-3).</li>
    <li>Specificity is something we want to leverage, not something we want to keep a lid on [\[↩\]](#footnote-4).</li>
    <li>The namespace can be anything, for example: `#someId #anotherId .andAclass` [\[↩\]](#footnote-5).</li>
    <li>`!important` is not related to specificity per se [\[↩\]](#footnote-6).</li>
</ol>
