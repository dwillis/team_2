We wrote the code based on what the WMATA API suggests. This format requests the data and returns an error if there's something wrong

import httplib, urllib, base64
import csv
import requests

headers = {
    # Request headers
    'api_key': '3c1275f8a9a44dba91b79c2acdac77d5',
}

params = urllib.urlencode({
})

try:
    conn = httplib.HTTPSConnection('api.wmata.com')
    conn.request("GET", "/Incidents.svc/json/Incidents?%s" % params, "{body}", headers)
    response = conn.getresponse()
    data = response.read()
    #print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))
    

First, we need to make sure we're collecting the data in json, which we are not doing right now, as we can see by checking the type:
type(data)

FIRST PROBLEM: how do we collect the data in json or is there a way to convert it through code?

Because we have to monitor the incidents to tweet them out, we have have to keep running the code for an extended period of time to collect many instances
Also, it has to refresh the requests every 30 seconds (which is the data rate of refresh). But we also have to consider limits to the API
Because the incidents don't stay in the system we created a list that appends every time new data comes in.
list_of_incidents = []
list_of_incidents.append(data)
print list_of_incidents

That raises the question of how .append() works when there is no data. Does it append an empty line or it doesn't append anything?

The next step is storing it in a csv file
SECOND PROBLEM: This code works, but the csv is blank, even though we have an item in the list.
outfile = open("./wmata_incidents.csv","wb")
writer = csv.writer(outfile)
writer.writerows(list_of_incidents)

The final step is to code the Twitter portion of the bot.
THIRD PROBLEM: figuring out how to do that, exactly.
