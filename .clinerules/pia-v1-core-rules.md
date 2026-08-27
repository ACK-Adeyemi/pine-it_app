# Core Rules for Pine It app v1
----
This file changing constantly so always re-read it.
Commit your changes to git after each POC Revision implementation.

- Changes to files are only allowed in the pine-it_app folder and it's sub folders.
- Commands can only be executed within the context of the pine-it_app folder contents and it's sub folders and their contents. 

## Initial Details for the App to create documentation

  - Using Flutter and Cline free agents, build Production ready better version of Gumtree, FB Martketplace, AutoTrader, and motors.co.uk / Cazoo combined!
  - Monolith or microservice? Let’s plan the architecture first then decide
  - Gap in the market - FB Marketplace require FB account, Gumtree has terrible filters and search functionality, motors.co.uk isn’t an app, AutoTrader is too expensive 
  - Cazoo is the core competition hoewever they aren't focused on drawing in Ad listings from other sites right now.
  - Pine It should allow people to sell cars easily for cheap across all platforms, low barrier to entry, but also sell anything - General market place that has the strongest filter engine on the market!
  - I want the database and its assets to be easily migratable across any provider. E.g. to go on prem to Azure, should have minimal issues. As straightforward as possible export import.
  - It needs to be as fast as the competitors are. Doesn’t need to be faster perf wise.
  - Also harvest all the other platform’s Ads and display on this platform - problem, how to get original user / uploader? Just an initial way to show how great the app is? Affiliate traffic? Broker a deal? Not sure need to plan.
  - Core point is to pull people’s attentions away into my app… Away from FB marketplace, AutoTrader, Gumtree, and motors.co.uk.
  - Let's build for Cars first, then include general market items later.
  - UK app initially, aim is to spread all ofer Europe if possible.

### Future Implementation Notes

  - How to display Car insurance info? Partner with Confused.com maybe? How to choose? Let them come to me?
  - Let's implement a show up to x records filter. Display max 100 at a time, give options 10, 20, 30, 50, 100. This of course means, we need to implement pages for the listing page. So also show page numbers with arrow either side below the results. One the phone TBC - need to see what Cazoo did - e.g. Forever scroll?.
  - AI Assistant to help search for cars, doesn't recommend or does? Not sure, but defo helps to automatically set some filters for you as a starting point.
  - Auto fetch details based on reg number - why again?
  - Charging customers to bump their Ads, make them feature, to be on page1 for certain number of hours
  - Blend AutoTraders Ad Profile view page image view where you can quickly scroll though images and get good overview and Cazoo's main pic while bottom images are scrollable and scroll as you swipe. Users should be able to toggle between both views easily while or before looking through the photos.
  - More Sort Options that can be toggled in user settings page
  - Let's add sign in, sign up, guest view, logged in view, favourite ads list, saved searches list, recently viewed, user preferences (light mode, dark mode, custom theme)
  - Need to add mocked images to test photo viewer feature

# Core Competition
  - Motors.co.uk
  - AutoTrader
  - Cazoo
  - Gumtree
  - Facebook Marketplace
  

## For PoC once ducomentation is ready

  - Build an initial html, css, and javascript PoC that is maintained in a single index.html file.

### PoC Revision 1

The PoC needs to be enhanced and enriched with the following features:
  - For Year or Age, min = 1910, max = current year.
  - Users can use filter row above ads list cards (click x the remove filter, the clear all button)
  - In the left filtering pane, the UX and Options should be modelled after motor.co.uk - collapsed when searching and users can multiselect
  - If you're able to access the interface of Cazoo, be sure to plan out the mobile layout so that it matches as this is the best mobile app implementation


### PoC Revision 2

The PoC needs to be enhanced and enriched with the following features:
  - Still missing many filter options like Engine size, Keyword search and much more that motors.co.uk has. Review what the website is using and implement them all. I guess the search bar is behvaing like the keyword search but it doesn't allow you to type 'Qashqai petrol' like it suggests, so need to implement smart keyword search and a delimeter like ',' to create multiple chips e.g. 'Qashqai, petrol'.
  - Missing filters - seats, ULEZ and running costs (annual tax, insurance group, fuel efficiency, and co2 emissions), drivetrain, bhp, EV / Hybrid options (Range, Fast charge time, leased battery, battery has warranty), towing weight (max towing braked, max towing unbraked), Commonly searched vehicle features (Carplay, Bluetooth, Wheelchair access, Leather seats), Safety rating, Number of keys, Has MOT, Vehicle History (Imported, exclude Cat N, or D, or S or C), Vehicle Usage (exlude ex-hire, exclude ex-fleet, only show ex-demo), Dealer rating, Dealer type, P/X Part Exchange available, Sold Status, Only show Ads with multiple images, Reduced Cars, Recently Added. Motors.co.uk places these fields into relevant visually grouped parent sections like 'Advert Options' for the last 4 in the list above. Do the same.
  
