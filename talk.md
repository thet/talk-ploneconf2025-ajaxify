<!-- .slide: data-background="lime" -->
<!-- .slide: data-background="Blue" -->
<!-- .slide: data-background="Yellow" -->
<!-- .slide: data-background="Purple" -->
<!-- .slide: data-background="DarkViolet" -->
<!-- .slide: data-background="Cyan" -->


<!-- .slide: data-background="lime" -->
## Look at this!

- https://sfd.at




<!-- .slide: data-background="Yellow" -->
## What and why


<!-- .slide: data-background="Yellow" -->
schule für dichtung, wien
vienna poetry school


<!-- .slide: data-background="Yellow" -->
bringing together<br>
<strong>poets, writers</strong> and <strong>artists</strong><br>
with <strong>audiences</strong><br>
in <strong>workshops, readings, courses</strong> and other events.


<!-- .slide: data-background="Yellow" -->
online/offline courses

https://sfd.at/programm

https://sfd.at/lehren-und-lernen/faculty


<!-- .slide: data-background="Yellow" -->
founded 1992
in the web since ~ 1998


<!-- .slide: data-background-image="./resources/imgs/1-sfd-1998.png" class="t-r white" -->
Perl 1997<!-- .element: class="big" -->
Note:
https://web.archive.org/web/19981201054353/http://sfd.at/


<!-- .slide: data-background-image="./resources/imgs/1-sfd-2004.png" class="t-r" -->
Zope 2002<!-- .element: class="big" -->
Note:
https://web.archive.org/web/20050106061058/http://www2.sfd.at:8080/


<!-- .slide: data-background-image="./resources/imgs/1-sfd-2010.png" class="t-r" -->
Plone 4 2010<!-- .element: class="big" -->
Note:
https://alt.sfd.at/


<!-- .slide: data-background-image="./resources/imgs/1-sfd-2025.png" class="t-r" -->
Plone 6.1 2025<!-- .element: class="big" -->
Note:
https://sfd.at/


<!-- .slide: data-background="Yellow" -->
### Relaunch motivation

- Mobile friendly<!-- .element: class="fragment" -->
- Technology upgrade<!-- .element: class="fragment" -->



<!-- .slide: data-background="Yellow" -->
### Own motivation

- Eating own dog food (Classic UI over Volto)<!-- .element: class="fragment" -->
- Playground for experiments<!-- .element: class="fragment" -->





<!-- .slide: data-background="lime" -->
## how


<!-- .slide: data-background="lime" -->
### pat-inject<!-- .element: class="r-fit-text" -->


<!-- .slide: data-background="lime" -->
- Patternslib AJAX Pattern.
- https://patternslib.com/demos


<!-- .slide: data-background-iframe="https://patternslib.com/demos" data-background-interactive -->
Note:
https://patternslib.com/demos


<!-- .slide: data-background="lime" -->
- Declare AJAX behavior in HTML.
- <a href="https://github.com/Patternslib/Patterns/blob/master/src/pat/inject/documentation.md">Documentation</a>


<!-- .slide: data-background="lime" -->
### pat-inject example


<!-- .slide: data-background="lime" -->
Include the library:
```html
<script src="https://cdn.jsdelivr.net/npm/@patternslib/patternslib@9.10.4-alpha.0/dist/remote.min.js"></script>
```


<!-- .slide: data-background="lime" -->
Declare the injection:

```html [1-12|1|2|3-5,10-12]
<a href="https://corsproxy.io/?https://2025.ploneconf.org/schedule/speakers/johannes-raggam?"
   class="pat-inject"
   data-pat-inject="
       source: .profile .card-summary;
       target: .about-me;
   ">
  Load speaker info
</a>

<div class="about-me">
  <!-- content will be loaded here -->
</div>
```


<!-- .slide: data-background="lime" -->
Demo:

<a href="https://corsproxy.io/?https://2025.ploneconf.org/schedule/speakers/johannes-raggam?"
   class="pat-inject"
   data-pat-inject="
       source: .profile .card-summary;
       target: .about-me;
   ">
  Load speaker info
</a>

