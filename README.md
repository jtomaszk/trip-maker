#trip-maker
This app enables users of Inbox by Gmail to manually create Trip bundles.
---
Google's [Inbox by Gmail](http://www.google.com/inbox/) is an intuitive email client that aims to support the way we actually use email.  I'm a big fan of the way Inbox groups emails into bundles in the inbox based on context, and brings them to your attention just when you need them.

For example, a Trip bundle is created when you receive a confirmation email for a hotel reservation or a flight.  But there isn't (yet) any way to manually create a trip and add your own emails to the bundle.  This is a pain, because Inbox only recognises reservations from the big boys, like Booking.com and the major airlines - more of why that is later.  Based on a workaround I found [here](http://www.scottgreenstone.com/2016/03/manuallycreatetrips.html),  I decided to implement a simple web application to manually create Trip bundles.  The Google Apps Script code in this repository is the result!

When I first started looking into this, I assumed that Google used some natural language processing witchcraft to parse the body of my email, work out if it was a hotel reservation and extract the details of the booking.  Actually it's not quite as clever as that.  Instead the web giants have collaborated on a set of [standards](http://schema.org) for incorporating structured data into emails and websites.  This data is hidden as JSON formatted name:value pairs between script tags in the html body of the email.  Inbox simply reads details like the check-in and check-out times from the corresponding items. 

But to ensure Inbox only highlights relevant content, it ignores structured data unless [some pretty strict criteria](https://developers.google.com/gmail/markup/registering-with-google#registration_guidelines) are met.

Firstly, the sender of the email must be registered on a whitelist of bulk mailers (Google checks they're not sending spam).  As I only want to set up occasional trips on my own account, it wasn't realistic to get on this list.  Fortunately Inbox does accept structured data in emails sent to and from the same Gmail account for evaluation purposes - this is what makes this app possible.

Secondly, emails must be digitally signed using DKIM or SPF authentication.  I found by inspecting the headers that emails sent from Gmail's API don't have this signature.  But Google's Apps Script does send authenticated messages, so I used this framework instead.

Finally, the structured data must be in the proper format and include a minimum set of fields.

I wanted to implement a reasonably polished front-end, so I've used client-side JavaScript and Bootstrap styling to implement a simple form.  When the user clicks the *Create Trip* button, client-side functions assemble the data from the input fields into a JavaScript object in the proper format.  This object is passed to a server-side function where it is top-and-tailed with the necessary html tags and sent in an email to the user's own address.

I've published the web app [here](https://goo.gl/grOK2u).  You'll need to be signed in to Google and authorise the app to send emails using your Gmail account.  Just fill in the details of the reservation and click *Create Trip*.  An email should arrive in your inbox pretty much immediately, and Inbox should create a new Trip bundle a few minutes (or hours) later... be patient, it works in the end!

NEW! Thanks to [Onlinedispatcher](https://github.com/Onlinedispatcher) for adding a tab for creating flights.
