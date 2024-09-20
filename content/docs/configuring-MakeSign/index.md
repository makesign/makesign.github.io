---
title: 'MakeSign Task & Use-Cases'
date: 2019-02-11T19:27:37+10:00
weight: 7
---

# 2. Task & Use-Cases

The requirements defined by the Makerspace and IDiA were a program that would make it as easy as possible to upload images and short videos, which would then be displayed on two monitors in the Makerspace. The monitors should also display the utilization of the machines from the booking system. The focus here was on ease of use.

Makerspace's previous solution worked via Raspberry Pis, but it often went out and showed a black screen. Adding new slides was also complicated.

### Use Case 1: Add new slide with expiration date

Goal: Insert a new slide with an expiration date into a slideshow

Actor: Makerspace

Precondition: Makerspace has created an image with the relevant information, is logged into MakeSign, and the monitor in the Makerspace is connected to a correctly set up Raspberry Pi.

Scenario:

1. Makerspace opens MakeSign and logs in.
2. They navigate to the “Upload Slide” page and upload the image.
3. After uploading, the slide is automatically cropped in 16:9 format.
4. Makerspace creates a new slideshow or adds the slide to an existing slideshow.
5. They set the display duration of the slide to 20 seconds and specify an expiration date (e.g., two days later).
6. The changes are saved on the Raspberry Pi and the system automatically recognizes the adjustment.

Postcondition: The slide is displayed in the slideshow on the monitor and automatically removed after the set date has expired.

### Use Case 2: Display Dashboard

Goal: Display machine utilization from the booking system on the monitors

Actor: Makerspace

Precondition: The connection to the booking system is active and the monitor in the Makerspace is connected to a correctly set up Raspberry Pi.

Scenario:

1. Makerspace logs in to MakeSign and navigates to the dashboard.
2. Makerspace sets the dashboard to be displayed on the second monitor.
3. The Raspberry Pi receives the settings and displays the dashboard.

Postcondition: The monitor displays the machine utilization.

### Use Case 3: Edit slide and adjust display duration

Goal: Edit an existing slide in the slideshow and adjust its display duration

Actor: Makerspace

Precondition: Makerspace is logged into MakeSign and has already created a slideshow that is displayed on a monitor.

Scenario:

1. Makerspace logs into MakeSign and goes to the “Slideshow” overview.
2. They select a slide that is part of a slideshow.
3. They use the edit function to adjust the duration of the display (e.g., from 15 seconds to 30 seconds).
4. Makerspace saves the changes and the system updates the slideshow automatically.
5. The Raspberry Pi recognizes the changes and updates the display duration on the monitor.

Postcondition: The changed slide is now displayed on the monitor for 30 seconds as part of the slideshow.

