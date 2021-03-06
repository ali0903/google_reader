---
layout: post
title:  "Sass Style Guide"
date:   2013-05-29 20:02:59
author: Chris Coyier
categories: program
---

## Sass Style Guide
### by Chris Coyier
### at 2013-05-29 20:02:59
### original <http://css-tricks.com/sass-style-guide/>

<p>With more people than ever writing in Sass, it bears some consideration how we format it. <a href="http://css-tricks.com/css-style-guides/">CSS style guides</a> are common, so perhaps we can extend those to cover choices unique to Sass. </p>
<p>Here are some ideas that I've been gravitating toward. Perhaps they are useful to you or help you formulate ideas of your own.</p>
<p><span></span></p>
<h3>Use Your Regular CSS Formatting Rules / Style Guide</h3>
<p>This post is about Sass-specific stuff, but as a base to this, you should follow a whatever good CSS formatting guidelines you are already following. If you aren't yet, this <a href="http://css-tricks.com/css-style-guides/">roundup of style guides</a> may help you. This includes things like:</p>
<ol>
<li>Be consistant with indentation</li>
<li>Be consistant about where spaces before/after colons/braces go</li>
<li>One selector per line, One rule per line</li>
<li>List related properties together</li>
<li>Have a plan for class name naming</li>
<li>Don't use ID's #hotdrama</li>
<li>etc</li>
</ol>
<h3>List @extend(s) First</h3>
<pre rel="SCSS"><code>.weather {
  @extends %module; 
  ...
}</code></pre>
<p>Knowing right off the bat that this class inherits another whole set of rules from elsewhere is good.</p>
<h3>List "Regular" Styles Next</h3>
<pre rel="SCSS"><code>.weather {
  @extends %module; 
  background: LightCyan;
  ..
}</code></pre>
<h3>List @include(s) Next</h3>
<pre rel="SCSS"><code>.weather {
  @extends %module; 
  background: LightCyan;
  @include transition(all 0.3s ease-out);
  ...
}</code></pre>
<p>This visually separates the @extends and @includes as well as groups the @includes for easier reading. You might also want to make the call on separating user-authored @includes and vendor-provided @includes.</p>
<h3>Nested Selectors Last</h3>
<pre rel="SCSS"><code>.weather {
  @extends %module; 
  background: LightCyan;
  @include transition(all 0.3s ease);
  &gt; h3 {
    border-bottom: 1px solid white;
    @include transform(rotate(90deg));
  }
}</code></pre>
<p>Nothing goes after the nested stuff. And the same order as above within the nested selector would apply.</p>
<h3>All Vendor Prefixes Use @mixins</h3>
<p>Vendor prefixes are a time-sensitive thing. As browsers update over time, the need for them will fall away. You can update mixins (or the libraries you use will update) to reflect those changes. Even if the mixin ends up being a one-liner, that's OK.</p>
<p>The only time I wouldn't @mixin a vendor prefix is when it's super proprietary, unlikely to be standardized as is, and so including other vendor prefixes or the non-prefixed version is likely to cause more harm that good. I'm thinking of things like <code>-webkit-line-clamp</code> or <code>-ms-content-zoom-chaining</code> or things like that that.</p>
<h3>Maximum Nesting: Three Levels Deep</h3>
<pre rel="SCSS"><code>.weather {
  .cities {
    li {
      // no more!
    }
  }
}</code></pre>
<p>Chances are, if you're deeper than that, you're writing a crappy selector. Crappy in that it's too reliant on HTML structure (fragile), overly specific (too powerful), and not very reusable (not useful). It's also on the edge of being difficult to understand.</p>
<p>If you really want to use tag selectors because the class thing is getting too much for you, you may want to get pretty specific about it to avoid undesired cascading. And possibly even make use of extend so it has the benefit on the CSS side of re-usability.</p>
<pre rel="SCSS"><code>.weather
  &gt; h3 {
    @extend %line-under;
  }
}</code></pre>
<h3>Maximum Nesting: 50 Lines</h3>
<p>If a nested block of Sass is longer than that, there is a good chance it doesn't fit on one code editor screen, and starts becoming difficult to understand. The whole point of nesting is convenience and to assist in mental grouping. Don't use it if it hurts that.</p>
<h3>Global and Section-Specific Sass Files Are just Table of Contents</h3>
<p>In other words, no styles directly in them. Force yourself to keep all styles organized into component parts.</p>
<h3>List Vendor/Global Dependancies First, Then Author Dependancies, Then Patterns, Then Parts</h3>
<p>So the "table of contents" things comes together like:</p>
<pre rel="SCSS"><code>/* Vendor Dependencies */
@import "compass";

/* Authored Dependencies */
@import "global/colors";
@import "global/mixins";

/* Patterns */
@import "global/tabs";
@import "global/modals";

/* Sections */
@import "global/header";
@import "global/footer";</code></pre>
<p>The dependencies like Compass, colors, and mixins generate no compiled CSS at all, they are purely code dependancies. Listing the patterns next means that more specific "parts", which come after, have the power to override patterns without having a specificity war.</p>
<h3>Break Into As Many Small Files As Makes Sense</h3>
<p>There is no penalty to splitting into many small files. Do it as much as feels good to the project. I know I find it easier to jump to small specific files and navigate through them than fewer/larger ones.</p>
<pre rel="SCSS"><code>...

