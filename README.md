# LabelBridge (working title)

Use a **Brother PT-2100** label printer with **Brother P-touch Editor for Mac**.

Brother does not offer macOS support for the PT-2100: there is no Mac driver, and
P-touch Editor for Mac does not list the printer. This project makes the printer
usable on a Mac, so that a device that works fine is not thrown away.

Status: experimental, one developer, one printer. On an Apple Silicon Mac,
P-touch Editor 5.3.24 prints a test label and its media check detects the
inserted 6 mm laminated tape, using approach 1 below. Other tape widths and
cutting have not been tested. Approach 2 is not working yet. Not affiliated with
Brother.

## The problem

P-touch Editor for Mac does not print through a normal printer driver (CUPS).
It opens USB printers itself, through the IOKit USB interfaces, and offers only
the models in a list built into the app. A driver, a PPD file or a CUPS backend
therefore cannot add a printer to it. The PT-2100 itself speaks the same raster
command set as models Editor already supports (it identifies as `CMD:PT-CBP`).
So far, only the model check has stood in the way: with it out of the way,
printing works and Editor's media check detects the inserted tape correctly.

## What the project does

Two ways to make Editor see a model it supports, and to pass the print job to the
real PT-2100 unchanged (only the model identity differs):

1. **Today: a small library inside a separate copy of Editor.** It changes the
   model name, USB product ID and the model code in the printer's status reply
   that Editor sees, so the PT-2100 appears as a PT-P700. The installed Editor is
   never modified; a separate, re-signed copy is built from the user's own
   installed app. This works, but needs that separate copy and may break with
   an Editor update.
2. **Planned: a synthetic USB device.** A small background program creates a
   virtual USB host controller with one device that describes itself as a model
   Editor supports and relays the job and the replies to the real PT-2100. The
   unmodified, installed Editor then works as it is. This needs Apple's
   restricted entitlement `com.apple.developer.usb.host-controller-interface`,
   which is why a request is being made. The program is written as far as the
   entitlement allows (controller and device bring-up); the transfer handling is
   not written yet, because it cannot be tested without the entitlement.

## What it uses and does not use

- It uses the printer's own USB interface and the raster command set used by
  Brother's P-touch models. The project contains no Brother code or artwork; a
  few model identifiers (model codes) were taken from the installed Editor app.
- It handles no personal data, uses no network and does not change any system
  setting. The virtual device exists only while the program runs and only for this
  printer: one controller, one port, one full-speed device (control endpoint and
  one bulk IN and one bulk OUT endpoint, no isochronous transfers).

## Who it is for

Owners of a PT-2100 who use a Mac. The same approach may help with other Brother
label printers that Editor for Mac does not list, but only the PT-2100 is tested.

## Distribution

Planned: a paid app on the Mac App Store, based on approach 2 only. Approach 1 is
a development technique and a personal-use workaround: it works on a copy of
Brother's app that is built on the user's own Mac from their own installed
copy, and it is not distributed.

## Trademarks

Brother and P-touch are trademarks of Brother Industries, Ltd. This project is
independent and not endorsed by Brother.
