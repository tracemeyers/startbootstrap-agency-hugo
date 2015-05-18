# startbootstrap-agency-hugo-test

A [Hugo](http://gohugo.io) port of
[Agency](http://startbootstrap.com/template-overviews/agency/), a one page
agency portfolio theme for [Bootstrap](http://getbootstrap.com/) created by
[Start Bootstrap](http://startbootstrap.com/). This theme features several
content sections, a responsive portfolio grid with hover effects, full page
portfolio item modals, a responsive timeline, and a working PHP contact form.

## Getting Started

### Create a Hugo site with the theme

```
hugo new site mysite
cd mysite
mkdir -p themes
git clone themes/<this repo>
```

### Update the site's config

Edit config.toml and add the following:

Configure the header's content
* leadin and heading are the main text displayed
* btn url can be any link (e.g. a link to the first section of the page called services)
* btn title is the button's displayed
```
[Params]
    [Params.header]
        leadin = "Welcome To Our Studio!"
        heading = "It's Nice To Meet You"
        [[Params.header.btn]]
            url = "#services"
            title = "Tell me more"
```

Configure the footer's content
* copyright is the message to display in the bottom left
* btn is a list of buttons to display links and icons in the bottom center
* link is a list of links to add in the bottom right
```
    [Params.footer]
        copyright = "Copyright © Your Website 2015"
        [[Params.footer.btn]]
            url = "#"
            icon = "fa-twitter"

        [[Params.footer.btn]]
            url = "#"
            icon = "fa-facebook"

        [[Params.footer.btn]]
            url = "#"
            icon = "fa-linkedin"

        [[Params.footer.link]]
            url = "#"
            title = "Privacy Policy"

        [[Params.footer.link]]
            url = "#"
            title = "Terms of Use"
```

Configure the modal's common content
* close is the button's text that is displayed at the bottom of each modal
```
    [Params.modal]
        close = "Close Project"
```

### Set the header background

Add your image to 'mysite/static/img/header-bg.jpg' to replace the theme's.

### Add at least one page section

Create a Hugo page containing the one page section's content.  Warning, don't confuse a Hugo section with this
theme's one page section.
```
hugo new section/services.html
```

Edit the page to configure its parameters:
* The title is used as the name in the nav at the top of the page.  If you want
  an expanded title in the section then set the 'alttitle' parameter.
```
title = "Services"                                                              
alttitle = "Our Services"                                                              
```
* The description is displayed under the title.
```
description = "Lorem ipsum dolor sit amet consectetur"                          
```
* The weight defines the order of the section in the page.  Make this be the
  first page by giving it a 1.
```
weight = 1                                                                      
```
* The rowopts can be used to add additional classes to the html tag (e.g.
  centering the text).
```
rowopts = "text-center"                                                         
```
* The optional CSS class to apply to the section.  Typically this used to
  alternate the background of each section.
```
class = "bg-light-gray"
```
                                                                                
#### Section content

The original theme uses 5 types of one page sections.  These are implemented by
a shortcode or series of shortcodes listed in parentheses.

1. Service (service)
2. Portfolio (modalbtn)
3. About (timeline)
4. Team (teammember)
5. Contact (form, textinput, textarea)

In theory these sections can be intermixed.

##### Service

This section displays a series of services provided with an icon, a heading and
a short description.  It can be used in the content area of a page like so:
```
{{% service icon="fa-shopping-cart" heading="E-Commerce" %}}                    
<p class="text-muted">                                                          
Lorem ipsum dolor sit amet, consectetur adipisicing elit. Minima maxime quam arc
hitecto quo inventore harum ex magni, dicta impedit.                            
</p>                                                                            
{{% /service %}}                                                                
```

##### Portfolio (modalbtn)

This section displays a series of portfolio modal buttons, each with their own
icon, heading and subheading.  It can be used in the content area of a page like so:
```
{{% modallist %}}
{{% modalbtn title="Round Icons" %}}
... more modalbtn ..
{{% /modallist %}}
```
The title must exactly match a page's title in the 'modal' Hugo section (i.e.
mysite/layouts/modal/).

The button icon, heading and subheading are taken from the modal page.  The
following is an example of a modal page's parameters that are used for the
icon, heading and subheading respectively:
```
image = "img/portfolio/roundicons.png"
title = "Round Icons"                 
description = "Graphic Design"        
```

##### About (timeline)

This section displays a series of events in a timeline.  It can be used in the
content area of a page like so:
```
{{% timeline section="event" limit="past" %}}
```
In this example it loads the events from the 'event' Hugo section and limits
the display of these events to only ones that have occurred in the past (or
present).  The limit field accepts 'past' and 'future' but if nothing is
provided then it displays all the pages in the given section.

##### Team (teammember)

This section displays a series of team members located in the 'people' Hugo
section.  It can be used in the content area of a page like so:
```
{{% teammember name="Kay Garland" usesocial="true" %}}
```
In this example it loads page whose title matches the given name and displays
the person's social networks.

##### Contact (form, textinput, textarea)

This section displays a form to send a message.  It can be used in the content area of a page like so:
```
{{< form name="sentMessage" id="contactForm" button="Send Message" >}}
<div class="col-md-6">
{{< textinput type="text"  placeholder="Your Name *"  id="name"  message="Please enter your name." >}}
{{< textinput type="email" placeholder="Your Email *" id="email" message="Please enter your email address." >}}
{{< textinput type="tel"   placeholder="Your Phone *" id="phone" message="Please enter your phone number." >}}
</div>
<div class="col-md-6">
{{< textarea placeholder="Your Message *" id="message" message="Please enter your message." >}}
</div>
{{< /form >}}
```

## Advanced configuration

### Modifying CSS

The theme's CSS is built using [less](http://lesscss.org/).  To build the theme's CSS use the following command:
```
lessc static/less/agency.less > static/css/agency.css
``` 

#### Basic color scheme changes

Open static/less/variables.less and edit the colors as desired.  For example, to change the theme to be blue instead of yellow and have a darker light gray then add the following:
```
@brand-primary: #4c6aa9;
@brand-danger: #193776;
@gray-darkest: #222;
@gray: #777;
@gray-lighter: #e8e8e8;
@placeholder-text: #bbbbbb;
```
Run lessc as listed before.