<div class="about-me">
  <!-- content will be loaded here -->
</div>




<!-- .slide: data-background="Cyan" -->
## pat-inject hacks


<!-- .slide: data-background="Cyan" -->
Recall the example from before:

```html [1-12|2]
<a href="https://corsproxy.io/?https://2025.ploneconf.org/schedule/speakers/johannes-raggam?"
   class="pat-inject"
   data-pat-inject="
       source: .profile .card-summary;
       target: .about-me;
   ">
  Load speaker info
</a>


<!-- .slide: data-background="Cyan" -->
The pat-inject trigger:

```js
const inject = {
    name: "inject",
    trigger: "a.pat-inject, form.pat-inject, .pat-subform.pat-inject",
    parser: parser,

    [...]
};
```

<small>https://github.com/Patternslib/Patterns/blob/master/src/pat/inject/inject.js#L48</small>


<!-- .slide: data-background="Cyan" -->
What if we change the trigger to catch all links?


<!-- .slide: data-background="Cyan" -->
Like so:

```js [1-4|3]
// Add a new trigger for the inject pattern.
registry.patterns.inject.trigger =
    `.pat-inject a[href^="${window.location.origin}"]`
;
```


<!-- .slide: data-background="Cyan" -->
Or actually so:

```js [1-23|5-6|3-4|7-16|17-18|19-20|21-22|23-25]
// Add a new trigger for the inject pattern.
registry.patterns.inject.trigger =
    // All anchors with the pat-inject class.
    `a.pat-inject`
    // All anchors within an element with the pat-inject class.
    + `.pat-inject a[href^="${window.location.origin}"]`
    // Except files.
    + `:not([href*="@@download"])`
    + `:not([href$=zip])`
    + `:not([href$=pdf])`
    + `:not([href$=mp3])`
    + `:not([href$=wav])`
    + `:not([href$=jpg])`
    + `:not([href$=webp])`
    + `:not([href$=png])`
    + `:not([href$=txt])`
    // Don't inject dropdown "buttons"
    + `:not(.dropdown-toggle)`
    // Don't inject on the autotoc tab headings.
    + `:not([id^="autotoc"])`
    // Don't handle explicitly set pat-inject elements.
    + `:not(.pat-inject)`
    // Stop global injection when explicitly asked. You can still explicitly
    // set pat-inject on these elements.
    + `:not(.stop-pattern)`
;
```


<!-- .slide: data-background="Cyan" -->
Markup changes:

```html [1-17|2-6|3|7-14|8-11|12-13]
  <body tal:define="body_class python:plone_layout.bodyClass(template, view);"
        class="
            pat-inject

            ${body_class}
        "
        data-pat-inject="
            source: #portal-column-content::element;
            target: #portal-column-content::element;
            history: record;
            scroll: top &&
            source: head title;
            target: head title;
        "
>
    ...
</body>
```


<!-- .slide: data-background="Cyan" -->
Or maybe so - depends on your needs and more testing:

```html
  <body tal:define="body_class python:plone_layout.bodyClass(template, view);"
        class="
            pat-inject

            ${body_class}
        "
        data-pat-inject="
            source: #portal-column-content::element;
            target: #portal-column-content::element;
            history: record;
            scroll: top &&
            source: head title;
            target: head title &&
            source: #language_navigation::element;
            target: #language_navigation::element &&
            source: head link[rel=canonical]::element;
            target: head link[rel=canonical]::element;
            remove-tags: ;
        "
>
    ...
</body>
```


<!-- .slide: data-background="Cyan" -->
One more thing ...

```xml [1-5|2-4]
<registry>
  <record name="plone.use_ajax_main_template">
    <value>True</value>
  </record>
</registry>
```

See:
https://github.com/plone/Products.CMFPlone/pull/4169
https://github.com/plone/plone.app.layout/pull/405


<!-- .slide: data-background="Cyan" -->
... before we dive even a bit deeper.



<!-- .slide: data-background="Purple" data-background-image="./resources/imgs/thats_all_folks.svg" -->




<!-- .slide: data-background="DarkViolet" -->
## Questions
