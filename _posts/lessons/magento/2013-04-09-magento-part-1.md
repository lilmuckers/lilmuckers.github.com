---
layout: post
category : magento
title : "Magento Development Guide - Part 1"
tagline: "Conciliation"
tags : [tutorial, magento, php, best practices]
summary: "Basic concepts a good magento developer should follow"
image: /assets/images/posts/magento-part-1/header.png
---
{% include JB/setup %}

## Introduction

Put yourself in the position of someone who has never touched Magento before, and all that you know of it is what you have read on the website, now imagine you’ve never done any eCommerce development in your life, and you consider the Zend Framework it be black magic and witchcraft. If this describes you then kindly turn around and leave, Magento isn’t something you can just pick up from scratch without any foreknowledge of the territory.

If you’re anything like me, however, you’ll read that warning and go “Pah! I can do it, what do you know about my abilities?” but stand warned that although I am now a seasoned and practised Magento developer, with dozens of sites under my belt, it did come at the cost of a small piece of my sanity, and 2 months in bed with a broken leg (oh my is there a story there…).

If you’re still reading then well done, you’ve managed to plough through my tawdry words and phrases and you still want to hear what nuggets of wisdom (or perhaps not) I am about to spout about the eCommerce platform that has brought me such joy, and so much pain.

I call this section "Conciliation" (to make compatible) because I’m going to deal with something that so many new Magento developers neglect, and that’s making sure their code is within the bounds of Good Practise (a phrase that’s banded around like so much ice-cream-and-jelly in development circles).

---

### Your Target Audience

There are two people you should be making your code accessible to. Advance warning, however, this may sound like I’m spouting abhorrent words more suited to a wondering vagrant with a unspecified items in an old Tescos carrier bag, shouting at passing cars, rather than being massaged into a wordpress blog by a seasoned PHP developer, but stick with me, you’ll enjoy the ride.

 1. **Present You** – Obviously, you’re writing for you, here, now. Isn’t life so much more satisfying when you can sit back and go “damn, that’s some good looking code!” after finishing something?
 2. **Future You** – Someone’s going to have to maintain, extend and adapt this code. It very well might be you! Do you really think that 3 months and 2 projects down the line you’ll remember everything about how something works? So make it easy on yourself! write it in such a way that you’ll be able to easily figure out how it works! At least make it so that you can find what needs doing with judicious use of **grep**

---

### The Most Important Rule First

Do not, I repeat, do **NOT!** directly modify core code. This is crucial for a number of very good practical reasons, as well as wishy-washy Good Practice reasons. I can’t count the number of times I’ve had issues on websites where a previous developer has dived into the core code like a child into gravy (just me?), fixed something for their particular use-case, and completely banjaxed other critical functionality. So here follows the main reasons not to modify core code:

 1. **Upgrades** – If you modify core code, later updates to your Magento install can easily overwrite it, leaving your custom functionality malfunctioning.
 2. **Maintainability** – Say your change was a single modification to a line of code, what if down the line this needs changed, how is future-you to know or remember what was changed, and what is core and what isn’t core.
 3. **Side-effects** – For example – you want to change how prices are displayed on product pages, so you tweak the price renderer. Not realising that whilst this fixes you issue on the product page, it will make the same modifications to the checkout, cart, wishlist, admin section, etc. This is an extreme situation but Magento reuses code a heck of a lot all over the place, changing it for one thing could have outcomes system-wide.

Hopefully most of you are reading this nodding along in agreement, or looking perplexed as to why I’m stating such obvious trivialities, but it never ceases to amaze me how many people ignore such a simple part of the process. I will agree that sometimes it seems silly to create a whole new module to change a single line of core code, but the advantages outweigh the time it takes.

---

### Events

Learn to love them. Simply put, a well used event hook can save you hours of recoding to override a core function.  I’ll cover how events work in a later guide, but so for now I’ll simply list some of the benefits of using event handlers rather than overriding the core functionality.

 1. **Upgrades** – Using event handlers means you won’t be modifying core code at all, and the core code will still be being used. Any changes to the core code in later updates to the core will be taken into account with your site, so you will always be on the latest version of the code.
 2. **Efficiency** – Writing a simple event handler is much quicker than doing a full functional override, so you get the benefits of not modifying core code, but you are able to do it much more quickly.
 3. **Extensions** – Core code can only be overridden once. If you install or write an extension that has to override core code, then your event based code will still work. Whereas if two modules try to override the same functionality, only one of them will win – and which one does win depends on the scope and name of the module.

I love using events to work, it makes my life so much easier. Sometimes, if I need to modify core functionality and there isn’t an event being fired that I can hook onto, I will override the code simply to put an event in there. This is because I can use an event handler multiple times to the same effect and not have to worry about it in future (this can have other problems in terms of compatibility, but I can deal with that on a case-by-case basis).

---

### Naming

This sounds obvious, and silly, but keeping to a logical naming convention can save you a lot of bother further down the road. Making sure that everything is namespaced appropriately can save you hours of headaches during the maintenance phase of the site. This might mean you end up with a tree of directories with a simple PHP file at the bottom just so you can namespace it correctly, but in the future it will mean you will have a contextual way of knowing what this file does just by looking at where it is located.

For example, in my time i have seen good and bad and overdone namespacing in a Magento project (and yes, some of them might have been me, but I like to gloss over those instances). So follows a quick Good/Bad comparison of class names.


<div class="span6">
  <h4>Good</h4>
  <p><strong>PatrickMcKinley_Blog_Model_Post_Image_Type</strong> – You can quickly tell, just by the classname, what this class is responsible for. It is clearly a model for handling the image type on a post. This means that if there’s an issue with the code that handles the image type then you can quickly jump to the code that is causing the issue.</p>
  <p><strong>PatrickMcKinley_Blog_Post_CommentController</strong> - Following the class naming paradigm of controllers, you can tell that this controller is for managing the comments attached to a blogpost. This doesn’t follow the usual Zend/Magento name-spacing as it is a controller, and you don’t (generally) want this to be auto-loadable.</p>
</div>
<div class="span6">
  <h4>Bad</h4>
  <p><strong>MySite_Derp_Model_Biscuit</strong> – What, pray tell, is a "derp" and what does it relate to? There’s no easy way of telling just by looking at the directory structure and the classname what this class does. Presumably it does something to do with “Biscuits” that relates to “Derp” but what exactly is that all about? I always like to think of things like this as if someone else is going to have to maintain it, even if it’s personal work, because after-all; <strong>Future You</strong> is a different person to <strong>Present You</strong>. You know it to be true, Search your feelings...</p>
</div>
<div class="span12">
  <h4>In Summary...</h4>
  <p>...using a good naming convention in your code can save Future You a lot of headaches when it comes to modifying or maintaining the code should problems crop up. I know, as developers, we always strive to write flawless code every time, but we all know that never happens – there is always a flaw, somewhere.</p>
</div>

<hr style="clear:both;">

### Next time on "Patrick McKinleys guide to Magento Development&trade;"
 * Gasp as Patrick launches into an irreverent tirade about "best practices"
 * Be stunned by examples on module creation and patterns.