@import "global/header/header/";
@import "global/header/logo/";
@import "global/header/dropdowns/";
@import "global/header/nav/";
@import "global/header/really-specific-thingy/";</code></pre>
<p>I'd probably do this right in the global.scss, rather than have global @import a _header.scss file which has its own sub-imports. All that sub-importing could get out of hand. </p>
<p><a href="https://github.com/chriseppstein/sass-globbing">Globbing</a> might help if there starts to be too many to list.</p>
<h3>Partials are named _partial.scss</h3>
<p>This is a common naming convention that indicates this file isn't meant to be compiled by itself. It likely has dependancies that would make it impossible to compile by itself. Personally I like dashes in the "actual" filename though, like _dropdown-menu.scss.</p>
<h3>Locally, Compile Expanded with Line Mapping</h3>
<p><a href="http://css-tricks.com/line-number-mapping-for-sass-in-chrom-dev-tools/">See here.</a> This means dev tools can tell you exactly what file and on what line rules are coming from, even if it is an imported partial.</p>
<h3>In Deployment, Compile Compressed</h3>
<p>Live websites should only ever have compressed CSS.</p>
<h3>Don't Even Commit .css Files</h3>
<p>This might take some DevOps work, but it's pretty nice if .css files aren't even in your repository. The compilation happens during deployment. So the only thing you see in the repo are your nicely formatted hand authored Sass files. This makes the diffs useful as well. A diff is a comparison view of what changed provided by version control providers. The diff for a compressed CSS file is useless.</p>
<h3>Be Generous With Comments</h3>
<p>It is rare to regret leaving a comment in code. It is either helpful or easily ignorable. Comments get stripped when compiling to compressed code, so there is no cost.</p>
<pre rel="SCSS"><code>.overlay {
  /* modals are 6000, saving messages are 5500, header is 2000 */
  z-index: 5000; 
}</code></pre>
<p>And speaking of comments, you may want to standardize on that. The // syntax in Sass is pretty nice especially for blocks of comments, so it is easier to comment/uncomment individual lines.</p>
<h3>Variablize All Common Numbers, and Numbers with Meaning</h3>
<p>If you find yourself using a number other than 0 or 100% over and over, it likely deserves a variable. Since it likely has meaning and controls consistency, being able to tweak it enmasse may be useful. </p>
<p>If a number clearly has strong meaning, that's a use case for variablizing as well.</p>
<pre rel="SCSS"><code>$zHeader: 2000;
$zOverlay: 5000;
$zMessage: 5050;

.header {
  z-index: $zHeader;
}
.overlay {
  z-index: $zOverlay;
}
.message {
  z-index: $zMessage;
}</code></pre>
<p>Those numbers are probably in a separate file @import-ed as a dependency. That way you can keep track of your whole z-index stack in one place.</p>
<h3>Variablize All Colors</h3>
<p>Except perhaps white and black. Chances are a color isn't one-off, and even if you think it is, once it's in a variable you might see uses for it elsewhere. Variations on that color can often be handled by the Sass <a href="http://sass-lang.com/docs/yardoc/Sass/Script/Functions.html">color functions</a> like lighten() and darken() - which make updating colors easier (change in one place, whole color scheme updates).</p>
<h3>Nest and Name Your Media Queries</h3>
<p>The ability to nest media queries in Sass means 1) you don't have to re-write the selector somewhere else which can be error prone 2) the rules that you are overriding are very clear and obvious, which is usually not the case when they are at the bottom of your CSS or in a different file.</p>
<pre rel="SCSS"><code>.sidebar {
  float: right;
  width: 33.33%;
  @include bp(mama-bear) {
    width: 25%;
  }
}</code></pre>
<p><a href="http://css-tricks.com/naming-media-queries/">More on this</a> and the importance of naming them well.</p>
<h3>Shame Last</h3>
<p>In your global stylesheet, @import a _shame.scss file last.</p>
<pre rel="SCSS"><code>@import "compass"

...

@import "shame"</code></pre>
<p>If you need to make a quick fix, you can do it here. Later when you have proper time, you can move the fix into the proper structure/organization. <a href="http://csswizardry.com/2013/04/shame-css/">See more.</a></p>
<h3>Final Output Is On You</h3>
<p>Sass doesn't do anything you don't tell it to do, so claiming that Sass output is bloated is just claiming that you write bloated code. Write Sass such that the final CSS output is just as you would have written it without Sass.</p>
<p><hr>

<p><strong>Need a new look for your portfolio? Check out the <a href="http://thethemefoundry.com/wordpress/snap/?utm_campaign=css_tricks_snap">Snap WordPress theme</a> from The Theme Foundry. Sass files and Compass config are included!</strong></p>

<hr>

<p><small><a href="http://css-tricks.com/sass-style-guide/">Sass Style Guide</a> is a post from <a href="http://css-tricks.com">CSS-Tricks</a></small></p></p>