+++
date = "2015-05-15T10:54:40-04:00"
draft = false
title = "contact"
alttitle = "Contact Us"
description = "Lorem ipsum dolor sit amet consectetur."
weight = 5

+++
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
