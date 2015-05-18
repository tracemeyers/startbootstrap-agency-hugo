# How startbootstrap-agency was ported to Hugo

## Disclaimer

This was my first crack at a Hugo theme so I likely made many choices that at
best not the Hugo way or at worst stupid.

## Goal

Create a 1:1 port, or at least as close as possible, of the original theme
while expanding it to easily support customization.

Note that the page is often different because of additional HTML tags
introduced by the markdown processor when using shortcodes.  Ideally we could
fix them by identifying the deviations by diffing (ignore whitespace) the
original theme against the generated theme.  

## Steps

### (Optional) Create a repo

```
cd <hugo site>/themes
git clone https://github.com/tracemeyers/startbootstrap-agency-hugo
```

### Create empty hugo theme

Create it from the Hugo site's base directory.
**If using a repo then create the theme in a temporary directory and move the
files to the repo.**
```
cd <hugo site>
hugo new theme startbootstrap-agency-hugo
```


### Hugo-ify the original theme

1. Clone the original theme:
```
git clone https://github.com/IronSummitMedia/startbootstrap-agency
```

2. Move the following files to 'startbootstrap-agency-hugo/static/':
```
startbootstrap-agency/css/ startbootstrap-agency/font-awesome/ startbootstrap-agency/fonts/ startbootstrap-agency/img/ startbootstrap-agency/js/ startbootstrap-agency/less/ startbootstrap-agency/mail/
```

3. Move 'startbootstrap-agency/index.html' to 'startbootstrap-agency-hugo/layouts/index.html'

4. Remove original theme

5. Test it

Go to the address printed out by this command.  You should see a site that is
identical to the original theme.
```
hugo server --watch --theme=startbootsrap-agency-hugo
```


### Create base partials

This section extracts portions of index.html into separate HTML files.  This
will eventually ease the customization of the site.

#### head.html

The 'head' defines the site.  The only customization needed is the name (i.e.
the name that is displayed in the browser tab).

1. Extract the content between the 'head' tags from index.html and put in 'head.html'.  Replace with
```
   {{ partial "head.html" . }}
```
2. Replace the content between the 'title' tags to look like this:
```
   <title>{{ .Site.Title }}</title>
```


#### nav.html

1. Extract the content between the 'nav' tags from index.html and put in 'nav.html'.  Replace with
```
   {{ partial "nav.html" . }}
```
2. Make the top left site name be configurable by the site.  Replace the following:
```
   <a class="navbar-brand page-scroll" href="#page-top">Start Bootstrap</a>
```
with
```
   <a class="navbar-brand page-scroll" href="#page-top">{{ .Site.Title }}</a>
```
3. Make the nav links load from a Hugo section confusingly named 'section' (it
comes from the HTML tag).  Each 'section' uses the page's title as the nav link
name.  A Replace the following:
```
   <li>
       <a class="page-scroll" href="#services">Services</a>
   </li>
   <li>
       <a class="page-scroll" href="#portfolio">Portfolio</a>
   </li>
   <li>
       <a class="page-scroll" href="#about">About</a>
   </li>
   <li>
       <a class="page-scroll" href="#team">Team</a>
   </li>
   <li>
       <a class="page-scroll" href="#contact">Contact</a>
   </li>
```
with
```
   {{ range where .Data.Pages.ByWeight "Type" "section" }}
   <li>
       <a class="page-scroll" href="#{{ .Title | urlize }}">{{ .Title }}</a>
   </li>
   {{ end }}
```
Note the use of urlize in href in case the title has spaces or something else
that isn't safe.
4. Create the nav link pages in the Hugo section also named section (i.e.
content/section/<page>).  For each page add 'weight = 1' and increment the
value for each value.
```
hugo new section/services.md
hugo new section/portfolio.md
hugo new section/about.md
hugo new section/team.md
hugo new section/contact.md
```


#### header.html

The 'header' is the big main thing you see when you visit a site using this
theme.  It displays the large background picture with the main headline for the
page.

1. Extract the content between the 'header' tags from index.html and put in 'header.html'.  Replace with
```
   {{ partial "header.html" . }}
```
2. Add the following to your site's config (update the text as you want):
```
    [Params]
        [Params.header]
            leadin = "Welcome To Our Studio!"
            heading = "It's Nice To Meet You"
            [[Params.header.btn]]
                url = "#services"              
                title = "Tell me more"   
```
3. Add Hugo customization sourced from the site config. Replace the following:
```
        <div class="intro-lead-in">Welcome To Our Studio!</div>
        <div class="intro-heading">It's Nice To Meet You</div>
        <a href="#services" class="page-scroll btn btn-xl">Tell Me More</a>
```
with
```
       <div class="intro-lead-in">{{ .Site.Params.header.leadin }}</div>
       <div class="intro-heading">{{ .Site.Params.header.heading }}</div>
       {{ range .Site.Params.header.btn }}
       <a href="{{ .url }}" class="page-scroll btn btn-xl">{{ .title }}</a>
       {{ end }}
```


