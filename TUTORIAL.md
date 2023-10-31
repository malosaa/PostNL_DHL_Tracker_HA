# Home Assistant - PostNL Tracking Code

This repository contains a custom Home Assistant integration for tracking parcels from PostNL, a Dutch postal service company. With this integration, you can easily monitor the status and details of your PostNL deliveries within your Home Assistant setup.
My first time using GITHUB, so go easy on me :D

## Features

- Real-time tracking information for your PostNL parcels.
- Displays detailed information about your delivery, such as recipient, sender, delivery time, and status.
- Supports Lovelace UI integration for a user-friendly frontend experience.
- Update directly after you enter the trackingcode 

## Configuration

To set up the PostNL Tracking Integration in Home Assistant, follow these steps:
1. create 2 text input helpers named ``` PostNL Postal Code ``` and ```PostNL Tracking Code```
  

2. You'll start by configuring the PostNL tracking sensors and template sensors in your configuration.yaml or your preferred configuration file.
   the unique_id can be everything you want.

```yaml
rest:
  - authentication: basic
    resource_template: "{{ states('sensor.postNL_rest_resource') }}"
    scan_interval: 80000
    sensor:
      - name: postnl_tracker
        unique_id: 854729072388394756
        value_template: 'ok'
        json_attributes:
          - "processed"
          - "colli"
 
sensor:
  - platform: template
    sensors:
      postnl_rest_resource:
        friendly_name: "PostNL REST Resource"
        value_template: >-
          https://jouw.postnl.nl/track-and-trace/api/trackAndTrace/{{ states('input_text.postnl_tracking_code') }}-NL-{{ states('input_text.postnl_postal_code') }}?language=nl&D=NL
```
In the code above, we've defined the RESTful sensor for PostNL tracking. It retrieves tracking information based on the provided tracking code and postal code. We also have template sensors to extract specific attributes from the tracking data.

## Step 2: Template Sensors
The template sensors extract specific information from the PostNL tracking data.

```yaml

sensor:
  - platform: template
    sensors:
      
      postnl_rest_resource:
        friendly_name: "PostNL REST Resource"
        value_template: >-
          https://jouw.postnl.nl/track-and-trace/api/trackAndTrace/{{ states('input_text.postnl_tracking_code') }}-NL-{{ states('input_text.postnl_postal_code') }}?language=nl&D=NL
      
      
      
      postnl_identification:
        friendly_name: 'Identification'
        value_template: >
          {% set tracking_code = states('input_text.postnl_tracking_code') %}
          {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['identification'] }}

      postnl_recipient:
        friendly_name: 'Recipient'
        value_template: >
          {% set tracking_code = states('input_text.postnl_tracking_code') %}
          {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['recipient']['names']['personName'] }}
        attribute_templates:
           streetname: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['recipient']['address']['street'] }}
           postalCode: >
             {% set tracking_code = states('input_text.postnl_tracking_code') %}
             {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['recipient']['address']['postalCode'] }}
           town: >
             {% set tracking_code = states('input_text.postnl_tracking_code') %}
             {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['recipient']['address']['town'] }}
           country: >
             {% set tracking_code = states('input_text.postnl_tracking_code') %}
             {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['recipient']['address']['country'] }}
      postnl_sender:
        friendly_name: 'Sender'
        value_template: >
          {% set tracking_code = states('input_text.postnl_tracking_code') %}
          {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['sender']['names']['personName'] }}
        attribute_templates:
           companyname: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['sender']['names']['companyName'] }}            
           streetname: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['sender']['address']['street'] }}
           postalCode: >
             {% set tracking_code = states('input_text.postnl_tracking_code') %}
             {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['sender']['address']['postalCode'] }}
           town: >
             {% set tracking_code = states('input_text.postnl_tracking_code') %}
             {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['sender']['address']['town'] }}
           country: >
             {% set tracking_code = states('input_text.postnl_tracking_code') %}
             {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['sender']['address']['country'] }}
             
      postnl_delivery_time:
        friendly_name: 'Delivery Time'
        value_template: >
          {% set tracking_code = states('input_text.postnl_tracking_code') %}
          {% set start_date_str = state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['start'].split('T')[0] %}
          {% set end_date_str = state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['end'].split('T')[0] %}
          {% set start_time_str = state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['start'].split('T')[1] | regex_replace('.[0-9]{2}\\+.*', '') | regex_replace('T', ' ') %}
          {% set end_time_str = state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['end'].split('T')[1] | regex_replace('.[0-9]{2}\\+.*', '') | regex_replace('T', ' ') %}
          {% set start_date = as_datetime(start_date_str) %}
          {% set end_date = as_datetime(end_date_str) %}
          {% set today = now().date() %}
          {% set yesterday = today - timedelta(days=1) %}
          {% set tomorrow = today + timedelta(days=1) %}
          {% set soon = today + timedelta(days=2) %}
          {% if start_date.date() == today %}
            {% set date_string = "Today" %}
          {% elif start_date.date() == tomorrow %}
            {% set date_string = "Tomorrow" %}
          {% elif start_date.date() == yesterday %}
            {% set date_string = "Yesterday" %}
          {% elif start_date.date() < soon %}
            {% set date_string = "Soon" %}
          {% else %}
            {% set date_string = start_date_str %}
          {% endif %}
          "{{ date_string }} between  {{ start_time_str }} and  {{ end_time_str }}"
       
        attribute_templates:
          date: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['start'].split('T')[0] }}
          start_time: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['start'].split('T')[1] | regex_replace(':[0-9]{2}.*$', '') }}
          end_time: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['eta']['end'].split('T')[1] | regex_replace(':[0-9]{2}.*$', '') }}
          isDelivered: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['isDelivered'] }}
          deliveryDate: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
              {% set delivery_date = state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['deliveryDate'] %}
              {% set delivery_datetime = as_timestamp(delivery_date) %}
              {% set today = as_timestamp(now()) %}
              {% set difference = today - delivery_datetime %}
              {% if difference < 0 %}
                "In the future"
              {% elif difference < 86400 %}
                "Today at {{ delivery_datetime | timestamp_custom('%H:%M') }}"
              {% elif difference < 172800 %}
                "Yesterday at {{ delivery_datetime | timestamp_custom('%H:%M') }}"
              {% else %}
                "{{ (today - delivery_datetime) // 86400 }} days ago at {{ delivery_datetime | timestamp_custom('%H:%M') }}"
              {% endif %}
          isDeliveryDateChanged: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['isDeliveryDateChanged'] }}
          isPickedUpAtRetailLocation: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['isPickedUpAtRetailLocation'] }}
          beforeFirstDeliveryAttempt: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['beforeFirstDeliveryAttempt'] }}
          isReturnShipment: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['isReturnShipment'] }}
          expectedReturnDate: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['expectedReturnDate'] }}
          isAtRetailLocation: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['isAtRetailLocation'] }}
          hasProofOfDelivery: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['hasProofOfDelivery'] }}
          statusMessage: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['statusPhase']['message'] }}
          statusRoute: >
            {% set tracking_code = states('input_text.postnl_tracking_code') %}
            {{ state_attr('sensor.postnl_tracker', 'colli')[tracking_code]['statusPhase']['route'] }}
```
 In the code above, we extract information such as identification, recipient details, sender details, and delivery time.

