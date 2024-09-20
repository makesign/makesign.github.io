title: "MakeSign Docs"
date: 2024-09-20
---

# B2 - Digital Signage for the HTW Makerspace

![Makesign_Logo.png](/Images/Makesign_Logo.png)

---

**Members**: Niklas Aporius, Airon Jasinski, Hyun Bin Jeoung, Rudolfs Spridis, Nemo Ziener

**Course**: 9193124 B24.12 Praxisphase 2

**Supervisor:** Martin Holzhauer, Prof. Dr. Barne Kleinen

HTW - University of Applied Science Berlin

---

# 1. Summary

MakeSign is a digital signage solution developed in collaboration with IDiA. It is intended to give the HTW Makerspace a simple way to display the availability of the various tools and 3D printers with a dashboard and to present advertisements and announcements via a slideshow using a program.

With the help of Raspberry Pis, the dashboard and uploaded images can be displayed on monitors at the Makerspace.

The MakeSign program is written in Ruby on Rails, with JavaScript for the dashboard and Bootstrap CSS for the styling.

# 2. Task & Use-Cases

The requirements defined by the Makerspace and IDiA were a program that would make it as easy as possible to upload images and short videos, which would then be displayed on two monitors in the Makerspace. The monitors should also display the utilization of the machines from the booking system. The focus here was on ease of use.

Makerspace's previous solution worked via a Raspberry Pis, but it often went out and showed a black screen. Adding new slides was also complicated.

### Use Case 1: Add new slide with expiration date

Goal: Insert a new slide with an expiration date into a slideshow

Actor: Makerspace

Precondition: Makerspace has created an image with the relevant information, is logged into MakeSign, and the monitor in the Makerspace is connected to a correctly set up Raspberry Pi.

Scenario:

1. Makerspace opens MakeSign and logs in.
2. he navigates to the “Upload Slide” page and uploads the image.
3. after uploading, the slide is automatically cropped in 16:9 format
4. Makerspace creates a new slideshow or adds the slide to an existing slideshow.
5. he sets the display duration of the slide to 20 seconds and specifies an expiration date (e.g. two days later).
6. the changes are saved on the Raspberry Pi and the system automatically recognizes the adjustment.

Postcondition: The slide is displayed in the slideshow on the monitor and automatically removed after the set date has expired.

### Use Case 2: Display Dashboard

Goal: Display machine utilization from the booking system on the monitors

Actor: Makerspace

Precondition: The connection to the booking system is active and the monitor in the Makerspace is connected to a correctly set up Raspberry Pi.

Scenario:

1. Makerspace logs in to MakeSign and navigates to the dashboard
2. Makerspace sets the dashboard to be displayed on the second monitor
3. the Raspberry Pi receives the settings and displays the dashboard.

Postcondition: The monitor displays the machine utilization.

### Use Case 3: Slide bearbeiten und Anzeigedauer anpassen

Goal: Edit an existing slide in the slideshow and adjust its display duration

Actor: Makerspace

Precondition: Makerspace is logged into MakeSign and has already created a slideshow that is displayed on a monitor.

Scenario:

1.  Makerspace logs into MakeSign and goes to the “Slideshow” overview.
2. He selects a slide that is part of a slideshow.
3. He uses the edit function to adjust the duration of the display (e.g. from 15 seconds to 30 seconds).
4. Makerspace saves the changes and the system updates the slideshow automatically.
5. The Raspberry Pi recognizes the changes and updates the display duration on the monitor.

Postcondition: The changed slide is now displayed on the monitor for 30 seconds as part of the slideshow.

# 3. Finale Product

The final product is MakeSign, a website that allows the user to upload slides, add them to slideshows with different lengths and expiration dates and finally display them on different screens. It also displays the dashboard of the booking system.

![*Homepage of MakeSign*](/Images/Homepage.png)

*Homepage of MakeSign*

Here we see the homepage, or the root of MakeSign. The application itself can be used without being logged in. You can view the slides, slideshows and screens, but you cannot edit or create them. To be able to use the app fully, you have to log in using the “Sign In” button.

![*Create Page of Slides*](/Images/Slides_Createpage.png)

*Create Page of Slides*

After you have registered, you can upload slides. These can be images (JPG, PNG etc.) or GIFs and are automatically cropped to 16:9 for display in landscape format. JavaScript is used to change the inner text field of “Insert File” to the name of the image/GIF.

You can then take a closer look at the slide in 16:9 format. After simply clicking on the image, you can see the original format of the image via the Bootstrap Modal function. To merge the individual slides, you must first create a slideshow and select the individual slides.

![*Create Page of Slideshows*](/Images/Slideshows_Createpage.png)

*Create Page of Slideshows*

The slides are selected using a simple checkbox system where you click on the slides themselves and they are then darkened. The checkbox design looks different from Showtime. Here we received feedback that the selected checkboxes were not easy to recognize. Once you have selected the title and slides, you are taken directly to the edit page.

![*Edit Page of Slideshows*](/Images/Slideshows_Editpage.png)

*Edit Page of Slideshows*

