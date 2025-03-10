---
title: Reolink IP NVR/camera
description: Instructions on how to integrate Reolink devices (NVR/cameras) into Home Assistant.
ha_category:
  - Camera
  - Update
ha_iot_class: Local Push
ha_release: 2023.1
ha_domain: reolink
ha_codeowners:
  - '@starkillerOG'
ha_config_flow: true
ha_platforms:
  - binary_sensor
  - button
  - camera
  - light
  - number
  - select
  - sensor
  - siren
  - switch
  - update
ha_integration_type: integration
ha_dhcp: true
---

The integration allows you to control [Reolink](https://reolink.com/) NVRs or cameras.

{% include integrations/config_flow.md %}

A Reolink user account with admin privileges is needed for the proper operation of this integration.

{% include integrations/option_flow.md %}
{% configuration_basic %}
Protocol:
  description: Switch between RTSP, RTMP or FLV streaming protocol.
{% endconfiguration_basic %}

## Camera streams

This integration creates a few camera entities, one for each stream type with different resolutions: Main, Sub, Ext, Snapshots Main, and Snapshots Sub.
The Sub stream camera entity is enabled by default; the other streams are disabled by default.
The Images stream provides a sequence of image snapshots giving very low latency at the cost of a very low frame rate; this can be used when the RTMP/RTSP/FLV video stream has too much lag.
Dual lens cameras provide additional streams for the second lens.

## Binary sensors

Depending on the supported features of the camera, binary sensors are added for:

- Motion detection
- Doorbell presses
- AI person detection
- AI vehicle detection
- AI pet detection
- AI face detection

These sensors receive events using 3 methods in order: ONVIF push, ONVIF long polling or fast polling (every 5 seconds).
The latency for receiving the events is the best for ONVIF push and the worst for fast polling, the fastest available method that is detected to work will be used, and slower methods will not be used.
For redundancy, these sensors are polled every 60 seconds together with the update of all other entities.
Not all camera models generate ONVIF push events for all event types, some binary sensors might, therefore, only be polled.
For list of Reolink products that support ONVIF see the [Reolink Support Site](https://support.reolink.com/hc/en-us/articles/900000617826).
To ensure you have the best latency possible, refer to the [Reducing latency of motion events](#Reducing_latency_of_motion_events) section.

## Asterisk (*) next to entities listed in this documentation

If an entity listed below has an asterisk (*) next to its name, it means it is disabled by default. To use such an entity, you must [enable the entity](/common-tasks/general/#enabling-entities) first.

## Number entities

Depending on the supported features of the camera, number entities are added for:

- Optical zoom control
- Focus control
- Floodlight turn on brightness
- Volume
- Guard return time
- Motion sensitivity
- AI face sensitivity
- AI person sensitivity
- AI vehicle sensitivity
- AI pet sensitivity
- AI face delay*
- AI person delay*
- AI vehicle delay*
- AI pet delay*
- Auto quick reply time
- Auto track limit left
- Auto track limit right
- Auto track disappear time
- Auto track stop time

"Floodlight turn on brightness" controls the brightness of the floodlight when it is turned on internally by the camera (see "Floodlight mode" select entity) or when using the "Floodlight" light entity.

When the camera is not moved and no person/pet/vehicle is detected for the "Guard return time" in seconds, and the "Guard return" switch is ON, the camera will move back to the guard position.

When a Reolink doorbell is pressed the quick reply message from the "Auto quick reply message" select entity will be played after "Auto quick reply time" seconds, unless the "Auto quick reply message" is set to off.

If the "Auto tracking" switch entity is enabled, and a object disappears from view OR stops moving for the "Auto track disappear time"/"Auto track stop time", the camera goes back to its original position.

## Button entities

Depending on the supported features of the camera, button entities are added for:

- PTZ stop
- PTZ left
- PTZ right
- PTZ up
- PTZ down
- PTZ calibrate
- PTZ zoom in*
- PTZ zoom out*
- Guard go to
- Guard set current position
- Restart*

PTZ left, right, up, down, zoom in and zoom out will continually move the camera in the respective position until the PTZ stop is called or the hardware limit is reached.

"Guard set current position" will set the current position as the new guard position.

## Select entities

Depending on the supported features of the camera, select entities are added for:

- Floodlight mode (Off, Auto, Schedule)
- Day night mode (Auto, Color, Black&White)
- PTZ preset
- Auto quick reply message
- Auto track method (Digital, Digital first, Pan/Tilt first)
- Status LED (Doorbell only: Stay off, Auto, Auto & always on at night)

PTZ preset positions can be set in the Reolink app/windows/web client, the names of the presets will be loaded into Home Assistant at the start of the integration. When adding new preset positions, please restart the Reolink integration.

Auto quick reply messages can be recorded in the Reolink app where a name is also supplied. New or updated quick reply messages will be loaded into Home Assistant at the start of the integration. When adding new quick reply messages, please restart the Reolink integration.

## Siren entities

If the camera supports a siren, a siren entity will be created.
When using the siren turn-on service, the siren will continue to sound until the siren turn-off service is called.

In some camera models, there is a delay of up to 5 seconds between the turn-off command and the sound stopping. The siren turn-on service supports setting a volume and a duration (no turn-off service call is needed in that case).

## Switch entities

Depending on the supported features of the camera, switch entities are added for:

- Record audio
- Siren on event
- Auto tracking
- Auto focus
- Guard return
- Doorbell button sound
- Record
- Push notifications
- Buzzer on event
- Email on event
- FTP upload

For NVRs, a global switch for Record, Push, Buzzer, Email, and FTP will be available under the NVR device as well as a switch per channel of the NVR under the camera device. The respective feature will only be active for a given channel if both the global and that channel switch are enabled (as is also the case in the Reolink app/client).

## Light entities

Depending on the supported features of the camera, light entities are added for:

- Floodlight
- Infra red lights in night mode
- Status LED

When the floodlight entity is ON always ON, when OFF controlled based on the internal camera floodlight mode (Off, Auto, Schedule), see the "Floodlight mode" select entity.

When IR light entity is OFF always OFF, when ON IR LEDs will be on when the camera is in night vision mode, see the "Day night mode" select entity.

## Sensor entities

Depending on the supported features of the camera, the following sensor entities are added:

- PTZ pan position
- Wi-Fi signal*

## Update entity

An update entity is available that checks for firmware updates every 12 hours.
This does the same as pressing the "Check for latest version" in the Reolink applications.
Unfortunately this does not always shows the latest available firmware (also not in the Reolink applications).
The latest firmware can be downloaded from the [Reolink download center](https://reolink.com/download-center/) and uploaded to the camera/NVR manually.

## Tested models

The following models have been tested and confirmed to work:

- C1 Pro*
- C2 Pro*
- [CX410](https://reolink.com/product/cx410/)
- [E1 Zoom](https://reolink.com/product/e1-zoom/)
- [E1 Outdoor](https://reolink.com/product/e1-outdoor/)
- [E1 Outdoor PoE](https://reolink.com/product/e1-outdoor-poe/)
- [E1 Outdoor Pro](https://reolink.com/product/e1-outdoor-pro/)
- RLC-410*
- [RLC-410W](https://reolink.com/product/rlc-410w/)
- RLC-411*
- RLC-420*
- RLC-423*
- [RLC-510A](https://reolink.com/product/rlc-510a/)
- RLC-511*
- RLC-511W*
- [RLC-511WA](https://reolink.com/product/rlc-511wa/)
- RLC-520*
- [RLC-520A](https://reolink.com/product/rlc-520a/)
- RLC-522*
- [RLC-810A](https://reolink.com/product/rlc-810a/)
- [RLC-811A](https://reolink.com/product/rlc-811a/)
- [RLC-81PA](https://reolink.com/product/rlc-81pa/)
- [RLC-820A](https://reolink.com/product/rlc-820a/)
- [RLC-822A](https://reolink.com/product/rlc-822a/)
- [RLC-823A](https://reolink.com/product/rlc-823a/)
- [RLC-833A](https://reolink.com/product/rlc-833a/)
- [RLC-1224A](https://reolink.com/product/rlc-1224a/)
- [RLN8-410 NVR](https://reolink.com/product/rln8-410/)
- [RLN16-410 NVR](https://reolink.com/product/rln16-410/)
- [RLN36 NVR](https://reolink.com/product/rln36/)
- [Reolink Duo WiFi](https://reolink.com/product/reolink-duo-wifi-v1/)
- [Reolink Duo 2 WiFi](https://reolink.com/product/reolink-duo-wifi/)
- [Reolink Duo Floodlight PoE](https://reolink.com/product/reolink-duo-floodlight-poe/)
- Reolink TrackMix ([PoE](https://reolink.com/product/reolink-trackmix-poe/) and [Wi-Fi](https://reolink.com/product/reolink-trackmix-wifi/))
- Reolink Video Doorbell ([PoE](https://reolink.com/product/reolink-video-doorbell/) and [Wi-Fi](https://reolink.com/product/reolink-video-doorbell-wifi/))

*These models are discontinued and not sold anymore, they will continue to work with Home Assistant.

Battery-powered cameras are not yet supported.

The following models are lacking the HTTP web server API and can, therefore, not work directly with this integration.
However, these cameras can work with this integration through an NVR in which the NVR is connected to Home Assistant.

- E1 Pro
- E1
- Reolink Lumus

## Initial Setup

A brand new Reolink camera first needs to be connected to the network and initialized. During initialization, the credentials for the camera need to be set.
There are serveral ways to achieve this.

### Reolink app/client

The recommended way is to use the [Reolink mobile app, Windows, or Mac client](https://reolink.com/software-and-manual/). Follow the on-screen instructions.  In Home Assistant, use the credentials you just configured in the Reolink app/client.

### Web browser

When your camera has a LAN port (most Wi-Fi cameras also have a LAN port), first connect the camera to your network using a LAN cable.
Find the IP address of the camera (for example by checking in your router) and go to the IP address in a web browser.
Follow the on-screen instructions to first setup the credentials (use the same credentials in Home Assistant).
If it is a Wi-Fi camera, go to **settings** (gear icon) > **Network** and fill in your Wi-Fi SSID and password. After that you can disconnect the LAN cable and the camera will automatically switch to the Wi-Fi connection.
Now set up the Reolink Home Assistant integration using the credentials you just specified.

### QR code

You can also connect a Wi-Fi camera using a self-made QR code. Once connected, follow the instructions under **Web browser**.
Create a QR code using ISO-8859-1 character encoding (not UTF-8) with the following XML string:

    <QR><S>ssid</S><P>password</P><C>last4</C></QR>

Use the `ssid` and `password` of your Wi-Fi network.
The `last4` are the last 4 digits of the QR code which is printed (on the underside) of the camera itself.
Normally, the digits are printed directly under the QR code. Alternatively, you could scan the QR code and grab the last 4 digits.

Then power up the camera while pointing it at the QR code. It takes about a minute to initialize, read the QR code, and connect to your Wi-Fi.

## Troubleshooting

- Older firmware versions do not expose the necessary information the integration needs to function. Ensure the camera is updated to the [latest firmware](https://reolink.com/download-center/) prior to setting up the integration. Note that Reolink auto update and check for update functions in the app/windows/web client often do not show the latest available firmware version. Therefore check the version in the [Reolink download center](https://reolink.com/download-center/) online.
- Ensure at least one of the HTTP/HTTPS ports is enabled in the [windows](https://reolink.com/software-and-manual/)/web client under `Settings`->`Network`->`Advanced`->`Port Settings`, see [additional instructions](https://support.reolink.com/hc/en-us/articles/900004435763-How-to-Set-up-Reolink-Ports-Settings-via-Reolink-Client-New-Client-) on the Reolink site.
- On some camera models, the RTMP port needs to be enabled in order for the HTTP(S) port to function properly. Make sure this port is also enabled if you get a `Cannot connect to host` error while one of the HTTP/HTTPS ports is already enabled.
- Setting a static IP address for Reolink cameras/NVRs in your router is advisable to prevent (temporal) connectivity issues when the IP address changes.
- Do not set a static IP in the Reolink device itself, but leave the **Connection Type** on **DHCP** under **Settings** > **Network** > **Network Information** > **Set Up**. If you set it to **static** on the Reolink device itself, this is known to cause incorrect DHCP requests on the network. The incorrect DHCP request causes Home Assistant to use the wrong IP address for the camera, resulting in connection issues. The issue originates from the Reolink firmware, which keeps sending DCHP requests even when you set a static IP address in the Reolink device.
- Reolink cameras can support a limited amount of simultaneous connections. Therefore using third-party software like Frigate, Blue Iris, or Scrypted, or using the ONVIF integration at the same time can cause the camera to drop connections. This results in short unavailabilities of the Reolink entities in Home Assistant. Especially when the connections are coming from the same device (IP) where Home Assistant is running, the Reolink cameras can get confused, dropping one connection in favor of the other originating from the same host IP. If you experience disconnections/unavailabilities of the entities, please first temporarily shut down the other connections (like Frigate) to diagnose if that is the problem. If that is indeed the problem, you could try moving the third-party software to a different host (IP address) since that is known to solve the problem most of the time. You could also try switching the protocol to FLV on Home Assistant and/or the third-party software, as that is known to be less resource-intensive on the camera.

### Reducing latency of motion events

ONVIF push will result in slightly faster state changes of the binary motion/AI event sensors than ONVIF long polling.
However, ONVIF push has some additional network configuration requirements:

- Reolink products can not push motion events to an HTTPS address (SSL).
Therefore, make sure a (local) HTTP address at which HA is reachable is configured under **Home Assistant URL** in the {% my network title="network settings" %}.
A valid address could, for example, be `http://192.168.1.10:8123` where `192.168.1.10` is the IP of the Home Assistant device".

- Since a HTTP address is needed, Reolink push is incompatible with a global SSL certificate.
Therefore, ensure no Global SSL certificate is configured in the [`configuration.yaml` under HTTP](/integrations/http/#ssl_certificate).
An SSL certificate can still be enforced for external connections, by, for instance, using the [NGINX add-on](https://github.com/home-assistant/addons/tree/master/nginx_proxy) or [NGINX Proxy Manager add-on](https://github.com/hassio-addons/addon-nginx-proxy-manager) instead of a globally enforced SSL certificate.