## Step 3: Lovelace Card Configuration
You can create a Lovelace card to display the PostNL tracking information:
```yaml
cards:
  - type: entities
    entities:
      - entity: input_text.postnl_tracking_code
      - entity: input_text.postnl_postal_code
  - type: markdown
    content: >
      **PostNL Tracking Information**

      - **Identification:** {{ states('sensor.postnl_identification') }}
      - **Recipient:** {{ states('sensor.postnl_recipient') }}
      - **Sender:** {{ states('sensor.postnl_sender') }}
      - **Delivery Time:** {{ states('sensor.postnl_delivery_time') }}
      - **Is Delivered:** {{ state_attr('sensor.postnl_delivery_time', 'isDelivered') }}
      - **Delivery Date:** {{ state_attr('sensor.postnl_delivery_time', 'deliveryDate') }}
      - **Is Delivery Date Changed:** {{ state_attr('sensor.postnl_delivery_time', 'isDeliveryDateChanged') }}
      - **Is Picked Up at Retail Location:** {{ state_attr('sensor.postnl_delivery_time', 'isDeliveryDateChanged') }}
      - **Before First Delivery Attempt:** {{ state_attr('sensor.postnl_delivery_time', 'beforeFirstDeliveryAttempt') }}
      - **Is Return Shipment:** {{ state_attr('sensor.postnl_delivery_time', 'isReturnShipment') }}
      - **Expected Return Date:** {{ state_attr('sensor.postnl_delivery_time', 'expectedReturnDate') }}
      - **Is at Retail Location:** {{ state_attr('sensor.postnl_delivery_time', 'isAtRetailLocation') }}
      - **Has Proof of Delivery:** {{ state_attr('sensor.postnl_delivery_time', 'hasProofOfDelivery') }}
      - **Status Message:** {{ state_attr('sensor.postnl_delivery_time', 'statusMessage') }}
      - **Status Route:** {{ state_attr('sensor.postnl_delivery_time', 'statusRoute') }}
```
In this code, you create a Lovelace card with an entities card to enter the tracking code and postal code. The markdown card displays the extracted PostNL tracking information.

## Step 4: Automation and Notifications (Optional)

You can set up an automation to receive notifications when there are updates to the tracking status.

Now, you have successfully set up PostNL tracking in Home Assistant and created a Lovelace card to display the tracking information.

Don't forget to customize it according to your preferences and Home Assistant setup. 


## Usage
Once you've configured the PostNL Tracking Integration and sensors, you can use the Home Assistant frontend to monitor your PostNL deliveries. The Lovelace UI card provided in this repository allows you to see tracking information at a glance, it will trigger the rest entity once you change the input of your trackingcode.


Support
If you encounter any issues or have questions about using this integration, please create an issue on this repository.
