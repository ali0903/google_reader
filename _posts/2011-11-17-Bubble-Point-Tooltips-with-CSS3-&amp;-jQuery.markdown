---
layout: post
title:  "Bubble Point Tooltips with CSS3 &amp; jQuery"
date:   2011-11-17 23:59:21
author: Chris Coyier
categories: program
---

## Bubble Point Tooltips with CSS3 &amp; jQuery
### by Chris Coyier
### at 2011-11-17 23:59:21
### original <http://css-tricks.com/14950-bubble-point-tooltips-with-css3-jquery/>

<p>I needed some tooltips for a thing. In looking around for a little inspiration, I didn't have to go further than my dock<span></span>:</p>  <img src="http://cdn.css-tricks.com/wp-content/uploads/2011/11/dockmenu.png" alt="" title="dockmenu" width="320" height="212"> The inspiration: contextual menus from OS X dock <p>This is where I ended up:</p>  <img src="http://cdn.css-tricks.com/wp-content/uploads/2011/11/tooltip.png" alt="" title="tooltip" width="471" height="199"> <a href="http://css-tricks.com/examples/CSS3Tooltips/">View Demo</a>   <a href="http://css-tricks.com/examples/CSS3Tooltips.zip">Download Files</a> <h3>The HTML: Keepin' it Clean</h3><p>Links can have tooltips in HTML with no fancy coding whatsoever. Just give a link a <code>title</code> attribute.</p><pre rel="HTML"><code>&lt;a href=&quot;#&quot; title=&quot;Hi, I&#39;m a tooltip thingy.&quot;&gt;link&lt;/a&gt;</code></pre><p>Then when you hover over that link a second or two, you'll get the yellow box thingy:</p>  <img src="http://cdn.css-tricks.com/wp-content/uploads/2011/11/yellowboxthingy.png" alt="" title="yellowboxthingy" width="432" height="126"> Default tooltip in pretty much any browser. <p>Let's not mess with that. Our HTML will be exactly what is shown above.</p><p>The thing is, that yellow box thingy is absolutely unstyleable. Maybe someday it will be once shadow DOM stuff like that is specced out, but today, it's not possible in any browser. So we're gonna run some JavaScript which is going to yank out that attribute and make <code>&lt;div&gt;</code>'s with the same text, which we'll position/hide/show as needed.</p><h3>The JavaScript: jQuery Plugin</h3><p>As usual around these parts, we'll use jQuery. This is the perfect kind of thing to make into a plugin. You might want to use this functionality on an arbitrary collection of elements of your own choosing, as well as chain it, so the plugin pattern is ideal.</p><p>Calling it will be as simple as:</p><pre rel="jQuery"><code>$("article a[title]").tooltips();</code></pre><p>That will tooltip-ize all links with titles that happen to be inside <code>&lt;article&gt;</code> elements. You could make that selector whatever you want.</p><p>Our plugin will do this:</p><ol><li>Loop through each link</li><li>Create a (hidden) div.tooltip for each of them, the text inside matching that links title attribute.</li><li>Remove the links title attribute (only way to get rid of default yellow box popup</li><li>When link is hovered on...</li><li>Position the tooltip accordingly and slide it into visibility.</li><li>When link is hovered off...</li><li>Slide link out of visibility</li></ol><p>Massive code dump, commented for your pleasure:</p><pre rel="jQuery"><code>// IIFE to ensure safe use of $
(function( $ ) {

  // Create plugin
  $.fn.tooltips = function(el) {

    var $tooltip,
      $body = $(&#39;body&#39;),
      $el;

    // Ensure chaining works
    return this.each(function(i, el) {

      $el = $(el).attr(&quot;data-tooltip&quot;, i);

      // Make DIV and append to page
      var $tooltip = $(&#39;&lt;div class=&quot;tooltip&quot; data-tooltip=&quot;&#39; + i + &#39;&quot;&gt;&#39; + $el.attr(&#39;title&#39;) + &#39;&lt;div class=&quot;arrow&quot;&gt;&lt;/div&gt;&lt;/div&gt;&#39;).appendTo(&quot;body&quot;);

      // Position right away, so first appearance is smooth
      var linkPosition = $el.position();

      $tooltip.css({
        top: linkPosition.top - $tooltip.outerHeight() - 13,
        left: linkPosition.left - ($tooltip.width()/2)
      });

      $el
      // Get rid of yellow box popup
      .removeAttr(&quot;title&quot;)

      // Mouseenter
      .hover(function() {

        $el = $(this);

        $tooltip = $(&#39;div[data-tooltip=&#39; + $el.data(&#39;tooltip&#39;) + &#39;]&#39;);

        // Reposition tooltip, in case of page movement e.g. screen resize
        var linkPosition = $el.position();

        $tooltip.css({
          top: linkPosition.top - $tooltip.outerHeight() - 13,
          left: linkPosition.left - ($tooltip.width()/2)
        });

        // Adding class handles animation through CSS
        $tooltip.addClass(&quot;active&quot;);

        // Mouseleave
      }, function() {

        $el = $(this);

        // Temporary class for same-direction fadeout
        $tooltip = $(&#39;div[data-tooltip=&#39; + $el.data(&#39;tooltip&#39;) + &#39;]&#39;).addClass(&quot;out&quot;);

        // Remove all classes
        setTimeout(function() {
          $tooltip.removeClass(&quot;active&quot;).removeClass(&quot;out&quot;);
          }, 300);

        });

      });

    }

})(jQuery);</code></pre><p>Couple things to note: <strong>1)</strong> The final demo is going to have the tooltips animated a bit. None of that happens here in the JavaScript. Animations are design. Design is CSS. Thus, we do all that in the CSS. <strong>2)</strong> One little design touch on these is that the tooltips slide in and slide out the same direction. In CSS, by just adding and removing a single class and using transitions, that's not really possible. The transition will run in reverse upon the classes removal and thus will slide out same direction it slide in. By using a <code>setTimeout</code>, we can apply a temporary class, and animate that as well. Any more elegant ideas for that, let me know in the comments below.</p><h3>A Semantic Bummer?</h3><p>Adding divs to the bottom of the document feels like a bummer. Just doesn't feel very semantic. They aren't connected to the links they came from in any meaningful way through HTML alone.</p><p>Also removing the title tag doesn't sit quite right with me. I've heard they don't do much for accessibility anyway, but still. I wish <code>preventDefault()</code> would prevent that from showing, but it does not.</p><p>Any ideas on that stuff, let's hear 'em in the comments below.</p><h3>The CSS: Close Counts</h3><p>Notice in the OS X screenshot at the top of this article, the background and borders themselves are a bit transparent. I was unable to achieve that here. Those things are possible in general, but the pointy arrow down is an additional element and how the borders and backgrounds connect to each other is just through overlapping and it looks bad with any transparency at all. So, solid colors, NBD.</p><p>Oftentimes pointy arrows like we have here are doable with no additional markup or images by using pseudo elements and CSS triangles. In our case, we are going to use a pseudo element, but we'll need a real element as well. The real element (the <code><span></span></code> that the JavaScript inserted in each tooltip <code>&lt;div&gt;</code>) serves as a positional box does the cropping. The pseudo element is the actual pointer. A box styled the exact same way as the tooltip, only rotated 45deg and cropped out by it's parent. Here's the schematics:</p>  <img src="http://cdn.css-tricks.com/wp-content/uploads/2011/11/tooltip-scematics.jpg" alt="" title="tooltip-scematics" width="500" height="371"> The thing goes in the thing <p>And so:</p><pre rel="CSS"><code>.tooltip, .arrow:after {
  background: black;
  border: 2px solid white;
}

.tooltip {
  pointer-events: none;
  opacity: 0;
  display: inline-block;
  position: absolute;
  padding: 10px 20px;
  color: white;
  border-radius: 20px;
  margin-top: 20px;
  text-align: center;
  font: bold 14px "Helvetica Neue", Sans-Serif;
  font-stretch: condensed;
  text-decoration: none;
  text-transform: uppercase;
  box-shadow: 0 0 7px black;
}
.arrow {
  width: 70px;
  height: 16px;
  overflow: hidden;
  position: absolute;
  left: 50%;
  margin-left: -35px;
  bottom: -16px;
}
.arrow:after {
  content: "";
  position: absolute;
  left: 20px;
  top: -20px;
  width: 25px;
  height: 25px;
  -webkit-box-shadow: 6px 5px 9px -9px black,
                      5px 6px 9px -9px black;
  -moz-box-shadow: 6px 5px 9px -9px black,
                   5px 6px 9px -9px black;
  box-shadow: 6px 5px 9px -9px black,
              5px 6px 9px -9px black;
  -webkit-transform: rotate(45deg);
  -moz-transform:    rotate(45deg);
  -ms-transform:     rotate(45deg);
  -o-transform:      rotate(45deg);
}
.tooltip.active {
  opacity: 1;
  margin-top: 5px;
  -webkit-transition: all 0.2s ease;
  -moz-transition:    all 0.2s ease;
  -ms-transition:     all 0.2s ease;
  -o-transition:      all 0.2s ease;
}
.tooltip.out {
  opacity: 0;
  margin-top: -20px;
}</code></pre><p>Note the <code>.active</code> class is what does the slide in animation and the <code>.out</code> class does the slide out.</p><h3>Thanks</h3><p>To <a href="https://twitter.com/#!/metalculus84">Adrian Adkison</a> for helping with some ideas and code along the way.</p><h3>Fair Warning about Opera</h3><p>Opera doesn't support pointer-events in CSS, so this whole demo will appear not to work. That's because the tooltips are actually positioned right on top of the links with just their opacity turned down to 0. If you want to fix it up in Opera, you'll need to literally display none/block them in the JavaScript, or use CSS to move them off the links far enough they don't impede clickability. If someone wants to make that change but have it otherwise function exactly as this demo is, I'm down to update it.</p><h3>Demo / Download</h3><p><a href="http://css-tricks.com/examples/CSS3Tooltips/">View Demo</a>   <a href="http://css-tricks.com/examples/CSS3Tooltips.zip">Download Files</a></p><p><a href="http://css-tricks.com/14950-bubble-point-tooltips-with-css3-jquery/">Bubble Point Tooltips with CSS3 &amp; jQuery</a> is a post from <a href="http://css-tricks.com">CSS-Tricks</a></p>