#### footer.html

The 'footer' is the exactly what you expect: the last thing on each page in the
site.

1. Extract the content between the 'footer' tags from index.html and put in 'footer.html'.  Replace with
```
   {{ partial "footer.html" . }}
```
2. Add the following to your site's config (update the text as you want):
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
3. Add Hugo customization sourced from the site config.
    1. Copyright
```
       <span class="copyright">Copyright &copy; Your Website 2014</span>
```
       with
```
       <span class="copyright">{{ .Site.Params.footer.copyright }}</span>

    2. Buttons
```
           <li><a href="#"><i class="fa fa-twitter"></i></a>
           </li>
           <li><a href="#"><i class="fa fa-facebook"></i></a>
           </li>
           <li><a href="#"><i class="fa fa-linkedin"></i></a>
           </li>
```
with
```
           {{ range .Site.Params.footer.btn }}
           <li><a href="{{ .url }}"><i class="fa {{ .icon }}"></i></a>
           </li>
           {{ end }}
```

    3. Links
```
           <li><a href="#">Privacy Policy</a>
           </li>
           <li><a href="#">Terms of Use</a>
           </li>
```
with
```
           {{ range .Site.Params.footer.link}}
           <li><a href="{{ .url }}">{{ .title }}</a>
           </li>
           {{ end }}
```


### Create Section Partial

The HTML section tag is what creates the clearly defined sections of a one page
theme like Agency.  Therefore we'll try to keep it DRY as possible by using a
partial that contains the common code while each section will use shortcodes
for the content.

1. Modify index.html to load each HTML section from the Hugo section
unsurprisingly named 'section'.  The order is determined by the section's
weight.  Add the following to index.html just after the closing 'header' tag:
```
{{ range where .Site.Pages.ByWeight "Section" "section" }}
    {{ partial "section.html" . }}
{{ end }}
```
2. Create layouts/partials/section.html and add customization.  If you look at
the original theme you'll notice this is common to all sections.
```
<section id="{{ .Title | urlize }}" class="{{ .Params.class }}">
    <div class="container">
        <div class="row">
            <div class="col-lg-12 text-center">
                <h2 class="section-heading">{{ .Title }}</h2>
                <h3 class="section-subheading text-muted">{{ .Description }}</h3>
            </div>
        </div>
        <div class="row {{ .Params.rowopts }}">
            {{ .Content }}
        </div>
    </div>
