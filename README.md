# Home-Assistant---Unifi-Access---Webhook-integration
Describes how to integrate Unifi Access with Home Assistant to capture access events.

**Goal**  
Forward alarms from Unifi Access to Home Assistent. 
For example, send information to Home Assistant about who has opened the a door and what method was used.

<img width="573" height="82" alt="image" src="https://github.com/user-attachments/assets/5cd65408-c6dc-4d89-9728-6c1f6761ffad" />
  
**Configure an automation with a webhook trigger in Home Assistant**
1. Open the Home Assistant client
2. **Settings** --> **Automations & scenes** --> **Create  automation** --> **Create new automation**
3. Below **When** --> **Add trigger**
4. Select **Webhook** as the trigger type
5. Click the **Gear** and configure the webhook for **POST** and **Only accessible from the local network**
6. Click the copy icon to copy the webhook to the clipboard.
7. Click **Save**, provide a name and save the automation/

**Configure webhook in Unifi Access**
1. Open the Unifi Access portal
2. Open the Alarm manager
3. Create a new alarm
4. Below **Trigger**, select **Door unlocked**
5. Below **Scope**, select  **Include**
6. Below **Location** select the Unifi Access device to monitor.
7. Below **Action**, select **Webhook**
8. Below  **Delivery URL**, paste the Home Assistant Webhook url from the clipboard.  
   Make sure the format looks like http://your HA instance:8123/api/webhook/-eN3X0jkYKtVCcS3q8n7lB1Zr
9. Below **Delivery Method**, select **POST**
10. Name your alarm and click **save**

**Check the web hook**
1. Open the newly created automation in home assustant
2. Trigger a door event by for example opening the door with a PIN code
3. Verify the webhook is recognized by the trigger:    
   <img width="479" height="68" alt="image" src="https://github.com/user-attachments/assets/350a6b47-51f6-48dc-a7ed-0c0caecf1777" />

The webook includes information about the event that triggered the alarm in JSON format.
```
json:
    alarm_id: 019a249e-15ac-75e0-a3cf-9019c6e180a3
    events:
      - direction: entered
        id: access.unlocks.location_unlocked
        location: 38853957-8276-3f88-584a-5452cb45307f
        scope:
          locations: 38853957-8276-3f88-584a-5452cb45307f
        trigger_user: 2e852fdc-2214-434c-9011-0f457ea9eaf0
        unlock_method: touch_pass
        unlock_method_text: Touch Pass
        user: 2e852fdc-2214-434c-9011-0f457ea9eaf0
```
The provided JSON message can be examined through the trace option in the Home Assistant Automation, check **Step details** --> **Changed variables**.  
By using a template you can extract the information from the JSON message and for example use it to populate text help helpers:

```
action: input_text.set_value
metadata: {}
data:
  value: "{{trigger.json.events[0].direction}}"
target:
  entity_id: input_text.access_webhook_voordeur_direction
```


