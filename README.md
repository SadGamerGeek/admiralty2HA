# admiralty2HA
Getting data from admiralty api into Home Assistant

-----------------------------------------------------------------------------------------------------------
_Python file_

#!/usr/bin/python3

import requests,json

response = requests.get(
  "https://admiraltyapi.azure-api.net/uktidalapi/api/V1/Stations/0124/TidalEvents?duration=7",
  headers={ 'Ocp-Apim-Subscription-Key': '5607cc6e7ef24675a466393e2feba56f' }
)

# Do something with response data.
json_data = response.json()

print('{ \"data\":',json.dumps(json_data, sort_keys=True, indent=4),'}')

-----------------------------------------------------------------------------------------------------------
_Markdown card_

type: markdown
title: Mersea high tides
content: >
  {% set ns = namespace() %}  {% set s = "&nbsp;&nbsp;&nbsp;|" %}

  &nbsp;| || 1st  |       || 2nd |        |

  ------|-||-----:|:------||----:|:------:| 

  {%- for entry in states.sensor.tides_admiralty.attributes.data  if
  entry.EventType == 'HighWater' -%}
     {%- set day  = as_timestamp(entry.DateTime) | timestamp_custom("%a | %d/%m")  -%}
     {%- set tide = s ~ as_timestamp(entry.DateTime) | timestamp_custom("%H:%M") ~ "|**" ~ entry.Height | round(1) ~ "m**" -%}

      {%- if  ns.lineDate != entry.Date %}
        {%- if  ns.lineDate is defined %}
     {{ns.day}} | {{ns.tide1}} | {{ns.tide2}} |
        {%- endif %}
        {%- set ns.day    = day  -%}
        {%- set ns.tide1  = tide -%}    
        {%- set ns.tide2  = s ~ "  -  |  - " -%}    
      {% else %}
        {%- set ns.tide2  = tide -%}    
      {%- endif -%}
      {%- set ns.lineDate=entry.Date -%}
  {% endfor %}
     {{ns.day}} | {{ns.tide1}} | {{ns.tide2}} |

-----------------------------------------------------------------------------------------------------------


