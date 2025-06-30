# dynamics-test

This repo is strictly for test files that go with docs-plugin for Grafana.  We're experimenting.  

## How to Write Interactive Learning Guides

1. Learning guides in raw HTML (like documentation) currently can't interact with the product
2. In order to interact with the product, they'd have to capture which buttons or UX elements they're talking about in Grafana, and then what to do with them.
3. What we're exploring here is how we can use semantic markup with HTML to extend simple text documentation to do this.
   
Let's get more concrete and look at a short example:

```
  <!-- Highlight a menu item and click it -->
  <li class="interactive" 
      data-reftarget="a[data-testid='data-testid Nav menu item'][href='/connections']"
      data-targetaction='highlight'>
    Click Connections in the left-side menu.</li>
  <li>
```

All of the magic is in the `data-*` attributes. You'll notice what we have is:

0. `class="interactive"` this tells us from the beginning this is an interactive documentation element.
1. `data-reftarget`.  This is the "target of the interactive step".  In this case, that is a DOM selector that corresponds to the "Connections" menu in the left nav of Grafana.  It is very important reftargets be unique.
2. `data-targetaction`.  This tells us what to do with the target.  We could highlight it, or click the button for example.

## A Complete Example

More complex example:

```
  <!-- An interactive one-shot block sequence -->
  <span id="test1" class="interactive" data-targetaction="sequence" data-reftarget="span#test1"> 
      <ul>
        <!-- Highlight a menu item and click it -->
        <li class="interactive" 
            data-reftarget="a[data-testid='data-testid Nav menu item'][href='/connections']"
            data-targetaction='highlight'>
          Click Connections in the left-side menu.</li>
        <li>
          Under Connections, click Add new connection.</li>
        <!-- Fill out a form item -->
        <li class="interactive" data-reftarget="input[type='text']"
          data-targetaction='formfill' data-targetvalue='Prometheus'>
          Enter Prometheus in the search bar.</li>
        <!-- Highlight a menu item and click it -->
        <li class="interactive" 
            data-reftarget="a[href='/connections/datasources/prometheus']"
            data-targetaction='highlight'>
          Click Prometheus data source.</li>
        <!-- n.b. sometimes semantic HTML is insufficient to locate a button.
             So we have the 'button' interactive primitive that finds any button
             element whose text is reftarget -->
        <li class="interactive"
            data-reftarget="Add new data source"
            data-targetaction='button'>
          Click Add new data source in the upper right.
        </li>
      </ul>
  </span>
```

A "sequence" means that all steps in the sequence can be executed as one atomic operation.

Notice that "formfill" is a `data-targetaction` in which case you need another value `data-targetvalue` (what to fill the form out with).

## What to do if you don't have a good DOM selector

Look at this special case:

```
  <li class="interactive"
      data-reftarget="Add new data source"
      data-targetaction='button'>
    Click Add new data source in the upper right.
  </li>
```

Often buttons in Grafana land don't have IDs on the DOM and may have generated CSS class names. In this case,
we need to find a button _by the button text_.  Notice that the data-reftarget is a string, not a DOM selector.

This would instruct docs-plugin to find the button by text.
