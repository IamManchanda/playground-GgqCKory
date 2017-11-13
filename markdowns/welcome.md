# Part 1 - Intro to Cascade and BEM.

Like any other programming language, CSS should be reusable, maintainable, and scalable. If you are adding CSS selectors on top of it to fix a problem, most likely you have made your CSS less reusable, maintainable, and scalable. In fact, if you have added CSS selectors to override a display issue caused elsewhere in your stylesheet, the next time a style change is necessary you might need to use the dreaded `!important`.

This is one of the things that all the various CSS architecture ideas (or even CSS-in-JS ideas like Styled Components) are meant to solve: no longer fighting specificity.

> Specificity determines which CSS rule is applied by the browsers. If two selectors apply to the same element, the one with **higher specificity wins**.

In the beginning of most projects, the CSS will start out fairly simple. You would have to do something terrible to make maintenance of the CSS problematic. But as your project grows, so does the CSS, and maintenance becomes more difficult. 

![Source: https://csswizardry.com/wp-content/uploads/2014/10/specificity-graph-01.png](https://d2mxuefqeaa7sj.cloudfront.net/s_043E65AF3EF3BB37503CCB5A25DF5E327B4531B8593ACA920CB59E98A1EC0C1A_1505387157232_specificity-graph-01.png)

Typically, the longer a project is around, the more designers and developers are involved in working on the CSS. The CSS will become harder to maintain day by day. The edge cases and browser workarounds need to be maintained too, resulting in more complexity. 

Still, we can’t ignore that specificity is useful.  It allows us to breathe with some logic in our CSS. The rules that are more specific than others get applied. That’s the way it should be. But the problem is that not all selectors are created equal. Selectors can be made up of IDs, classes, attribute, and tag selectors. This is how specificity is [calculated](https://www.w3.org/TR/css3-selectors/#specificity):

```scss
*               /* a=0 b=0 c=0 -> specificity = 000 */
LI              /* a=0 b=0 c=1 -> specificity = 001 */
UL LI           /* a=0 b=0 c=2 -> specificity = 002 */
UL OL+LI        /* a=0 b=0 c=3 -> specificity = 003 */
H1 + *[REL=up]  /* a=0 b=1 c=1 -> specificity = 011 */
UL OL LI.red    /* a=0 b=1 c=3 -> specificity = 013 */
LI.red.level    /* a=0 b=2 c=1 -> specificity = 021 */
#x34y           /* a=1 b=0 c=0 -> specificity = 100 */
#s12:not(FOO)   /* a=1 b=0 c=1 -> specificity = 101 */
```

There is something missing above: the `**style**` attribute. The problem with `**style**` attribute is [that](https://www.w3.org/TR/css-style-attr/):

> The declarations in a **style attribute** apply to the element to which the attribute belongs. In the cascade, these declarations are considered to have **author origin** and a **specificity higher than any selector**.

That is where `!important` comes in, as you can use `!important` to override the previously assigned CSS declarations. An `!important` rule would mean that the selectors below it won’t be affected and won’t be mutated and thus it’s a risky road to drive in your CSS declarations. That said, you should be using `!important` when you need to force immutability. That means, when you are sure that the declaration within the CSS selector shouldn't be changed with its succeeding selectors like `.text-blue` should never have `color` as `red`. In those cases, use `!important`. 

But still, if that is not an issue with you, the biggest concern with specificity is that ID selectors are **infinitely** more specific than class based selectors. Thus, you just can’t override any selector that contains an ID based selector with class based selectors.  Check this below code as an example:

```scss
.menu {
    /* Menu in Navbar */
}

nav#navbar .menu {
    /* Menu in a nav element with the ID of navbar */
}

.navbar-primary .menu {
    /* This won't work unless you use `!important` */
}
```

In a small codebase, this won’t be a big issue. You can create ever-more-specific rules. But, what if the CSS within your project has been split into many smaller parts, looking to find a rule that is not letting you override this can become an tedious issue. 

The problem isn't just these overly-powerful ID selectors. What about unequally weighted selectors in the stylesheets? You can think of this as if Brock Lesnar is fighting against Hornswoggle. Surely it won’t be a fair contest. This is why specificity is such a big headache.

## **The** **“****Cascade****”** **of CSS**

Cascade is a core part of the CSS and undoubtedly very useful. Even if you don’t like it, you can’t ignore the fact that it allows rules equal in value nested within the CSS that gets applied to the parent rules. The problem is, within large scale projects, the cascade will inevitably become undesirable one day. According to [Ben Frain’s](https://twitter.com/benfrain) book, [Enduring CSS](https://leanpub.com/enduringcss):

> This can happen for a number of reasons. As an example, authors more familiar with other languages often lack the confidence or intimate knowledge of the CSS codebase to be able to confidently remove or amend existing code. They therefore take the safe option and override existing rules using a more specific set of rules. In practical terms this means adding the new rules, with whatever selectors are necessary to get the job done, to the bottom of the existing styles. The problem with leaning on the cascade in this way is that over time and iteration, the CSS code becomes bloated with redundant rules. The consumers of this CSS (the users) are downloading CSS full of cruft that their browser simply doesn't need.

## **What BEM/BEMIT solved?**

[Block Element Modifier](https://en.bem.info/methodology/quick-start/) [(BEM)](https://en.bem.info/methodology/quick-start/) is a front-end naming methodology, a simple naming convention that helps you create reusable components. It's easy to understand, reusable, scalable, modular and very flexible as it can be recomposed and configured into the way you like. It encourages semantic thinking and doesn’t need you to implement global styles like SMACSS neither you need those CSS resets or normalization. 

BEM is very robust and explicit, and a lot more strict then methodologies like SMACSS. This approach looks to ensure that whoever is participating in the development of the project should be working with a single codebase. This means that there are more transparency and meaning to other developers working on that very same project.

![Image Source: Quora](https://d2mxuefqeaa7sj.cloudfront.net/s_043E65AF3EF3BB37503CCB5A25DF5E327B4531B8593ACA920CB59E98A1EC0C1A_1506331166365_quora-bem.png)

BEM makes CSS performance great as it makes your styling independent of your DOM nesting and your code becomes more modular. This would mean that the browser has less amount of evaluation and your CSS will render fast. BEM helps a lot to make the CSS Environment Agnostic. It is arguably a quick way to deliver prototypes and helps you make the right decision (ensures that you plan) and most importantly this is semantic. 

```scss
.modal {} /* Block */
.modal__item {} /* Element */
.modal--large {} /* Modifier */
```

And there is [BEMIT](https://csswizardry.com/2015/08/bemit-taking-the-bem-naming-convention-a-step-further/) (BEM + ITCSS),

```scss
.o-media-object__image {} // Object, Block, Element
.c-modal--wide@large {} // Component, Block, Modifier, Responsive Breakpoint
.c-accordion__title--vertical {} // Component, Block, Element, Modifier 
.u-text-center {} // Utility, Block
.t-default {} // Theme, Block
.s-healthchecks {} // Scope, Block
._c-footer {} // Hack, Component, Block

// Same Approach like SMACSS for state
.is-active {} // state
.has-dropdown {} // state
```

Another thing BEMIT conventional BEM is responsive suffixes. Add `@{breakpoint}` to a class to affect at what size the class takes effect:

```html
<div class="o-bordered@md c-modal c-modal--primary">
  <img src="" class="o-bordered__image@md  c-modal__image" />
  <p class="o-bordered__content@md c-modal__content">...</p>
</div>
```
    
## **Downsides of BEM**

Before I add my viewpoint, here's what another [Harry](https://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) says:

> A common argument against BEM is that it’s ugly; I dare say that if you shy away from code based purely on its looks then you’re often missing the point. Unless the code becomes unnecessarily difficult to maintain, or genuinely more difficult to read, then perhaps you do need to think twice before using it, but if it ‘just looks odd’ but has a valid purpose, then it should definitely be fully considered before writing it off.

If you ask me, “ugly” is not a problem. The heart of the matter is that the class name,  `modal__item__heading` is verbose and has to be repeated a lot in HTML.  The markup becomes bloated with all those long class names and especially those double underscores `__` or double hyphens `--` that developers will never like.  Also, people seem to dislike BEM as it impacts text selection when working within an editor.  

We can’t ignore the fact that long class names increase bytes in your CSS and HTML (though minification and compression reduce the problem).  Also, I feel that BEM involves a lot of code duplication within the project, though it’s true that it can be easily mitigated with the use of mixins within preprocessors like Sass like this below [example](https://www.sassmeister.com/gist/53216fa13acc13d8ae3e5df4cad75982) :

```scss
.accordion {
  /* CSS for Block `.accordion` goes here */
  &__item {
    /* CSS for Element `.accordion__item` goes here */ 
  }
  &--primary {
    /* CSS for Modifier `.accordion--primary` goes here */ 
  }
}
```
    
## **App compiler’s to the rescue!**

*Disclaimer: Please note that App compiler is just an idea coined by me and doesn’t have a real implementation. That said, I have talked with some experienced JavaScript developers and they feel that though it may take a lot of tooling, it’s possible. I know Vue.js and React.js a good bit so definitely possible in atleast these two for sure.*

We can't hide the stuff that it’s a very long HTML, and one solution to this can be to make something that I call as an App Framework Level Compiler. Any JavaScript framework like Angular, Vue, Ember or React can help you make this kind of stuff with ease. Yes, it involves a significant amount of tooling requirement at the usage level, but we can’t ignore the fact that the output is sweet. Here is a small example in Vue.js on how it will look like if you choose this option. 

A Semantic Vue would be something like this,

```html
<accordion :modifier="{primary, large}">
  <item :parent="accordion" :objects="{bordered, rounded}">
    <heading :parent="{accordion, item}">
      Accordion Heading
    </heading>
    <content :parent="{accordion, item}">
      Lorem ipsum dolor
    </content>
  </item>
</accordion>
```    

which will automatically convert the code into a BEMIT code that look like this within the DOM

```html
<div class="c-accordion c-accordion--primary c-accordion--large">
  <div class="c-accordion__item o-bordered o-rounded">
    <div class="c-accordion__item__heading">
      Accordion Heading
    </div>
    <div class="c-accordion__item__content">
      Lorem ipsum dolor
    </div>
  </div>
</div>
```   

**Some Downsides**: The problem with these compilers is that it may not be that easy (though it can happen) to create these type of compilers for other programming languages like Ruby, Java, Php and their respective frameworks such as Rails, Wordpress, Laravel, etc.

## **Further Reading**
  - https://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/
  - https://csswizardry.com/2015/08/bemit-taking-the-bem-naming-convention-a-step-further/

## **Wrapping Up**

Hope you liked my view point on BEM. This is just the first part of the story, [next up](https://tech.io/playgrounds/9923/the-state-of-css-specificity-part-2) I will cover up the CSS Methodology coming from JavaScript Community which according to me (yes, opinionated) can become the **worst** approach if separation of concerns is not taken care of. Next up we will cover CSS in JS and will also talk about how you can take care of separation of concerns within your JavaScript with ease.

**Thanks a lot**, If you liked my article and also my passion for teaching and want to say hello… my twitter handle is [@harmanmanchanda](https://bit.ly/tw-harry). My DM’s are open to the public so just hit me up.