This page displays slides that can still be inserted as well as the slides that are already in the slideshow. Here you can edit the duration, i.e. how long the slides are displayed. These range from 10 - 120 seconds. After consultation with the Makerspace during showtime, there is an optional setting for expiry dates, where the slide is automatically removed from the slideshow if the date has been exceeded. This works with the “whenever” gem, which creates cronjobs that we run in a cycle according to our wishes. In this case, the slides with an expired date are removed every day at 11:59 pm. 

```ruby
every :day, at: "11:59pm" do
  rake "slide_mappings:delete_expired"
end
```

On the show page of Slideshow, the slides are displayed with their name, duration and their expiry date so that you do not have to switch to the edit page.

The finished slideshows can be selected in the Screens section so that they can be displayed with the Raspberry Pi. The Screens section is a new function that we added after Showtime, after consultation with Prof. Dr. Barne Kleinen.

![*Edit Page of Screens*](/Images/Screens_Editpage.png)

*Edit Page of Screens*

You can select exactly one slideshow on the screen itself. Using JavaScript code, we have limited the selection of the checkbox to one, so that several slideshows cannot be displayed at the same time. In the show page of the screen there is a button that leads to the display page. The display page is basically what is displayed on the Raspberry Pi at the end. A full screen of the carousel of the slideshow, which is displayed without the navbar. For the carousel itself, we used the template provided by Bootstrap: [https://getbootstrap.com/docs/5.3/components/carousel/](https://getbootstrap.com/docs/5.3/components/carousel/).

The URL of the display page is entered into the Raspberry Pi so that the carousel is displayed automatically after starting. Screens is for a simple and convenient display of the slideshow on the screen. We can create a certain number of screens, which corresponds to the number of Raspberry Pis, and give each of them the URL. This way you don't have to change the URL after every slideshow on the Raspberry Pis, and you can replace the favorite slideshow in the screen edit. The URL always remains the same.

In cases where a slideshow is edited while it is displayed on the Raspberry Pi, we have added a small inline script in the display HTML page of Screens. This updates the page every hour so that the slideshow is also updated with its changes. 

```jsx
  setTimeout(function() {
    location.reload();
  }, 3600000);
```

![*Dashboard Display*](/Images/Dashboard.png)

*Dashboard Display*

The final dashboard automatically switches between the 3D printers and lasers & milling machines every 20 seconds. The bars represent from when to when a tool is reserved. For the 3D printers, the dashboard also scrolls down slowly to show everything. The dashboard itself is also integrated into the MakeSign website and can be accessed manually via the homepage. 

We recommend using Raspberry Pis to operate the screens and the dashboard and to display the URL. We have described how to set these up as satellites in our guides. Basically, all you need is a system that can display a website in full screen.

# 4. Docs

### How to setup the Raspberry Pi as Screen for MakeSign

**Steps**

1. First you'll need to decide if you want to run the digital signage screen via Wifi(Eduroam/Easyroam) or Ethernet. If you choose to run it via Wifi, the setup will be a little more difficult. 
2. If you want to Setup the Screen using Wifi follow [Setup of screen using wifi(Easyroam)]
3. If you want to Setup the Screen using Ethernet follow this Guide

**Setting up the screen using an Ethernet connection** 

If you can connect the screen via Ethernet you are saving yourself the hassle of installing a custom GUI and managing the Easyroam certificates. In order to display the display URL of the MakeSign screen, we are going to configure and use Fullapage OS

**Installing Fullpage OS:** 

1. Your going to need the Raspberry pi imager on your computer: [https://www.raspberrypi.com/software/](https://www.raspberrypi.com/software/) 
	Insert the SD card into your computer
			1. Open the Imager 
			2. Choose your device 
			3. For the OS you'll need to choose  
				->Other general-purpose OS
					->FullPage OS
			4. Choose your sd card 
			5. Now click next, a popup is gonna appear, discard all the edit configurations and flash the image to your card
2. To set the Makesigns URL as the url to be displayed, Open /boot/fullpageos.txt in a text editor, it should show up in the boot volume when you attach the SD card to a windows, mac or linux. 
3. Now when connecting the Pi to Ethernet and a screen, your  digital signage board should boot up as expected. 

Congratulations! Your screen is setup :D

### How to install Bootstrap for the MakeSign application

Generating the necessary files for bootstrap to run: 
Run:

```
bundle install

```

And install Bootstrap:

```
./bin/rails css:install:bootstrap

```

In Terminal/Bash you might need to do:

```
  bundle add jsbundling-rails

```

Then run:

```
  ./bin/rails javascript:install:esbuild

```

you should not have any errors...

If you get an error regarding Turborails and stimulus run:

```
  yarn add bootstrap @popperjs/core @hotwired/turbo-rails @hotwired/stimulus

```

Now try “bin/rails s” The server should be running. Try the dropdown menu in the Navbar to test that the js is working...If there is no Navbar you can add it via copying the html code from the Bootstrap Docs

Bootstrap Docs:
[https://getbootstrap.com/docs/5.2](https://getbootstrap.com/docs/5.2)

Bootstrap Docs for navbar:
[https://getbootstrap.com/docs/5.2/components/navbar/](https://getbootstrap.com/docs/5.2/components/navbar/)