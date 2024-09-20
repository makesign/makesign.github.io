---
title: 'MakeSign Final Product'
date: 2019-02-11T19:27:37+10:00
weight: 8
---

# 3. Final Product

The final product is MakeSign, a website that allows the user to upload slides, add them to slideshows with different lengths and expiration dates, and finally display them on different screens. It also displays the dashboard of the booking system.

![Homepage of MakeSign](/Images/Homepage.png)

*Homepage of MakeSign*

Here we see the homepage, or the root of MakeSign. The application itself can be used without being logged in. You can view the slides, slideshows, and screens, but you cannot edit or create them. To fully use the app, you need to log in using the “Sign In” button.

![Create Page of Slides](/Images/Slides_Createpage.png)

*Create Page of Slides*

After registering, you can upload slides. These can be images (JPG, PNG, etc.) or GIFs and are automatically cropped to 16:9 for display in landscape format. JavaScript is used to change the inner text field of “Insert File” to the name of the image/GIF.

You can then view the slide in 16:9 format. After clicking on the image, you can see the original format via the Bootstrap Modal function. To merge individual slides, you must first create a slideshow and select the slides.

![Create Page of Slideshows](/Images/Slideshows_Createpage.png)

*Create Page of Slideshows*

Slides are selected using a checkbox system. The selected checkboxes are darkened. Once you have selected the title and slides, you are taken directly to the edit page.

![Edit Page of Slideshows](/Images/Slideshows_Editpage.png)

*Edit Page of Slideshows*

This page displays slides that can still be inserted as well as the slides that are already in the slideshow. You can edit the duration (e.g., from 15 seconds to 30 seconds). After saving the changes, the system updates the slideshow automatically. The Raspberry Pi recognizes the changes and updates the display duration on the monitor.

Finished slideshows can be selected in the Screens section to be displayed with the Raspberry Pi. This new function was added after Showtime.

![Edit Page of Screens](/Images/Screens_Editpage.png)

*Edit Page of Screens*

You can select one slideshow per screen. JavaScript limits the selection to one slideshow to avoid displaying multiple slideshows simultaneously. The display page on the Raspberry Pi shows the carousel of the slideshow in full screen.

The URL of the display page is entered into the Raspberry Pi, allowing the carousel to be displayed automatically after starting. Screens can be created corresponding to the number of Raspberry Pis and given a URL to avoid changing it for each slideshow.

To update the slideshow on the Raspberry Pi, a small inline script in the display HTML page refreshes the page every hour:

```jsx
  setTimeout(function() {
    location.reload();
  }, 3600000);