### PoC Revision 3
  - The chip filters with the x button: the x / clear button for each filter chip doesn't work when clicked. Clear all works perfectly FYI.
  - The chip filter for year read e.g. 'Year: 2,001 – 2,003' but it should read 'Year: 2001 – 2003'. Fix this.

### PoC Revision 4
  - Let's add collapse all and expand all buttons to control all the filter sections at once.
  - Sort has 'Newest Year' and 'Lowest Mileage' but people may want to make that asc or desc, so lets make those, price, and distance generic and when clicked, an arrow appears next to sort that is clickable and applies the relevant sort. 
  - Let's add a convenient way at the top of the filter section on the left pane, to search for filters using an autocomplete field that will help you find filters you're looking for much easier. It should also have an x in the field that users can click to quickly clear the text. The search text field should remain fixed the way the 'Refine' and the clear button do. Change the name of the filter section from "Refine" to "Filter Options".  

### PoC Revision 5
  - Need to figure out core offering: how to surface the info from other sites into this application. It's mocked for now but let's add 1 real example for the demo for each competitor into the app to showcase the capability. Based on the order of the "Core Competition" list in this doc, let's discuss how to get the data from the apps 1 by 1. Need to figure test and image data. Any questions, ask.

  - **Implemented (v0.2.3, 2026-08-25; remediated v0.2.4, 2026-08-26)** — see ADR-0005. The app now shows a "Live examples from our competitors" strip with one real snapshot per competitor (motors.co.uk, AutoTrader, Cazoo, Gumtree, Facebook Marketplace): real listing facts + a real deep link + mobile app routing. **Image decision:** we NEVER store or hotlink a competitor's photo — every card shows our own representative illustration labelled "Representative image" (Trader v CarGurus case + per-platform ToS review in ADR-0005). Production path = licensed feeds/APIs (AutoTrader Connect, dealer DMS feeds, opt-in FB cross-post), never scraping. **v0.2.4 remediation:** deep links are now verified live single-ad PDPs for motors.co.uk / AutoTrader / Gumtree plus an owner-supplied Cazoo PDP (NaN/undefined showcase bug fixed); FB remains an area-level link with an honest on-card note (login-wall).

### PoC Revision 6
  - Ad posting fields are no where near enough to the competition. Loads of basic fields are missing. Ideally each Ad should have some level of detail relating to the filters available within the app. Review the competition and their implementations so we can be on par here if not better. Any questions, ask.
  - We need to implement strong 'Spares and Repairs' and 'Non Starter' filter options that read the title and description and is able to easily dicern what is a running vehicle and what isn't. Should we consider LLM use here or is it simple enough to have a strong phrase list to match againt?

  - **Implemented (v0.3.0, 2026-08-26)** — see ADR-0006. Posting parity achieved via an **optional collapsible "Specification & running details"** section mapping every filter-engine facet into the post form (blank fields auto-filled deterministically so posted ads stay fully filterable), plus an explicit **Running status selector** — the seller's Spares & Repairs / Non Starter choice **always wins**, auto-detect is only a backstop. Detection itself is a **strong client-side phrase list** (`runningStatus()`, word-boundary matched, precedence Non Starter > Spares & Repairs > Running, `For parts` ⇒ repairs) rather than an LLM — the static single-file PoC (ADR-0001) has no backend or API keys; a server-side LLM assist is recorded as the production enhancement path. A **"Condition & running"** filter section holds **Running status as an honest multi-select (Running · Spares & Repairs · Non Starter)** alongside the relocated **Condition** multi-select — default selection {Running} keeps scrap/spares cars out of the hero feed without any hidden-gate special-casing (restructured in v0.3.2 after the first cut's reveal-toggle counts proved misleading), with amber/red badges on cards and detail, and the detail view renders the full `attrs` spec sheet.

### PoC Revision 7
  - Need to add advertising banners around the site in a way that isn't too obstructive - e.g. ad banner row repeating every 9 car ad cards, and a fixed banner under the nav of which the content and visiblilty can easily be controlled from an external file.Can also have embedded sponsored content that presents itself within the space of a car ad in the app, again not too obstructive so spaced out e.g. every 10 car ads for a sponsored ad. For the ADR we'll need a straightforward management system for this of some kind, ideally an admin UI console. Implement something basic for management for the sake of the PoC, update the Prod doc regarding recommended implementation (e.g. a seperate URL location). Admin console should include ad and account moderation too.
  
### PoC Revision 8
  - Let's figure out a strategy to get Ads from Facebook Marketplace otherwise remove it from this since it requires a real account and is more complex that other competitors.
  