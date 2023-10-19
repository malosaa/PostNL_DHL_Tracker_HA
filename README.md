**Unlocking Free PostNL and DHL Tracking**

🚚🆓

No need to break the bank with paid APIs! :moneybag:

I've uncovered a fantastic solution that's absolutely free for tracking PostNL and DHL shipments. The best part? It's hassle-free and requires no paid APIs. :smiley:

**What's in Store?**

Here's a sneak peek:

* Free, no-cost tracking
* Works perfectly with Dutch tracking codes
* Seamless testing process

Soon, I'll be sharing the complete code, so you can set it up yourself. Stay tuned for the release! Your Dutch PostNL and DHL tracking will soon be at your fingertips. :rocket:

here a screenshot, sorry some info needed to be redacted as its from a tester.
![image](https://github.com/malosaa/PostNL_DHL_Tracker_HA/assets/13116501/d48b510a-9379-452b-b916-ed77633fd778)


**How It Works**

This free PostNL tracking integration leverages a REST sensor to retrieve comprehensive tracking information. By setting up two input sensors and an automation, you can seamlessly monitor and update your PostNL tracking data without any additional costs.

**Key Components**

1. **Input Sensors:** This integration requires two input sensors - one for the tracking code and another for the postal code. When you update these values, the automation is triggered to refresh the tracking information.
2. **Automation:** The automation component listens for changes in the input sensors. When the tracking code or postal code is modified, it initiates a request to the PostNL API via the REST sensor to obtain the latest tracking data.
3. **Resource Template:** To seamlessly call data from the two input sensors, the integration creates a resource template. This template assists in constructing the URL for the REST sensor to access the PostNL API.

**Entities and Attributes**

The integration generates a collection of entities with associated attributes for a detailed view of your PostNL tracking:

* **Sender Information:** Contains sender details such as name, address, postal code, town, and company name.
* **Receiver Information:** Provides receiver information, including name, sender, company, postal code, and town.
* **Delivery Information:** This entity is the hub for comprehensive tracking details. It includes attributes like delivery status, delivery date, delivery time, return shipment status, and much more.

**With this improved integration, you can easily track your PostNL shipments and access all the essential tracking information right in your Home Assistant dashboard.** 

