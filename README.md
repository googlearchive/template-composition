template-composition
====================

This is a Polymer behavior to compose superclass and subclass templates.
It's available at `Polymer.TemplateComposition`. The composition mechanism
is inspired by [stampino](https://github.com/justinfagnani/stampino). It allows you to (1) compose a superclass template into a subclass template, and (2) compose sub-parts of the subclass template called `blocks` into a composed superclass template.

Here's a quick [example](http://jsbin.com/fezohu/edit?html,output). It shows placing a super-class template somewhere in a subclass. You indicate the insertion spot by creating a special template block called `<template block="super">`.

``` html
<!-- A base element -->
<dom-module id="x-base">
  <template>
    I am x-base!
  </template>
  <script>
    Polymer({
      is: 'x-base'
    });
  </script>
</dom-module>

<!-- An extendor that compose base's template using `block="super"` -->
<dom-module id="x-extend">
  <template>
    <template block="super"></template>
    I am x-extend!
  </template>
  <script>
    Polymer({
      is: 'x-extend',
      extends: 'x-base'
    });
  </script>
</dom-module>

```

Here's another [example](http://jsbin.com/vodanu/edit?html,output). It shows composing subclass `blocks` into a superclass template. Do this by matching block names between the super and subclass. The subclass blocks that should be used to 'fill in' the superclass should be nested in a 'super block'.

``` html
<!-- A base element -->
  <dom-module id="x-base">
  <template>
    <div>
      <div>Who am I?</div>
      <template block="content">I am x-base!</template>
    </div>
  </template>
  <script>
    Polymer({
      is: 'x-base',
      behaviors: [Polymer.TemplateComposition]
    });
  </script>
</dom-module>

<!-- An extendor that composes base's template! -->
<dom-module id="x-extend">
  <template>
    <template block="super">
      <template block="content">I am x-extend!</template>      
    </template>
  </template>
  <script>
    Polymer({
      is: 'x-extend',
      extends: 'x-base',
      behaviors: [Polymer.TemplateComposition]
    });
  </script>
</dom-module>
```

In the above example, the composed dom for `x-base` and `x-extend` will look like this:
``` html
<!-- x-base -->
<div>
  <div>Who am I?</div>
  I am x-base!
</div>

<!-- x-extend -->
<div>
  <div>Who am I?</div>
  I am x-extend!
</div>
```

Here's a [slightly more complicated example](http://jsbin.com/bireraf/edit?html,output) that shows extending an already extended template. 


Notes:
* a superclass needs to include the `Polymer.TemplateComposition` behavior if it defines overridable blocks.
* a subclass that composes a superclass template will automatically pre-pend the superclass styles.
* including the `super` template is currently all or nothing. You cannot include only certain blocks in a super template. In addition a subclass cannot supply blocks to fill in a template from a super's superclass.
* customization: composition can be customized by overriding `_processTemplate(template)` and returning the fully composed template; styling can be customized by overriding `_collectStyles` and returning an array of style elements to be used to style the element.