</section>
```
Note the following customizations:
* Page title becomes the section id.
* class to use for the section.  It is optional.
  * Typcial use is to set the background (e.g. bg-light-gray).
* title and description are the text at the top of the section.
* rowopts alters the content's row.  It is optional.
  * Typcial use is to set the text alignment (e.g. text-center).
3. Verify the page added empty sections like "Services", "Portfolio", etc.

#### Services

1. Set the site's content/section/services.md parameters to match the original
theme:
    1. Set page's heading and subheading:
    ```
    title = "Services"
    description = "Lorem ipsum dolor sit amet consectetur"
    ```
    2. Center the text of each service:
    ```
    rowopts = "text-center"
    ```
2. Load the service data
    1. How should the service data be loaded?
        1. Option #1: All content in a single page.
            * Pro: All data in one place.
            * Con: Shortcode is hard to read.
            ```
            {{% service icon="fa-map" %}}
                #### Title
                {{% textmuted %}}
                    Some description
                {{% /textmuted %}}
            {{% /service %}}
            ```
        2. Option #2: Content is put in other pages.
            * Pro: Easy to configure since each service is just a page.
            * Con: Cannot see all the services and their content in one file.
            ```
            {{% service "page name" %}}
            ```
    2. Load using option #1 just because I can't decide which I like better.
        1. Create the shortcode layouts/shortcodes/service.html:
        ```
        <div class="col-md-4">
            <span class="fa-stack fa-2x">
                {{ if .Get "icon" }}
                <i class="fa fa-circle fa-stack-2x text-primary"></i>
                <i class="fa {{ .Get "icon" }} fa-stack-1x fa-inverse"></i>
                {{ end }}
            </span>
            <h4 class="service-heading">{{ .Get "heading" }}</h4>
            {{ .Inner }}
        </div>
        ```
        2. Create the services in the site's content/section/services.md
        ```
        {{% service icon="fa-shopping-cart" heading="E-Commerce" %}}
        <p class="text-muted">
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Minima maxime quam architecto quo inventore harum ex magni, dicta impedit.
        </p>
        {{% /service %}}

        {{% service icon="fa-laptop" heading="Responsive Design" %}}
        <p class="text-muted">
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Minima maxime quam architecto quo inventore harum ex magni, dicta impedit.
        </p>
        {{% /service %}}

        {{% service icon="fa-lock" heading="Web Security" %}}
        <p class="text-muted">
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Minima maxime quam architecto quo inventore harum ex magni, dicta impedit.
        </p>
        {{% /service %}}
        ```
3. Remove the old services section from layouts/index.html

#### Portfolio

1. Set the site's content/section/portfolio.md parameters to match the original
theme:
    1. Set page's heading and subheading:
    ```
    title = "Portfolio"
    description = "Lorem ipsum dolor sit amet consectetur"
    ```
    2. Set background color and center the text of each modal button:
    ```
    class = "bg-light-gray"
    rowopts = "text-center"
    ```
2. Load the portfolio data
    1. Create the modal pages used to source the data.  Here's an example of
    the first modal page:
        1. Create the page
        ```
        hugo new modal/Round-Icons.md
        ```
        2. Set the title, description and image that will be displayed in the
        portfolio link.
        ```
        title = "Round Icons"
        description = "Graphic Design"
        image = "img/portfolio/roundicons.png"
        ```
    2. Create a Hugo shortcode layouts/shortcodes/modalbtn.html to display
    a modal button.  The only argument the title of the modal.
    ```
    <div class="col-md-4 col-sm-6 portfolio-item">
        {{ $id := (.Get "title" | urlize) }}
        {{ range where (where .Page.Site.Pages "Section" "modal") "Title" $id }}
        <a href="#{{ $id }}" class="portfolio-link" data-toggle="modal">
            <div class="portfolio-hover">
                <div class="portfolio-hover-content">
                    <i class="fa fa-plus fa-3x"></i>
                </div>
            </div>
            <img src="{{ .Params.image }}" class="img-responsive" alt="">
        </a>
        <div class="portfolio-caption">
            <h4>{{ .Title }}</h4>
            <p class="text-muted">
                {{ .Description }}
            </p>
        </div>
        {{ end }}
    </div>
    ```
    2. Edit <site>/content/section/portfolio.md to add the content.
    ```
    {{% modalbtn title="Round Icons" %}}
    ...
    ```
3. Remove the old portfolio section from layouts/index.html

#### About

1. Set the site's content/section/about.md parameters to match the original
theme:
    1. Set page's heading and subheading:
    ```
    title = "About"
    description = "Lorem ipsum dolor sit amet consectetur."
    ```
2. Load the About data
    1. Create the event pages used to source the data.  Here's an example of
    the first timeline page:
        1. Create the page
        ```
        hugo new event/Our-Humble-Beginnings.md
        hugo new event/An-Agency-Is-Born.md
        ...
        ```
        2. Set the date, description and image that will be displayed in the
        event.  Note that the date variable will control whether the event is
        displayed under some configurations.  Here's an example for the first
            event.
        ```
        date = "2009-01-01T00:00:00-00:00"
        description = "2009-2011"
        image = "img/about/1.jpg"
        ```
        3. Set the content of the event.
        ```
        <p class="text-muted">
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Sunt ut voluptatum eius sapiente, totam reiciendis temporibus qui quibusdam, recusandae sit vero unde, sed, incidunt et ea quo dolore laudantium consectetur!
        </p>
        ```
    2. Create a Hugo shortcode layouts/shortcodes/timeline.html to display
    events.  The arguments are:
        * section: The Hugo section the events are loaded from.
        * limit: Optional, limits the events to "past" or "future".  Otherwise
          all events in the Hugo section are displayed.
    Note that there is probably a better way to simplify the logic here so let
    me know if you have one.
    ```
    <div class="col-lg-12">
        <ul class="timeline">
            {{ $section := .Get "section" }}
            {{ $limit := .Get "limit" }}

            {{ range $i, $event := (where .Page.Site.Pages "Section" $section).Reverse }

            {{ $.Page.Scratch.Set "addevent" true }}
            {{ if eq $limit "past" }}
                {{ $.Page.Scratch.Set "addevent" (le .Date.Unix .Now.Unix) }}
            {{ else if eq $limit "future" }}
                {{ $.Page.Scratch.Set "addevent" (ge .Date.Unix .Now.Unix) }}
            {{ end }}

            {{ if ($.Page.Scratch.Get "addevent") }}
            <li class="timeline{{ if not (modBool $i 2) }}-inverted{{ end }}">
            <div class="timeline-image">
                <img class="img-circle img-responsive" src="{{ .Params.image }}" alt="">
            </div>
            <div class="timeline-panel">
                <div class="timeline-body">
                    <div class="timeline-heading">
                        <h4>{{ .Description }}</h4>
                        <h4 class="subheading">{{ .Title }}</h4>
                    </div>
                    {{ .Content }}
                </div>
            </div>
            </li>
            {{ end }}
            {{ end }}
        </ul>
    </div>
    ```
    3. Edit <site>/content/section/about.md to add the content.
    ```
    {{% timeline section="event" limit="past" %}}
    ```
3. Remove the old about section from layouts/index.html

#### Team

1. Set the site's content/section/team.md parameters to match the original
theme:
    1. Set page's heading and subheading:
    ```
    title = "Team"
    description = "Lorem ipsum dolor sit amet consectetur."
    ```
2. Load the Team data
    1. Create the team member pages used to source the data.  Here's an example
    of the first:
        1. Create the page
        ```
        hugo new event/Kay-Garland.md
        ...
        ```
        2. Set the description, image and social links that will be displayed.
        ```
        description = "Lead Designer"
        image = "img/team/1.jpg"
        class = "bg-light-gray"
        [[social]]
            url = "#"
            icon = "fa-twitter"
        [[social]]
            url = "#"
            icon = "fa-facebook"
        [[social]]
            url = "#"
            icon = "fa-linkedin"
        ```
        Note that the page's content for the team member is not used for the
        team section.  The page's content could have defined how to display
        itself with the side effect that some team members may display
        themselves differently.  Additionally the team member's page content
        could provide detailed information on a separate page.  Therefore it
        was decided to let the team section and a shortcode determine the
        layout.
    2. Create a Hugo shortcode layouts/shortcodes/teammember.html to display
    team members.  The arguments are:
        * name: The name of the team member matched against the Title
          parameter.
        * role: Optional, override the text underneath the name that is
          typically loaded from the page's Description parameter.
        * usesocial: Optional, set to "true" to display the social buttons.
    ```
    {{ $section := "people" }}
    {{ $name := .Get "name" }}
    {{ $role := .Get "role" }}
    {{ $usesocial := .Get "usesocial" }}

    {{ range where (where .Page.Site.Pages "Section" $section) "Title" $name }}
    <div class="col-sm-4">
        <div class="team-member">
            <img src="{{ .Params.image }}" class="img-responsive img-circle" alt="">
            <h4>{{ .Title }}</h4>
            <p class="text-muted">
            {{ if $role }}
                {{ $role }}
            {{ else }}
                {{ .Description }}
            {{ end }}
            </p>
            {{ if $usesocial }}
            <ul class="list-inline social-buttons">
                {{ range .Params.social }}
                <li><a href="{{ .url }}"><i class="fa {{ .icon }}"></i></a>
                </li>
                {{ end }}
            </ul>
            {{ end }}
        </div>
    </div>
    {{ end }}
    ```
    3. Edit <site>/content/section/team.md to add the content.
    ```
    {{% teammember name="Kay Garland" social="true" %}}
    ...
    ```
3. Add optional alternative section title.  The original theme uses "Our
Amazing Team" for the section title even though the official section name is
just "Team".
    1. Edit layouts/partial/section.html and replace the section heading with
    the following:
    ```
     <h2 class="section-heading">
         {{ if .Params.alttitle }}
             {{ .Params.alttitle }}
         {{ else }}
             {{ .Title }}
         {{ end }}
     </h2>
    ```
    2. Edit <site>/content/section/team.md to add the alternative title:
    ```
    alttitle = "Our Amazing Team"
    ```
4. Remove the old about section from layouts/index.html

#### Contact

This was not a highly desirable feature for me when porting the theme so I
haven't tested it.

1. Set the site's content/section/contact.md parameters to match the original
theme:
    1. Set page's heading and subheading:
    ```
    title = "Contact"
    alttitle = "Contact Us"
    description = "Lorem ipsum dolor sit amet consectetur."
    weight = 5
    ```
2. Create the shortcodes for the form.
    1. Create a 'form' block in layouts/shortcodes/form.html.  The layout of
    the form excluding the submit button is customizable via other shortcodes.
    ```
                {{ $name := .Get "name" }}
                {{ $id := .Get "id" }}
                {{ $button := .Get "button" }}
                <form name="{{ $name }}" id="{{ $id }}" novalidate>
                    <div class="row">
                        {{ .Inner }}
                        <div class="clearfix"></div>
                        <div class="col-lg-12 text-center">
                            <div id="success"></div>
                            <button type="submit" class="btn btn-xl">{{ $button }}</button>
                        </div>
                    </div>
                </form>
    ```
    2. Create a 'textinput' in layouts/shortcodes/textinput.html.  This is used
    for the name, email, etc..
    ```
                            {{ $type := .Get "type" }}
                            {{ $ph := .Get "placeholder" }}
                            {{ $id := .Get "id" }}
                            {{ $msg := .Get "message" }}
                            <div class="form-group">
                                <input type="{{ $type }}" class="form-control" placeholder="{{ $ph }}" id="{{ $id }}" required data-validation-required-message="{{ $msg }}">
                                <p class="help-block text-danger"></p>
                            </div>
    ```
    3. Create a 'textarea' in layouts/shortcodes/textarea.html.  This is used
    for the multiline text field.
    ```
                            {{ $ph := .Get "placeholder" }}
                            {{ $id := .Get "id" }}
                            {{ $msg := .Get "message" }}
                            <div class="form-group">
                                <textarea class="form-control" placeholder="{{ $ph }}" id="{{ $id }}" required data-validation-required-message="{{ $msg }}"></textarea>
                                <p class="help-block text-danger"></p>
                            </div>
    ```
3. Modify the content of <site>/layouts/section/contact.md to generate the form.  Note that it '{{<' in order to skip markdown processing.
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
4. Remove the old contact section from layouts/index.html


### Create the modals

1. Add the following modal parameters to the site's config.
    * close: The text to display in the bottom button that closes the modal.
```
    [Params.modal]
        close = "Close Project"
```
2. Create Hugo partial modal.html that uses the page's title, align(ment),
description, and content as customization.
```
    <div class="portfolio-modal modal fade" id="{{ .Title | urlize}}" tabindex="-1" role="dialog" aria-hidden="true">
        <div class="modal-content">
            <div class="close-modal" data-dismiss="modal">
                <div class="lr">
                    <div class="rl">
                    </div>
                </div>
            </div>
            <div class="container">
                <div class="row" align="{{ .Params.align }}">
                    <div class="col-lg-8 col-lg-offset-2">
                        <div class="modal-body">
                            <!-- Project Details Go Here -->
                            <h2>{{ .Title }}</h2>
                            <p class="item-intro text-muted">{{ .Description }}</p>
                            {{ .Content }}
                            <button type="button" class="btn btn-primary" data-dismiss="modal"><i class="fa fa-times"></i> {{ $.Site.Params.modal.close }}</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
```
3. Update modal pages with an optional alignment parameter for the content.
```
align = "center"
```
4. Update the modal pages with the new content.  Here's an example for the first modal:
```
<img class="img-responsive img-centered" src="img/portfolio/roundicons-free.png" alt="">
Use this area to describe your project. Lorem ipsum dolor sit amet, consectetur adipisicing elit. Est blanditiis dolor em culpa incidunt minus dignissimos deserunt repellat aperiam quasi sunt officia expedita beatae cupiditate, maiores repudiandae, nostrum, reiciendis facere nemo!
                                                                             
**Want these icons in this portfolio item sample?** You can download 60 of them for free, courtesy of
[RoundIcons.com](https://getdpd.com/cart/hoplink/18076?referrer=bvbo4kax5k8ogc)
, or you can purchase the 1500 icon set [here](https://getdpd.com/cart/hoplink/18076?referrer=bvbo4kax5k8ogc).

<ul class="list-inline">
<li>Date: July 2014</li>
<li>Client: Round Icons</li>
<li>Category: Graphic Design</li>
</ul>
```
Note that the HTMl could be converted into shortcodes.  The image is a good example since a number of other pages will probably want that too.  However, I'll leave that for the future.


### TODOs

Some designs were left out for one reason or another.  This lists the known
missing pieces.

#### Why is there a hangin line from the last entry in a timeline?

The original theme had a final timeline entry titled "BE PART OF OUR STORY!" that terminated the timeline.  This could be customaized but I left it out just because I didn't need it and I don't mind the hanging line.

#### Where's the 'Clients' aside?

I have no use for this and it complicates the templating of the 'section' pages.  Therefore I'll leave it as a future enhancement.
